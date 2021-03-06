---
title: 'Quickstart: Biblioteca de clientes de Análise de Texto para C# Microsoft Docs'
description: 'Começar com a biblioteca de clientes Text Analytics para C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2fa2e40ba2a7fe84b6df57bfb711d01332b8f523
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275386"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

[v3 Documentação de referência](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 Biblioteca código](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | fonte[v3 Pacote v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[v2 Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 Código fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | da biblioteca[v2 Pacote v2 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 Amostras](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* O [Estúdio Visual IDE](https://visualstudio.microsoft.com/vs/)
* Assim que tiver a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="sua subscrição"  target="_blank">Azure, crie um recurso Text Analytics criar um recurso <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics no portal Azure para obter a sua chave e ponto final.  Depois de ser implantado, clique em **ir para o recurso**.
    * Necessitará da chave e do ponto final do recurso que cria para ligar a sua aplicação à API textanalytics. Vaicolar a chave e o ponto final no código abaixo no arranque rápido.
    * Você pode usar o nível de preços gratuitos (`F0`) para experimentar o serviço, e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-net-core-application"></a>Criar uma nova aplicação .NET Core

Utilizando o Visual Studio IDE, crie uma nova aplicação de consola .NET Core. Isto criará um projeto "Hello World" com um único ficheiro de origem C#: *program.cs*.

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Instale a biblioteca do cliente clicando corretamente sobre a solução no **Solution Explorer** e selecionando **pacotes Manage NuGet**. No gestor de pacotes que abre selecione **Browse,** verifique **Incluir pré-lançamento**, e procurar `Azure.AI.TextAnalytics`por . Selecione a versão, `1.0.0-preview.4`e depois **instale**. Também pode utilizar a consola de gestor de [pacotes.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs)que contém os exemplos de código neste arranque rápido. 

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Instale a biblioteca do cliente clicando corretamente sobre a solução no **Solution Explorer** e selecionando **pacotes Manage NuGet**. No gestor de pacotes que abre, `Microsoft.Azure.CognitiveServices.Language.TextAnalytics` **selecione Navegar** e procurar . Clique nele e, em seguida, **instale**. Também pode utilizar a consola de gestor de [pacotes.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs)que contém os exemplos de código neste arranque rápido. 

---

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Abra o ficheiro *program.cs* `using` e adicione as seguintes diretivas:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Na aula da `Program` aplicação, crie variáveis para a chave e ponto final do seu recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Substitua o `Main` método da aplicação. Definirá os métodos aqui chamados mais tarde.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Abra o ficheiro *program.cs* `using` e adicione as seguintes diretivas:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Na aula da `Program` aplicação, crie variáveis para a chave e ponto final do seu recurso. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Substitua o `Main` método da aplicação. Definirá os métodos aqui chamados mais tarde.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Modelo de objeto

O cliente Text `TextAnalyticsClient` Analytics é um objeto que autentica o Azure usando a sua chave, e fornece funções para aceitar texto como cordas únicas ou como um lote. Pode enviar sms para a API sincronicamente, ou assincronicamente. O objeto de resposta conterá as informações de análise de cada documento que enviar. 

Se estiver a `3.0-preview` utilizar a versão do serviço, pode utilizar uma instância opcional `TextAnalyticsClientOptions` para inicializar o cliente com várias definições predefinidas (por exemplo, idioma padrão ou sugestão de país). Também pode autenticar utilizando um símbolo do Diretório Ativo Azure. 

## <a name="code-examples"></a>Exemplos de código

* [Análise de sentimento](#sentiment-analysis)
* [Deteção de linguagem](#language-detection)
* [Reconhecimento de Entidades Nomeadas](#named-entity-recognition-ner)
* [Ligação de entidades](#entity-linking)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Certifique-se de que o seu método principal de antes cria um novo objeto de cliente com o seu ponto final e credenciais.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie `ApiKeyServiceClientCredentials` uma nova classe para armazenar as credenciais e adicioná-las aos pedidos do cliente. Dentro dele, crie `ProcessHttpRequestAsync()` uma sobreposição para `Ocp-Apim-Subscription-Key` que adicione a sua chave ao cabeçalho.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Crie um método para instantaneamente o objeto [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) com o seu ponto final e um `ApiKeyServiceClientCredentials` objeto que contenha a sua chave.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Análise de sentimentos

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma `SentimentAnalysisExample()` nova função chamada que leve o `AnalyzeSentiment()` cliente que criou anteriormente, e chame a sua função. O `Response<DocumentSentiment>` objeto devolvido conterá o rótulo de sentimento e a pontuação de todo o documento de entrada, bem como uma análise de sentimento para cada frase se for bem sucedida. Se houve um erro, vai `RequestFailedException`lançar um .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [length {sentence.GraphemeLength}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.GraphemeOffset, sentence.GraphemeLength)}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Document sentiment: Positive

        Sentence [length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma `SentimentAnalysisExample()` nova função chamada que leve o cliente que criou anteriormente, e chame a sua função [de Sentimento.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) O objeto ["SentimentResult"](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) `Score` devolvido conterá `errorMessage` o sentimento se for bem sucedido, e um se não. 

Uma pontuação que está perto de 0 indica um sentimento negativo, enquanto uma pontuação que está mais perto de 1 indica um sentimento positivo.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Deteção de idioma

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)


Crie uma `LanguageDetectionExample()` nova função chamada que leve o `DetectLanguage()` cliente que criou anteriormente, e chame a sua função. O `Response<DetectedLanguage>` objeto devolvido conterá a linguagem detetada juntamente com o seu nome e o código ISO-6391. Se houve um erro, vai `RequestFailedException`lançar um .

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar `countryHint` o parâmetro para especificar um código de país de 2 letras. Por padrão, a API está a usar os "EUA" como país padrãoHint, para remover este `countryHint = ""`comportamento pode redefinir este parâmetro definindo este valor para uma cadeia vazia . Para definir um padrão `TextAnalyticsClientOptions.DefaultCountryHint` diferente, detete a propriedade e passe-a durante a inicialização do cliente.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Saída

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma `languageDetectionExample()` nova função chamada que leve o cliente que criou anteriormente, e ligue para a sua função [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) O objeto [Idioma Devolvido Result](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) conterá a `DetectedLanguages` lista de `errorMessage` idiomas detetados em caso de sucesso, e um se não. Imprima a primeira língua devolvida.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar `countryHint` o parâmetro para especificar um código de país de 2 letras. Por padrão, a API está a usar os "EUA" como país padrãoHint, para remover este `countryHint = ""` comportamento pode redefinir este parâmetro definindo este valor para uma cadeia vazia .

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Saída

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeada (NER)

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)


> [!NOTE]
> Novo em `3.0-preview`versão:
> * A ligação de entidades é agora separada do reconhecimento da entidade.


Crie uma `EntityRecognitionExample()` nova função chamada que leve o `RecognizeEntities()` cliente que criou anteriormente, ligue para a sua função e iterar através dos resultados. O `Response<IReadOnlyCollection<CategorizedEntity>>` objeto devolvido conterá a lista de entidades detetadas. Se houve um erro, vai `RequestFailedException`lançar um .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Length: 7,      Score: 0.92

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Length: 9,      Score: 0.80
```

## <a name="entity-linking"></a>Ligação de entidades

Crie uma `EntityLinkingExample()` nova função chamada que leve o `RecognizeLinkedEntities()` cliente que criou anteriormente, ligue para a sua função e iterar através dos resultados. O `Response<IReadOnlyCollection<LinkedEntity>>` devolvido representa a lista de entidades detetadas. Se houve um erro, vai `RequestFailedException`lançar um . Uma vez que as entidades ligadas são identificadas de `LinkedEntity` forma única, `LinkedEntityMatch` as ocorrências da mesma entidade são agrunadas sob um objeto como uma lista de objetos.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tLength: {match.GraphemeLength},\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Saída

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Length: 11,     Score: 0.78

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Length: 10,     Score: 0.55

                Text: Gates
                Length: 5,      Score: 0.55

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Length: 10,     Score: 0.53

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Length: 9,      Score: 0.47

                Text: Microsoft
                Length: 9,      Score: 0.47

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Length: 7,      Score: 0.25

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Length: 5,      Score: 0.28
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

> [!NOTE]
> Na versão 2.1, a ligação da entidade está incluída na resposta NER.

Crie uma `RecognizeEntitiesExample()` nova função chamada que leve o cliente que criou anteriormente, e ligue para a função de [Entidades.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) Iterar através dos resultados. O objeto de dispôs [EntidadesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) `Entities` conterá a `errorMessage` lista de entidades detetadas em caso de sucesso, e um se não. Para cada entidade detetada, imprima o seu nome Tipo, Sub-Tipo, Wikipédia (se existirem) bem como as localizações do texto original.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extração de expressões-chave

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma `KeyPhraseExtractionExample()` nova função chamada que leve o `ExtractKeyPhrases()` cliente que criou anteriormente, e chame a sua função. O `<Response<IReadOnlyCollection<string>>` objeto devolvido conterá a lista de frases-chave detetadas. Se houve um erro, vai `RequestFailedException`lançar um .

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Saída

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma `KeyPhraseExtractionExample()` nova função chamada que leve o cliente que criou anteriormente e chame a sua função [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) O resultado conterá a lista de `KeyPhrases` frases-chave `errorMessage` detetadas em caso de sucesso, e um se não. Imprima as frases-chave detetadas.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Saída

```console
Key phrases:
    cat
    veterinarian
```

---
