---
title: Criar um cluster privado de serviço Azure Kubernetes
description: Saiba como criar um cluster privado do Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: 87f52c5a749b531e5b0656e0b30ff0fe9c1a57bf
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398052"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Criar um cluster privado de serviço Azure Kubernetes

Num cluster privado, o plano de controlo ou servidor API tem endereços IP internos definidos no documento [RFC1918 - Atribuição de Endereços para Internets Privadas.](https://tools.ietf.org/html/rfc1918) Ao utilizar um cluster privado, pode garantir que o tráfego de rede entre o seu servidor API e as piscinas do nó permanece apenas na rede privada.

O plano de controlo ou servidor API está numa subscrição Azure Kubernetes Service (AKS) gerida pelo Azure. O cluster ou piscina de nó de um cliente está na subscrição do cliente. O servidor e o cluster ou piscina de nó podem comunicar entre si através do [serviço Azure Private Link][private-link-service] na rede virtual do servidor API e de um ponto final privado que está exposto na subnet do cluster AKS do cliente.

## <a name="prerequisites"></a>Pré-requisitos

* A versão Azure CLI 2.2.0 ou posterior

## <a name="create-a-private-aks-cluster"></a>Criar um cluster PRIVADO AKS

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos ou utilize um grupo de recursos existente para o seu cluster AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Rede básica padrão 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Onde *-enable-private-cluster* é uma bandeira obrigatória para um aglomerado privado. 

### <a name="advanced-networking"></a>Networking avançado  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Onde *-enable-private-cluster* é uma bandeira obrigatória para um aglomerado privado. 

> [!NOTE]
> Se a ponte Docker abordar o CIDR (172.17.0.1/16) entrar em conflito com a sub-rede CIDR, mude o endereço da ponte Docker adequadamente.

## <a name="options-for-connecting-to-the-private-cluster"></a>Opções de ligação ao cluster privado

O ponto final do servidor API não tem endereço IP público. Para gerir o servidor API, terá de utilizar um VM que tenha acesso à Rede Virtual Azure (VNet) do cluster AKS. Existem várias opções para estabelecer a conectividade da rede com o cluster privado.

* Crie um VM na mesma Rede Virtual Azure (VNet) que o cluster AKS.
* Utilize um VM numa rede separada e instale [o peering de rede virtual][virtual-network-peering].  Consulte a secção abaixo para obter mais informações sobre esta opção.
* Utilize uma rota expressa ou uma ligação [VPN.][express-route-or-VPN]

Criar um VM no mesmo VNET que o cluster AKS é a opção mais fácil.  A Express Route e as VPNs adicionam custos e requerem complexidade adicional de networking.  O epeering de rede virtual requer que planeie as gamas CIDR da sua rede para garantir que não existem gamas sobrepostas.

## <a name="virtual-network-peering"></a>Peering de rede virtual

Como mencionado, o peering VNet é uma forma de aceder ao seu cluster privado. Para utilizar o vnet peering você precisa configurar uma ligação entre a rede virtual e a zona privada DNS.
    
1. Vá ao grupo de recursos MC_* no portal Azure.  
2. Selecione a zona privada de DNS.   
3. No painel esquerdo, selecione o link de **rede Virtual.**  
4. Crie um novo link para adicionar a rede virtual do VM à zona privada de DNS. Leva alguns minutos para que a ligação de zona DNS fique disponível.  
5. Volte para o grupo de recursos MC_* no portal Azure.  
6. No painel certo, selecione a rede virtual. O nome da rede virtual está na forma *aks-vnet-\**.  
7. No painel esquerdo, selecione **Peerings**.  
8. Selecione **Adicionar,** adicione a rede virtual do VM e, em seguida, crie o peering.  
9. Vá à rede virtual onde tem o VM, selecione **Peerings,** selecione a rede virtual AKS e, em seguida, crie o peering. Se o endereço variar na rede virtual AKS e no choque de rede virtual do VM, o olhar falha. Para mais informações, consulte o peering da [rede Virtual.][virtual-network-peering]

## <a name="hub-and-spoke-with-custom-dns"></a>Hub e falou com DNS personalizado

[O hub e as arquiteturas faladas](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) são comumente usados para implantar redes em Azure. Em muitas destas implementações, as definições de DNS nos VNets falados são configuradas para fazer referência a um avançado central do DNS para permitir a resolução dNS baseada no local e no Azure. Ao implantar um cluster AKS num ambiente de networking, há algumas considerações especiais que devem ser tidas em conta.

![Centro de cluster privado e falou](media/private-clusters/aks-private-hub-spoke.png)

1. Por predefinição, quando um cluster privado é provisionado, um ponto final privado (1) e uma zona privada de DNS (2) são criados no grupo de recursos geridos pelo cluster. O cluster utiliza um registo A na zona privada para resolver o IP do ponto final privado para comunicação ao servidor API.

2. A zona privada de DNS está ligada apenas ao VNet a que os nós do cluster estão ligados (3). Isto significa que o ponto final privado só pode ser resolvido pelos anfitriões do VNet ligado. Em cenários em que não está configurado nenhum DNS personalizado na VNet (predefinição), este funciona sem problemas como o ponto de adesão em 168.63.129.16 para DNS que pode resolver registos na zona privada de DNS devido ao link.

3. Nos cenários em que o VNet que contém o seu cluster tem configurações dNS personalizadas (4), a implementação do cluster falha a menos que a zona privada de DNS esteja ligada à VNet que contém os resolvers DNS personalizados (5). Esta ligação pode ser criada manualmente após a criação da zona privada durante o fornecimento de clusterou ou através da automatização após a deteção da criação da zona utilizando a Política Azure ou outros mecanismos de implantação baseados em eventos (por exemplo, rede de eventos Azure e Funções Azure).

## <a name="dependencies"></a>Dependências  

* O serviço Private Link é suportado apenas no Standard Azure Load Balancer. O Equilíbrio básico de carga azure não é suportado.  
* Para utilizar um servidor DNS personalizado, adicione o Azure DNS IP 168.63.129.16 como o servidor DNS a montante no servidor DNS personalizado.

## <a name="limitations"></a>Limitações 
* As gamas autorizadas IP não podem ser aplicadas ao ponto final do servidor api privado, aplicam-se apenas ao servidor público da API
* As Zonas de Disponibilidade são atualmente suportadas para determinadas regiões, ver o início deste documento 
* As limitações do [serviço Azure Private Link][private-link-service] aplicam-se a clusters privados, pontos finais privados do Azure e pontos finais de serviço de rede virtual, que não são atualmente suportados na mesma rede virtual.
* Nenhum suporte para nós virtuais em um cluster privado para girar instâncias privadas de contentores Azure (ACI) em uma rede virtual azure privada
* Nenhum apoio à integração de Azure DevOps fora da caixa com clusters privados
* Para os clientes que precisam de permitir que o Registo de Contentores do Azure trabalhe com AKS privados, a rede virtual de registo de contentores deve ser espreitada com a rede virtual do cluster de agentes.
* Nenhum suporte atual para espaços Azure Dev
* Nenhum apoio para converter os aglomerados AKS existentes em clusters privados
* A apagamento ou modificação do ponto final privado na sub-rede do cliente fará com que o cluster deixe de funcionar. 
* O Monitor Azure para recipientes Live Data não é suportado atualmente.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

