---
title: 'Quickstart: Pesquisa de vídeos usando a REST API e Python - Bing Video Search'
titleSuffix: Azure Cognitive Services
description: Use este quickstart para enviar pedidos de pesquisa de vídeo para a API de pesquisa de vídeo bing usando Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: fbf20c2d54506b0f314697d6df34f9a430e7c016
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382689"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-python"></a>Quickstart: Procure vídeos usando a Bing Video Search REST API e Python

Use este quickstart para fazer a sua primeira chamada para a API de pesquisa de vídeo bing e veja um resultado de pesquisa a partir da resposta JSON. Esta simples aplicação Python envia uma consulta de pesquisa de vídeo HTTP para a API, e exibe a resposta. Embora esta aplicação esteja escrita em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código fonte deste exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingVideoSearchv7.py) com processamento de erros e anotações de código adicionais.

Pode executar este exemplo como um bloco de notas do Jupyter no [MyBinder](https://mybinder.org), ao clicar no destaque de lançamento do Binder: 

[![Aglutinante](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Pré-requisitos

* Python [2.x ou 3.x](https://python.org)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e importe as seguintes bibliotecas,

    ```python
    import requests
    from IPython.display import HTML
    ```
2.  Crie variáveis para a sua chave de subscrição, ponto final de pesquisa e um termo de pesquisa. `search_url`pode ser o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.
    
    ```python
    subscription_key = None
    assert subscription_key
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
    search_term = "kittens"
    ```

3. Adicione a sua `Ocp-Apim-Subscription-Key` chave de subscrição a um cabeçalho criando um novo dicionário para associar a corda do cabeçalho à sua chave.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="send-your-request"></a>Envie o seu pedido

1. Adicione os parâmetros ao seu pedido `params`criando um dicionário chamado . Adicione o seu `q` termo de pesquisa ao parâmetro, uma contagem de vídeo de 5, `free` para o preço dos vídeos devolvidos e `short` para o comprimento do vídeo.

    ```python
    params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
    ```

2. Use `requests` a biblioteca em Python para ligar para a API de Pesquisa de Vídeo Bing. Passe a tecla API e os `headers` `params` parâmetros de pesquisa utilizando o e dicionário.
    
    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

3. Para ver um dos vídeos retornados, `search_results` obtenha um resultado de pesquisa do objeto. Insira a `embedHtml` propriedade `IFrame`do resultado num .  
    
    ```python
    HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
    ```


## <a name="json-response"></a>Resposta JSON

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Consulte também 

 [O que é a API de Pesquisa de Vídeos do Bing?](../overview.md)
