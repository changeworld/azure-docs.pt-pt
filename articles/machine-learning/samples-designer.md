---
title: Exemplo de oleodutos de design
titleSuffix: Azure Machine Learning
description: Utilize amostras no designer de Machine Learning Azure para iniciar os seus oleodutos de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389346"
---
# <a name="designer-sample-pipelines"></a>Oleodutos de amostra de design

Use os exemplos incorporados no designer de Machine Learning Azure para começar rapidamente a construir os seus próprios oleodutos de aprendizagem automática. O [repositório GitHub,](https://github.com/Azure/MachineLearningDesigner) designer de machine learning azure, contém documentação detalhada para o ajudar a compreender alguns cenários comuns de aprendizagem automática.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://aka.ms/AMLFree)
* Um espaço de trabalho azure machine learning com a Enterprise SKU.


## <a name="how-to-use-sample-pipelines"></a>Como utilizar os gasodutos de amostra

O designer guarda uma cópia dos gasodutos de amostra para o seu espaço de trabalho no estúdio. Pode editar o oleoduto para adaptá-lo às suas necessidades e guardá-lo como seu. Use-os como ponto de partida para iniciar os seus projetos.

### <a name="open-a-sample-pipeline"></a>Abra um gasoduto de amostra

1. Inscreva-se na <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>e selecione o espaço de trabalho com que pretende trabalhar.

1. Selecione **Designer**.

1. Selecione um gasoduto de amostra sob a secção **new pipeline.**

    Selecione **Mostrar mais amostras** para uma lista completa de amostras.

### <a name="submit-a-pipeline-run"></a>Submeter uma execução de gasoduto

Para executar um oleoduto, primeiro tem de definir o alvo de computação padrão para executar o gasoduto.

1. No painel **Definições** à direita da tela, **selecione Selecione o alvo computacional**.

1. No diálogo que aparece, selecione um alvo computacional existente ou crie um novo. Selecione **Guardar**.

1. Selecione **Submeter** na parte superior da tela para submeter uma execução de gasoduto.

Dependendo do pipeline da amostra e das definições de cálculo, as execuções podem demorar algum tempo a ser concluídas. As definições de computação padrão têm um tamanho mínimo de nó de 0, o que significa que o designer deve alocar recursos depois de estar inativo. As repetidas corridas de gasodutos demorarão menos tempo, uma vez que os recursos da computação já estão atribuídos. Além disso, o designer utiliza resultados em cache para cada módulo para melhorar ainda mais a eficiência.


### <a name="review-the-results"></a>Rever os resultados

Depois de o gasoduto terminar de funcionar, pode rever o gasoduto e ver a saída de cada módulo para saber mais.

Utilize os seguintes passos para visualizar as saídas do módulo:

1. Selecione um módulo na tela.

1. No módulo os detalhes painelam à direita da tela, selecione **Saídas + troncos**. Selecione ![o ícone](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) de visualização do ícone do gráfico para ver os resultados de cada módulo. 

Use as amostras como pontos de partida para alguns dos cenários mais comuns de aprendizagem automática.

## <a name="regression-samples"></a>Amostras de regressão

Saiba mais sobre as amostras de regressão incorporadas.

| Título de exemplo | Descrição | 
| --- | --- |
| [Amostra 1: Regressão - Previsão do Preço do Automóvel (Básico)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Preveja os preços dos carros usando regressão linear. |
| [Amostra 2: Regressão - Previsão de Preço do Automóvel (Avançada)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Preveja os preços dos automóveis usando a floresta de decisão e impulsionou os regressos das árvores de decisão. Compare modelos para encontrar o melhor algoritmo.

## <a name="classification-samples"></a>Amostras de classificação

Saiba mais sobre as amostras de classificação incorporadas. Pode saber mais sobre as amostras sem ligações de documentação, abrindo as amostras e visualizando os comentários do módulo.

| Título de exemplo | Descrição | 
| --- | --- |
| [Amostra 3: Classificação binária com seleção de características - Previsão de Rendimento](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Preveja o rendimento tão alto ou baixo, usando uma árvore de decisão impulsionada por duas classes. Use a correlação pearson para selecionar funcionalidades.
| [Amostra 4: Classificação binária com script python personalizado - Previsão de Risco de Crédito](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Classifique os pedidos de crédito como de alto ou baixo risco. Utilize o módulo execute python script para pesar os seus dados.
| [Amostra 5: Classificação Binária - Previsão da Relação com o Cliente](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Preveja o rebuliço do cliente usando árvores de decisão impulsionadas por duas classes. Utilize o SMOTE para recolher dados tendenciosos.
| [Amostra 7: Classificação de Texto - Wikipedia SP 500 Dataset](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Classifique os tipos de empresas de artigos da Wikipédia com regressão logística multiclasse. |
| Amostra 12: Classificação multiclasse - Reconhecimento de cartas | Crie um conjunto de classificadores binários para classificar as letras escritas. |

## <a name="recommender-samples"></a>Amostras de recomendação

Saiba mais sobre as amostras de recomendação incorporadas. Pode saber mais sobre as amostras sem ligações de documentação, abrindo as amostras e visualizando os comentários do módulo.

| Título de exemplo | Descrição | 
| --- | --- |
| Amostra 10: Recomendação - Tweets de classificação de filmes | Construa um motor de recomendação de filme a partir de títulos de filme e classificação. |

## <a name="utility-samples"></a>Amostras de utilidade

Saiba mais sobre as amostras que demonstram utilitários e funcionalidades de aprendizagem automática. Pode saber mais sobre as amostras sem ligações de documentação, abrindo as amostras e visualizando os comentários do módulo.

| Título de exemplo | Descrição | 
| --- | --- |
| [Amostra 6: Utilize script R personalizado - Previsão de atraso de voo](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Amostra 8: Validação cruzada para classificação binária - Previsão do Rendimento de Adultos | Utilize validação cruzada para construir um classificador binário para o rendimento adulto.
| Amostra 9: Importância da característica permutação | Use a importância da funcionalidade de permutação para calcular as pontuações de importância para o conjunto de dados do teste. 
| Amostra 11: Parâmetros de sintonia para classificação binária - Previsão do Rendimento adulto | Utilize hiperparâmetros do Modelo tune para encontrar hiperparâmetros ideais para construir um classificador binário. |

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba como construir e implementar modelos de machine learning com [tutorial: Prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md)
