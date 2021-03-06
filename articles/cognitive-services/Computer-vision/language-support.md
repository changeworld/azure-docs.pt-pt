---
title: Suporte linguístico - Visão Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo fornece uma lista de línguas naturais suportadas pelas funcionalidades da Visão Computacional; OCR, Reconhecer Texto e Ler.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220140"
---
# <a name="language-support-for-computer-vision"></a>Suporte linguístico para visão computacional

Algumas funcionalidades da Visão Computacional suportam múltiplas línguas; quaisquer características não mencionadas aqui apenas suportam inglês.

## <a name="text-recognition"></a>Reconhecimento de texto

A Visão Computacional pode reconhecer texto em muitas línguas. Especificamente, a [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API suporta uma variedade de idiomas, enquanto a [API read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) e [recognise Text](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API apenas suporta o inglês. Consulte [reconhecer texto impresso e manuscrito](concept-recognizing-text.md) para obter mais informações sobre esta funcionalidade e as vantagens de cada API.

O OCR deteta automaticamente a linguagem do material de entrada, pelo que não é necessário especificar um código de idioma na chamada API. No entanto, os códigos linguísticos são sempre devolvidos como o valor do `"language"` nó na resposta JSON.

|Idioma| Código do idioma | OCR API |
|:-----|:----:|:-----:|
|Árabe | `ar`|✔ |
|Chinês (Simplificado) | `zh-Hans`|✔ |
|Chinês (Tradicional) | `zh-Hant`|✔ |
|Checo | `cs` |✔ |
|Dinamarquês | `da` |✔ |
|Neerlandês | `nl` |✔ |
|Inglês | `en` |✔ |
|Finlandês | `fi` |✔ |
|Francês | `fr` |✔ |
|Alemão | `de` |✔ |
|Grego | `el` |✔ |
|Húngaro | `hu` |✔ |
|Italiano | `it` |✔ |
|Japonês | `ja` |✔ |
|Coreano | `ko` |✔ |
|Norueguês | `nb` |✔ |
|Polaco | `pl` |✔ |
|Português | `pt` |✔ |
|Romeno | `ro` |✔ |
|Russo | `ru` |✔ |
|Sérvio (Cirílico) | `sr-Cyrl` |✔ |
|Sérvio (Latim) | `sr-Latn` |✔ |
|Eslovaco | `sk` |✔ |
|Espanhol | `es` |✔ |
|Sueco | `sw` |✔ |
|Turco | `tr` |✔ |

## <a name="image-analysis"></a>Análise de imagem

Algumas ações da [Análise -](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) APi de imagem podem devolver `language` resultados em outras línguas, especificadas com o parâmetro de consulta. Outras ações devolvem resultados em inglês, independentemente da língua especificada, e outras lançam uma exceção para línguas não apoiadas. As ações são `visualFeatures` especificadas com os parâmetros e `details` consultas; consulte a [visão geral](home.md) para uma lista de todas as ações que pode fazer com a análise de imagem.

|Idioma | Código do idioma | Categorias | Etiquetas | Descrição | Adulto | Marcas | Cor | Rostos | Tipo de imagem | Objetos | Celebridades | Pontos de referência |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinês | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Inglês | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonês | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Português | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espanhol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Passos seguintes

Começar a usar as funcionalidades de Visão computacional mencionadas neste guia.

* [Analisar uma imagem local (REST)](./quickstarts/csharp-analyze.md)
* [Extrair texto impresso (REST)](./quickstarts/csharp-print-text.md)