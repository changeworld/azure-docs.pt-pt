---
title: Método de deteção de texto de tradutor API
titleSuffix: Azure Cognitive Services
description: Identifique a linguagem de um pedaço de texto com o método de deteção de texto sonoro do tradutor de serviços cognitivos Azure API Detect.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 370f3b14c12fc05f181d6497b7069bbf1cf3c9cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837304"
---
# <a name="translator-text-api-30-detect"></a>Texto tradutor API 3.0: Detetar

Identifica a linguagem de um pedaço de texto.

## <a name="request-url"></a>URL do Pedido

Envie `POST` um pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido transmitidos na corda de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>api-version</td>
    <td>*Parâmetro necessário*.<br/>Versão da API solicitada pelo cliente. O valor `3.0`deve ser.</td>
  </tr>
</table> 

Os cabeçalhos de pedido incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Cabeçalho de autenticação</td>
    <td><em>Cabeçalho de pedido exigido</em>.<br/>Consulte <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação.</a></td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Cabeçalho de pedido exigido*.<br/>Especifica o tipo de conteúdo da carga útil. Os valores `application/json`possíveis são: .</td>
  </tr>
  <tr>
    <td>Comprimento do conteúdo</td>
    <td>*Cabeçalho de pedido exigido*.<br/>O comprimento do corpo de pedido.</td>
  </tr>
  <tr>
    <td>X-ClientTraceid</td>
    <td>*Opcional.*<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Note que pode omitir este cabeçalho se incluir o id de traço `ClientTraceId`na corda de consulta usando um parâmetro de consulta chamado .</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento matriz é um objeto JSON com uma propriedade de cordas chamada `Text`. A deteção de idiomas `Text` é aplicada ao valor do imóvel. Um corpo de pedido de amostra é assim:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Aplicam-se as seguintes limitações:

* A matriz pode ter no máximo 100 elementos.
* O valor de texto de um elemento matriz não pode exceder 10.000 caracteres, incluindo espaços.
* Todo o texto incluído no pedido não pode exceder 50.000 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem sucedida é um array JSON com um resultado para cada corda na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `language`: Código da linguagem detetada.

  * `score`: Um valor flutuante que indique a confiança no resultado. A pontuação é entre zero e um e uma pontuação baixa indica uma baixa confiança.

  * `isTranslationSupported`: Um valor booleano que é verdade se a língua detetada for uma das línguas suportadas para a tradução de texto.

  * `isTransliterationSupported`: Um valor booleano que é verdade se a língua detetada for uma das línguas suportadas para a transliteração.
  
  * `alternatives`: Uma série de outras línguas possíveis. Cada elemento da matriz é outro objeto `language`com `score` `isTranslationSupported` as `isTransliterationSupported`mesmas propriedades acima listadas: , e .

Um exemplo de resposta JSON é:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>X-Requestid</td>
    <td>Valor gerado pelo serviço para identificar o pedido. É usado para fins de resolução de problemas.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado http que um pedido devolve. 

<table width="100%">
  <th width="20%">Código de Estado</th>
  <th>Descrição</th>
  <tr>
    <td>200</td>
    <td>Êxito.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está em falta ou não é válido. Corretos parâmetros de pedido antes de tentar novamente.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>O pedido não pôde ser autenticado. Verifique se as credenciais são especificadas e válidas.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>O pedido não está autorizado. Verifique a mensagem de erro dos detalhes. Isto indica frequentemente que todas as traduções gratuitas fornecidas com uma subscrição experimental foram usadas.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O servidor rejeitou o pedido porque o cliente excedeu os limites de pedido.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Ocorreu um erro inesperado. Se o erro persistir, informe-o com: data e hora `X-RequestId`da falha, solicite `X-ClientTraceId`o identificador do cabeçalho de resposta e identificador do cliente do cabeçalho do pedido .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente indisponível. Tente o pedido. Se o erro persistir, informe-o com: data e hora `X-RequestId`da falha, solicite `X-ClientTraceId`o identificador do cabeçalho de resposta e identificador do cliente do cabeçalho do pedido .</td>
  </tr>
</table> 

Se ocorrer um erro, o pedido também devolverá uma resposta de erro da JSON. O código de erro é um número de 6 dígitos que combina o código de estado HTTP de 3 dígitos seguido de um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns podem ser encontrados na página de referência da API do [texto do tradutor v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

O exemplo que se segue mostra como recuperar línguas suportadas para tradução de texto.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```
