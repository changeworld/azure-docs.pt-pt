---
title: Métricas e Alertas no Gestor de Tráfego Azure
description: Neste artigo, conheça as métricas e alertas disponíveis para O Gestor de Tráfego em Azure.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: rohink
ms.openlocfilehash: 521e6ac605d187c0f95545611a17a86cfda6e1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938592"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Métricas e alertas do Gestor de Tráfego

O Traffic Manager fornece-lhe um equilíbrio de carga baseado em DNS que inclui múltiplos métodos de encaminhamento e opções de monitorização de pontos finais. Este artigo descreve as métricas e os alertas associados que estão disponíveis para os clientes. 

## <a name="metrics-available-in-traffic-manager"></a>Métricas disponíveis no Gestor de Tráfego 

O Traffic Manager fornece as seguintes métricas por perfil que os clientes podem usar para compreender a sua utilização do Gestor de Tráfego e o estado dos seus pontos finais sob esse perfil.  

### <a name="queries-by-endpoint-returned"></a>Consultas por endpoint devolvidos
Utilize [esta métrica](../azure-monitor/platform/metrics-supported.md) para visualizar o número de consultas que um perfil do Gestor de Tráfego processa durante um período determinado. Também pode ver a mesma informação a um nível final de granularidade que o ajuda a entender quantas vezes um ponto final foi devolvido nas respostas de consulta do Gestor de Tráfego.

No exemplo seguinte, a Figura 1 exibe todas as respostas de consulta que o perfil do Gestor de Tráfego devolve. 

  
![Vista agregada de todas as consultas](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Figura 1: Vista agregada com todas as consultas*
  
A figura 2 apresenta a mesma informação, no entanto, é dividida por pontos finais. Como resultado, pode ver o volume de respostas de consulta em que um ponto final específico foi devolvido.

![Métricas do Gestor de Tráfego - vista dividida do volume de consulta por ponto final](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Figura 2: Vista dividida com volume de consulta mostrado por ponto final devolvido*

## <a name="endpoint-status-by-endpoint"></a>Estado do ponto final por ponto final
Utilize [esta métrica](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) para compreender o estado de saúde dos pontos finais do perfil. São precisos dois valores:
 - use **1** se o ponto final estiver para cima.
 - use **0** se o ponto final estiver para baixo.

Esta métrica pode ser mostrada como um valor agregado que representa o estado de todas as métricas (Figura 3), ou pode ser dividida (ver Figura 4) para mostrar o estado de pontos finais específicos. Se o primeiro, se o nível de agregação for selecionado como **Avg,** o valor desta métrica é a média aritmética do estado de todos os pontos finais. Por exemplo, se um perfil tem dois pontos finais e apenas um é saudável, então esta métrica tem um valor de **0,50** como mostrado na Figura 3. 


![Métricas do Gestor de Tráfego - visão composta do estado do ponto final](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Figura 3: Vista composta da métrica do estado do ponto final – agregação "Avg" selecionada*


![Métricas do Gestor de Tráfego - visão dividida do estado do ponto final](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Figura 4: Visão dividida das métricas do estado do ponto final*

Pode consumir estas métricas através do portal do [serviço Azure Monitor,](../azure-monitor/platform/metrics-supported.md) [REST API,](https://docs.microsoft.com/rest/api/monitor/) [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)e [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.applicationinsights)ou através da secção de métricas da experiência do portal do Gestor de Tráfego.

## <a name="alerts-on-traffic-manager-metrics"></a>Alertas sobre métricas do Gestor de Tráfego
Além de processar e exibir métricas do Traffic Manager, o Azure Monitor permite aos clientes configurar e receber alertas associados a estas métricas. Pode escolher quais as condições que devem ser satisfeitas nestas métricas para que ocorra um alerta, quantas vezes essas condições precisam de ser monitorizadas e como os alertas devem ser enviados para si. Para mais informações, consulte [o Monitor Azure alerta a documentação.](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o serviço Azure Monitor](../azure-monitor/platform/metrics-supported.md)
- Saiba como [criar um gráfico usando o Monitor Azure](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)
