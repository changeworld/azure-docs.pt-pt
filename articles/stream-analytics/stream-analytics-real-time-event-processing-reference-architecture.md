---
title: Processamento de eventos em tempo real usando Azure Stream Analytics
description: Este artigo descreve a arquitetura de referência para alcançar o processamento e análise de eventos em tempo real usando o Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: d219b3fcb27b23527c0a651bc8e842a9e036bfc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431489"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Arquitetura de referência: Processamento de eventos em tempo real com o Microsoft Azure Stream Analytics
A arquitetura de referência para processamento de eventos em tempo real com o Azure Stream Analytics destina-se a fornecer uma planta genérica para implementar uma plataforma em tempo real como uma solução de processamento de fluxo de serviço (PaaS) com o Microsoft Azure.

## <a name="summary"></a>Resumo
Tradicionalmente, as soluções de análise têm sido baseadas em capacidades como ETL (extrato, transformação, carga) e armazenamento de dados, onde os dados são armazenados antes da análise. A alteração dos requisitos, incluindo dados que chegam mais rapidamente, estão a levar este modelo existente até ao limite. A capacidade de analisar dados dentro de fluxos em movimento antes do armazenamento é uma solução, e embora não seja uma nova capacidade, a abordagem não foi amplamente adotada em todas as verticais da indústria. 

O Microsoft Azure fornece um extenso catálogo de tecnologias de análise que são capazes de suportar uma série de diferentes cenários e requisitos de soluções. A seleção dos serviços azure a implementar para uma solução de ponta a ponta pode ser um desafio dada a amplitude das ofertas. Este trabalho foi concebido para descrever as capacidades e interoperações dos vários serviços Azure que suportam uma solução de streaming de eventos. Também explica alguns dos cenários em que os clientes podem beneficiar deste tipo de abordagem.

## <a name="contents"></a>Conteúdos
* Resumo Executivo
* Introdução à Análise em Tempo Real
* Proposta de valor dos dados em tempo real em Azure
* Cenários comuns para análise em tempo real
* Arquitetura e Componentes
  * Origens de Dados
  * Camada de integração de dados
  * Camada de Análise em tempo real
  * Camada de armazenamento de dados
  * Apresentação / Camada de Consumo
* Conclusão

**Autor:** Charles Feddersen, Solution Architect, Data Insights Center of Excellence, Microsoft Corporation

**Publicado:** janeiro de 2015

**Revisão:** 1.0

**Download:** [Processamento de eventos em tempo real com o Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Obter ajuda
Para mais assistência, experimente o [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

