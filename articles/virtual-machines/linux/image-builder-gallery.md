---
title: Use o Azure Image Builder com uma galeria de imagens para VMs Linux (pré-visualização)
description: Crie imagens De Linux VM com Azure Image Builder e Galeria de Imagem Partilhada.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945026"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Pré-visualização: Crie uma imagem Linux e distribua-a para uma Galeria de Imagem Partilhada 

Este artigo mostra-lhe como pode usar o Azure Image Builder, e o Azure CLI, para criar uma versão de imagem numa Galeria de [Imagem Partilhada,](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)e depois distribuir a imagem globalmente. Também pode fazê-lo utilizando [o Azure PowerShell.](../windows/image-builder-gallery.md)


Vamos usar um modelo de amostra .json para configurar a imagem. O ficheiro .json que estamos a usar está aqui: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Para distribuir a imagem para uma Galeria de Imagem Partilhada, `distribute` o modelo utiliza a [SharedImage](image-builder-json.md#distribute-sharedimage) como o valor para a secção do modelo.

> [!IMPORTANT]
> O Azure Image Builder está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="register-the-features"></a>Registe as características
Para utilizar o Azure Image Builder durante a pré-visualização, é necessário registar a nova funcionalidade.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Verifique o estado do registo da funcionalidade.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Verifique o seu registo.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Se não disserem registado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões 

Vamos usar algumas peças de informação repetidamente, por isso vamos criar algumas variáveis para armazenar essa informação.

Para pré-visualização, o construtor de imagens só apoiará a criação de imagens personalizadas no mesmo Grupo de Recursos que a imagem gerida pela fonte. Atualize o nome do grupo de recursos neste exemplo para ser o mesmo grupo de recursos que a sua fonte gerida imagem.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Crie uma variável para o seu ID de subscrição. Pode obter isto `az account show | grep id`usando.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Crie o grupo de recursos.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Dê permissão ao Azure Image Builder para criar recursos nesse grupo de recursos. O `--assignee` valor é o ID de registo de aplicações para o serviço Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Criar uma definição de imagem e galeria

Para utilizar o Image Builder com uma galeria de imagens partilhada, é necessário ter uma galeria de imagens e definição de imagem existentes. O Image Builder não irá criar a galeria de imagens e a definição de imagem para si.

Se ainda não tem uma definição de galeria e imagem para usar, comece por criá-las. Primeiro, crie uma galeria de imagens.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Então, criar uma definição de imagem.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Descarregue e configure o .json

Descarregue o modelo .json e configure-o com as suas variáveis.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Criar a versão de imagem

Esta próxima parte criará a versão de imagem na galeria. 

Envie a configuração de imagem para o serviço Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Inicie a construção da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Criar a imagem e replicá-la a ambas as regiões pode demorar algum tempo. Espere até que esta peça esteja terminada antes de passar para a criação de um VM.


## <a name="create-the-vm"></a>Crie a VM

Crie um VM a partir da versão de imagem que foi criada pelo Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

SSH na VM.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Deve ver que a imagem foi personalizada com uma *Mensagem do Dia* assim que a sua ligação SSH estiver estabelecida!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser agora tentar repersonalizar a versão de imagem para criar uma nova versão da mesma imagem, ignore os próximos passos e vá ao [Azure Image Builder para criar outra versão](image-builder-gallery-update-image-version.md)de imagem .


Isto irá apagar a imagem que foi criada, juntamente com todos os outros ficheiros de recursos. Certifique-se de que está terminado com esta implantação antes de apagar os recursos.

Ao eliminar os recursos da galeria de imagens, precisa de eliminar todas as versões de imagem antes de poder eliminar a definição de imagem utilizada para as criar. Para apagar uma galeria, primeiro é necessário ter eliminado todas as definições de imagem na galeria.

Elimine o modelo de construtor de imagens.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Obtenha a versão de imagem criada pelo `0.`construtor de imagens, isto sempre começa com, e depois eliminar a versão de imagem

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Apagar a definição de imagem.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Apague a galeria.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Elimine o grupo de recursos.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as Galerias de [Imagem Partilhada azure.](shared-image-galleries.md)