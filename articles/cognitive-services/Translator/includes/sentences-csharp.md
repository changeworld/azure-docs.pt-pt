---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 3d92d3f959e2ad44daa82d6b609b9357cee969c9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906877"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Criar um projeto .NET Core

Abra um novo pedido de comando (ou sessão terminal) e execute estes comandos:

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

O primeiro comando faz duas coisas. Cria uma nova aplicação de consola .NET, e cria um diretório chamado `sentences-sample`. O segundo comando muda para o diretório para o seu projeto.

Em seguida, terá de instalar Json.Net. A partir do diretório do seu projeto, corra:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Selecione a versão em língua C#

Este quickstart requer C# 7.1 ou mais tarde. Existem algumas formas de alterar a versão C# para o seu projeto. Neste guia, vamos mostrar-lhe como `sentences-sample.csproj` ajustar o ficheiro. Para todas as opções disponíveis, tais como alterar o idioma no Estúdio Visual, consulte [Selecione a versão em língua C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Abra o seu `sentences-sample.csproj`projeto e abra. Certifique-se `LangVersion` de que está definido para 7.1 ou mais tarde. Se não houver um grupo imobiliário para a versão linguística, adicione estas linhas:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Adicione espaços de nome sinuosos necessários ao seu projeto

O `dotnet new console` comando que dirigiu anteriormente `Program.cs`criou um projeto, incluindo. Este ficheiro é onde vai colocar o seu código de inscrição. Abra `Program.cs`e substitua os existentes usando declarações. Estas declarações garantem que tem acesso a todos os tipos necessários para construir e executar a aplicação de amostras.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Criar aulas para a resposta json

Em seguida, vamos criar uma classe que é usada ao desserializar a resposta JSON devolvida pela API de Texto tradutor.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class BreakSentenceResult
{
    public int[] SentLen { get; set; }
    public DetectedLanguage DetectedLanguage { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}
```

## <a name="get-subscription-information-from-environment-variables"></a>Obtenha informações de subscrição de variáveis ambientais

Adicione as seguintes `Program` linhas à aula. Estas linhas lêem a sua chave de subscrição e ponto final a partir de variáveis ambientais, e lança um erro se você encontrar algum problema.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-determine-sentence-length"></a>Criar uma função para determinar o comprimento da frase

Na `Program` aula, crie uma `BreakSentenceRequest()`nova função chamada . Esta função requer `subscriptionKey`quatro `endpoint` `route`argumentos: `inputText`, , e .

```csharp
static public async Task BreakSentenceRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-break-sentence-request"></a>Serialize o pedido de frase de rutura

Em seguida, é necessário criar e serializar o objeto JSON que inclui o texto. Lembre-se, pode passar mais do `body` que um objeto na matriz.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Instantie o cliente e faça um pedido

Estas linhas instantaneamente `HttpClient` `HttpRequestMessage`o e o:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Construir o pedido e imprimir a resposta

Dentro `HttpRequestMessage` do seu vai:

* Declarar o método HTTP
* Construa o pedido URI
* Insira o corpo de pedido (objeto JSON serializado)
* Adicione cabeçalhos necessários
* Faça um pedido assíncrono
* Imprimir a resposta

Adicione este código `HttpRequestMessage`ao :

```csharp
// Build the request.
// Set the method to POST
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(endpoint + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
BreakSentenceResult[] deserializedOutput = JsonConvert.DeserializeObject<BreakSentenceResult[]>(result);
foreach (BreakSentenceResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    Console.WriteLine("The first sentence length is: {0}", o.SentLen[0]);
}
```

Se estiver a utilizar uma subscrição multi-serviço `Ocp-Apim-Subscription-Region` de Serviços Cognitivos, também deve incluir os parâmetros do seu pedido. [Saiba mais sobre autenticação com a subscrição de vários serviços.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="put-it-all-together"></a>Juntar tudo

O último passo `BreakSentenceRequest()` é `Main` chamar a função. Localize-o `static void Main(string[] args)` e substitua-o por este código:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string route = "/breaksentence?api-version=3.0";
    // Feel free to use any string.
    string breakSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await BreakSentenceRequest(subscriptionKey, endpoint, route, breakSentenceText);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

Vais reparar nisso, `Main`estás a `subscriptionKey` `endpoint`declarar, `route`e no `breakSentenceText`texto para avaliar.

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para executar a sua aplicação de amostras. A partir da linha de comando (ou sessão terminal), navegue até ao seu diretório de projeto e corra:

```console
dotnet run
```

## <a name="sample-response"></a>Resposta de amostra

Depois de executar a amostra, deve ver o seguinte impresso no terminal:

```bash
The detected language is \'en\'. Confidence is: 1.
The first sentence length is: 25
```

Esta mensagem é construída a partir do JSON cru, que será assim:

```json
[
    {
        "detectedLanguage":
        {
            "language":"en",
            "score":1.0
        },
        "sentLen":[25,32,35]
    }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Certifique-se de remover quaisquer informações confidenciais do código fonte da sua aplicação de amostra, como as chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

Veja a referência da API para entender tudo o que pode fazer com a API de Texto tradutor.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
