---
title: Compreenda o esquema do webhook usado em alertas de registo de atividade
description: Saiba mais sobre o esquema do JSON que é publicado num URL de webhook quando um alerta de registo de atividade ativa.
ms.topic: conceptual
ms.date: 03/31/2017
ms.subservice: alerts
ms.openlocfilehash: c076b8dcea350f9ddd66977e89ce99b81f377b17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669051"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooks para alertas de registo de atividade do Azure
Como parte da definição de um grupo de ação, pode configurar pontos finais do webhook para receber notificações de alerta de registo de atividade. Com webhooks, pode encaminhar estas notificações para outros sistemas para pós-processamento ou ações personalizadas. Este artigo mostra como é a carga para o HTTP POST para um webhook.

Para obter mais informações sobre alertas de registo de atividade, consulte como criar alertas de registo de [atividade do Azure](activity-log-alerts.md).

Para obter informações sobre grupos de ação, consulte como [criar grupos](../../azure-monitor/platform/action-groups.md)de ação .

> [!NOTE]
> Também pode utilizar o esquema de [alerta comum,](https://aka.ms/commonAlertSchemaDocs)que proporciona a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Monitor Azure, para as suas integrações no webhook. [Conheça as definições comuns de esquemade alerta.](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>Autenticar o webhook
O webhook pode utilizar opcionalmente uma autorização baseada em token para autenticação. O webhook URI é guardado com um `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`ID simbólico, por exemplo, .

## <a name="payload-schema"></a>Esquema de carga útil
A carga útil JSON contida na operação POST difere com base nos dados da carga útil.contextLog.eventSource field.

### <a name="common"></a>Common

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Administrativa

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Segurança

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Recomendação

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Para obter detalhes específicos sobre os alertas de registo de atividade de notificação de saúde do serviço, consulte notificações de [saúde](../../azure-monitor/platform/service-notifications.md)do serviço . Além disso, aprenda a configurar notificações de webhook de [saúde de serviço com as suas soluções de gestão de problemas existentes.](../../service-health/service-health-alert-webhook-guide.md)

### <a name="resourcehealth"></a>Saúde dos Recursos

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Nome do elemento | Descrição |
| --- | --- |
| status |Usado para alertas métricos. Sempre definido para "ativado" para alertas de registo de atividade. |
| contexto |Contexto do evento. |
| nome fornecedor de recursos |O fornecedor de recursos do recurso impactado. |
| condiçãoTipo |Sempre "Evento". |
| nome |Nome da regra do alerta. |
| ID |Identificação do recurso do alerta. |
| descrição |Descrição do alerta definida quando o alerta é criado. |
| subscriptionId |Id de subscrição Azure. |
| carimbo de data/hora |O momento em que o evento foi gerado pelo serviço Azure que processou o pedido. |
| resourceId |Identificação de recursos do recurso impactado. |
| resourceGroupName |Nome do grupo de recursos para o recurso impactado. |
| propriedades |Conjunto `<Key, Value>` de pares (isto `Dictionary<String, String>`é, ) que inclui detalhes sobre o evento. |
| evento |Elemento que contém metadados sobre o evento. |
| autorização |As propriedades de Controlo de Acesso Baseado em Papel do evento. Estas propriedades geralmente incluem a ação, o papel e o âmbito. |
| categoria |Categoria do evento. Os valores suportados incluem Administrativo, Alerta, Segurança, Serviços De Saúde e Recomendação. |
| chamador |Endereço de e-mail do utilizador que realizou a operação, reclamação UPN ou reclamação SPN com base na disponibilidade. Pode ser nulo para certas chamadas do sistema. |
| correlationId |Normalmente um GUID em formato de corda. Os eventos com correlationId pertencem à mesma ação maior e geralmente partilham uma correlação. |
| eventoDescrição |Descrição do texto estático do evento. |
| eventoDataId |Identificador único para o evento. |
| eventoSource |Nome do serviço Azure ou infraestrutura que gerou o evento. |
| httpRequest |O pedido geralmente inclui o método clienteRequestId, clientIpAddress e HTTP (por exemplo, PUT). |
| nível |Um dos seguintes valores: Crítico, Erro, Aviso e Informação. |
| operationId |Normalmente, um GUID partilhou entre os eventos correspondentes a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Propriedades do evento. |
| status |Cadeia. Estado da operação. Os valores comuns incluem Iniciado, Em Progresso, Bem Sucedido, Falhado, Ativo e Resolvido. |
| subEstatuto |Normalmente inclui o código de estado HTTP da chamada REPOUSA correspondente. Também pode incluir outras cordas que descrevem um subestatuto. Os valores comuns do subestatuto incluem OK (Código de Estado HTTP: 200), Criado (Código de Estado HTTP: 201), Aceito (Código de Estado HTTP: 202), Sem Conteúdo (Código de Estado HTTP: 204), Pedido De Mau Pedido (Código de Estado HTTP: 400), Não Encontrado (Código de Estado HTTP: 404), Conflito (Código de Estado HTTP: 409 ), Erro do servidor interno (Código de Estado HTTP: 500), Serviço Indisponível (Código de Estado HTTP: 503) e Timeout gateway (Código de Estado HTTP: 504). |

Para obter detalhes específicos sobre todos os outros alertas de registo de atividade, consulte a [visão geral do registo de atividade do Azure](../../azure-monitor/platform/platform-logs-overview.md).

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre o registo de atividade](../../azure-monitor/platform/platform-logs-overview.md).
* [Execute scripts de automação Azure (Livros de execução) em alertas Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Utilize uma aplicação lógica para enviar um SMS via Twilio a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este exemplo é para alertas métricos, mas pode ser modificado para trabalhar com um alerta de registo de atividade.
* [Utilize uma aplicação lógica para enviar uma mensagem Slack a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este exemplo é para alertas métricos, mas pode ser modificado para trabalhar com um alerta de registo de atividade.
* [Utilize uma aplicação lógica para enviar uma mensagem para uma fila Azure a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este exemplo é para alertas métricos, mas pode ser modificado para trabalhar com um alerta de registo de atividade.

