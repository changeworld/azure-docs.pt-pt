---
title: Criar um recurso de Serviços Cognitivos no portal Azure
titleSuffix: Azure Cognitive Services
description: Inicie-se com os Serviços Cognitivos Azure criando e subscrevendo um recurso no portal Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219480"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Criar um recurso de Serviços Cognitivos utilizando o portal Azure

Use este quickstart para começar a usar Serviços Cognitivos Azure. Depois de criar um recurso do Serviço Cognitivo no portal Azure, terá um ponto final e uma chave para autenticar as suas aplicações.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição Azure válida - [Crie uma gratuitamente.](https://azure.microsoft.com/free/)

## <a name="create-a-new-azure-cognitive-services-resource"></a>Criar um novo recurso azure Cognitive Services

1. Criar um recurso.

    #### <a name="multi-service-resource"></a>[Recurso multi-serviço](#tab/multiservice)
    
    O recurso multi-serviço é nomeado **Serviços Cognitivos** no portal. [Criar um recurso de Serviços Cognitivos.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)
    
    Neste momento, o recurso multi-serviço permite o acesso aos seguintes Serviços Cognitivos:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Imagem Digitalizada  | Content Moderator                                    | Rostos               | Compreensão de Idiomas (LUIS) | Análise de Texto   |
    | Texto do Tradutor  | Bing Search v7 <br>(Web, Imagem, Notícias, Vídeo, Visual) | Pesquisa Personalizada do Bing | Pesquisa de Entidades do Bing            | Sugestão Automática do Bing |
    | Verificação Ortográfica do Bing |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[Recurso de serviço único](#tab/singleservice)

    Utilize os links abaixo para criar um recurso para os Serviços Cognitivos disponíveis:

    | Visão                      | Voz                  | Idioma                          | Decisão             | Pesquisa                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Visão do computador](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Serviços de Voz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Leitor imersivo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Detetor de Anomalias](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Search API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Serviço de visão personalizada](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Reconhecimento de Orador](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Compreensão de Idiomas (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Pesquisa Personalizada do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Rostos](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizador](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Pesquisa de Entidades do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Reconhecedor de Tinta Digital](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Análise de Texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Verificação de feitiço sorhções](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Texto do Tradutor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Sugestão Automática do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Na página **Criar,** forneça as seguintes informações:

    #### <a name="multi-service-resource"></a>[Recurso multi-serviço](#tab/multiservice)

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o seu recurso de serviços cognitivos. Por exemplo, *MyCognitiveServicesResource*. |
    | **Assinatura** | Selecione uma das suas subscrições Azure disponíveis. |
    | **Localização** | A localização da sua instância de serviço cognitivo. Diferentes localizações podem introduzir latência, mas não têm impacto na disponibilidade de tempo de execução do seu recurso. |
    | **Nível de preços** | O custo da sua conta de Serviços Cognitivos depende das opções que escolher e do seu uso. Para mais informações, consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/cognitive-services/)da API .
    | **Grupo de recursos** | O grupo de recursos Azure que irá conter o seu recurso Serviços Cognitivos. Pode criar um novo grupo ou adicioná-lo a um grupo pré-existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Clique em **Criar**.

    #### <a name="single-service-resource"></a>[Recurso de serviço único](#tab/singleservice)

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o seu recurso de serviços cognitivos. Por exemplo, *TextAnalyticsResource*. |
    | **Assinatura** | Selecione uma das suas subscrições Azure disponíveis. |
    | **Localização** | A localização da sua instância de serviço cognitivo. Diferentes localizações podem introduzir latência, mas não têm impacto na disponibilidade de tempo de execução do seu recurso. |
    | **Nível de preços** | O custo da sua conta de Serviços Cognitivos depende das opções que escolher e do seu uso. Para mais informações, consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/cognitive-services/)da API .
    | **Grupo de recursos** | O grupo de recursos Azure que irá conter o seu recurso Serviços Cognitivos. Pode criar um novo grupo ou adicioná-lo a um grupo pré-existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Clique em **Criar**.

    ***


## <a name="get-the-keys-for-your-resource"></a>Pegue as chaves para o seu recurso

1. Depois de o seu recurso ser implementado com sucesso, clique em **Ir para recorrer** em **Próximos Passos**.

    ![Pesquisa de Serviços Cognitivos](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. A partir do painel de arranque rápido que abre, pode aceder à sua chave e ponto final.

    ![Obter chave e ponto final](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos contidos no grupo.

1. No portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha **Grupos de Recursos**, para apresentar a lista dos seus grupos de recursos.
2. Localizar o grupo de recursos que contém o recurso a eliminar
3. Clique na listagem do grupo de recursos. Selecione **Eliminar grupo de recursos** e confirme.

## <a name="see-also"></a>Consulte também

* [Autenticação de pedidos aos Serviços Cognitivos Azure](authentication.md)
* [O que é serviços cognitivos Azure?](Welcome.md)
* [Apoio à linguagem natural](language-support.md)
* [Suporte de contentores Docker](cognitive-services-container-support.md)
