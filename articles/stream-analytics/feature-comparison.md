---
title: Comparação de funcionalidades Azure Stream Analytics
description: Este artigo compara as funcionalidades suportadas para os trabalhos de nuvem Azure Stream Analytics e IoT Edge no portal Azure, Visual Studio e Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: cdae6a3b7319aefa9d4f19b5d613d1afb8b6804a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235314"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Comparação de funcionalidades Azure Stream Analytics

Com o Azure Stream Analytics, pode criar soluções de streaming na nuvem e no IoT Edge utilizando o [portal Azure,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)e Visual [Studio Code](quick-create-vs-code.md). As tabelas deste artigo mostram quais as funcionalidades suportadas por cada plataforma para ambos os tipos de trabalho.

## <a name="cloud-job-features"></a>Características do trabalho na nuvem


|Funcionalidade  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Plataforma transversal     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Autoria de script     |Sim         |Sim         |Sim         |
|Script Intellisense     |Destaque da sintaxe         |Destaque da sintaxe</br>Conclusão do código</br>Marcador de erro         |Destaque da sintaxe</br>Conclusão do código</br>Marcador de erro         |
|Defina todos os tipos de inputs, saídas e configurações de trabalho     |Sim         |Sim         |Sim         |
|Controlo de código fonte     |Não         |Sim         |Sim         |
|Suporte ci/CD     |Parcial         |Sim         |Sim         |
|Partilhar inputs e saídas em várias consultas     |Não         |Sim         |Sim         |
|Teste de consulta com um arquivo de amostra     |Sim         |Sim        |Sim         |
|Testes locais de dados ao vivo     |Não         |Sim       |Sim      |
|Listar empregos e visualizar entidades de emprego     |Sim         |Sim        |Sim         |
|Exportar um emprego para um projeto local     |Não         |Sim         |Sim         |
|Submeta, comece e pare os empregos     |Sim         |Sim         |Sim         |
|Ver métricas de trabalho e diagrama     |Sim         |Sim         |Abrir no Portal         |
|Ver erros de execução de emprego     |Sim         |Sim         |Não         |
|Registos de diagnósticos     |Sim         |Não         |Não         |
|Propriedades de mensagem personalizada     |Sim         |Sim         |Não       |
|Função de código personalizado C# e Deserializer|Modo de leitura|Sim|Não|
|JavaScript UDF e UDA     |Sim         |Sim         |Apenas no Windows         |
|Serviço do Machine Learning     |Sim        |Sim         |Não         |
|Machine Learning Studio     |Sim, mas a consulta não pode ser testada.        |Sim |Não         |
|Nível de compatibilidade     |1.0</br>1.1</br>1.2 (predefinido)         |1.0</br>1.1</br>1.2 (predefinido)           |1.0</br>1.1</br>1.2 (predefinido)           |
|Funções incorporadas de deteção de anomalias baseadas em ML     |Sim         |Sim         |Sim         |
|Funções GeoEspacial incorporadas     |Sim         |Sim         |Sim         |



## <a name="iot-edge-job-features"></a>Características de trabalho ioT Edge

|Funcionalidade  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Autoria de trabalho     |Sim         |Sim         |Não         |
|Controlo de código fonte     |Não         |Sim         |Não         |
|Exportar um emprego para um projeto local     |Não         |Sim         |Não         |
|Teste de consulta com um arquivo de amostra     |Sim         |Sim         |Não         |
|Partilhar inputs e saídas em várias consultas     |Não         |Sim         |Não         |
|C# UDF     |Não         |Sim         |Não         |
|Submeter postos de trabalho     |Sim         |Sim         |Não         |
|Listar empregos e visualizar entidades de emprego     |Sim         |Sim         |Não         |
|Ver métricas de trabalho e diagrama     |Sim         |Parcial         |Não         |
|Ver erros de execução de emprego     |Sim         |Parcial         |Não         |
|Suporte ci/CD     |Não         |Não         |Não         |


## <a name="next-steps"></a>Passos seguintes

* [Azure Stream Analytics no IoT Edge](stream-analytics-edge.md)
* [Tutorial: Escreva uma função c# definida pelo utilizador para o trabalho de Azure Stream Analytics IoT Edge (Pré-visualização)](stream-analytics-edge-csharp-udf.md)
* [Desenvolver trabalhos de Streaming Analytics IoT Edge usando ferramentas do Estúdio Visual](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use o Estúdio Visual para ver empregos da Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Explore o Azure Stream Analytics com o Código do Estúdio Visual (Pré-visualização)](visual-studio-code-explore-jobs.md)


