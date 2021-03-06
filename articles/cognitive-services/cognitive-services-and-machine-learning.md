---
title: Serviços Cognitivos e Aprendizagem automática
titleSuffix: Azure Cognitive Services
description: Saiba onde se enquadram os Serviços Cognitivos do Azure entre as outras ofertas do Azure para aprendizagem automática.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: cde505e4c95de9b9693a0e9d260d7fa84f3e905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75531484"
---
# <a name="cognitive-services-and-machine-learning"></a>Serviços Cognitivos e aprendizagem automática

Os Serviços Cognitivos fornecem capacidades de aprendizagem automática para resolver problemas gerais, como analisar texto para sentimento emocional ou analisar imagens para reconhecer objetos ou rostos. Não é preciso aprendizagem automática especial ou conhecimento de ciência de dados para usar estes serviços. 

[Os Serviços Cognitivos](welcome.md) são um grupo de serviços, cada um apoiando diferentes capacidades de previsão generalizadas. Os serviços estão divididos em diferentes categorias para ajudá-lo a encontrar o serviço certo. 

|Categoria de serviço|Objetivo|
|--|--|
|[Decisão](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Crie aplicações que obtenham recomendações para assegurar uma tomada de decisões informada e eficiente.|
|[Língua](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Permita às suas aplicações processar linguagem natural com scripts pré-criados, avaliar sentimentos e aprender a reconhecer o que os utilizadores pretendem.|
|[Procurar](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Adicione APIs da Pesquisa do Bing às suas aplicações e tire partida da capacidade de lidar com milhares de milhões de páginas Web, imagens, vídeos e notícias com uma única chamada à API.|
|[Voz](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Converta voz em texto e texto em voz natural. Traduza de um idioma para outro e ative o reconhecimento e a verificação de orador.|
|[Visão](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Reconheça, identifique, legende, indexe e modere as suas imagens, vídeos e conteúdo com tinta digital.|
||||

Utilize os Serviços Cognitivos quando:

* Pode usar uma solução generalizada.
* Solução de acesso a partir de uma programação REST API ou SDK. 

Utilize outra solução de aprendizagem automática quando:

* Precisa escolher o algoritmo e precisa treinar em dados muito específicos.

## <a name="what-is-machine-learning"></a>O que é o Machine Learning?

Machine learning é um conceito onde se reúnem dados e um algoritmo para resolver uma necessidade específica. Uma vez treinados os dados e o algoritmo, a saída é um modelo que pode voltar a usar com dados diferentes. O modelo treinado fornece insights com base nos novos dados. 

O processo de construção de um sistema de machine learning requer algum conhecimento de machine learning ou ciência de dados.

O machine learning é fornecido utilizando produtos e serviços de [Aprendizagem automática (AML) azure.](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)

## <a name="what-is-a-cognitive-service"></a>O que é um Serviço Cognitivo?

Um Serviço Cognitivo fornece parte ou todos os componentes numa solução de machine learning: dados, algoritmo e modelo treinado. Estes serviços destinam-se a exigir conhecimentos gerais sobre os seus dados sem precisar de experiência com machine learning ou ciência de dados. Estes serviços fornecem tanto os SDKs REST API(s) como os SDKs baseados em línguas. Como resultado, é necessário ter conhecimentos linguísticos de programação para utilizar os serviços.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Como é que os Serviços Cognitivos e a Aprendizagem automática azure (AML) são semelhantes?

Ambos têm como objetivo final a aplicação da inteligência artificial (IA) para melhorar as operações comerciais, embora a forma como cada um fornece isso nas respetivas ofertas seja diferente. 

Geralmente, o público é diferente:

* Os Serviços Cognitivos são para desenvolvedores sem experiência de aprendizagem automática.
* O Azure Machine Learning é adaptado para cientistas de dados. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Como é que um Serviço Cognitivo é diferente do machine learning?

Um Serviço Cognitivo fornece um modelo treinado para si. Isto reúne dados e um algoritmo, disponíveis a partir de um REST API ou SDK. Pode implementar este serviço em poucos minutos, dependendo do seu cenário.  Um Serviço Cognitivo fornece respostas a problemas gerais, tais como frases-chave no texto ou identificação de itens em imagens. 

O machine learning é um processo que geralmente requer um período de tempo mais longo para implementar com sucesso. Este tempo é gasto na recolha de dados, limpeza, transformação, seleção de algoritmos, treino de modelos e implantação para chegar ao mesmo nível de funcionalidade fornecido por um Serviço Cognitivo. Com o machine learning, é possível fornecer respostas a problemas altamente especializados e/ou específicos. Os problemas de aprendizagem automática requerem familiaridade com o assunto específico e os dados do problema em questão, bem como a experiência em ciência dos dados.

## <a name="what-kind-of-data-do-you-have"></a>Que tipo de dados tem?

Os Serviços Cognitivos, como um grupo de serviços, não podem exigir nenhum, alguns, ou todos os dados personalizados para o modelo treinado. 

### <a name="no-additional-training-data-required"></a>Não são necessários dados adicionais de formação

Os serviços que fornecem um modelo totalmente treinado podem ser tratados como uma _caixa preta._ Não precisas de saber como funcionam ou que dados foram usados para os treinar. Trazes os teus dados para um modelo totalmente treinado para obteres uma previsão. 

### <a name="some-or-all-training-data-required"></a>Alguns ou todos os dados de formação necessários

Alguns serviços permitem-lhe trazer os seus próprios dados e depois treinar um modelo. Isto permite-lhe estender o modelo utilizando os dados e algoritmos do Serviço com os seus próprios dados. A saída corresponde às suas necessidades. Quando traz os seus próprios dados, poderá ter de marcar os dados de uma forma específica do serviço. Por exemplo, se estiver a treinar um modelo para identificar flores, pode fornecer um catálogo de imagens de flores juntamente com a localização da flor em cada imagem para treinar o modelo. 

Um serviço pode _permitir-lhe_ fornecer dados para melhorar os seus próprios dados. Um serviço pode _exigir_ que forneça dados. 

### <a name="real-time-or-near-real-time-data-required"></a>Dados em tempo real ou próximo de tempo real necessários

Um serviço pode necessitar de dados em tempo real ou em tempo real para construir um modelo eficaz. Estes serviços processam quantidades significativas de dados de modelos. 

## <a name="service-requirements-for-the-data-model"></a>Requisitos de serviço para o modelo de dados

Os seguintes dados categorizam cada serviço pelo tipo de dados que permite ou exige.

|Serviço Cognitivo|Não são necessários dados de formação|Fornece alguns ou todos os dados de treino|Recolha de dados em tempo real ou perto de tempo real|
|--|--|--|--|
|[Detetor de Anomalias](./Anomaly-Detector/overview.md)|x|x|x|
|Pesquisa do Bing |x|||
|[Visão Computorizada](./Computer-vision/Home.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Visão Personalizada](./Custom-Vision-Service/home.md)||x||
|[Rostos](./Face/Overview.md)|x|x||
|[Reconhecedor de Formato](./form-recognizer/overview.md)||x||
|[Leitura Avançada](./immersive-reader/overview.md)|x|||
|[Reconhecedor de Tinta Digital](./Ink-recognizer/overview.md)|x|x||
|[Compreensão de Idiomas (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizador](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Reconhecedor de Altifalantes](./speaker-recognition/home.md)||x||
|[Texto-a-fala da fala (TTS)](speech-service/text-to-speech.md)|x|x||
|[Discurso-a-texto (STT)](speech-service/speech-to-text.md)|x|x||
|[Tradução da Fala](speech-service/speech-translation.md)|x|||
|[Análise de Texto](./text-analytics/overview.md)|x|||
|[Texto do Tradutor](./translator/translator-info-overview.md)|x|||
|[Texto tradutor - tradutor personalizado](./translator/custom-translator/overview.md)||x||

*O Personalizer apenas necessita de dados de formação recolhidos pelo serviço (como opera em tempo real) para avaliar a sua política e dados. O personalizer não necessita de grandes conjuntos históricos de dados para treino frontal ou de lote. 

## <a name="where-can-you-use-cognitive-services"></a>Onde pode usar os Serviços Cognitivos?
 
Os serviços são utilizados em qualquer aplicação que possa fazer chamadas REST API ou SDK. Exemplos de aplicações incluem web sites, bots, realidade virtual ou mista, desktop e aplicações móveis. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Como é que a Pesquisa Cognitiva Azure está relacionada com os Serviços Cognitivos?

[Azure Cognitive Search](../search/search-what-is-azure-search.md) é um serviço de pesquisa em nuvem separado que usa opcionalmente os Serviços Cognitivos para adicionar imagem e processamento de linguagem natural a cargas de trabalho indexantes. Os Serviços Cognitivos estão expostos na Pesquisa Cognitiva Azure através [de habilidades incorporadas](../search/cognitive-search-predefined-skills.md) que envolvem APIs individuais. Você pode usar um recurso gratuito para walkthroughs, mas planeja criar e anexar um [recurso faturado](../search/cognitive-search-attach-cognitive-services.md) para volumes maiores.

## <a name="how-can-you-use-cognitive-services"></a>Como pode usar os Serviços Cognitivos?

Cada serviço fornece informações sobre os seus dados. Pode combinar serviços em conjunto com soluções em cadeia, como converter a fala (áudio) em texto, traduzir o texto em muitas línguas, depois usar as línguas traduzidas para obter respostas de uma base de conhecimento. Embora os Serviços Cognitivos possam ser usados para criar soluções inteligentes por si só, também podem ser combinados com projetos tradicionais de aprendizagem automática para complementar modelos ou acelerar o processo de desenvolvimento. 

Serviços Cognitivos que fornecem modelos exportados para outras ferramentas de aprendizagem automática:

|Serviço Cognitivo|Informação do modelo|
|--|--|
|[Visão Personalizada](./custom-vision-service/home.md)|[Exportação](./Custom-Vision-Service/export-model-python.md) para Tensorflow para Android, CoreML para iOS11, ONNX para Windows ML|

## <a name="learn-more"></a>Saiba mais

* [Guia de Arquitetura - Quais são os produtos de machine learning na Microsoft?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Machine learning - Introdução à aprendizagem profunda vs. machine learning](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Passos seguintes

* Crie a sua conta de Serviço Cognitivo no [portal Azure](cognitive-services-apis-create-account.md) ou com [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli).
* Aprenda a [autenticar](authentication.md) um Serviço Cognitivo.
* Utilize [a exploração de diagnóstico](diagnostic-logging.md) para identificação de problemas e depuração. 
* Implante um Serviço Cognitivo num [recipiente](cognitive-services-container-support.md)do Docker.
* Mantenha-se atualizado com [as atualizações](https://azure.microsoft.com/updates/?product=cognitive-services)do serviço .
