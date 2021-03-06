---
title: Tutorial do Kubernetes no Azure - Implementar um cluster
description: Neste tutorial do Azure Kubernetes Service (AKS), irá criar um cluster do AKS e utilizar o kubectl para ligar ao nó principal do Kubernetes.
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 72d7d3b8a4dc2831f397326d54560358c19b9b92
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616816"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Tutorial: Implementar um cluster do Serviço Kubernetes do Azure (AKS)

O Kubernetes dispõe de uma plataforma distribuída para aplicações em contentores. Com aks, você pode rapidamente criar um cluster kubernetes pronto para produção. Neste tutorial, parte três de sete, é implementado um cluster do Kubernetes no AKS. Saiba como:

> [!div class="checklist"]
> * Implementar um cluster Kubernetes AKS que pode autenticar um registo de contentores Azure
> * Instalar a CLI do Kubernetes (kubectl)
> * Configurar o kubectl para ligar ao seu cluster do AKS

Em tutoriais adicionais, a aplicação Azure Vote é implantada para o cluster, dimensionada e atualizada.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, foi criada e carregada uma imagem de contentor para uma instância do Azure Container Registry. Se ainda não fez estes passos, e gostaria de seguir em frente, comece no Tutorial 1 – Criar imagens de [contentores.][aks-tutorial-prepare-app]

Este tutorial requer que esteja a executar a versão Azure CLI 2.0.53 ou mais tarde. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Criar um cluster do Kubernetes

Os clusters do AKS podem utilizar os controlos de acesso baseado em funções (RBAC) do Kubernetes. Estes controlos permitem-lhe definir o acesso a recursos com base nas funções atribuídas a utilizadores. As permissões são combinadas se um utilizador tiver várias funções, e as permissões podem ser consultadas para um único espaço de nome ou em todo o cluster. Por predefinição, a CLI do Azure ativa automaticamente o RBAC quando cria um cluster do AKS.

Crie um cluster do AKS com [az aks create][]. O exemplo seguinte cria um cluster com o nome *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*. Este grupo de recursos foi criado no [tutorial anterior.][aks-tutorial-prepare-acr] Para permitir que um cluster AKS interaja com outros recursos Azure, é criado automaticamente um diretor de serviço de Diretório Ativo Azure, uma vez que não especificou um. Aqui, este diretor de serviço tem o direito de [retirar imagens][container-registry-integration] da instância do Registo de Contentores Azure (ACR) que criou no tutorial anterior.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Também pode configurar manualmente um diretor de serviço para retirar imagens do ACR. Para mais informações, consulte a [autenticação ACR com os diretores](../container-registry/container-registry-auth-service-principal.md) de serviço ou [authenticaa a partir de Kubernetes com um segredo](../container-registry/container-registry-auth-kubernetes.md)de pull .

Após alguns minutos, a implementação completa e devolve informações formatadas pela JSON sobre a implantação da AKS.

> [!NOTE]
> Para garantir que o seu cluster funcione de forma fiável, deve executar pelo menos 2 (dois) nós.

## <a name="install-the-kubernetes-cli"></a>Instalar a CLI do Kubernetes

Para ligar ao cluster de Kubernetes a partir do computador local, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes.

Se utilizar o Azure Cloud Shell, o `kubectl` já está instalado. Também pode instalá-lo a nível local com o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Ligar ao cluster com o kubectl

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][]. O exemplo que se segue obtém credenciais para o cluster AKS chamado *myAKSCluster* no *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao seu cluster, execute o [kubectl obtenha][kubectl-get] comando de nós para devolver uma lista dos nós do cluster:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.14.8
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, um cluster do cluster do Kubernetes foi implementado no AKS e configurou `kubectl` para se ligar ao mesmo. Aprendeu a:

> [!div class="checklist"]
> * Implementar um cluster Kubernetes AKS que pode autenticar um registo de contentores Azure
> * Instalar a CLI do Kubernetes (kubectl)
> * Configurar o kubectl para ligar ao seu cluster do AKS

Prossiga para o próximo tutorial para saber como implementar uma aplicação no cluster.

> [!div class="nextstepaction"]
> [Implementar aplicação no Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
