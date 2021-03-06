---
title: Conteúdo adulto, picante e sangrento - Visão Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com a deteção de conteúdo adulto em imagens utilizando o Computer Vision APi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71718520"
---
# <a name="detect-adult-content"></a>Detetar conteúdo adulto

A Computer Vision pode detetar material adulto em imagens para que os desenvolvedores possam restringir a exibição destas imagens no seu software. As bandeiras de conteúdo são aplicadas com uma pontuação entre zero e uma para que os desenvolvedores possam interpretar os resultados de acordo com as suas próprias preferências.

> [!NOTE]
> Grande parte desta funcionalidade é oferecida pelo serviço De moderador de [conteúdo do Azure.](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) Consulte esta alternativa para soluções para cenários de moderação de conteúdo mais rigorosos, tais como moderação de texto e fluxos de trabalho de revisão humana.

## <a name="content-flag-definitions"></a>Definições de bandeira de conteúdo

Dentro da classificação "adulto" estão várias categorias diferentes:

- As imagens **adultas** são definidas como aquelas que são explicitamente de natureza sexual e muitas vezes retratam nudez e atos sexuais.
- As imagens **picantes** são definidas como imagens que são sexualmente sugestivas na natureza e muitas vezes contêm conteúdo menos sexualmente explícito do que imagens marcadas como **Adultos**.
- As imagens **sangrentas** são definidas como aquelas que retratam gore.

## <a name="use-the-api"></a>Utilizar a API

Pode detetar conteúdo adulto com a [API de Imagem de Análise.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Quando adiciona o `Adult` valor do parâmetro de consulta **visualFeatures,** a API `isRacyContent`devolve `isGoryContent` &mdash;três propriedades&mdash;`isAdultContent`booleanas, e na sua resposta JSON. O método também&mdash;`adultScore`devolve `racyScore`propriedades `goreScore` &mdash;correspondentes, e que representam pontuações de confiança entre zero e uma para cada categoria respetiva.

- [Quickstart: Analise uma imagem (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Quickstart: Analise uma imagem (REST API)](./quickstarts/csharp-analyze.md)
