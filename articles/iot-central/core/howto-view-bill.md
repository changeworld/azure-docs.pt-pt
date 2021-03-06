---
title: Gerencie a sua conta e converta-se a partir do plano de preços gratuitos na aplicação Azure IoT Central [ Microsoft Docs
description: Como administrador, aprenda a gerir a sua conta e passe do plano de preços gratuitos para um plano de preços padrão na sua aplicação Azure IoT Central
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157527"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Gerencie a sua conta numa aplicação IoT Central

Este artigo descreve como, como administrador, pode gerir a sua conta na aplicação Azure IoT Central na secção de administração. Você vai aprender como você pode mover a sua aplicação do plano de preços gratuitopara um plano de preços padrão, e também como atualizar ou desvalorizar o seu plano de preços.

Para aceder e utilizar a secção **Administração,** deve estar na função *de Administrador* ou ter uma *função de utilizador personalizada* que lhe permita visualizar a faturação de uma aplicação Azure IoT Central. Se criar uma aplicação Azure IoT Central, é automaticamente designado para o papel de **Administrador** para essa aplicação.

## <a name="move-from-free-to-standard-pricing-plan"></a>Passar de plano de preços gratuito para padrão

- As aplicações que utilizam o plano de preços gratuitos são gratuitas durante sete dias antes de expirarem. Para evitar perder dados, pode movê-los para um plano de preços padrão a qualquer momento antes de expirarem.
- As aplicações que utilizam um plano de preços padrão são cobradas por dispositivo, com os dois primeiros dispositivos gratuitos, por aplicação.

Saiba mais sobre os preços na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Na secção de preços, pode mover a sua aplicação do livre para um plano de preços padrão.

Para completar este processo de self-service, siga estes passos:

1. Vá à página **de Preços** na secção **administração.**

    ![Estado experimental](media/howto-view-bill/freetrialbilling.png)

1. Selecione **Converter para um plano pago**.

    ![Converter julgamento](media/howto-view-bill/convert.png)

1. Selecione o Diretório Ativo Azure apropriado e, em seguida, a subscrição Azure para utilizar para a sua aplicação que utiliza um plano pago.

1. Depois de selecionar **Converte,** a sua aplicação agora usa um plano pago e começa a ser faturada.

> [!Note]
> Por predefinição, é convertido para um plano de preços *Standard 2.*

## <a name="how-to-change-your-application-pricing-plan"></a>Como alterar o seu plano de preços de aplicação

As aplicações que utilizam um plano de preços padrão são cobradas por dispositivo, com os dois primeiros dispositivos gratuitos, por aplicação.

Na secção de preços, pode atualizar ou desvalorizar o seu plano de preços Azure IoT a qualquer momento.

1. Vá à página **de Preços** na secção **administração.**

    ![Estado experimental](media/howto-view-bill/pricing.png)

1. Selecione o **Plano** e clique em **Guardar** para atualizar ou desvalorizar.

## <a name="view-your-bill"></a>Ver a sua fatura

1. Selecione o Diretório Ativo Azure apropriado e, em seguida, a subscrição Azure para utilizar para a sua aplicação que utiliza um plano pago.

1. Depois de selecionar **Converte,** a sua aplicação agora usa um plano pago e começa a ser faturada.

> [!Note]
> Por predefinição, é convertido para um plano de preços *Standard 2.*

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir a sua conta na aplicação Azure IoT Central, o próximo passo sugerido é aprender sobre a [aplicação Customize UI](howto-customize-ui.md) em Azure IoT Central.