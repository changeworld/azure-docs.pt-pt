---
title: Frase chave Extração Kubernetes config e desdobrar passos
titleSuffix: Azure Cognitive Services
description: Frase chave Extração Kubernetes config e desdobrar passos
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6ef7efe3d48fd20c5141803430260a80395faa82
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877850"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Desloque o recipiente de extração de frases-chave para um aglomerado AKS

1. Abra o Azure CLI e inscreva-se no Azure.

    ```azurecli
    az login
    ```

1. Inscreva-se no aglomerado AKS. `your-cluster-name` Substitua `your-resource-group` e com os valores apropriados.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Após a duração deste comando, reporta uma mensagem semelhante à seguinte:

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Se tiver várias subscrições disponíveis na sua `az aks get-credentials` conta Azure e o comando devolver com um erro, um problema comum é que está a usar a subscrição errada. Detete o contexto da sua sessão Azure CLI para usar a mesma subscrição com que criou os recursos e tentar novamente.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Abra o editor de texto de eleição. Este exemplo utiliza o Código do Estúdio Visual.

    ```console
    code .
    ```

1. Dentro do editor de texto, crie um novo ficheiro chamado *keyphrase.yaml*, e cola o seguinte YAML nele. Certifique-se `billing/value` de `apikey/value` substituir e com as suas próprias informações.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Guarde o ficheiro e feche o editor de texto.
1. Executar o comando `apply` Kubernetes com o ficheiro *chave.yaml* como alvo:

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    Após o comando aplicar com sucesso a configuração de implementação, uma mensagem aparece semelhante à seguinte saída:

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Verifique se a cápsula foi implantada:

    ```console
    kubectl get pods
    ```

    A saída para o estado de funcionamento da cápsula:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verifique se o serviço está disponível e obtenha o endereço IP.

    ```console
    kubectl get services
    ```

    A saída para o estado de funcionamento do serviço *de frases-chave* na cápsula:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
