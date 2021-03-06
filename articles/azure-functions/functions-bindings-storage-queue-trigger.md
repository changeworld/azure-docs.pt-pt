---
title: Gatilho de armazenamento de fila azure para funções azure
description: Aprenda a executar uma Função Azure à medida que os dados de armazenamento da Fila Azure mudam.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277378"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Gatilho de armazenamento de fila azure para funções azure

O gatilho de armazenamento de fila funciona com uma função à medida que as mensagens são adicionadas ao armazenamento da Fila Azure.

## <a name="encoding"></a>Codificação

As funções esperam uma cadeia codificada *base64.* Quaisquer ajustes ao tipo de codificação (a fim de preparar dados como uma cadeia codificada *base64)* devem ser implementados no serviço de chamada.

## <a name="example"></a>Exemplo

Utilize o gatilho da fila para iniciar uma função quando um novo item for recebido numa fila. A mensagem da fila é apresentada como uma entrada da função.

# <a name="c"></a>[C #](#tab/csharp)

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que vota na `myqueue-items` fila e escreve um registo cada vez que um item de fila é processado.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho da fila num ficheiro *function.json* e no código [c# script (.csx)](functions-reference-csharp.md) que utiliza a ligação. A função `myqueue-items` faz sondagens na fila e escreve um registo cada vez que um item de fila é processado.

Aqui está o ficheiro *função.json:*

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A secção de [configuração](#configuration) explica estas propriedades.

Aqui está o código de script C#:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

A secção de `myQueueItem` [utilização](#usage) explica, `name` que é nomeada pela propriedade em função.json.  A secção de metadados de [mensagens](#message-metadata) explica todas as outras variáveis mostradas.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação do gatilho da fila num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função `myqueue-items` faz sondagens na fila e escreve um registo cada vez que um item de fila é processado.

Aqui está o ficheiro *função.json:*

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A secção de [configuração](#configuration) explica estas propriedades.

> [!NOTE]
> O parâmetro de nome `context.bindings.<name>` reflete como no código JavaScript que contém a carga útil do item da fila. Esta carga útil também é passada como o segundo parâmetro da função.

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

A secção de `myQueueItem` [utilização](#usage) explica, `name` que é nomeada pela propriedade em função.json.  A secção de metadados de [mensagens](#message-metadata) explica todas as outras variáveis mostradas.

# <a name="python"></a>[Pitão](#tab/python)

O exemplo seguinte demonstra como ler uma mensagem de fila passada para uma função através de um gatilho.

Um gatilho de fila de armazenamento é *type* definido na `queueTrigger` *função.json* onde o tipo está definido para .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

O código * _ \_inite_\_.py* declara `func.ServiceBusMessage`um parâmetro como , o que lhe permite ler a mensagem de fila na sua função.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

O exemplo de Java que se segue mostra uma função `myqueuename`de gatilho de fila de armazenamento, que regista a mensagem ativada colocada na fila .

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize os seguintes atributos para configurar um gatilho de fila:

* [Atributo de Gatilho de Fila](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  O construtor do atributo toma o nome da fila para monitorizar, como mostra o seguinte exemplo:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Pode definir `Connection` a propriedade para especificar a definição da aplicação que contém a cadeia de ligação à conta de armazenamento a utilizar, como mostra o seguinte exemplo:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Para um exemplo completo, consulte o [exemplo](#example).

* [ArmazenamentoAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fornece outra forma de especificar a conta de armazenamento a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado no parâmetro, método ou nível de classe. O exemplo que se segue mostra o nível de classe e o nível de método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A conta de armazenamento a utilizar é determinada na seguinte ordem:

* A `QueueTrigger` propriedade do `Connection` atributo.
* O `StorageAccount` atributo aplicado ao mesmo `QueueTrigger` parâmetro que o atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A definição da aplicação "AzureWebJobsStorage".

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Pitão](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A `QueueTrigger` anotação dá-lhe acesso à fila que desencadeia a função. O exemplo seguinte disponibiliza a mensagem `message` de fila para a função através do parâmetro.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Propriedade    | Descrição |
|-------------|-----------------------------|
|`name`       | Declara o nome do parâmetro na assinatura da função. Quando a função é ativada, o valor deste parâmetro tem o conteúdo da mensagem de fila. |
|`queueName`  | Declara o nome da fila na conta de armazenamento. |
|`connection` | Aponta para o fio de ligação da conta de armazenamento. |

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `QueueTrigger` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d| Tem de `queueTrigger`ser definido para. Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção**| n/d | Apenas no ficheiro *função.json.* Tem de `in`ser definido para. Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/d |O nome da variável que contém a carga útil do item da fila no código de função.  |
|**queueName** | **Nome da fila**| O nome da fila para a sondagem. |
|**conexão** | **Conexão** |O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se `connection` definir para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "MyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza `AzureWebJobsStorage`a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C #](#tab/csharp)

Aceda aos dados da mensagem utilizando `string paramName`um parâmetro de método como . Pode ligar-se a qualquer um dos seguintes tipos:

* Objeto - As funções desserializa uma carga útil JSON numa instância de uma classe arbitrária definida no seu código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Se tentar ligar-se `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à versão [SDK de armazenamento correta](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Aceda aos dados da mensagem utilizando `string paramName`um parâmetro de método como . É `paramName` o valor especificado `name` na propriedade da *função.json*. Pode ligar-se a qualquer um dos seguintes tipos:

* Objeto - As funções desserializa uma carga útil JSON numa instância de uma classe arbitrária definida no seu código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Se tentar ligar-se `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à versão [SDK de armazenamento correta](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A carga útil do item da fila está disponível através `context.bindings.<NAME>` do local onde `<NAME>` corresponde ao nome definido na *função.json*. Se a carga útil for JSON, o valor é desserializado num objeto.

# <a name="python"></a>[Pitão](#tab/python)

Aceda à mensagem de fila através do parâmetro digitado como [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="java"></a>[Java](#tab/java)

A anotação [QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) dá-lhe acesso à mensagem de fila que desencadeou a função.

---

## <a name="message-metadata"></a>Metadados de mensagens

O gatilho da fila fornece várias [propriedades de metadados.](./functions-bindings-expressions-patterns.md#trigger-metadata) Estas propriedades podem ser usadas como parte de expressões de ligação noutras encadernações ou como parâmetros no seu código. As propriedades são membros da classe [CloudQueueMessage.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`QueueTrigger`|`string`|Carga útil da fila (se for uma corda válida). Se a carga de mensagem `QueueTrigger` de fila for uma corda, `name` tem o mesmo valor que a variável nomeada pela propriedade em *função.json*.|
|`DequeueCount`|`int`|O número de vezes que esta mensagem foi desmarcada.|
|`ExpirationTime`|`DateTimeOffset`|O tempo que a mensagem expirar.|
|`Id`|`string`|Id de mensagem de fila.|
|`InsertionTime`|`DateTimeOffset`|O tempo que a mensagem foi adicionada à fila.|
|`NextVisibleTime`|`DateTimeOffset`|O tempo que a mensagem será a próxima será visível.|
|`PopReceipt`|`string`|O recibo da mensagem.|

## <a name="poison-messages"></a>Mensagens venenosas

Quando uma função de gatilho de fila falha, as Funções Azure retestam a função até cinco vezes para uma determinada mensagem de fila, incluindo a primeira tentativa. Se todas as cinco tentativas falharem, as funções de tempo de funcionamento adicionam uma mensagem a uma fila chamada * &lt;originalmente nome de fila> veneno*. Pode escrever uma função para processar mensagens da fila de veneno, registando-as ou enviando uma notificação de que é necessária atenção manual.

Para manusear mensagens venenosas manualmente, verifique o [desfilar Contagem](#message-metadata) da mensagem de fila.

## <a name="polling-algorithm"></a>Algoritmo de sondagem

O gatilho da fila implementa um algoritmo de back-off exponencial aleatório para reduzir o efeito das sondagens de fila inativa nos custos de transação de armazenamento.

O algoritmo usa a seguinte lógica:

- Quando uma mensagem é encontrada, o tempo de execução espera dois segundos e, em seguida, verifica por outra mensagem
- Quando nenhuma mensagem é encontrada, espera cerca de quatro segundos antes de tentar novamente.
- Após tentativas falhadas subsequentes de obter uma mensagem de fila, o tempo de espera continua a aumentar até atingir o tempo máximo de espera, o que se desfaz para um minuto.
- O tempo máximo de espera `maxPollingInterval` é configurável através da propriedade no [ficheiro host.json](functions-host-json.md#queues).

Para o desenvolvimento local, o intervalo máximo de sondagens não se aplica a dois segundos.

No que diz respeito à faturação, o tempo gasto nas sondagens até ao fim é "gratuito" e não contado contra a sua conta.

## <a name="concurrency"></a>Simultaneidade

Quando há várias mensagens de fila à espera, o gatilho da fila recupera um lote de mensagens e invoca casos de funções simultaneamente para processá-las. Por padrão, o tamanho do lote é de 16. Quando o número que está a ser processado chega a 8, o tempo de execução recebe outro lote e começa a processar essas mensagens. Assim, o número máximo de mensagens simultâneas que estão a ser processadas por função numa máquina virtual (VM) é de 24. Este limite aplica-se separadamente a cada função ativada pela fila em cada VM. Se a sua aplicação de função se dimensionar para vários VMs, cada VM aguardará por gatilhos e tentará executar funções. Por exemplo, se uma aplicação de função se dimensionar para 3 VMs, o número máximo padrão de instâncias simultâneas de uma função desencadeada pela fila é de 72.

O tamanho do lote e o limiar para obter um novo lote são configuráveis no [ficheiro host.json](functions-host-json.md#queues). Se pretender minimizar a execução paralela para funções ativadas pela fila numa aplicação de função, pode definir o tamanho do lote para 1. Esta definição elimina a moeda apenas enquanto a sua aplicação de função funcionar numa única máquina virtual (VM). 

O gatilho da fila impede automaticamente uma função de processar uma mensagem de fila várias vezes; funções não têm que ser escritas para ser idempotente.

## <a name="hostjson-properties"></a>propriedades host.json

O ficheiro [host.json](functions-host-json.md#queues) contém definições que controlam o comportamento do gatilho da fila. Consulte a secção de [definições host.json](functions-bindings-storage-queue-output.md#hostjson-settings) para obter mais detalhes sobre as definições disponíveis.

## <a name="next-steps"></a>Passos seguintes

- [Escreva mensagens de armazenamento de fila (encadernação de saída)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
