---
title: 'Quickstart: Analise uma imagem remota com a REST API e Node.js'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, vai analisar uma imagem através da API de Imagem Digitalizada com o Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18, seo-javascript-september2018, seo-javascript-october2019
ms.openlocfilehash: 98fb7eab52c710d9008aafefc50c2d00979d3350
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973837"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-with-nodejs"></a>Quickstart: Analise uma imagem remota utilizando a API DE DESCANSO DE Visão Computacional com Node.js

Neste arranque rápido, irá analisar uma imagem armazenada remotamente para extrair funcionalidades visuais utilizando a API de Repouso de Visão Computacional com Node.js. Com o método [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (Analisar Imagem), pode extrair caraterísticas visuais com base no conteúdo da imagem.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter o [Node.js](https://nodejs.org) 4.x ou posterior instalado.
- Tem de ter o [npm](https://www.npmjs.com/) instalado.
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Você pode obter uma chave de teste gratuita da [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou, siga as instruções na [Conta Criar uma Conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever a Visão Computacional e obter a sua chave. Em seguida, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT`chave e corda final de serviço, nomeada e, respectivamente.

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, siga os seguintes passos:

1. Instale o [`request`](https://www.npmjs.com/package/request) pacote npm.
   1. Abra a janela da linha de comandos como administrador.
   1. Execute o seguinte comando:

      ```console
      npm install request
      ```

   1. Depois de o pacote ser instalado com êxito, feche a janela da linha de comandos.

1. Copie o código seguinte para um editor de texto.
1. Opcionalmente, substitua o valor de `imageUrl` pelo URL de uma imagem diferente que pretende analisar.
1. Opcionalmente, substitua o valor do parâmetro de pedido `language` por um idioma diferente.
1. Guarde o código como um ficheiro com uma extensão `.js`. Por exemplo, `analyze-image.js`.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `node` para executar o ficheiro. Por exemplo, `node analyze-image.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v2.1/analyze';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>Examinar a resposta

O JSON devolve uma resposta de êxito. O exemplo analisa e apresenta uma resposta de êxito na janela da linha de comandos, semelhante ao seguinte exemplo:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o ficheiro e, em seguida, desinstale o pacote `request` do npm. Para desinstalar o pacote, siga os seguintes passos:

1. Abra a janela da linha de comandos como administrador.
2. Execute o seguinte comando:

   ```console
   npm uninstall request
   ```

3. Depois de o pacote ser desinstalado com êxito, feche a janela da linha de comandos.

## <a name="next-steps"></a>Passos seguintes

Explore as APIs de Imagem Digitalizada utilizadas para analisar uma imagem, detetar celebridades e marcos, criar uma miniatura e extrair texto manuscrito e impresso. Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar a API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
