---
title: Monitores em Funções Duráveis - Azure
description: Aprenda a implementar um monitor de estado utilizando a extensão funções duráveis para funções azure.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ed92156df9d8e1e07b56cea4b1e64edee11d68d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562127"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Monitorizar cenário em Funções Duráveis - Amostra de observador meteorológico

O padrão de monitor refere-se a um processo *flexível recorrente* num fluxo de trabalho - por exemplo, sondagens até que determinadas condições sejam satisfeitas. Este artigo explica uma amostra que utiliza [Funções Duráveis](durable-functions-overview.md) para implementar a monitorização.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

Esta amostra monitoriza as condições meteorológicas atuais de um local e alerta um utilizador por SMS quando o céu está limpo. Pode utilizar uma função regular de acionamento do temporizador para verificar o tempo e enviar alertas. No entanto, um dos problemas com esta abordagem é a **gestão vitalícia.** Se for enviado apenas um alerta, o monitor tem de se desativar depois de ser detetado um tempo limpo. O padrão de monitorização pode pôr fim à sua própria execução, entre outros benefícios:

* Os monitores funcionam em intervalos, não em horários: um gatilho *temporizador funciona* a cada hora; um monitor *espera* uma hora entre as ações. As ações de um monitor não se sobreporão a menos que especificadas, o que pode ser importante para tarefas de longa duração.
* Os monitores podem ter intervalos dinâmicos: o tempo de espera pode mudar com base em alguma condição.
* Os monitores podem terminar quando alguma condição é satisfeita ou ser terminada por outro processo.
* Os monitores podem ter parâmetros. A amostra mostra como o mesmo processo de monitorização meteorológica pode ser aplicado a qualquer local e número de telefone solicitados.
* Os monitores são escaláveis. Como cada monitor é uma instância de orquestração, vários monitores podem ser criados sem ter que criar novas funções ou definir mais código.
* Os monitores integram-se facilmente em fluxos de trabalho maiores. Um monitor pode ser uma secção de uma função de orquestração mais complexa, ou uma [sub-orquestração.](durable-functions-sub-orchestrations.md)

## <a name="configuration"></a>Configuração

### <a name="configuring-twilio-integration"></a>Configurar a integração do Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Configurar a integração do Weather Underground

Esta amostra envolve a utilização da API Weather Underground para verificar as condições meteorológicas atuais para obter uma localização.

A primeira coisa que precisas é de uma conta no Weather Underground. Pode criar um de [https://www.wunderground.com/signup](https://www.wunderground.com/signup)graça em . Assim que tiver uma conta, terá de adquirir uma chave API. Pode fazê-lo visitando [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1)e, em seguida, selecionando As Definições de Chave. O plano de desenvolvimento stratus é gratuito e suficiente para executar esta amostra.

Assim que tiver uma tecla API, adicione a seguinte definição de **aplicação** à sua aplicação de função.

| Nome de definição de aplicativo | Descrição do valor |
| - | - |
| **WeatherUndergroundApiKey**  | A tua chave Weather Underground API. |

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação da amostra:

* `E3_Monitor`: Uma [função orquestradora](durable-functions-bindings.md#orchestration-trigger) que liga `E3_GetIsClear` periodicamente. Chama `E3_SendGoodWeatherAlert` se `E3_GetIsClear` voltar a verdade.
* `E3_GetIsClear`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que verifique as condições meteorológicas atuais para um local.
* `E3_SendGoodWeatherAlert`: Uma função de atividade que envia uma mensagem SMS via Twilio.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor função orquestradora

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

O orquestrador requer uma localização para monitorizar e um número de telefone para enviar uma mensagem para quando o local ficar claro. Estes dados são transmitidos ao orquestrador `MonitorRequest` como um objeto fortemente dactilografado.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A função **E3_Monitor** utiliza a *função padrão.json* para funções orquestradoras.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Aqui está o código que implementa a função:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

---

Esta função orquestradora executa as seguintes ações:

1. Obtém o **MonitorRequest** constituído pela *localização* para monitorizar e o número de *telefone* para o qual enviará uma notificação SMS.
2. Determina o tempo de validade do monitor. A amostra usa um valor codificado para a brevidade.
3. As chamadas **E3_GetIsClear** para determinar se há céu limpo no local solicitado.
4. Se o tempo estiver limpo, as chamadas **E3_SendGoodWeatherAlert** para enviar uma notificação SMS para o número de telefone solicitado.
5. Cria um temporizador durável para retomar a orquestração no intervalo de votação seguinte. A amostra usa um valor codificado para a brevidade.
6. Continua a funcionar até que o tempo atual da UTC passe o tempo de validade do monitor, ou seja enviado um alerta sms.

Várias instâncias orquestradoras podem ser executadas simultaneamente, chamando a função de orquestrador várias vezes. A localização para monitorizar e o número de telefone para enviar um alerta SMS pode ser especificada.

### <a name="e3_getisclear-activity-function"></a>função de atividade E3_GetIsClear

Tal como acontece com outras amostras, as funções de atividade do ajudante são funções regulares que utilizam a ligação do `activityTrigger` gatilho. A função **E3_GetIsClear** obtém as condições meteorológicas atuais usando a API weather underground e determina se o céu está limpo.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A *função.json* é definida da seguinte forma:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

E aqui está a implementação.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>função de atividade E3_SendGoodWeatherAlert

A função **E3_SendGoodWeatherAlert** utiliza a ligação Twilio para enviar uma mensagem SMS notificando o utilizador final de que é uma boa hora para uma caminhada.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> Terá de instalar `Microsoft.Azure.WebJobs.Extensions.Twilio` o pacote Nuget para executar o código da amostra.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A sua *função.json* é simples:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

E aqui está o código que envia a mensagem SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

---

## <a name="run-the-sample"></a>Executar o exemplo

Utilizando as funções desencadeadas pelo HTTP incluídas na amostra, pode iniciar a orquestração enviando o seguinte pedido HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

O **E3_Monitor** caso começa e questiona as condições meteorológicas atuais para a localização solicitada. Se o tempo estiver limpo, chama uma função de atividade para enviar um alerta; caso contrário, define um temporizador. Quando o temporizador expirar, a orquestração será retomada.

Pode ver a atividade da orquestração olhando para os registos de funções no portal Funções Azure.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

A orquestração [terminará](durable-functions-instance-management.md) assim que o seu tempo for atingido ou se detetar céu limpo. Também pode `TerminateAsync` utilizar (.NET) ou `terminate` (JavaScript) dentro de outra função ou invocar o **webhook post post terminateAdo PostUri** http referenciado na resposta de 202 acima, substituindo `{text}` pela razão da rescisão:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Passos seguintes

Esta amostra demonstrou como utilizar funções duráveis para monitorizar o estado de uma fonte externa utilizando [tempos duráveis](durable-functions-timers.md) e lógica condicional. A próxima amostra mostra como usar eventos externos e [tempos duráveis](durable-functions-timers.md) para lidar com a interação humana.

> [!div class="nextstepaction"]
> [Executar a amostra de interação humana](durable-functions-phone-verification.md)
