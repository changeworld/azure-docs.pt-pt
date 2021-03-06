---
title: Folha de batota do algoritmo de aprendizagem automática
titleSuffix: Azure Machine Learning
description: Uma folha de batota de algoritmo de aprendizagem automática imprimível ajuda-o a escolher o algoritmo certo para o seu modelo preditivo no designer de Machine Learning Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
ms.openlocfilehash: 85fbb1c1d26f71903adab2eb96b0c1dd3bf74c33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328626"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Folha de batota de algoritmo de aprendizagem automática para designer de aprendizagem automática azure

A Folha de Batota do Algoritmo de **Aprendizagem automática Azure** ajuda-o a escolher o algoritmo certo para um modelo de análise preditiva.

O Azure Machine Learning tem uma grande biblioteca de algoritmos da ***classificação,*** ***sistemas de recomendação,*** ***agrupamento,*** ***deteção de anomalias,*** ***regressão***e famílias de análise de ***texto.*** Cada um é projetado para resolver um tipo diferente de problema de aprendizagem automática.

Para obter orientação adicional, consulte [Como selecionar algoritmos](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Download: Machine Learning Algorithm Cheat Sheet

**Descarregue a folha de batota aqui: [Machine Learning Algorithm Cheat Sheet (11x17 in.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Folha de batota do algoritmo de aprendizagem automática: Aprenda a escolher um algoritmo de aprendizagem automática.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Descarregue e imprima a Folha de Batota do Algoritmo de Aprendizagem automática em tamanho tabloide para mantê-lo à mão e obter ajuda na escolha de um algoritmo.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Como usar a folha de batota do algoritmo de aprendizagem automática

As sugestões oferecidas nesta folha de batota de algoritmo são regras aproximadas do polegar. Alguns podem ser dobrados, e outros podem ser flagrantemente violados. Esta folha de batota destina-se a sugerir um ponto de partida. Não tenha medo de fazer uma competição frente-a-frente entre vários algoritmos nos seus dados. Simplesmente não há substituto para compreender os princípios de cada algoritmo e o sistema que gerou os seus dados.

Cada algoritmo de aprendizagem automática tem o seu próprio estilo ou preconceito indutivo. Para um problema específico, vários algoritmos podem ser apropriados, e um algoritmo pode ser melhor do que outros. Mas nem sempre é possível saber de antemão qual é o melhor ajuste. Em casos como este, vários algoritmos estão listados juntos na folha de batota. Uma estratégia adequada seria experimentar um algoritmo, e se os resultados ainda não forem satisfatórios, experimente os outros. 

Para saber mais sobre os algoritmos em Azure Machine Learning, vá ao Algoritmo e à referência do [módulo.](algorithm-module-reference/module-reference.md)

## <a name="kinds-of-machine-learning"></a>Tipos de aprendizagem automática

Existem três categorias principais de aprendizagem automática: *aprendizagem supervisionada,* *aprendizagem sem supervisão*e aprendizagem de *reforço.*

### <a name="supervised-learning"></a>Aprendizagem supervisionada

Na aprendizagem supervisionada, cada ponto de dados é rotulado ou associado a uma categoria ou valor de interesse. Um exemplo de um rótulo categórico é atribuir uma imagem como um "gato" ou um "cão". Um exemplo de um rótulo de valor é o preço de venda associado a um carro usado. O objetivo da aprendizagem supervisionada é estudar muitos exemplos rotulados como estes, e depois ser capaz de fazer previsões sobre futuros pontos de dados. Por exemplo, identificar novas fotos com o animal correto ou atribuir preços de venda precisos a outros carros usados. Este é um tipo popular e útil de aprendizagem automática.

### <a name="unsupervised-learning"></a>Aprendizagem sem supervisão

Na aprendizagem não supervisionada, os pontos de dados não têm rótulos associados a eles. Em vez disso, o objetivo de um algoritmo de aprendizagem não supervisionado é organizar os dados de alguma forma ou descrever a sua estrutura. Os grupos de aprendizagem não supervisionados são dados em clusters, como o K-means faz, ou encontra diferentes formas de olhar para dados complexos para que pareça mais simples.

### <a name="reinforcement-learning"></a>Aprendizagem por reforço

Na aprendizagem do reforço, o algoritmo pode escolher uma ação em resposta a cada ponto de dados. É uma abordagem comum na robótica, onde o conjunto de leituras de sensores em um ponto do tempo é um ponto de dados, e o algoritmo deve escolher a próxima ação do robô. É também um ajuste natural para aplicações da Internet das Coisas. O algoritmo de aprendizagem também recebe um sinal de recompensa pouco tempo depois, indicando quão boa foi a decisão. Com base neste sinal, o algoritmo modifica a sua estratégia de forma a alcançar a maior recompensa. 

## <a name="next-steps"></a>Passos seguintes

* Consulte orientação adicional sobre [como selecionar algoritmos](how-to-select-algorithms.md)

* [Conheça o estúdio em Azure Machine Learning e o portal Azure.](overview-what-is-azure-ml.md)

* [Tutorial: Construa um modelo de previsão no designer de machine learning Azure.](tutorial-designer-automobile-price-train-score.md)

* [Saiba mais sobre aprendizagem profunda vs. machine learning](concept-deep-learning-vs-machine-learning.md).
