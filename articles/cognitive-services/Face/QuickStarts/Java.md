---
title: 'Quickstart: Detete rostos numa imagem com a API e Java do REST Azure'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, utilizará a API Azure Face REST com Java para detetar rostos numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: d6d0a5cdf4b33ba290042627f0ceaf4cf73a375c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169317"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Início rápido: detetar rostos numa imagem com a API REST e Java

Neste arranque rápido, você usará a API Face Face Azur com Java para detetar rostos humanos numa imagem.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de subscrição Face. Você pode obter uma chave de subscrição de teste gratuito da [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou, siga as instruções na [Conta Criar uma Conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço Face e obter a sua chave.
- Qualquer Java IDE à sua escolha.

## <a name="create-the-java-project"></a>Criar o projeto Java

1. Crie uma nova aplicação Java de linha de comando no seu IDE e adicione uma classe **Principal** com um método **principal.**
1. Importe as seguintes bibliotecas para o projeto Java. Se estiver a utilizar o Maven, as coordenadas do Maven são fornecidas para cada biblioteca.
   - [Cliente Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [Núcleo Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [Biblioteca JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Exploração madeireira apache Commons](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (registo de comuns:registo de comuns:1.1.2)

## <a name="add-face-detection-code"></a>Adicionar código de deteção facial

Abra a classe principal do seu projeto. Aqui, irá adicionar o código necessário para carregar imagens e detetar rostos.

### <a name="import-packages"></a>Importar pacotes

Adicione as `import` seguintes declarações ao topo do ficheiro.

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>Adicionar campos essenciais

Substitua a classe **Principal** pelo seguinte código. Estes dados especificam como ligar ao serviço Face e onde obter os dados de entrada. Terá de atualizar o `subscriptionKey` campo com o valor da sua `uriBase` chave de subscrição e alterar a cadeia de modo a conter a cadeia de ponto final correto. Também pode querer definir `imageWithFaces` o valor para um caminho que aponta para um ficheiro de imagem diferente.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

O `faceAttributes` campo é simplesmente uma lista de certos tipos de atributos. Especificará quais as informações a obter sobre os rostos detetados.

```Java
public class Main {
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    private static final String uriBase =
        "https://<My Endpoint String>.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Ligue para a deteção facial REST API

Adicione o método **principal** com o seguinte código. Constrói uma chamada REST para a API facial para detetar `faceAttributes` informações faciais na imagem remota (a cadeia especifica quais os atributos faciais para recuperar). Em seguida, escreve os dados de saída para uma cadeia JSON.

```Java
    public static void main(String[] args) {
        HttpClient httpclient = HttpClientBuilder.create().build();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>Parse a resposta JSON

Diretamente abaixo do código anterior, adicione o seguinte bloco, que converte os dados JSON devolvidos num formato mais facilmente legível antes de imprimi-los à consola. Finalmente, feche o bloco de tentativas, o método **principal,** e a classe **Principal.**

```Java
            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="run-the-app"></a>Executar a aplicação

Compila o código e executa-o. Uma resposta bem sucedida mostrará os dados do Face em formato JSON facilmente legível na janela da consola. Por exemplo:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma simples aplicação de consola Java que utiliza chamadas REST para a API Face Azure para detetar rostos numa imagem e devolver os seus atributos. Em seguida, saiba fazer mais com esta funcionalidade numa aplicação Android.

> [!div class="nextstepaction"]
> [Tutorial: Criar uma aplicação Android para detetar e enquadrar rostos](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
