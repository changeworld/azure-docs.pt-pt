---
title: Visão geral dos registos e métricas da Firewall Azure
description: Pode monitorizar os registos do Azure Firewall com registos de firewall. Também pode utilizar os registos de atividades para auditar operações nos recursos do Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76315036"
---
# <a name="azure-firewall-logs-and-metrics"></a>Registos e métricas do Azure Firewall

Pode monitorizar os registos do Azure Firewall com registos de firewall. Também pode utilizar os registos de atividades para auditar operações nos recursos do Azure Firewall.

Pode aceder a alguns destes registos através do portal. Os registos podem ser enviados para [registos do Monitor Azure,](../azure-monitor/insights/azure-networking-analytics.md)Storage e Event Hubs e analisados em registos do Monitor Azure ou por diferentes ferramentas como o Excel e o Power BI.

As métricas são leves e podem suportar cenários próximos em tempo real tornando-os úteis para alertar e detetar rapidamente problemas.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

 Os seguintes registos de diagnóstico estão disponíveis para o Azure Firewall:

* **Registo de regra de Aplicação**

   O registo de regras da Aplicação é guardado numa conta de armazenamento, transmitido para os hubs do Evento e/ou enviado para registos do Monitor Azure apenas se o tiver ativado para cada Firewall Azure. Cada nova ligação que corresponde a uma das suas regras de aplicação configuradas resulta num registo da ligação aceite/negada. Os dados são registados no formato JSON, conforme mostrado no exemplo seguinte:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Registo de regra de Rede**

   O registo de regras da Rede é guardado numa conta de armazenamento, transmitido para os hubs do Evento e/ou enviado para registos do Monitor Azure apenas se o tiver ativado para cada Firewall Azure. Cada nova ligação que corresponde a uma das suas regras de rede configuradas resulta num registo da ligação aceite/negada. Os dados são registados no formato JSON, conforme mostrado no exemplo seguinte:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Tem três opções para armazenar os registos:

* **Conta de armazenamento**: as contas de armazenamento são ideais para os registos quando estes são armazenados durante um período mais longo e revistos quando necessário.
* **Hubs de eventos**: os Hubs de eventos são uma excelente opção para integrar com outras informações de segurança e ferramentas de gestão de eventos (SEIM) para obter alertas sobre os seus recursos.
* **Registos do Monitor Azure:** Os registos do Monitor Azure são melhor utilizados para a monitorização geral em tempo real da sua aplicação ou para analisar as tendências.

## <a name="activity-logs"></a>Registos de atividade

   As entradas de registos de atividades são recolhidas por predefinição e pode visualizá-las no portal do Azure.

   Pode utilizar registos de atividade do [Azure](../azure-resource-manager/management/view-activity-logs.md) (anteriormente conhecidos como registos operacionais e registos de auditoria) para visualizar todas as operações submetidas à sua subscrição azure.

## <a name="metrics"></a>Métricas

As métricas no Monitor Azure são valores numéricos que descrevem algum aspeto de um sistema num determinado momento. As métricas são recolhidas a cada minuto, e são úteis para alertar porque podem ser amostradas com frequência. Um alerta pode ser disparado rapidamente com uma lógica relativamente simples.

As seguintes métricas estão disponíveis para o Firewall Azure:

- As regras de **candidatura atingem a contagem** - O número de vezes que uma regra de candidatura foi atingida.

    Unidade: contagem

- As regras da **rede atingem a contagem** - O número de vezes que uma regra de rede foi atingida.

    Unidade: contagem

- **Dados processados** - Quantidade de dados que atravessam a firewall.

    Unidade: bytes

- Firewall estado de **saúde** - Indica a saúde da firewall com base na disponibilidade da porta SNAT.

    Unidade: por cento

   Esta métrica tem duas dimensões:
  - Estado: Os valores possíveis são *saudáveis,* *degradados,* *insalubres.*
  - Razão: Indica a razão do estado correspondente da firewall. 

     Se as portas SNAT forem utilizadas > 95%, são consideradas exaustas e a saúde é de 50% com estatuto=**Degradada** e razão=**Porta SNAT**. A firewall continua a processar o tráfego e as ligações existentes não são afetadas. No entanto, novas ligações podem não ser estabelecidas intermitentemente.

     Se as portas SNAT forem usadas < 95%, então a firewall é considerada saudável e a saúde é mostrada como 100%.

     Se não for reportado o uso das portas SNAT, a saúde é mostrada como 0%. 

- **Utilização da porta SNAT** - A percentagem de portas SNAT que foram utilizadas pela firewall.

    Unidade: por cento

   Quando adiciona mais endereços IP públicos à sua firewall, existem mais portas SNAT disponíveis, reduzindo a utilização das portas SNAT. Adicionalmente, quando a firewall se esescala por diferentes razões (por exemplo, CPU ou entrada) também ficam disponíveis portas SNAT adicionais. Assim, efetivamente, uma determinada percentagem de utilização de portas SNAT pode diminuir sem que você adicione quaisquer endereços IP públicos, apenas porque o serviço escalou. Pode controlar diretamente o número de endereços IP públicos disponíveis para aumentar as portas disponíveis na sua firewall. Mas não se pode controlar diretamente a escala de firewall. Atualmente, as portas SNAT são adicionadas apenas para os primeiros cinco endereços IP públicos.   


## <a name="next-steps"></a>Passos seguintes

- Para aprender a monitorizar os registos e métricas da Firewall Azure, consulte [Tutorial: Monitor Azure Firewall logs](tutorial-diagnostics.md).

- Para saber mais sobre métricas no Monitor Azure, consulte [Métricas no Monitor Azure](../azure-monitor/platform/data-platform-metrics.md).
