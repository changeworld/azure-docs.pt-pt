---
title: Personalize e sugestione consultas de pesquisa de imagem - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Saiba sobre personalizar as consultas de pesquisa que envia para a API de Pesquisa de Imagem Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: d833b017004365e9dad7241e360f42ff41a55883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67542748"
---
# <a name="customize-and-suggest-image-search-queries"></a>Personalize e sugira consultas de pesquisa de imagem

Use este artigo para aprender a personalizar consultas e sugerir termos de pesquisa para enviar para a API de Pesquisa de Imagem Bing.

## <a name="suggest-search-terms"></a>Sugerir termos de pesquisa

Se a sua aplicação tiver uma caixa de pesquisa onde os termos de pesquisa são introduzidos, pode utilizar a [API Bing Autosuggest](../../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API pode apresentar termos de pesquisa sugeridos em tempo real. A API retorna sugeriu cordas de consulta baseadas em termos de pesquisa parcial e serviços cognitivos.

## <a name="pivot-the-query"></a>Pivô da consulta

Se bing pode segmentar a consulta de `pivotSuggestions`pesquisa original, o objeto [imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) retornados contém . As sugestões de pivô podem ser apresentadas como termos de pesquisa opcionais para o utilizador. Por exemplo, se a consulta original fosse *o Microsoft Surface,* o Bing poderia segmentar a consulta para *a Microsoft* e o *Surface* e fornecer pivôs sugeridos para cada um. Estas sugestões podem ser apresentadas como termos de consulta opcionais ao utilizador.

O exemplo que se segue mostra as sugestões de pivô para o *Microsoft Surface:*  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

O campo `pivotSuggestions` contém a lista de segmentos (pivôs) em que a consulta original foi dividida. Para cada pivô, a resposta contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) que contêm as consultas sugeridas. O `text` campo contém a consulta sugerida. O `displayText` campo contém o termo que substitui o pivô na consulta original. Um exemplo é data de lançamento da superfície.

Se a corda de consulta de pivô é `text` `thumbnail` o que o utilizador procura, use os campos e os campos para exibir as cordas de consulta de pivô. Faça a miniatura e o `webSearchUrl` texto clicáveis utilizando o URL ou o `searchLink` URL. Utilize `webSearchUrl` para enviar o utilizador para os resultados de pesquisa do Bing. Se fornecer a sua própria `searchLink`página de resultados, utilize .

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Expandir a consulta

Se o Bing puder expandir a consulta para restringir a pesquisa original, o objeto [Imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) contém o campo `queryExpansions`. Por exemplo, se a consulta fosse o *Microsoft Surface,* as consultas expandidas poderiam ser:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Hub**.

O exemplo seguinte mostra as consultas expandidas para *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

O campo `queryExpansions` contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj). O `text` campo contém a consulta expandida. O `displayText` campo contém o termo de expansão. Se a cadeia de consulta expandida é o `text` que `thumbnail` o utilizador procura, use os campos e os campos para exibir as cordas de consulta expandidas. Faça a miniatura e o `webSearchUrl` texto clicáveis utilizando o URL ou o `searchLink` URL. Utilize `webSearchUrl` para enviar o utilizador para os resultados de pesquisa do Bing. se fornecer a sua própria `searchLink`página de resultados, use .

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Limitar pedidos

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passos seguintes

Se ainda não experimentou a API de Pesquisa de Imagem bing antes, tente um [arranque rápido](../quickstarts/csharp.md). Se procura algo mais complexo, experimente o tutorial para criar uma [aplicação web de uma página única.](../tutorial-bing-image-search-single-page-app.md)
