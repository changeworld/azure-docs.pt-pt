---
title: O que é o Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Saiba como a Azure Dev Spaces proporciona uma experiência rápida e iterativa de desenvolvimento de Kubernetes para equipas em clusters de Serviço Saque Dev Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8b22181bcddda9e4156c0e0dbe61d7d813498d96
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529720"
---
# <a name="what-is-azure-dev-spaces"></a>O que é o Azure Dev Spaces?

A Azure Dev Spaces proporciona uma experiência rápida e iterativa de desenvolvimento da Kubernetes para equipas em clusters do Serviço Azure Kubernetes (AKS). A Azure Dev Spaces também permite desbugicar e testar todos os componentes da sua aplicação em AKS com a configuração mínima da máquina de desenvolvimento, sem replicar ou ridicularizar dependências.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Como os Espaços de Programador do Azure simplificam o desenvolvimento da Kubernetes

A Azure Dev Spaces ajuda as equipas a concentrarem-se no desenvolvimento e rápida iteração da sua aplicação de microserviços, permitindo que as equipas trabalhem diretamente com toda a sua arquitetura de microserviços ou aplicações em aks. A Azure Dev Spaces também fornece uma forma de atualizar independentemente partes da sua arquitetura de microserviços isoladamente sem afetar o resto do cluster AKS ou outros desenvolvedores. A Azure Dev Spaces destina-se ao desenvolvimento e teste em ambientes de desenvolvimento e teste de nível inferior e não se destina a funcionar em clusters AKS de produção.

Uma vez que as equipas podem trabalhar com toda a aplicação e colaborar diretamente na AKS, Azure Dev Spaces:

* Minimiza a configuração da máquina local
* Diminui o tempo de configuração para novos desenvolvedores na equipa
* Aumenta a velocidade de uma equipa através de uma iteração mais rápida
* Reduz o número de ambientes redundantes de desenvolvimento e integração, uma vez que os membros da equipa podem partilhar um cluster
* Remove a necessidade de replicar ou simular dependências
* Melhora a colaboração entre equipas de desenvolvimento, bem como as equipas com as que trabalham, como as equipas de DevOps

A Azure Dev Spaces fornece ferramentas para gerar ativos da Docker e da Kubernetes para os seus projetos. Esta ferramenta permite-lhe facilmente adicionar aplicações novas e existentes tanto a um espaço de v como a outros clusters AKS.

Para mais informações sobre como funciona o Azure Dev Spaces, consulte [como funciona o Azure Dev Spaces e está configurado.][how-dev-spaces-works]

## <a name="supported-regions-and-configurations"></a>Regiões e configurações suportadas

A Azure Dev Spaces é apoiada apenas por aglomerados AKS em [algumas regiões.][supported-regions] A Azure Dev Spaces suporta a utilização do Código de Estúdio [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Visual](https://code.visualstudio.com/download) com a [extensão Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) instalada no Linux, macOS ou Windows 8 ou maior para construir e executar as suas aplicações no AKS. Também suporta a utilização do [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) instalado no Windows 8 ou superior. Para o Visual Studio 2019, vai precisar da carga de trabalho do Azure Development. Para o Visual Studio 2017, você precisará da carga de trabalho de Desenvolvimento Web e [ferramentas de estúdio visual para Kubernetes.](https://aka.ms/get-vsk8stools)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o rápido desenvolvimento iterativo para equipas com O Desenvolvimento Azure Dev com o desenvolvimento da [equipa quickstart][team-development-quickstart].

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md
