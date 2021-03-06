---
title: Novidades no Reconhecedor de Formato?
titleSuffix: Azure Cognitive Services
description: Compreenda as últimas alterações à API do Reconhecimento de Formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: pafarley
ms.openlocfilehash: 7f20244906581dd2869bbc7fcd997d5245540eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155176"
---
# <a name="whats-new-in-form-recognizer"></a>Novidades no Reconhecedor de Formato?

O serviço 'Reconhecimento de Formulários' é atualizado de forma contínua. Utilize este artigo para se manter atualizado com melhorias de funcionalidades, correções e atualizações de documentação.

> [!NOTE]
> Os quickstarts e guias para reconhecimento de formulários usam sempre a versão mais recente da API, salvo especificação.

## <a name="march-2020"></a>março de 2020 

### <a name="extraction-enhancements"></a>Melhorias de extração

Esta versão inclui melhorias de extração e melhorias de precisão, especificamente, a capacidade de rotular e extrair múltiplos pares de chaves/valor na mesma linha de texto. 
 
### <a name="form-recognizer-sample-labeling-tool-is-now-open-source"></a>Ferramenta de rotulagem de amostra de reconhecimento de formulário seleção já é de código aberto

A ferramenta de rotulagem de amostra sinuosa está agora disponível como um projeto de código aberto. Pode integrá-lo dentro das suas soluções e fazer alterações específicas para o cliente para atender às suas necessidades.

Para obter mais informações sobre a ferramenta de rotulagem de amostras do reconhecimento de formulários, reveja a documentação disponível no [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="labeling-value-types"></a>Tipos de valor de rotulagem

Os tipos de valor estão agora disponíveis para utilização com a ferramenta de rotulagem de amostra sinuosa. Estes tipos de valor são atualmente suportados: 

* Cadeia
* Número 
* Número inteiro
* Date 
* Hora

Esta imagem mostra como é a seleção do tipo de valor dentro da ferramenta de rotulagem de amostra sinuosa:

> [!div class="mx-imgBorder"]
> ![Seleção de tipos de valor com ferramenta de rotulagem de amostra](./media/whats-new/formre-value-type.png)

A tabela extraída está disponível `pageResults`na saída JSON em .

### <a name="table-visualization"></a>Visualização de mesa 

A Ferramenta de Rotulagem do Reconhecimento de Formulários apresenta agora tabelas que foram reconhecidas no documento. Isto permite-lhe visualizar as tabelas que foram reconhecidas e extraídas do documento, antes da rotulagem e análise com a Ferramenta de Rotulagem de Amostras do Reconhecimento de Formulários. Esta função pode ser realternada utilizando a opção layers. 

Este é um exemplo de como as tabelas são reconhecidas e extraídas:

> [!div class="mx-imgBorder"]
> ![Visualização de tabelas utilizando a ferramenta de rotulagem da amostra](./media/whats-new/formre-table-viz.png)

> [!IMPORTANT]
> As mesas de rotulagem não são suportadas. Se as tabelas não forem reconhecidas e extratadas automaticamente, só pode rotulá-las como pares chave/valor. Ao rotular as tabelas como par de chaves/valor, rotule cada célula como um valor.

### <a name="tls-12-enforcement"></a>Imposição de TLS 1.2

* TLS 1.2 é agora aplicado para todos os pedidos http para este serviço. Para mais informações, consulte a [segurança dos Serviços Cognitivos Azure.](../cognitive-services-security.md)

## <a name="january-2020"></a>Janeiro de 2020

Esta versão introduz o Reconhecimento de Formulário 2.0 (pré-visualização). Nas secções abaixo, encontrará mais informações sobre novas funcionalidades, melhorias e alterações. 

### <a name="new-features"></a>Novas funcionalidades

* **Modelo personalizado**
  * **Trem com rótulos** Agora pode treinar um modelo personalizado com dados manualmente rotulados. Isto resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formas ou formas complexas que contenham valores sem teclas.
  * **API assíncrono** Pode utilizar chamadas API asincronizadas para treinar e analisar grandes conjuntos de dados e ficheiros.
  * **Suporte de ficheiro sonérs TIFF** Agora pode treinar e extrair dados de documentos TIFF.
  * **Melhorias na precisão da extração**

* **Modelo de recibo pré-construído**
  * **Valores de gorjeta** Agora pode extrair quantidades de ponta e outros valores manuscritos.
  * **Extração de artigos de linha** Pode extrair valores de itens de linha a partir de recibos.
  * **Valores de confiança** Pode ver a confiança do modelo por cada valor extraído.
  * **Melhorias na precisão da extração**

* **Extração de layout** Agora pode utilizar a API de layout para extrair dados de texto e dados de tabela saque a partir dos seus formulários.

### <a name="custom-model-api-changes"></a>Mudanças de Modelo personalizado API

Todas as APIs para treino e utilização de modelos personalizados foram renomeadas, e alguns métodos sincronizados são agora assíncronos. As seguintes alterações são as principais:

* O processo de formação de um modelo é agora assíncrono. Inicia o treino através da chamada **API /costume/modelos.** Esta chamada devolve um ID de operação, que pode passar em **personalizado/modelo/{modelID}** para devolver os resultados do treino.
* A extração chave/valor é agora iniciada pela chamada **/custom/models/{modelID}/analyze** API. Esta chamada devolve um ID de operação, que pode passar em **personalizado/modelos/{modelID}/analyzeResults/{resultID}** para devolver os resultados de extração.
* As iDs de operação para a operação do comboio encontram-se agora no cabeçalho de **localização** das respostas HTTP, e não no cabeçalho **Operação-Localização.**

### <a name="receipt-api-changes"></a>Alterações da API de recibo

As APIs para ler recibos de venda foram renomeadas.

* A extração de dados de recibo supõe-se agora na chamada **API /preconstruído/recibo/análise.** Esta chamada devolve um ID de operação, que pode passar para **/pré-construído/recibo/análiseResultados/{resultID}** para devolver os resultados da extração.

### <a name="output-format-changes"></a>Alterações no formato de saída

As respostas da JSON para todas as chamadas API têm novos formatos. Algumas teclas e valores foram adicionados, removidos ou renomeados. Consulte os quickstarts, por exemplo, dos formatos JSON atuais.

## <a name="next-steps"></a>Passos seguintes

Complete um [início rápido](quickstarts/curl-train-extract.md) para começar com as APIs do Reconhecimento de [Formulários](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).