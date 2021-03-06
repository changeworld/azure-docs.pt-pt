---
title: 'Adicionar Colunas: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Adicionar Colunas em Aprendizagem automática Azure para concatenar dois conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f2e067f76d6ed7d89a38e9b8920c407f161969a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456783"
---
# <a name="add-columns-module"></a>Adicionar módulo Colunas

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para concatenar dois conjuntos de dados. Combina todas as colunas dos dois conjuntos de dados que especifica como inputs para criar um único conjunto de dados. Se precisar de concatenar mais de dois conjuntos de dados, utilize várias instâncias de **Adicionar Colunas**.



## <a name="how-to-configure-add-columns"></a>Como configurar colunas adicionar
1. Adicione o módulo **Adicionar Colunas** ao seu pipeline.

2. Ligue os dois conjuntos de dados que pretende concatenar. Se quiser combinar mais de dois conjuntos de dados, pode acorrentar várias combinações de **Colunas Add**.

    - É possível combinar duas colunas que têm um número diferente de linhas. O conjunto de dados de saída é acolchoado com valores em falta para cada linha na coluna de origem mais pequena.

    - Não é possível escolher colunas individuais para adicionar. Todas as colunas de cada conjunto de dados são concatenadas quando utiliza **Colunas Add**. Portanto, se pretender adicionar apenas um subconjunto das colunas, utilize Colunas Select no Conjunto de Dados para criar um conjunto de dados com as colunas desejadas.

3. Submeta o oleoduto.

### <a name="results"></a>Resultados
Depois de o gasoduto ter corrido:

- Para ver as primeiras linhas do novo conjunto de dados, clique no módulo **Adicionar Colunas** e selecione Visualize. Ou Selecione o módulo e mude para o separador **Saídas** no painel direito, clique no ícone histograma nas **saídas da Porta** para visualizar o resultado.

O número de colunas no novo conjunto de dados é igual à soma das colunas de ambos os conjuntos de dados de entrada.

Se existirem duas colunas com o mesmo nome nos conjuntos de dados de entrada, é adicionado um sufixo numérico ao nome da coluna. Por exemplo, se houver dois casos de uma coluna chamada TargetOutcome, a coluna esquerda seria renomeada TargetOutcome_1 e a coluna direita seria renomeada TargetOutcome_2.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 