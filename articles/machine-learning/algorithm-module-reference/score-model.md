---
title: 'Modelo de pontuação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Score Model em Azure Machine Learning para gerar previsões usando um modelo de classificação ou regressão treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 56d8cad05a42da8de680ade487dddee9a97aab3a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364183"
---
# <a name="score-model-module"></a>Módulo do modelo de pontuação

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para gerar previsões utilizando um modelo de classificação ou regressão treinado.

## <a name="how-to-use"></a>Como utilizar

1. Adicione o módulo **'Modelo de Pontuação'** ao seu pipeline.

2. Anexe um modelo treinado e um conjunto de dados contendo novos dados de entrada. 

    Os dados devem estar num formato compatível com o tipo de modelo treinado que está a utilizar. O esquema do conjunto de dados de entrada também deve corresponder geralmente ao esquema dos dados utilizados para treinar o modelo.

3. Submeta o oleoduto.

## <a name="results"></a>Resultados

Depois de ter gerado um conjunto de pontuações usando [o Modelo de Pontuação:](./score-model.md)

+ Para gerar um conjunto de métricas utilizadas para avaliar a precisão do modelo (desempenho), pode ligar o conjunto de dados pontuado ao [Modelo de Avaliação,](./evaluate-model.md) 
+ Clique no módulo e **selecione Visualize** para ver uma amostra dos resultados.
<!-- + To Save the results to a dataset. -->

A pontuação, ou valor previsto, pode estar em muitos formatos diferentes, dependendo do modelo e dos seus dados de entrada:

- Para os modelos de classificação, [o Score Model](./score-model.md) produz um valor previsto para a classe, bem como a probabilidade do valor previsto.
- Para os modelos de regressão, [o Score Model](./score-model.md) gera apenas o valor numérico previsto.


## <a name="publish-scores-as-a-web-service"></a>Publique pontuações como um serviço web

Um uso comum de pontuação é devolver a saída como parte de um serviço web preditivo. Para mais informações, consulte [este tutorial](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) sobre como implementar um ponto final em tempo real baseado num oleoduto em Azure Machine Learning designer.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 