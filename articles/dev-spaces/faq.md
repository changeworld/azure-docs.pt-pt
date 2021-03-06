---
title: Perguntas frequentes sobre espaços Azure Dev
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Encontre respostas a algumas das perguntas comuns sobre os Espaços Azure Dev
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 1fd176084cd0737a290e037ed19af510fcab3e78
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382003"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Perguntas frequentes sobre espaços Azure Dev

Este endereço frequentemente fez perguntas sobre os Espaços Azure Dev.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quais as regiões azure que atualmente fornecem espaços Azure Dev?

Consulte [as regiões apoiadas][supported-regions] para obter uma lista completa das regiões disponíveis.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Posso migrar o meu aglomerado AKS com espaços Azure Dev para outra região?

Sim, se quiser mover o seu cluster AKS com o Azure Dev Spaces para outra [região apoiada,][supported-regions]recomendamos que crie um novo cluster na outra região e depois instale e configure os Espaços Azure Dev e implemente os seus recursos e aplicações para o seu novo cluster. Para obter mais informações sobre a migração do AKS, consulte [migrate para o Serviço Azure Kubernetes (AKS)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Posso usar espaços Azure Dev com ficheiros Dev existentes ou gráficos Helm?

Sim, se o seu projeto já tiver um Dockerfile ou um gráfico Helm, pode usar esses ficheiros com o Azure Dev Spaces. Quando correr, `azds prep`utilize `--chart` o parâmetro e especifique a localização do gráfico. A Azure Dev Spaces continuará a gerar um ficheiro *azds.yaml* e *Dockerfile.develop,* mas não substituirá ou modificará um Dockerfile existente ou um gráfico Helm. Pode ser necessário modificar os *ficheiros azds.yaml* e *Dockerfile.de forma* a que `azds up`tudo funcione corretamente com a sua aplicação existente durante a execução .

Ao utilizar o seu próprio Dockerfile ou gráfico Helm, existem as seguintes limitações:
* Se utilizar apenas um Dockerfile, deve incluir tudo o que precisa para permitir cenários de desenvolvimento, como o SDK linguístico e não apenas o tempo de execução. Se utilizar um Dockerfile separado para espaços Azure Dev, como um Dockerfile.develop, tudo o que precisa para permitir cenários de desenvolvimento deve ser incluído nesse Dockerfile.
* O seu gráfico Helm deve apoiar a passagem de parte ou de toda a etiqueta de imagem como um valor a partir de *valores.yaml*.
* Se estiver a modificar qualquer coisa com ingresso, também pode atualizar o seu gráfico Helm para utilizar a solução de entrada fornecida pela Azure Dev Spaces.
* Se pretender utilizar as capacidades de [encaminhamento fornecidas pela Azure Dev Spaces,][dev-spaces-routing]todos os serviços para um projeto individual devem caber num único espaço de nome kubernetes e devem ser implantados com nomeação simples, por *exemplo, serviço-a*. Nos gráficos padrão helm, esta atualização de nomeação pode ser feita especificando um valor para a propriedade *fullnameOverride.*

Para comparar o seu próprio Dockerfile ou gráfico Helm com uma versão existente que funciona com a Azure Dev Spaces, reveja os ficheiros gerados no [arranque rápido][quickstart-cli].


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Posso modificar os ficheiros gerados pela Azure Dev Spaces?

Sim, pode modificar o ficheiro *azds.yaml,* Dockerfile e Helm [gráfico gerado pela Azure Dev Spaces ao preparar o seu projeto][dev-spaces-prep]. Modificar estes ficheiros altera a forma como o projeto é construído e executado.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Posso usar espaços Azure Dev sem um endereço IP público?

Não, não pode fornecer espaços Azure Dev num cluster AKS sem um IP público. Um IP público é [necessário pela Azure Dev Spaces para o encaminhamento.][dev-spaces-routing]

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Posso usar a minha própria entrada com o Azure Dev Spaces?

Sim, pode configurar a sua própria entrada juntamente com a entrada que o Azure Dev Spaces cria. Por exemplo, pode utilizar [traefik][ingress-traefik] ou [NGINX][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Posso usar HTTPS com espaços Azure Dev?

Sim, pode configurar a sua própria entrada com HTTPS utilizando [traefik][ingress-https-traefik] ou [NGINX][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Posso usar o Azure Dev Spaces num cluster que usa CNI em vez de kubenet? 

Sim, você pode usar Espaços Azure Dev em um cluster AKS que usa CNI para networking. Por exemplo, pode utilizar espaços Azure Dev num cluster AKS com [contentores Windows existentes,][windows-containers]que utiliza CNI para networking. Mais informações sobre a utilização do CNI para networking com os Espaços Azure Dev estão disponíveis [aqui.](configure-networking.md#using-azure-cni)

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Posso usar espaços Azure Dev com recipientes windows?

Atualmente, o Azure Dev Spaces destina-se a funcionar apenas em cápsulas linux e nós, mas pode executar o Azure Dev Spaces num cluster AKS com [contentores Windows existentes.][windows-containers]

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Posso usar espaços Azure Dev em clusters AKS com gamas de endereços IP autorizados do servidor API ativadas?

Sim, pode utilizar espaços Azure Dev em clusters AKS com gamas de [endereços IP autorizados][aks-auth-range] para servidor aPi ativadas. Mais informações sobre a utilização de um cluster AKS com gamas de endereços IP autorizados do servidor API ativadas com espaços Azure Dev estão disponíveis [aqui](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Posso usar espaços Azure Dev em aglomerados AKS com tráfego de saída restrita para nós de cluster?

Sim, você pode usar Espaços Azure Dev em clusters AKS com tráfego de [saída restrita para nós][aks-restrict-egress-traffic] de cluster ativados uma vez que as FQDNs corretas foram permitidas. Mais informações sobre a utilização de um cluster AKS com tráfego de saída restrita para nós de cluster habilitados com espaços Azure Dev estão disponíveis [aqui](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Posso usar espaços Azure Dev em clusters AKS ativados por RBAC?

Sim, pode utilizar espaços Azure Dev em clusters AKS com ou sem RBAC ativado.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>O que acontece quando eu permitir a entrada para o projeto no Estúdio Visual?

Ao utilizar o Visual Studio para preparar o seu projeto, tem a opção de permitir a entrada para o seu serviço. Permitir a ingresso cria um ponto final público para aceder ao seu serviço ao executar o seu cluster AKS, que é opcional. Se não ativar a entrada, o seu serviço só é acessível a partir do seu cluster AKS.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Posso usar identidades geridas com o Azure Dev Spaces?

Sim, pode utilizar [identidades geridas][aks-pod-managed-id] por pod em clusters AKS com espaços Azure Dev ativados, mas existem [passos de configuração adicionais][dev-spaces-pod-managed-id-steps] depois de ativar o Azure Dev Spaces no seu cluster com identidades geridas por pod. Se tiver as identidades geridas pelo pod instaladas e quiser desinstalá-la, poderá encontrar mais detalhes nas [notas de desinstalada][aks-pod-managed-id-uninstall].

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Posso usar espaços Azure Dev com vários microserviços numa aplicação?

Sim, você pode usar O Espaço Azure Dev numa aplicação com vários microserviços, mas você deve preparar e executar os microserviços individuais na sua raiz. A extensão do Código VS Espaços Azure Dev, Azure Dev Spaces e Visual Studio Azure Development esperam que o ficheiro *azds.yaml* esteja na raiz do microserviço para executar e depurar. Consulte a [aplicação][bike-sharing] da amostra bike sharing para um exemplo de múltiplos microserviços numa única aplicação.

No Visual Studio Code, é possível [abrir projetos separados num único espaço][vs-code-multi-root-workspaces] de trabalho e desinvê-los separadamente através dos Espaços Azure Dev. Cada um dos projetos deve ser autossuficiente e preparado para os Espaços Azure Dev.

No Estúdio Visual, é possível configurar soluções .NET Core para depuração através dos Espaços Azure Dev.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md