---
title: Bing Image Search Python biblioteca cliente quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: e3dc3fd30d1eceab4b24b6699dd81a91bca51115
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78899595"
---
Use este quickstart para fazer a sua primeira pesquisa de imagem usando a biblioteca de clientes Bing Image Search, que é um invólucro para a API e contém as mesmas funcionalidades. Esta aplicação Python simples envia uma consulta de pesquisa de imagens, analisa a resposta JSON e apresenta o URL da primeira imagem devolvida.

O código fonte deste exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) com processamento de erros e anotações de código adicionais.

## <a name="prerequisites"></a>Pré-requisitos

* [Python 2.7 ou 3.4](https://www.python.org/) e superior.

* A [biblioteca de clientes Azure Image Search](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) para Python
    * Instalar com `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo script Python no seu IDE ou editor favorito e as seguintes importações:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Crie variáveis para a sua chave de subscrição e termo de pesquisa.

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Criar o cliente de pesquisa de imagens

1. Crie uma instância do `CognitiveServicesCredentials` e utilize-a para instanciar o cliente:

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```
1. Envie uma consulta de pesquisa para a API de Pesquisa de Imagens do Bing:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>Processar e ver os resultados

Analise os resultados da imagem devolvidos na resposta.


Se a resposta contiver os resultados da pesquisa, armazene o primeiro resultado e imprima os detalhes, como um URL de miniatura, o URL original, juntamente com o número total de imagens devolvidas.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Bing Image Search single-page app tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) (Tutorial de aplicação de página única da Pesquisa de Imagens do Bing)

## <a name="see-also"></a>Consulte também

* [O que é a Pesquisa de Imagens do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimentar uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obter uma chave de acesso aos Serviços Cognitivos gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Exemplos de Python para o SDK dos Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
