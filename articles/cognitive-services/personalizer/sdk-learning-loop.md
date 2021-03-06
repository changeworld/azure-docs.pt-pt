---
title: 'Quickstart: Criar e utilizar ciclo de aprendizagem com SDK - Personalizer'
description: Este quickstart mostra-lhe como criar e gerir a sua base de conhecimento usando o SDK do cliente.
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 7ebe22227b4323b2e6b1c3fc9ca31e171d1d97cd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77524874"
---
# <a name="quickstart-personalizer-client-library"></a>Quickstart: Personalizer client library

Exiba conteúdo personalizado neste quickstart com o serviço Personalizer.

Começar com a biblioteca de clientes Personalizer. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

 * Rank API - Seleciona o melhor item, a partir de itens de conteúdo, com base em informações em tempo real que fornece sobre conteúdo e contexto.
 * Reward API - Determina a pontuação de recompensa com base nas necessidades do seu negócio e, em seguida, envie-a para personalizer com esta API. Essa pontuação pode ser um único valor, como 1 para o bem, e 0 para o mal, ou um algoritmo que crias com base nas necessidades do teu negócio.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Como funciona o Personalizador](how-personalizer-works.md)

* [O que é o Personalizador?](what-is-personalizer.md)
* [Onde pode utilizar o Personalizador?](where-can-you-use-personalizer.md)
* [Resolução de problemas](troubleshooting.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
