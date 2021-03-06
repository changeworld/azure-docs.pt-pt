---
title: Criar e gerir aplicações Azure IoT Central a partir do portal CSP [ Microsoft Docs
description: Como CSP, como criar uma aplicação Azure IoT Central em nome do seu cliente.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 02481d5dcbaba15c9b17a27348207d9af64f3355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982043"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Criar e gerir uma aplicação Azure IoT Central a partir do portal CSP

O programa Microsoft Cloud Solution Provider (CSP) é um programa de revendedor da Microsoft. A sua intenção é fornecer aos nossos parceiros de canal um programa único para revender todos os Serviços Online Comerciais da Microsoft. Saiba mais sobre o [programa Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

Como CSP, pode criar e gerir aplicações Microsoft Azure IoT Central em nome dos seus clientes através do [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). Quando as aplicações Azure IoT Central são criadas em nome dos clientes por CSPs, tal como acontece com outros serviços csp geridos pelo Azure, os CSPs gerem a faturação para os clientes. Uma taxa para a Azure IoT Central aparecerá na sua conta total no Microsoft Partner Center.

Para começar, inicie sessão na sua conta no Portal do Parceiro da Microsoft e selecione um cliente para quem pretende criar uma aplicação Azure IoT Central. Navegue para gestão de serviços para o cliente da navegação à esquerda.

![Microsoft Partner Center, visão do cliente](media/howto-create-and-manage-applications-csp/image1.png)

A Azure IoT Central está listada como um serviço disponível para administrar. Selecione o link Azure IoT Central na página para criar novas aplicações ou gerir aplicações existentes para este cliente.

![Azure IoT Central disponível para gerir](media/howto-create-and-manage-applications-csp/image2.png)

Aterre na página do Gestor Central de Aplicações Do Azure IoT. O Azure IoT Central mantém o contexto de que veio do Microsoft Partner Center e que veio gerir esse cliente em particular. Veja isto reconhecido no cabeçalho da página do Gestor de Aplicações. A partir daqui, pode navegar para uma aplicação existente que criou anteriormente para este cliente gerir ou criar uma nova aplicação para o cliente.

![Criar Gestor para CSPs](media/howto-create-and-manage-applications-csp/image3.png)

Para criar uma aplicação Azure IoT Central, **selecione Build** no menu esquerdo. Escolha um dos modelos da indústria ou escolha a **aplicação Custom** para criar uma aplicação do zero. Isto irá carregar a página de Criação de Aplicações. Deve completar todos os campos desta página e, em seguida, escolher **Criar**. Encontra mais informações sobre cada um dos campos abaixo.

![Criar página de aplicação para CSPs](media/howto-create-and-manage-applications-csp/image4.png)

![Criar página de aplicação para CSPs](media/howto-create-and-manage-applications-csp/image4-1.png)

![Criar página de aplicação para Informações de Faturação de CSPs](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Plano de preços

Só é possível criar aplicações que utilizem um plano de preços padrão como CSP. Para mostrar o Azure IoT Central ao seu cliente, pode criar uma aplicação que utilize o plano de preços gratuitos separadamente. Saiba mais sobre os planos de preços gratuitos e standard na página de [preços Do Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

Só é possível criar aplicações que utilizem um plano de preços padrão como CSP. Para mostrar o Azure IoT Central ao seu cliente, pode criar uma aplicação que utilize o plano de preços gratuitos separadamente. Saiba mais sobre os planos de preços gratuitos e standard na página de [preços Do Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

## <a name="application-name"></a>Nome da aplicação

O nome da sua aplicação é apresentado na página do Gestor de **Aplicações** e em cada aplicação Azure IoT Central. Pode escolher qualquer nome para a sua aplicação Azure IoT Central. Escolha um nome que faça sentido para si e para os outros da sua organização.

## <a name="application-url"></a>URL da Aplicação

O URL da aplicação é o link para a sua aplicação. Pode guardar um marcador no seu navegador ou partilhá-lo com outros.

Quando introduz o nome da sua aplicação, o URL da sua aplicação é autogerado. Se preferir, pode escolher um URL diferente para a sua aplicação. Cada URL Central Azure IoT deve ser único dentro da Central Azure IoT. Verá uma mensagem de erro se o URL que escolher já tiver sido tomado.

## <a name="directory"></a>Diretório

Uma vez que a Azure IoT Central tem o contexto de que veio gerir o cliente que selecionou no Portal do Parceiro microsoft, vê apenas o inquilino do Azure Ative Directory para esse cliente na área do Diretório. 

Um inquilino do Azure Ative Directory contém identidades de utilizador, credenciais e outras informações organizacionais. Várias subscrições azure podem ser associadas a um único inquilino azure Ative Directory.

Para saber mais, consulte [o Azure Ative Directory.](https://docs.microsoft.com/azure/active-directory/)

## <a name="azure-subscription"></a>Subscrição do Azure

Uma subscrição do Azure permite-lhe criar instâncias de serviços do Azure. A Azure IoT Central encontra automaticamente todas as Assinaturas Azure do cliente a que tem acesso, e exibe-as numa queda na página **Create Application.** Escolha uma subscrição Azure para criar uma nova Aplicação Central Azure IoT.

Se não tiver uma subscrição Azure, pode criar uma no Microsoft Partner Center. Depois de criar a subscrição, regresse à página **Create Application** (Criar Aplicação). A subscrição nova aparece no menu pendente **Azure Subscription** (Subscrição do Azure).

Para saber mais, consulte [as subscrições do Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)

## <a name="location"></a>Localização

**Localização** é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar a aplicação. Normalmente, deve escolher o local fisicamente mais próximo dos seus dispositivos para obter um desempenho ideal. Atualmente, você pode criar uma aplicação IoT Central nas geografias **Da Austrália**, **Ásia-Pacífico,** **Europa,** **Estados Unidos,** **Reino Unido**e **Japão.** Uma vez que você escolhe um local, você não pode mais tarde mover a sua aplicação para um local diferente.

## <a name="application-template"></a>Modelo de aplicação

Escolha o modelo de aplicação que pretende utilizar para a sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar uma aplicação Azure IoT Central como CSP, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)
