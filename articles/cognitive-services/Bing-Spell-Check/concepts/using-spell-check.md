---
title: Utilizar a API de Verificação de Ortografia do Bing
titleSuffix: Azure Cognitive Services
description: Conheça os modos de verificação de feitiços de Bing, configurações e outras informações relacionadas com a API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: c5c9ad8be8bd4cd834b01a0c67e0bbc81b8cdd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881892"
---
# <a name="using-the-bing-spell-check-api"></a>Utilizar a API de Verificação de Ortografia do Bing

Use este artigo para aprender sobre a utilização da API bing spell check para realizar gramática contextual e verificação de feitiços. Embora a maioria dos verificadores de feitiços dependa de conjuntos de regras baseados no dicionário, o verificador de feitiços Bing aproveita a aprendizagem automática e a tradução de máquinas estatísticas para fornecer correções precisas e contextuais. 

## <a name="spell-check-modes"></a>Modos de verificação ortográfica

A API suporta dois modos de revisão de texto, `Proof` e `Spell`.  Experimente [estes](https://azure.microsoft.com/services/cognitive-services/spell-check/) exemplos.

### <a name="proof---for-documents"></a>Prova - para documentos 

O modo predefinido é `Proof`. O modo de ortografia `Proof` oferece as verificações mais abrangentes, a adição de capitalização, pontuação básica e outras funcionalidades para ajudar na criação de documentos. no entanto, está disponível apenas nos mercados en-US (inglês dos Estados Unidos), es-ES (espanhol), pt-BR (português do Brasil) (Nota: apenas na versão beta para espanhol e português). Para todos os outros mercados, defina o parâmetro de consulta de modo para Ortografia. 

> [!NOTE]
> Se o comprimento do texto de consulta exceder 4096, será truncado para 4096 caracteres, e ntão será processado. 

### <a name="spell----for-web-searchesqueries"></a>Feitiço - para pesquisas/consultas web

A `Spell` é mais agressiva para devolver resultados melhores de pesquisa. O modo `Spell` encontra a maioria dos erros de ortografia, mas não encontra alguns dos erros de gramática que o `Proof` encontra, por exemplo, a capitalização e as palavras repetidas.

> [!NOTE]
> * O comprimento máximo de consulta suportada é inferior. Se a consulta exceder o comprimento máximo, a consulta e os seus resultados não serão alterados.
>    * 130 caracteres para os seguintes códigos linguísticos: en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko. 
>    * 65 caracteres para todos os outros.
> * O modo Spell não suporta`[` caracteres `]`de suporte quadrado ( e) em consultas, e pode causar resultados inconsistentes. Recomendamos que as retire das suas consultas ao utilizar o modo Spell.

## <a name="market-setting"></a>Definição do mercado

Um código de [mercado](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) deve `mkt` ser especificado com o parâmetro de consulta a seu pedido. De outro modo, a API utilizará um mercado por defeito com base no endereço IP do pedido.


## <a name="http-post-and-get-support"></a>HTTP POST e GET suporte

A API suporta HTTP POST ou HTTP GET. O que utiliza depende do tamanho do texto que pretende verificar. Se as cadeias de carateres tiverem sempre menos de 1.500 carateres, utilizaria um GET. Mas se quiser suportar cadeias de carateres até 10.000 carateres, utilize o POST. A cadeia de texto pode incluir qualquer caráter válido UTF-8.

O exemplo seguinte mostra um pedido POST para verificar a ortografia e a gramática de uma cadeia de texto. O exemplo inclui o parâmetro de consulta de [modo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) para estar completo (poderia ter sido deixado de fora desde `mode` predefinições para Verificação). O parâmetro de consulta de [texto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) contém a cadeia a ser verificada.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Se utilizar o HTTP GET, iria incluir o parâmetro de consulta `text` na cadeia de consulta do URL
  
O código a seguir mostra a resposta ao pedido anterior. A resposta contém um objeto [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck). 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
O campo [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) apresenta uma lista de erros ortográficos e gramaticais que a API encontrou na cadeia de [texto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text). O campo `token` contém a palavra a ser substituída. Utilizaria o deslocamento baseado em zero no campo `offset` para encontrar o token na cadeia `text`. Teria de substituir a palavra naquela localização pela palavra no campo `suggestion`. 

Se o campo `type` é RepeatedToken, ainda deveria substituir o token por `suggestion`, mas provavelmente também teria de remover o espaço à direita.

## <a name="throttling-requests"></a>Limitar pedidos

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passos seguintes

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência da API de Verificação de Ortografia do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
