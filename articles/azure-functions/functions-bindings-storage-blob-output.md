---
title: Ligação de saída de armazenamento Azure Blob para funções Azure
description: Aprenda a fornecer dados de armazenamento Azure Blob a uma Função Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: c6e15c9a99a78f0f3637f718b35462fe49fd5ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277248"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Ligação de saída de armazenamento Azure Blob para funções Azure

A ligação de saída permite modificar e eliminar dados de armazenamento blob numa Função Azure.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](./functions-bindings-storage-blob.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C #](#tab/csharp)

O exemplo seguinte é uma [função C#](functions-dotnet-class-library.md) que usa um gatilho de bolha e duas ligações de bolha de saída. A função é desencadeada pela criação de uma bolha de imagem no recipiente *de imagens de amostra.* Cria cópias de tamanho pequeno e médio da bolha de imagem.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

<!--Same example for input and output. -->

O exemplo seguinte mostra as ligações de entrada e saída blob num ficheiro *function.json* e no código [de script C# (.csx)](functions-reference-csharp.md) que utiliza as encadernações. A função faz uma cópia de uma bolha de texto. A função é desencadeada por uma mensagem de fila que contém o nome da bolha para copiar. A nova bolha *chama-se {originalblobname}-Copy*.

No ficheiro *função.json,* a `queueTrigger` propriedade de metadados é `path` utilizada para especificar o nome blob nas propriedades:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A secção de [configuração](#configuration) explica estas propriedades.

Aqui está o código de script C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

O exemplo seguinte mostra as ligações de entrada e saída blob num ficheiro *function.json* e [código JavaScript](functions-reference-node.md) que utiliza as encadernações. A função faz uma cópia de uma bolha. A função é desencadeada por uma mensagem de fila que contém o nome da bolha para copiar. A nova bolha *chama-se {originalblobname}-Copy*.

No ficheiro *função.json,* a `queueTrigger` propriedade de metadados é `path` utilizada para especificar o nome blob nas propriedades:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A secção de [configuração](#configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Pitão](#tab/python)

<!--Same example for input and output. -->

O exemplo seguinte mostra as ligações de entrada e saída blob num ficheiro *function.json* e [código Python](functions-reference-python.md) que utiliza as encadernações. A função faz uma cópia de uma bolha. A função é desencadeada por uma mensagem de fila que contém o nome da bolha para copiar. A nova bolha *chama-se {originalblobname}-Copy*.

No ficheiro *função.json,* a `queueTrigger` propriedade de metadados é `path` utilizada para especificar o nome blob nas propriedades:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

A secção de [configuração](#configuration) explica estas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

Esta secção contém os seguintes exemplos:

* [Gatilho HTTP, utilizando OutputBinding](#http-trigger-using-outputbinding-java)
* [Gatilho de fila, utilizando o valor de retorno da função](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Gatilho HTTP, utilizando OutputBinding (Java)

 O exemplo seguinte mostra uma `HttpTrigger` função Java que utiliza a anotação para receber um parâmetro contendo o nome de um ficheiro num recipiente de armazenamento de bolhas. A `BlobInput` anotação lê então o ficheiro e `byte[]`passa o seu conteúdo para a função como . A `BlobOutput` anotação liga-se a `OutputBinding outputItem`, que é então utilizada pela função de escrever o conteúdo da bolha de entrada no recipiente de armazenamento configurado.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Gatilho de fila, utilizando o valor de retorno da função (Java)

 O exemplo seguinte mostra uma `QueueTrigger` função Java que utiliza a anotação para receber uma mensagem contendo o nome de um ficheiro num recipiente de armazenamento blob. A `BlobInput` anotação lê então o ficheiro e `byte[]`passa o seu conteúdo para a função como . A `BlobOutput` anotação liga-se ao valor de devolução da função, que é então utilizado pelo tempo de funcionamento para escrever o conteúdo da bolha de entrada no recipiente de armazenamento configurado.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 Na biblioteca de tempo de funcionamento das [funções java,](/java/api/overview/azure/functions/runtime)utilize a `@BlobOutput` anotação em parâmetros de função cujo valor seria escrito a um objeto no armazenamento de bolhas.  O tipo de `OutputBinding<T>`parâmetro deve ser, onde T é qualquer tipo java nativo ou um POJO.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)use o [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

O construtor do atributo segue o caminho para `FileAccess` a bolha e um parâmetro que indica ler ou escrever, como se pode ver no seguinte exemplo:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Pode definir `Connection` a propriedade para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Pitão](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

O `@BlobOutput` atributo dá-lhe acesso à bolha que desencadeou a função. Se utilizar um matriz de byte `dataType` `binary`com o atributo, definido para . Consulte o exemplo de [saída](#example) para mais detalhes.

---

Para um exemplo completo, consulte o [exemplo da saída](#example).

Pode utilizar `StorageAccount` o atributo para especificar a conta de armazenamento na classe, método ou nível de parâmetro. Para mais informações, consulte [Trigger - atributos](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `Blob` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de `blob`ser definido para. |
|**direção** | n/d | Deve ser `out` definido para uma ligação de saída. As exceções são observadas na secção [de utilização.](#usage) |
|**nome** | n/d | O nome da variável que representa a bolha no código de função.  `$return` Decidiu para referenciar o valor de devolução da função.|
|**caminho** |**BlobPath** | O caminho para o recipiente de bolhas. |
|**conexão** |**Conexão**| O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se `connection` definir para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "AzureWebJobsMyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza `AzureWebJobsStorage`a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada .<br><br>A cadeia de ligação deve ser para uma conta de armazenamento geral, não para uma [conta de armazenamento apenas para bolhas.](../storage/common/storage-account-overview.md#types-of-storage-accounts)|
|n/d | **Acesso** | Indica se vai ler ou escrever. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

No JavaScript, aceda aos `context.bindings.<name from function.json>`dados blob utilizando .

# <a name="python"></a>[Pitão](#tab/python)

Pode declarar os parâmetros de função como os seguintes tipos para escrever para o armazenamento de bolhas:

* Cordas como`func.Out(str)`
* Riachos como`func.Out(func.InputStream)`

Consulte o exemplo de [saída](#example) para mais detalhes.

# <a name="java"></a>[Java](#tab/java)

O `@BlobOutput` atributo dá-lhe acesso à bolha que desencadeou a função. Se utilizar um matriz de byte `dataType` `binary`com o atributo, definido para . Consulte o exemplo de [saída](#example) para mais detalhes.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace |  Referência |
|---|---|
| Blobs | [Códigos de erro blob](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Mesa, Fila |  [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Mesa, Fila |  [Resolução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando os dados de armazenamento blob mudar](./functions-bindings-storage-blob-trigger.md)
- [Leia os dados de armazenamento de blob quando uma função funciona](./functions-bindings-storage-blob-input.md)
