---
title: Certificados de rotação no Serviço Azure Kubernetes (AKS)
description: Aprenda a rodar os seus certificados num cluster do Serviço Azure Kubernetes (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00dcef4ae0f04fc7f550859238ae8c7e1ad19384
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549074"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Certificados de rotação no Serviço Azure Kubernetes (AKS)

O Serviço Azure Kubernetes (AKS) utiliza certificados para autenticação com muitos dos seus componentes. Periodicamente, poderá ter de rodar esses certificados por razões de segurança ou política. Por exemplo, pode ter uma política para rodar todos os seus certificados a cada 90 dias.

Este artigo mostra-lhe como rodar os certificados no seu cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que esteja a executar a versão Azure CLI 2.0.77 ou mais tarde. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certificados AKS, Autoridades de Certificados e Contas de Serviço

A AKS gera e utiliza os seguintes certificados, Autoridades de Certificados e Contas de Serviço:

* O servidor AKS API cria uma Autoridade de Certificados (CA) chamada Cluster CA.
* O servidor API tem um Cluster CA, que assina certificados para comunicação de ida do servidor API para kubelets.
* Cada kubelet também cria um Pedido de Assinatura de Certificado (CSR), assinado pelo Cluster CA, para comunicação do kubelet para o servidor API.
* A loja de valor-chave etcd tem um certificado assinado pelo Cluster CA para comunicação de etcd para o servidor API.
* A loja de valor-chave etcd cria um CA que assina certificados para autenticar e autorizar a replicação de dados entre réplicas etcd no cluster AKS.
* O agregador API utiliza o Cluster CA para emitir certificados para comunicação com outras APIs. O agregador API também pode ter o seu próprio CA para a emissão desses certificados, mas atualmente utiliza o Cluster CA.
* Cada nó usa um símbolo de Conta de Serviço (SA), assinado pelo Cluster CA.
* O `kubectl` cliente tem um certificado para comunicar com o cluster AKS.

> [!NOTE]
> Os clusters AKS criados antes de março de 2019 têm certificados que expiram após dois anos. Qualquer cluster criado após março de 2019 ou qualquer cluster que tenha os seus certificados rodados tem certificados De Acluster CA que expiram após 30 anos. Todos os outros certificados expiram após dois anos. Para verificar quando o seu `kubectl get nodes` cluster foi criado, use para ver a *idade* das piscinas do seu nó.
> 
> Além disso, pode verificar a data de validade do certificado do seu cluster. Por exemplo, o seguinte comando apresenta os dados do certificado para o cluster *myAKSCluster.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Rode os certificados de cluster

> [!WARNING]
> A rotação dos `az aks rotate-certs` certificados utilizando pode causar até 30 minutos de tempo de inatividade para o seu cluster AKS.

Use [az aks obter credenciais][az-aks-get-credentials] para iniciar sessão no seu cluster AKS. Este comando também descarrega `kubectl` e configura o certificado de cliente na sua máquina local.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Utilize `az aks rotate-certs` para rodar todos os certificados, CAs e SAs no seu cluster.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Pode levar até 30 `az aks rotate-certs` minutos para completar. Se o comando falhar antes `az aks show` de ser concluído, utilize para verificar se o estado do cluster é *rotativo*do certificado . Se o cluster estiver em estado `az aks rotate-certs` de falhas, volte a rodar os seus certificados novamente.

Verifique se os certificados antigos já `kubectl` não são válidos através do comando. Uma vez que não atualizou `kubectl`os certificados utilizados, verá um erro.  Por exemplo:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Atualize o `kubectl` certificado `az aks get-credentials`utilizado por execução .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Verifique se os certificados foram `kubectl` atualizados com um comando, que agora terá sucesso. Por exemplo:

```console
kubectl get no
```

> [!NOTE]
> Se tiver algum serviço que apareça em cima da AKS, como o [Azure Dev Spaces,][dev-spaces]poderá também ter de [atualizar certificados relacionados com esses serviços.][dev-spaces-rotate]

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como rodar automaticamente os certificados do seu cluster, CAs e SAs. Pode ver [as melhores práticas para a segurança do cluster e upgrades no Serviço Azure Kubernetes (AKS)][aks-best-practices-security-upgrades] para obter mais informações sobre as melhores práticas de segurança da AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
