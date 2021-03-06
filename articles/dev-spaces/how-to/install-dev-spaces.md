---
title: Enable Azure Dev Spaces on AKS & instalar as ferramentas do lado do cliente
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Aprenda a ativar o Azure Dev Spaces num cluster AKS e instale as ferramentas do lado do cliente.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898955"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Ative a Azure Dev Spaces num cluster AKS e instale as ferramentas do lado do cliente

Este artigo mostra-lhe várias formas de ativar o Azure Dev Spaces num cluster AKS, bem como instalar as ferramentas do lado do cliente.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Ativar ou remover espaços Azure Dev utilizando o CLI

Antes de poder ativar a Dev Spaces utilizando o CLI, precisa de:
* Uma subscrição do Azure. Se não tiver uma subscrição Azure, pode criar uma [conta gratuita.][az-portal-create-account]
* [O Azure CLI instalado][install-cli].
* [Um aglomerado aks][create-aks-cli] numa [região apoiada.][supported-regions]

Utilize `use-dev-spaces` o comando para ativar os Espaços Dev no seu cluster AKS e siga as instruções.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

O comando acima permite a Dev Spaces no cluster *myAKSCluster* no grupo *myResourceGroup* e cria um espaço de dev *padrão.*

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

O `use-dev-spaces` comando também instala o Azure Dev Spaces CLI.

Para remover os Espaços Azure Dev do `azds remove` seu cluster AKS, utilize o comando. Por exemplo:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

O comando acima remove os Espaços Azure Dev do cluster *MyAKS* no *MyResourceGroup*. Quaisquer espaços com nomes que tenha criado com espaços Azure Dev permanecerão juntamente com as suas cargas de trabalho, mas novas cargas de trabalho nesses espaços de nome não serão instrumentadas com espaços Azure Dev. Além disso, se reiniciar as cápsulas existentes instrumentadas com espaços Azure Dev, poderá ver erros. Essas cápsulas devem ser reimplantadas sem ferramentas azure Dev Spaces. Para remover totalmente os Espaços Azure Dev do seu cluster, elimine todas as cápsulas em todos os espaços de nome onde o Azure Dev Spaces estava ativado.

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Ativar ou remover espaços Azure Dev utilizando o portal Azure

Antes de poder ativar a Dev Spaces utilizando o portal Azure, precisa de:
* Uma subscrição do Azure. Se não tiver uma subscrição Azure, pode criar uma [conta gratuita.][az-portal-create-account]
* [Um aglomerado aks][create-aks-portal] numa [região apoiada.][supported-regions]

Para permitir a Azure Dev Spaces utilizando o portal Azure:
1. Inicie sessão no [Portal do Azure][az-portal].
1. Navegue para o seu aglomerado AKS.
1. Selecione o item do menu *Dev Spaces.*
1. Alterar *ativar espaços de dev* para *sim* e clicar em *Guardar*.

![Ativar espaços de dev no portal Azure](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Ativar o Azure Dev Spaces utilizando o portal Azure **não** instala nenhuma ferramenta do lado do cliente para os Espaços Azure Dev.

Para remover os Espaços Azure Dev do seu cluster AKS, altere *os espaços de Dev ativados* para *Não* e clique em *Guardar*. Quaisquer espaços com nomes que tenha criado com espaços Azure Dev permanecerão juntamente com as suas cargas de trabalho, mas novas cargas de trabalho nesses espaços de nome não serão instrumentadas com espaços Azure Dev. Além disso, se reiniciar as cápsulas existentes instrumentadas com espaços Azure Dev, poderá ver erros. Essas cápsulas devem ser reimplantadas sem ferramentas azure Dev Spaces. Para remover totalmente os Espaços Azure Dev do seu cluster, elimine todas as cápsulas em todos os espaços de nome onde o Azure Dev Spaces estava ativado.

## <a name="install-the-client-side-tools"></a>Instale as ferramentas do lado do cliente

Você pode usar as ferramentas do lado do cliente Azure Dev Spaces para interagir com espaços de v em um cluster AKS da sua máquina local. Existem várias formas de instalar as ferramentas do lado do cliente:

* No Código do [Estúdio Visual,][vscode]instale a [extensão Dos Espaços Azure Dev.][vscode-extension]
* No [Visual Studio 2019,][visual-studio]instale a carga de trabalho do Desenvolvimento Azure.
* No Visual Studio 2017, instale a carga de trabalho de Desenvolvimento Web e [ferramentas de estúdio visual para kubernetes.][visual-studio-k8s-tools]
* Descarregue e instale o [Windows,][cli-win] [Mac][cli-mac]ou [Linux][cli-linux] CLI.

## <a name="next-steps"></a>Passos seguintes

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários recipientes e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento de equipa em Espaços Azure Dev][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
