---
title: Como registar eventos para os Hubs de Eventos Azure na Azure API Management [ Microsoft Docs
description: Saiba como registar eventos para o Azure Event Hubs na Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 2f67079938ddcf4a65e01ef50ab7e5cdf7078b73
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260943"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Como registar eventos para hubs de eventos Azure na Gestão da API Azure
Os Event Hubs do Azure são um serviço de entrada de dados altamente dimensionável, que pode ingerir milhões de eventos por segundo para que possa processar e analisar os quantidades enormes de dados produzidos pelos dispositivos e aplicações ligados. O Event Hubs funciona como a "porta da frente" para um oleoduto de eventos, e uma vez recolhidos dados num centro de eventos, pode ser transformado e armazenado usando qualquer fornecedor de análise em tempo real ou adaptadores de lotação/armazenamento. Os Event Hubs desacoplam a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam aceder aos eventos de acordo com seu próprio agendamento.

Este artigo é um companheiro da [Integrate Azure API Management com](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) vídeo de Event Hubs e descreve como registar eventos de Gestão API usando Hubs de Eventos Azure.

## <a name="create-an-azure-event-hub"></a>Criar um Hub de Eventos do Azure

Para dar passos detalhados sobre como criar um hub de eventos e obter cordas de ligação que precisa para enviar e receber eventos de e para o Hub de Eventos, consulte [Create a Event Hubs namespace e um hub de eventos utilizando o portal Azure.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

## <a name="create-an-api-management-logger"></a>Criar um logger de Gestão API
Agora que tem um Hub de Eventos, o próximo passo é configurar um [Logger](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger) no seu serviço de Gestão API para que possa registar eventos no Event Hub.

Os madeireiros de gestão da API são configurados utilizando a [API Management REST API](https://aka.ms/apimapi). Para exemplos de pedidos detalhados, consulte [como criar Maders](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger/createorupdate).

## <a name="configure-log-to-eventhubs-policies"></a>Configure as políticas de log-to-eventhubs

Assim que o seu logger estiver configurado na Gestão API, pode configurar as suas políticas de log-to-eventhubs para registar os eventos desejados. A política de log-to-eventhubs pode ser utilizada na secção política de entrada ou na secção política de saída.

1. Navegue até à sua instância APIM.
2. Selecione o separador API.
3. Selecione a API à qual pretende adicionar a apólice. Neste exemplo, estamos adicionando uma política à **Echo API** no produto **Ilimitado.**
4. Selecione **todas as operações**.
5. Na parte superior do ecrã, selecione o separador Design.
6. Na janela de processamento de entrada ou saída, clique no triângulo (ao lado do lápis).
7. Selecione o editor de Código. Para mais informações, consulte [Como definir ou editar políticas.](set-edit-policies.md)
8. Posicione o cursor na `inbound` secção ou `outbound` política.
9. Na janela à direita, selecione **Políticas Avançadas** > **Faça log to EventHub**. Isto insere o modelo de `log-to-eventhub` declaração de política.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Substitua-a `logger-id` pelo `{new logger name}` valor utilizado no URL para criar o madeireiro no passo anterior.

Pode usar qualquer expressão que retorne uma `log-to-eventhub` corda como valor para o elemento. Neste exemplo, é registada uma cadeia que contenha a data e a hora, nome do serviço, id de pedido, endereço IP de pedido e nome de operação.

Clique em **Guardar** para salvar a configuração de política atualizada. Assim que é salva, a política está ativa e os eventos são registados no centro de eventos designado.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre os Hubs de Eventos Azure
  * [Começar com hubs de eventos Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com o EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração de Centros de Gestão e Eventos da API
  * [Referência da entidade logger](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger)
  * [referência política log-to-eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitorize as suas APIs com a Azure API Management, Event Hubs e Moesif](api-management-log-to-eventhub-sample.md)  
* Saiba mais sobre [integração com insights de aplicação azure](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
