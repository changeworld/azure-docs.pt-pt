---
title: Visão geral de Insights no Monitor Azure [ Microsoft Docs
description: Insights fornecem uma experiência de monitorização personalizada no Monitor Azure para aplicações e serviços particulares. Este artigo fornece uma breve descrição de cada uma das informações que estão atualmente disponíveis.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657253"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Visão geral de Insights no Monitor Azure
Insights fornecem uma experiência de monitorização personalizada para aplicações e serviços particulares. Armazenam dados na plataforma de [dados Do Monitor Do Azure](../platform/data-platform.md) e aproveitam outras funcionalidades do Monitor De Azure para análise e alerta, mas podem recolher dados adicionais e fornecer uma experiência única de utilizador no portal Azure. Acesso informações da secção **Insights** do menu Azure Monitor no portal Azure.

As seguintes secções fornecem uma breve descrição dos insights atualmente disponíveis no Monitor Azure. Consulte a documentação detalhada para obter detalhes sobre cada um.

## <a name="application-insights"></a>Application Insights
O Application Insights é um serviço de Gestão de Desempenho de Aplicações (APM) extensível para programadores Web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. Trabalha para aplicações em uma grande variedade de plataformas, incluindo .NET, Node.js e Java EE, hospedados no local, híbridos ou qualquer nuvem pública. Também se integra com o seu processo DevOps e tem pontos de ligação para uma variedade de ferramentas de desenvolvimento.

Ver [o que é Informação de Aplicação?](../app/app-insights-overview.md)

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor para Contentores
O Monitor Azure para contentores monitoriza o desempenho das cargas de trabalho dos contentores implantadas em instâncias de contentores Azure ou em clusters Kubernetes geridos hospedados no Serviço Azure Kubernetes (AKS). Monitorizar os seus contentores é fundamental, especialmente quando se está a executar um cluster de produção, em escala, com múltiplas aplicações.

Consulte [o Monitor Azure para ver a visão geral](../insights/container-insights-overview.md)dos recipientes .

![Azure Monitor para Contentores](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Monitor Azure para Grupos de Recursos (pré-visualização)
O Azure Monitor para grupos de recursos ajuda a triagem e diagnóstico de quaisquer problemas que os seus recursos individuais encontrem, ao mesmo tempo que oferece contexto sobre a saúde e desempenho do grupo de recursos como um todo.

Consulte [monitor de recursos com Monitor Azure (pré-visualização)](../insights/resource-group-insights.md).

![Monitor Azure para grupos de recursos](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Monitor Azure para VMs (pré-visualização)
O Monitor Azure para VMs monitoriza as suas máquinas virtuais Azure (VM) e conjuntos de escala de máquinas virtuais à escala. Analisa o desempenho e o estado de funcionamento das suas VMs do Windows e do Linux e monitoriza os respetivos processos e dependências noutros recursos e processos externos.

Vê [o que é o Monitor Azure para VMs?](vminsights-overview.md)

![Azure Monitor para VMs](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Monitor Azure para Redes (pré-visualização)
[O Azure Monitor for Networks](network-insights-overview.md) proporciona uma visão abrangente da saúde e das métricas para todos os recursos da sua rede. A capacidade avançada de pesquisa ajuda-o a identificar dependências de recursos, permitindo cenários como identificar recursos que estão hospedando o seu website, simplesmente procurando o nome do seu site.

![Azure Monitor para Redes](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a plataforma de [dados Azure Monitor](../platform/data-platform.md) alavancada por insights.
* Conheça as [diferentes fontes de dados utilizadas pelo Azure Monitor](../platform/data-sources.md) e os diferentes tipos de dados recolhidos por cada um dos insights.
