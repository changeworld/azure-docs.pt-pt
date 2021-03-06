---
title: Utilize o Azure Image Builder com uma galeria de imagens para VMs windows (pré-visualização)
description: Crie versões de imagem da Azure Shared Gallery utilizando o Azure Image Builder e o Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d5856780d0d9f1a1943bca1c2f076bb3ec914e1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263358"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Pré-visualização: Crie uma imagem do Windows e distribua-a para uma Galeria de Imagem Partilhada 

Este artigo é para lhe mostrar como pode usar o Azure Image Builder, e o Azure PowerShell, para criar uma versão de imagem numa Galeria de [Imagem Partilhada,](shared-image-galleries.md)e depois distribuir a imagem globalmente. Também pode fazê-lo utilizando o [Azure CLI](../linux/image-builder-gallery.md).

Vamos usar um modelo .json para configurar a imagem. O ficheiro .json que estamos a usar está aqui: [armTemplateWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Vamos descarregar e editar uma versão local do modelo, por isso este artigo é escrito usando a sessão local powerShell.

Para distribuir a imagem para uma Galeria de Imagem Partilhada, `distribute` o modelo utiliza a [SharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) como o valor para a secção do modelo.

O Azure Image Builder executa automaticamente sysprep para generalizar a imagem, este é um comando de sysprep genérico, que você pode [substituir](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) se necessário. 

Esteja ciente de quantas vezes coloca as personalizações em camadas. Pode executar o comando Sysprep até 8 vezes numa única imagem do Windows. Depois de executar o Sysprep 8 vezes, deve recriar a sua imagem do Windows. Para mais informações, consulte [Limites sobre quantas vezes pode executar sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> O Azure Image Builder está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="register-the-features"></a>Registe as características
Para utilizar o Azure Image Builder durante a pré-visualização, é necessário registar a nova funcionalidade.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Verifique o estado do registo da funcionalidade.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Espere `RegistrationState` até `Registered` passar para o próximo passo.

Verifique as inscrições do seu fornecedor. Certifique-se `Registered`de que cada devolução de cada retorna.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Se não regressarem, `Registered`utilize o seguinte para registar os prestadores:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Criar variáveis

Vamos usar algumas peças de informação repetidamente, por isso vamos criar algumas variáveis para armazenar essa informação. Substitua os valores pelas `username` `vmpassword`variáveis, como e, com a sua própria informação.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Crie um grupo de recursos e dê permissão ao Azure Image Builder para criar recursos nesse grupo de recursos.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>Criar a Galeria de Imagem Partilhada

Para utilizar o Image Builder com uma galeria de imagens partilhada, é necessário ter uma galeria de imagens e definição de imagem existentes. O Image Builder não irá criar a galeria de imagens e a definição de imagem para si.

Se ainda não tem uma definição de galeria e imagem para usar, comece por criá-las. Primeiro, crie uma galeria de imagens.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Descarregue e configure o modelo

Descarregue o modelo .json e configure-o com as suas variáveis.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>Criar a versão de imagem

O seu modelo deve ser submetido ao serviço, isto irá descarregar quaisquer artefactos dependentes, como scripts, e armazená-los no Grupo de Recursos de encenação, pré-fixado com *IT_*.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Para construir a imagem é necessário invocar 'Run' no modelo.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

Criar a imagem e replicá-la a ambas as regiões pode demorar algum tempo. Espere até que esta peça esteja terminada antes de passar para a criação de um VM.

Para obter informações sobre as opções de automatização do estado de construção da imagem, consulte o [Readme](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) para este modelo no GitHub.


## <a name="create-the-vm"></a>Crie a VM

Crie um VM a partir da versão de imagem que foi criada pelo Azure Image Builder.

Obtenha a versão de imagem que criou.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Criar o VM na segunda região que fosse a imagem foi replicada.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Verifique a personalização
Crie uma ligação Remote Desktop ao VM utilizando o nome de utilizador e a palavra-passe que definiu quando criou o VM. No interior do VM, abra um pedido cmd e escreva:

```console
dir c:\
```

Devia ver um diretório chamado `buildActions` que foi criado durante a personalização da imagem.


## <a name="clean-up-resources"></a>Limpar recursos
Se quiser agora tentar repersonalizar a versão de imagem para criar uma nova versão da mesma imagem, **ignore este passo** e continue a utilizar o [Azure Image Builder para criar outra versão](image-builder-gallery-update-image-version.md)de imagem .


Isto irá apagar a imagem que foi criada, juntamente com todos os outros ficheiros de recursos. Certifique-se de que está terminado com esta implantação antes de apagar os recursos.

Elimine primeiro o modelo do grupo de recursos, caso contrário, o grupo de recursos de encenação *(IT_*) utilizado pelo AIB não será limpo.

Obtenha O ResourceID do modelo de imagem. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Eliminar o modelo de imagem.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

eliminar o grupo de recursos.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Passos Seguintes

Para aprender a atualizar a versão de imagem que criou, consulte [o Use Azure Image Builder para criar outra versão de imagem](image-builder-gallery-update-image-version.md).
