---
title: 'Início rápido: detetar rostos numa imagem com a API REST e Go'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, deteta rostos de uma imagem utilizando o serviço Face com Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 246b9c81e3cb726236143b9749486ba362128756
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76165843"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-go"></a>Início rápido: detetar rostos numa imagem com a API REST e Go

Neste arranque rápido, você usará a API Face Face Azur com Go para detetar rostos humanos em uma imagem.

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de subscrição Face. Você pode obter uma chave de subscrição de teste gratuito da [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou, siga as instruções na [Conta Criar uma Conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço Face e obter a sua chave.
- Um editor de código como [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="write-the-script"></a>Escreva o guião

Crie um novo ficheiro, _faceDetection.go_e adicione o seguinte código. Isto chama a API facial para um determinado URL de imagem.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    const subscriptionKey = "<Subscription Key>"

    // You must use the same location in your REST call as you used to get your
    // subscription keys. For example, if you got your subscription keys from
    // westus, replace "westcentralus" in the URL below with "westus".
    const uriBase =
      "https://<My Endpoint String>.com/face/v1.0/detect"
    const imageUrl =
      "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg"

    const params = "?returnFaceAttributes=age,gender,headPose,smile,facialHair," +
        "glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    //Configure TLS, etc.
    tr := &http.Transport{
        TLSClientConfig: &tls.Config{
            InsecureSkipVerify: true,
        },
    }
    
    // Create the Http client
    client := &http.Client{
        Transport: tr,
        Timeout: time.Second * 2,
    }

    // Create the Post request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the Json data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the Json result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

Terá de atualizar o `subscriptionKey` valor com a sua `uriBase` chave de subscrição e alterar a cadeia de modo a conter a cadeia de ponto final correto.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

Também pode querer mudar `imageUrl` de campo para apontar para a sua própria imagem de entrada. Também pode querer alterar `returnFaceAttributes` o campo que especifica quais os atributos faciais a recuperar.

## <a name="run-the-script"></a>Executar o script

Abra um pedido de comando e construa o programa com o seguinte comando:

```shell
go build faceDetection.go
```

Em seguida, executar o programa:

```shell
detect-face
```

Deve ver uma cadeia json de dados faciais detetados impressos na consola. Segue-se um exemplo de uma resposta json bem sucedida.

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, escreveu uma Aplicação de Consola Go que chama o serviço Azure Face para detetar rostos numa imagem e devolver os seus atributos. Em seguida, explore a documentação de referência da API face para saber mais.

> [!div class="nextstepaction"]
> [API Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
