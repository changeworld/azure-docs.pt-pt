---
title: 'Quickstart: Realizar uma pesquisa de notícias com C# - Bing News Search REST API'
titleSuffix: Azure Cognitive Services
description: Use este quickstart para enviar um pedido para a API de pesquisa de notícias bing news usando C#, e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: e6911c51ecfe1c8f6924bf403e9ad00e14558a09
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448485"
---
# <a name="quickstart-search-for-news-using-c-and-the-bing-news-search-rest-api"></a>Quickstart: Procure notícias usando C# e a Bing News Search REST API

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa de Notícias Bing e veja a resposta JSON. Esta simples aplicação C# envia uma consulta de pesquisa de notícias para a API, e exibe a resposta. O código completo desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingNewsSearchv7.cs).

Apesar de esta aplicação estar escrita em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017 ou mais tarde.](https://www.visualstudio.com/downloads/)
* O framework [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote NuGet.
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. criar uma nova solução de consola C# no Estúdio Visual. Em seguida, adicione os seguintes espaços de nomes ao ficheiro de código principal.
    
    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Crie variáveis para o ponto final de API, a sua chave de subscrição e o termo de pesquisa. Pode utilizar o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```csharp
    const string accessKey = "enter key here";
    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/news/search";
    const string searchTerm = "Microsoft";
    ```
   ## <a name="create-a-struct-to-format-the-bing-news-search-response"></a>Crie uma estrutura para formatar a resposta bing news search

1. Defina uma estrutura `SearchResult` para conter os resultados da pesquisa de imagens e as informações do cabeçalho JSON.

    ```csharp
    struct SearchResult
    {
        public String jsonResult;
        public Dictionary<String, String> relevantHeaders;
    }
    ```

## <a name="create-and-handle-a-news-search-request"></a>Criar e lidar com um pedido de pesquisa de notícias

Crie um método com o nome `BingNewsSearch` para efetuar a chamada à API e defina o tipo de retorno para a estrutura `SearchResult` criada anteriormente. No método, execute os seguintes passos:

1. Construa o URI do pedido de pesquisa. Tenha em atenção que o termo de pesquisa `toSearch` tem de ser formatado antes de ser anexado à cadeia.

    ```csharp
    static SearchResult BingNewsSearch(string toSearch){

        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. Execute o pedido Web e obtenha a resposta como uma cadeia JSON.

    ```csharp
    WebRequest request = WebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = subscriptionKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    ```

3. Crie o objeto do resultado de pesquisa e extraia os cabeçalhos HTTP do Bing. Em seguida, devolva `searchResult`.

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

## <a name="process-the-response"></a>Processar a resposta

1. No método principal, chame `BingNewsSearch()` e armazene a resposta devolvida. Em seguida, anule a serialização do JSON para um objeto. Pode então ver os valores da resposta.

    ```csharp
    SearchResult result = BingNewsSearch(searchTerm);
    //deserialize the JSON response
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result.jsonResult);
    Console.WriteLine(jsonObj["value"][0])
    ```

## <a name="json-response"></a>Resposta JSON

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte:

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .

      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](tutorial-bing-news-search-single-page-app.md)
