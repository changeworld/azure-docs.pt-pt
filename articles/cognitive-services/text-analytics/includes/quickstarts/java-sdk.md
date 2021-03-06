---
title: 'Quickstart: Text Analytics v3 biblioteca de clientes para Java Microsoft Docs'
description: Começa com a biblioteca de clientes v3 Text Analytics para java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: a0e6b5b7d5cedc821ee34bdd219ae07bb9d43199
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "79481913"
---
<a name="HOLTop"></a>

[Documentação de referência](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Biblioteca Código fonte](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Amostras de](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics) [pacote](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) | 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) de Desenvolvimento Java (JDK) com versão 8 ou superior
* Assim que tiver a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="sua subscrição"  target="_blank">Azure, crie um recurso Text Analytics criar um recurso <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics no portal Azure para obter a sua chave e ponto final.  Depois de ser implantado, clique em **ir para o recurso**.
    * Necessitará da chave e do ponto final do recurso que cria para ligar a sua aplicação à API textanalytics. Vaicolar a chave e o ponto final no código abaixo no arranque rápido.
    * Você pode usar o nível de preços gratuitos (`F0`) para experimentar o serviço, e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="add-the-client-library"></a>Adicione a biblioteca de clientes

Crie um projeto Maven no seu IDE preferido ou ambiente de desenvolvimento. Em seguida, adicione a seguinte dependência ao ficheiro *pom.xml* do seu projeto. Você pode encontrar a sintaxe de implementação [para outras ferramentas](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) de construção on-line.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.3</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java)que contém os exemplos de código neste arranque rápido. 

Crie um `TextAnalyticsSamples.java`ficheiro Java chamado . Abra o ficheiro e `import` adicione as seguintes declarações:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

No ficheiro java, adicione uma nova classe e adicione a chave e o ponto final do seu recurso azul, como mostrado abaixo.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Adicione o seguinte método principal à classe. Definirá os métodos aqui chamados mais tarde.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Modelo de objeto

O cliente Text `TextAnalyticsClient` Analytics é um objeto que autentica o Azure usando a sua chave, e fornece funções para aceitar texto como cordas únicas ou como um lote. Pode enviar sms para a API sincronicamente, ou assincronicamente. O objeto de resposta conterá as informações de análise de cada documento que enviar. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis) 
* [Deteção de idioma](#language-detection)
* [Reconhecimento de Entidade Nomeada](#named-entity-recognition-ner) 
* [Ligação de entidades](#entity-linking)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um método `TextAnalyticsClient` para instantanear o objeto com a chave e ponto final para o seu recurso Text Analytics.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

No método do `main()` seu programa, ligue para o método de autenticação para instantanear o cliente.

## <a name="sentiment-analysis"></a>Análise de sentimentos

Crie uma `sentimentAnalysisExample()` nova função chamada que leve o `analyzeSentiment()` cliente que criou anteriormente, e chame a sua função. O `AnalyzeSentimentResult` objeto devolvido `documentSentiment` `sentenceSentiments` conterá e, `errorMessage` se for bem sucedido, ou se não. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="language-detection"></a>Deteção de idioma

Crie uma `detectLanguageExample()` nova função chamada que leve o `detectLanguage()` cliente que criou anteriormente, e chame a sua função. O `DetectLanguageResult` objeto devolvido conterá uma língua primária detetada, uma lista de outras línguas detetadas se for bem sucedida, ou se `errorMessage` não.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar `countryHint` o parâmetro para especificar um código de país de 2 letras. Por padrão, a API está a usar os "EUA" como país padrãoHint, para remover este `countryHint = ""`comportamento pode redefinir este parâmetro definindo este valor para uma cadeia vazia . Para definir um padrão `TextAnalyticsClientOptions.DefaultCountryHint` diferente, detete a propriedade e passe-a durante a inicialização do cliente.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getScore());
}
```

### <a name="output"></a>Saída

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidade Seleção (NER)

> [!NOTE]
> Na `3.0-preview`versão:
> * O NER inclui métodos separados para detetar informações pessoais. 
> * A ligação de entidades é um pedido separado do NER.

Crie uma `recognizeEntitiesExample()` nova função chamada que leve o `recognizeEntities()` cliente que criou anteriormente, e chame a sua função. O `RecognizeEntitiesResult` objeto devolvido conterá `NamedEntity` uma lista `errorMessage` de se for bem sucedido, ou se não.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="using-ner-to-recognize-personal-information"></a>Usar ner para reconhecer informações pessoais

Crie uma `recognizePIIEntitiesExample()` nova função chamada que leve o `recognizePiiEntities()` cliente que criou anteriormente, e chame a sua função. O `RecognizePiiEntitiesResult` objeto devolvido conterá `NamedEntity` uma lista `errorMessage` de se for bem sucedido, ou se não. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, %nentity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), 
entity sub-category: null, score: 0.85.
```

## <a name="entity-linking"></a>Ligação de entidades

Crie uma `recognizeLinkedEntitiesExample()` nova função chamada que leve o `recognizeLinkedEntities()` cliente que criou anteriormente, e chame a sua função. O `RecognizeLinkedEntitiesResult` objeto devolvido conterá `LinkedEntity` uma lista `errorMessage` de se for bem sucedido, ou se não. Uma vez que as entidades ligadas são identificadas de `LinkedEntity` forma única, `LinkedEntityMatch` as ocorrências da mesma entidade são agrunadas sob um objeto como uma lista de objetos.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
            "to develop and sell BASIC interpreters for the Altair 8800. " +
            "During his career at Microsoft, Gates held the positions of chairman, " +
            "chief executive officer, president and chief software architect, " +
            "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
                linkedEntity.getName(),
                linkedEntity.getDataSourceEntityId(),
                linkedEntity.getUrl(),
                linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
                    linkedEntityMatch.getText(),
                    linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Saída

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.78
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.55
Text: Gates, Score: 0.55
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.53
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.47
Text: Microsoft, Score: 0.47
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.25
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.28
```
## <a name="key-phrase-extraction"></a>Extração de expressões-chave

Crie uma `extractKeyPhrasesExample()` nova função chamada que leve o `extractKeyPhrases()` cliente que criou anteriormente, e chame a sua função. O `ExtractKeyPhraseResult` objeto devolvido conterá uma lista de frases-chave se for bem sucedida, ou se `errorMessage` não.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Saída

```console
Recognized phrases: 
cat
veterinarian
```
