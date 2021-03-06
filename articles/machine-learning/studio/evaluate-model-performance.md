---
title: Avaliar o desempenho do modelo
titleSuffix: ML Studio (classic) - Azure
description: Saiba avaliar o desempenho do modelo no Azure Machine Learning Studio (clássico) e sobre as métricas disponíveis para esta tarefa.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 3c041834b9ad191817cdf1380b0a75efc7639bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218149"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Como avaliar o desempenho do modelo no Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Este artigo demonstra como avaliar o desempenho de um modelo no Azure Machine Learning Studio (clássico) e fornece uma breve explicação das métricas disponíveis para esta tarefa. São apresentados três cenários comuns de aprendizagem supervisionada: 

* regressão
* classificação binária 
* classificação multiclasse



Avaliar o desempenho de um modelo é uma das fases centrais do processo de ciência de dados. Indica o sucesso da pontuação (previsões) de um conjunto de dados por um modelo treinado. 

O Azure Machine Learning Studio (clássico) suporta a avaliação do modelo através de dois dos seus principais módulos de machine learning: [Avaliar modelo][evaluate-model] e modelo [de validação cruzada][cross-validate-model]. Estes módulos permitem-lhe ver como o seu modelo funciona em termos de uma série de métricas que são comumente usadas em machine learning e estatísticas.

## <a name="evaluation-vs-cross-validation"></a>Avaliação vs. Validação Cruzada
Avaliação e validação cruzada são formas padrão de medir o desempenho do seu modelo. Ambos geram métricas de avaliação que pode inspecionar ou comparar com as de outros modelos.

[Avaliar][evaluate-model] o Model espera um conjunto de dados pontuado como entrada (ou dois caso queira comparar o desempenho de dois modelos diferentes). Por isso, é necessário treinar o seu modelo utilizando o módulo [Train Model][train-model] e fazer previsões em algum conjunto de dados utilizando o módulo ['Modelo de Pontuação'][score-model] antes de poder avaliar os resultados. A avaliação baseia-se nos rótulos/probabilidades pontuados juntamente com as verdadeiras etiquetas, todas elas saídas pelo módulo ['Modelo de Pontuação'.][score-model]

Em alternativa, pode utilizar a validação cruzada para realizar uma série de operações de avaliação de pontuação de comboio (10 dobras) automaticamente em diferentes subconjuntos dos dados de entrada. Os dados de entrada são divididos em 10 partes, onde uma é reservada para testes, e as outras 9 para treino. Este processo é repetido 10 vezes e as métricas de avaliação são médias. Isto ajuda a determinar até que ponto um modelo se generalizaria para novos conjuntos de dados. O módulo [Cross-Validate Model][cross-validate-model] acolhe um modelo não treinado e alguns conjuntos de dados rotulados e produz os resultados de avaliação de cada uma das 10 dobras, além dos resultados médios.

Nas seguintes secções, construiremos modelos simples de regressão e classificação e avaliaremos o seu desempenho, utilizando tanto o Modelo de [Avaliação][evaluate-model] como os módulos [Cross-Validate Model.][cross-validate-model]

## <a name="evaluating-a-regression-model"></a>Avaliação de um modelo de regressão
Assuma que queremos prever o preço de um carro usando características como dimensões, potência, especificações do motor, e assim por diante. Este é um problema típico de regressão, onde a variável-alvo *(preço)* é um valor numérico contínuo. Podemos encaixar um modelo linear de regressão que, dado os valores de características de um determinado carro, pode prever o preço desse carro. Este modelo de regressão pode ser usado para marcar o mesmo conjunto de dados em que treinamos. Uma vez que tenhamos os preços dos carros previstos, podemos avaliar o desempenho do modelo olhando para o quanto as previsões se desviam dos preços reais, em média. Para ilustrar isto, utilizamos o conjunto de dados de *preços do Automóvel (Raw)* disponível na secção **De dados guardados** no Estúdio de Aprendizagem Automática (clássico).

### <a name="creating-the-experiment"></a>Criação da Experiência
Adicione os seguintes módulos ao seu espaço de trabalho no Azure Machine Learning Studio (clássico):

* Dados sobre o preço do automóvel (Cru)
* [Regressão Linear][linear-regression]
* [Modelo de comboio][train-model]
* [Modelo de pontuação][score-model]
* [Modelo de avaliação][evaluate-model]

Ligue as portas como mostrado abaixo na Figura 1 e coloque a coluna de etiquetado do módulo [Modelo de Comboio][train-model] ao *preço*.

![Avaliação de um modelo de regressão](./media/evaluate-model-performance/1.png)

Figura 1: Avaliando um Modelo de Regressão.

### <a name="inspecting-the-evaluation-results"></a>Inspeção dos Resultados da Avaliação
Depois de executar a experiência, pode clicar na porta de saída do módulo ['Avaliar Modelo'][evaluate-model] e selecionar *Visualize* para ver os resultados da avaliação. As métricas de avaliação disponíveis para os modelos de regressão são: *Erro Absoluto Médio,* *Erro Absoluto Médio Raiz,* *Erro Absoluto Relativo,* *Erro Quadrado Relativo*e O *Coeficiente de Determinação*.

O termo "erro" aqui representa a diferença entre o valor previsto e o valor real. O valor absoluto ou o quadrado desta diferença é geralmente calculado para capturar a magnitude total de erro em todos os casos, uma vez que a diferença entre o valor previsto e o verdadeiro valor pode ser negativa em alguns casos. As métricas de erro medem o desempenho preditivo de um modelo de regressão em termos do desvio médio das suas previsões dos verdadeiros valores. Valores de erro mais baixos significam que o modelo é mais preciso na elaboração de previsões. Uma métrica de erro geral de zero significa que o modelo se adequa perfeitamente aos dados.

O coeficiente de determinação, que também é conhecido como R ao quadrado, é também uma forma padrão de medir o quão bem o modelo se encaixa nos dados. Pode ser interpretado como a proporção de variação explicada pelo modelo. Uma proporção maior é melhor neste caso, onde 1 indica um ajuste perfeito.

![Métricas lineares de avaliação de regressão](./media/evaluate-model-performance/2.png)

Figura 2. Métricas lineares de avaliação de regressão.

### <a name="using-cross-validation"></a>Utilização da Validação Cruzada
Como mencionado anteriormente, pode realizar treinos, pontuações e avaliações repetidas utilizando automaticamente o módulo [Cross-Validate Model.][cross-validate-model] Tudo o que precisa neste caso é de um conjunto de dados, um modelo destreinado e um módulo [Cross-Validate Model][cross-validate-model] (ver figura abaixo). Tem de definir a coluna de etiquetas ao *preço* nas propriedades do módulo [Cross-Validate Model.][cross-validate-model]

![Validação cruzada de um modelo de regressão](./media/evaluate-model-performance/3.png)

Figura 3. Cross-Validando um Modelo de Regressão.

Depois de executar a experiência, pode inspecionar os resultados da avaliação clicando na porta de saída direita do módulo [Cross-Validate Model.][cross-validate-model] Isto proporcionará uma visão detalhada das métricas para cada iteração (dobragem), e os resultados médios de cada uma das métricas (Figura 4).

![Resultados de validação cruzada de um modelo de regressão](./media/evaluate-model-performance/4.png)

Figura 4. Resultados de validação cruzada de um modelo de regressão.

## <a name="evaluating-a-binary-classification-model"></a>Avaliação de um modelo de classificação binária
Num cenário de classificação binária, a variável-alvo tem apenas dois resultados possíveis, por exemplo: {0, 1} ou {falso, verdadeiro}, {negativo, positivo}. Assuma que lhe é dado um conjunto de dados de empregados adultos com algumas variáveis demográficas e de emprego, e que lhe é pedido que preveja o nível de rendimento, uma variável binária com os valores {"<=50 K", ">50 K"}. Ou seja, a classe negativa representa os empregados que ganham menos ou igual a 50 K por ano, e a classe positiva representa todos os outros colaboradores. Tal como no cenário de regressão, treinávamos um modelo, marcaríamos alguns dados e avaliaríamos os resultados. A principal diferença aqui é a escolha de métricas Azure Machine Learning Studio (clássico) computs e saídas. Para ilustrar o cenário de previsão do nível de rendimento, usaremos o conjunto de dados [adulto](https://archive.ics.uci.edu/ml/datasets/Adult) para criar uma experiência Studio (clássica) e avaliar o desempenho de um modelo de regressão logística de duas classes, um classificador binário comumente usado.

### <a name="creating-the-experiment"></a>Criação da Experiência
Adicione os seguintes módulos ao seu espaço de trabalho no Azure Machine Learning Studio (clássico):

* Conjunto de dados de classificação binária de rendimento do recenseamento adulto
* [Regressão Logística de Duas Classes][two-class-logistic-regression]
* [Modelo de comboio][train-model]
* [Modelo de pontuação][score-model]
* [Modelo de avaliação][evaluate-model]

Ligue as portas como mostrado abaixo na Figura 5 e coloque a coluna de etiquetas do módulo [Modelo de Comboio][train-model] para o *rendimento*.

![Avaliação de um modelo de classificação binária](./media/evaluate-model-performance/5.png)

Figura 5. Avaliando um modelo de classificação binária.

### <a name="inspecting-the-evaluation-results"></a>Inspeção dos Resultados da Avaliação
Depois de executar a experiência, pode clicar na porta de saída do módulo ['Avaliar Modelo'][evaluate-model] e selecionar *Visualize* para ver os resultados da avaliação (Figura 7). As métricas de avaliação disponíveis para os modelos de classificação binária são: *Precisão,* *Precisão,* *Recall,* *Pontuação f1*e *AUC*. Além disso, o módulo produz uma matriz de confusão mostrando o número de verdadeiros positivos, falsos negativos, falsos positivos e verdadeiros negativos, bem como *curvas ROC,* *Precisão/Recall*e *Lift.*

A precisão é simplesmente a proporção de casos corretamente classificados. É normalmente a primeira métrica que se olha quando se avalia um classificador. No entanto, quando os dados do teste são desequilibrados (onde a maioria dos casos pertencem a uma das classes), ou se está mais interessado no desempenho em qualquer uma das classes, a precisão não captura realmente a eficácia de um classificador. No cenário de classificação do nível de rendimento, assuma que está a testar alguns dados onde 99% dos casos representam pessoas que ganham menos ou igual a 50K por ano. É possível obter uma precisão de 0,99 prevendo a classe "<=50K" para todos os casos. O classificador neste caso parece estar a fazer um bom trabalho em geral, mas na realidade, não classifica nenhum dos indivíduos de alto rendimento (o 1%) corretamente.

Por essa razão, é útil calcular métricas adicionais que captem aspetos mais específicos da avaliação. Antes de entrar nos detalhes de tais métricas, é importante entender a matriz de confusão de uma avaliação de classificação binária. As etiquetas de classe no conjunto de formação podem assumir apenas dois valores possíveis, que normalmente chamamos de positivo ou negativo. Os casos positivos e negativos que um classificador prevê corretamente são chamados verdadeiros positivos (TP) e verdadeiros negativos (TN), respectivamente. Da mesma forma, as instâncias classificadas incorretamente são chamadas falsos positivos (FP) e falsos negativos (FN). A matriz de confusão é simplesmente uma tabela que mostra o número de casos que se enquadram em cada uma destas quatro categorias. O Azure Machine Learning Studio (clássico) decide automaticamente qual das duas classes do conjunto de dados é a classe positiva. Se as etiquetas de classe forem booleanas ou inteiros, então as instâncias rotuladas "verdadeiras" ou "1" são atribuídas à classe positiva. Se as etiquetas forem cordas, como com o conjunto de dados de rendimento, as etiquetas são ordenadas alfabeticamente e o primeiro nível é escolhido para ser a classe negativa enquanto o segundo nível é a classe positiva.

![Matriz de confusão de classificação binária](./media/evaluate-model-performance/6a.png)

Figura 6. Matriz de confusão de classificação binária.

Voltando ao problema da classificação dos rendimentos, gostaríamos de fazer várias perguntas de avaliação que nos ajudam a compreender o desempenho do classificador utilizado. Uma questão natural é: "Dos indivíduos que o modelo previu ganhar >50 K (TP+FP), quantos foram classificados corretamente (TP)?". Esta pergunta pode ser respondida olhando para a **precisão** do modelo, que é a proporção de positivos que são classificados corretamente: TP/(TP+FP). Outra questão comum é "De todos os trabalhadores com rendimentos >50k (TP+FN), quantos classificaram corretamente o classificador (TP)". Esta é, na verdade, a **Recolha**, ou a verdadeira taxa positiva: TP/(TP+FN) do classificador. Pode notar que há uma troca óbvia entre precisão e recordação. Por exemplo, dado um conjunto de dados relativamente equilibrado, um classificador que prevê casos maioritariamente positivos, teria uma elevada recuperação, mas uma precisão bastante baixa, uma vez que muitos dos casos negativos seriam mal classificados, resultando num grande número de falsos positivos. Para ver um enredo de como estas duas métricas variam, pode clicar na curva **PRECISION/RECALL** na página de saída do resultado de avaliação (parte superior esquerda da Figura 7).

![Resultados da avaliação da classificação binária](./media/evaluate-model-performance/7.png)

Figura 7. Resultados de avaliação de classificação binária.

Outra métrica relacionada que é frequentemente usada é a **Pontuação de F1,** que leva tanto a precisão como a recordação em consideração. É a média harmónica destas duas métricas e é calculada como tal: F1 = 2 (precisão x recordação) / (precisão + recordação). A pontuação de F1 é uma boa maneira de resumir a avaliação num único número, mas é sempre uma boa prática olhar para a precisão e recordar juntos para entender melhor como um classificador se comporta.

Além disso, pode-se inspecionar a verdadeira taxa positiva vs. a taxa falsa positiva na curva **recetora Da Característica Operacional (ROC)** e na área correspondente Sob o valor **da Curva (AUC).** Quanto mais perto esta curva for do canto superior esquerdo, melhor é o desempenho do classificador (que está a maximizar a verdadeira taxa positiva, minimizando a taxa falsamente positiva). Curvas que estão perto da diagonal do enredo, resultam de classificadores que tendem a fazer previsões que estão perto de adivinhar aleatoriamente.

### <a name="using-cross-validation"></a>Utilização da Validação Cruzada
Tal como no exemplo de regressão, podemos realizar validação cruzada para treinar, pontuar e avaliar automaticamente diferentes subconjuntos dos dados. Da mesma forma, podemos usar o módulo [Cross-Validate Model,][cross-validate-model] um modelo de regressão logística destreinado e um conjunto de dados. A coluna de etiquetas deve ser definida para *o rendimento* nas propriedades do módulo [Cross-Validate Model.][cross-validate-model] Depois de executar a experiência e clicar na porta de saída direita do módulo [Cross-Validate Model,][cross-validate-model] podemos ver os valores métricos de classificação binário para cada dobra, além do desvio médio e padrão de cada um. 

![Validação cruzada de um modelo de classificação binária](./media/evaluate-model-performance/8.png)

Figura 8. Validação cruzada de um modelo de classificação binária.

![Resultados de validação cruzada de um classificador binário](./media/evaluate-model-performance/9.png)

Figura 9. Resultados de validação cruzada de um classificador binário.

## <a name="evaluating-a-multiclass-classification-model"></a>Avaliação de um modelo de classificação multiclasse
Nesta experiência, usaremos o popular conjunto de dados [da Íris,](https://archive.ics.uci.edu/ml/datasets/Iris "Íris") que contém instâncias de três tipos diferentes (classes) da fábrica da íris. Existem quatro valores de característica (comprimento/largura sepal e comprimento/largura da pétala) para cada instância. Nas experiências anteriores, treinamos e testámos os modelos utilizando os mesmos conjuntos de dados. Aqui, usaremos o módulo [Split Data][split] para criar dois subconjuntos dos dados, treinar no primeiro, e marcar e avaliar no segundo. O conjunto de dados iris está disponível publicamente no [Repositório de Aprendizagem Automática uci,](https://archive.ics.uci.edu/ml/index.html)e pode ser descarregado usando um módulo de [dados de importação.][import-data]

### <a name="creating-the-experiment"></a>Criação da Experiência
Adicione os seguintes módulos ao seu espaço de trabalho no Azure Machine Learning Studio (clássico):

* [Dados de Importação][import-data]
* [Floresta de Decisão de Várias Classes][multiclass-decision-forest]
* [Dividir Dados][split]
* [Modelo de comboio][train-model]
* [Modelo de pontuação][score-model]
* [Modelo de avaliação][evaluate-model]

Ligue as portas como mostrado abaixo na Figura 10.

Desloque o índice da coluna de etiquetas do módulo [Modelo de Comboio][train-model] para 5. O conjunto de dados não tem linha de cabeçalho, mas sabemos que as etiquetas da classe estão na quinta coluna.

Clique no módulo [dados de importação][import-data] e coloque a propriedade de origem de *dados* para URL web *via HTTP,* e o *URL* para http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Defino a fração de instâncias a utilizar para o treino no módulo [De dados split][split] (0.7 por exemplo).

![Avaliação de um Classifier Multiclasse](./media/evaluate-model-performance/10.png)

Figura 10. Avaliação de um Classifier Multiclasse

### <a name="inspecting-the-evaluation-results"></a>Inspeção dos Resultados da Avaliação
Executar a experiência e clicar na porta de saída do Modelo de [Avaliação][evaluate-model]. Os resultados da avaliação são apresentados sob a forma de uma matriz de confusão, neste caso. A matriz mostra os casos reais vs. previstos para as três classes.

![Resultados da avaliação de classificação multiclasse](./media/evaluate-model-performance/11.png)

Figura 11. Resultados da avaliação de classificação multiclasse.

### <a name="using-cross-validation"></a>Utilização da Validação Cruzada
Como mencionado anteriormente, pode realizar treinos, pontuações e avaliações repetidas utilizando automaticamente o módulo [Cross-Validate Model.][cross-validate-model] Precisaria de um conjunto de dados, de um modelo destreinado e de um módulo [modelo de validação cruzada][cross-validate-model] (ver figura abaixo). Mais uma vez, é necessário definir a coluna de etiquetas do módulo [Modelo Cross-Validate][cross-validate-model] (índice de coluna 5 neste caso). Depois de executar a experiência e clicar na porta de saída direita do [Modelo Cross-Validate,][cross-validate-model]pode inspecionar os valores métricos para cada dobra, bem como o desvio médio e padrão. As métricas aqui apresentadas são semelhantes às discutidas no caso de classificação binária. No entanto, na classificação multiclasse, a computação dos verdadeiros positivos/negativos e falsos positivos/negativos é feita contando com base numa base por classe, uma vez que não existe uma classe global positiva ou negativa. Por exemplo, ao calcular a precisão ou a recordação da classe 'Iris-setosa', presume-se que esta é a classe positiva e todas as outras como negativas.

![Validação cruzada de um modelo de classificação multiclasse](./media/evaluate-model-performance/12.png)

Figura 12. Validação cruzada de um modelo de classificação multiclasse.

![Resultados de validação cruzada de um modelo de classificação multiclasse](./media/evaluate-model-performance/13.png)

Figura 13. Resultados de validação cruzada de um modelo de classificação multiclasse.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/
