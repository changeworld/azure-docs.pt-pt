---
title: Use APIs de REPOUSO para fazer CI/CD para Azure Stream Analytics em IoT Edge
description: Aprenda a implementar um oleoduto de integração e implantação contínuo para o Azure Stream Analytics utilizando APIs REST.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 328ca7cd2c6f76095c8334ae6fdb4aa75fbb867d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292010"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementar CI/CD para Stream Analytics em IoT Edge usando APIs

Pode permitir a integração e implantação contínuas para trabalhos de Azure Stream Analytics utilizando APIs REST. Este artigo fornece exemplos sobre os quais as APIs utilizam e como usá-las. As APIs de REPOUSO não são suportadas na Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Ligue para APIs de diferentes ambientes

As APIs rest podem ser chamadas tanto do Linux como do Windows. Os seguintes comandos demonstram a sintaxe adequada para a chamada da API. A utilização específica da API será delineada em secções posteriores deste artigo.

### <a name="linux"></a>Linux

Para o Linux, `Curl` `Wget` pode utilizar ou comandos:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Para Windows, utilize powershell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Criar um trabalho asa em Edge 
 
Para criar o trabalho stream analytics, ligue para o método PUT utilizando a API Stream Analytics.

|Método|URL do Pedido|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
Exemplo de comando utilizando **caracóis:**

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Exemplo do órgão de pedido na JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Para mais informações, consulte a documentação da [API.](/rest/api/streamanalytics/stream-analytics-job)  
 
## <a name="publish-edge-package"></a>Publicar pacote Edge 
 
Para publicar um trabalho de Stream Analytics no IoT Edge, ligue para o método POST utilizando a API de Publicação do Pacote Edge.

|Método|URL do Pedido|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

Esta operação assíncrona devolve um estatuto de 202 até que o trabalho seja publicado com sucesso. O cabeçalho de resposta de localização contém o URI usado para obter o estado do processo. Enquanto o processo está em curso, uma chamada para o URI no cabeçalho de localização devolve um estado de 202. Quando o processo terminar, o URI no cabeçalho de localização devolve um estatuto de 200. 

Exemplo de uma chamada de publicação de pacote Edge usando **caracóis:** 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Depois de fazer a chamada do POST, deve esperar uma resposta com um corpo vazio. Procure o URL localizado no HEAD da resposta e grave-o para posterior utilização.
 
Exemplo do URL do HEAD of response:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Aguarde um a dois minutos antes de executar o seguinte comando para fazer uma chamada api com o URL encontrado na CABEÇA da resposta. Tente o comando se não receber uma resposta de 200.
 
Exemplo de fazer chamada aPi com URL devolvido com **caracol:**

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

A resposta inclui a informação que precisa adicionar ao script de implementação edge. Os exemplos abaixo mostram que informações precisa de recolher e onde adicioná-la no manifesto de implantação.
 
Corpo de resposta da amostra após publicação com sucesso:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

Amostra do manifesto de implantação: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Após a configuração do manifesto de implantação, consulte os módulos De implantação [Azure IoT Edge com o Azure CLI](../iot-edge/how-to-deploy-modules-cli.md) para implementação.


## <a name="next-steps"></a>Passos seguintes 
 
* [Azure Stream Analytics no IoT Edge](stream-analytics-edge.md)
* [ASA no tutorial IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Desenvolver trabalhos stream analytics edge usando ferramentas do Estúdio Visual](stream-analytics-tools-for-visual-studio-edge-jobs.md)
