---
title: Básicos de síntese de fala - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar o SDK do discurso para converter texto-a-fala. Neste artigo, você aprenderá sobre a construção de objetos, formatos de saída de áudio suportados e opções de configuração personalizadapara síntese de fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: b95b55588c51c4e202d7a02c6c158b26cdcd7ac7
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986234"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Aprenda o básico da síntese da fala

Neste artigo, aprende-se padrões comuns de design para fazer síntese de texto-a-fala usando o Speech SDK. Começa-se por fazer a configuração e síntese básicas e passa-se para exemplos mais avançados para o desenvolvimento de aplicações personalizadas, incluindo:

* Obtenção de respostas como fluxos de memória
* Personalizando a taxa de amostra de saída e a taxa de bit
* Submeter pedidos de síntese utilizando sSML (linguagem de marcação de síntese da fala)
* Usando vozes neurais

> [!TIP]
> Se ainda não tiveste oportunidade de completar uma das nossas partidas rápidas, encorajamo-lo a chutar os pneus e a tentar reconhecer a fala por ti mesmo.
> * [Reconhecer voz a partir de um microfone](quickstarts/text-to-speech.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>Passos seguintes

* [Introdução à Voz Personalizada](how-to-custom-voice.md)
* [Melhorar a sintetização com SSML](speech-synthesis-markup.md)