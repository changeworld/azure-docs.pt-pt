---
title: Executar recipiente de reconhecimento de formulário em instâncias de contentores azure
titleSuffix: Azure Cognitive Services
description: Desloque o recipiente 'Reconhecimento de Formulários' para uma instância de contentores Azure e teste-o num navegador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: acba4d9fab784181fda5728c30831c8c1838b91f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879535"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Desloque o recipiente de reconhecimento de formulário para as instâncias do contentor de Azure

Saiba como implantar o recipiente de reconhecimento de [formulários](form-recognizer-container-howto.md) de serviços cognitivos para as [instâncias](https://docs.microsoft.com/azure/container-instances/)do contentor azure . Este procedimento demonstra a criação de um recurso De Reconhecimento de Formulários Azure. Depois discutimos a imagem do contentor associado. Finalmente, destacamos a capacidade de exercer a orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão da infraestrutura para, em vez disso, concentrar-se no desenvolvimento de aplicações.

> [!IMPORTANT]
> Os recipientes 'Reconhecimento de Formulários' utilizam atualmente a versão 1.0 da API do Reconhecimento de Formulários. Pode aceder à versão mais recente da API utilizando o serviço gerido.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registo de contentores

Primeiro deve completar e submeter o formulário de pedido de acesso aos recipientes de reconhecimento de formulários de [serviços cognitivos](https://aka.ms/FormRecognizerContainerRequestAccess) para solicitar o acesso ao recipiente. Ao fazê-lo também o inscreve para a Visão Computacional. Não precisa de se inscrever separadamente para o formulário de pedido de visão computacional. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
