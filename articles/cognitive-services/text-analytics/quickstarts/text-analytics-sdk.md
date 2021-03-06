---
title: 'Quickstart: Biblioteca de clientes textanalytics v3 [ Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para ligar as suas aplicações à API textanalytics dos Serviços Cognitivos Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: cc3f48ec1113f954336cfae0bda2cba2499d9a1d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80240104"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Quickstart: Use a biblioteca de clientes Text Analytics

Começar com a biblioteca de clientes text Analytics. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

Utilize a biblioteca de clientes Text Analytics para executar:

* Análise de sentimentos
* Deteção de idioma
* Reconhecimento de entidades
* Extração de expressões-chave

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * A versão de pré-visualização `3.0-preview`mais recente da API text analytics é, que inclui uma pré-visualização pública para melhor análise de [sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e [reconhecimento de entidades nomeadas (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A última versão `2.1`estável é.
>    * Certifique-se de que segue apenas as instruções para a versão que está a utilizar.
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não seguras por razões de simplicidade. Para cenários de produção, recomendamos a utilização dos métodos assíncronos emlotados para o desempenho e escalabilidade. Consulte a documentação de referência abaixo.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Este quickstart é `3.0-preview` apenas para versão da biblioteca de clientes Text Analytics, que inclui uma pré-visualização pública para melhor análise de [sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e reconhecimento de [entidades nomeadas (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não seguras por razões de simplicidade. Para cenários de produção, recomendamos a utilização dos métodos assíncronos emlotados para o desempenho e escalabilidade. Consulte a documentação de referência abaixo.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * A versão de pré-visualização `3.0-preview`mais recente da API text analytics é, que inclui uma pré-visualização pública para melhor análise de [sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e [reconhecimento de entidades nomeadas (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A última versão `2.1`estável é.
>    * Certifique-se de que segue apenas as instruções para a versão que está a utilizar.
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não seguras por razões de simplicidade. Para cenários de produção, recomendamos a utilização dos métodos assíncronos emlotados para o desempenho e escalabilidade. Consulte a documentação de referência abaixo.
> * Também pode executar esta versão da biblioteca de clientes Text Analytics [no seu navegador.](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * A versão de pré-visualização `3.0-preview`mais recente da API text analytics é, que inclui uma pré-visualização pública para melhor análise de [sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e [reconhecimento de entidades nomeadas (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A última versão `2.1`estável é.
>    * Certifique-se de que segue apenas as instruções para a versão que está a utilizar.
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não seguras por razões de simplicidade. Para cenários de produção, recomendamos a utilização dos métodos assíncronos emlotados para o desempenho e escalabilidade. Consulte a documentação de referência abaixo. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Apoio linguístico adicional

Se clicou neste separador, provavelmente não viu um início rápido na sua linguagem de programação favorita. Não se preocupe, temos mais quickstarts disponíveis. Utilize a tabela para encontrar a amostra certa para a sua linguagem de programação.

| Idioma | Versão disponível | 
|----------|------------------------|
| Ruby     | [Versão 2.1](ruby-sdk.md) | 
| Ir       | [Versão 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore uma solução](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Descrição Geral da Análise de Texto](../overview.md)
* [Análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidades](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detetar idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento linguístico](../how-tos/text-analytics-how-to-language-detection.md)
