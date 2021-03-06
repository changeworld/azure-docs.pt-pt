---
title: incluir ficheiro
description: incluir ficheiro
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67509149"
---
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. **Selecione todos os serviços** no menu esquerdo e, em seguida, selecione Centros de **Notificação** na secção **Mobile.** Selecione o ícone da estrela ao lado do nome de serviço para adicionar o serviço à secção **FAVORITES** no menu esquerdo. Depois de adicionar Centros de **Notificação** aos **FAVORITOS,** selecione-o no menu esquerdo.

      ![Portal Azure - selecione Centros de Notificação](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Na página Centros de **Notificação,** selecione **Adicionar** na barra de ferramentas.

      ![Centros de Notificação - Adicione o botão de barra de ferramentas](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Na página Centro de **Notificação,** faça os seguintes passos:

    1. Introduza um nome no Centro de **Notificação**.  

    1. Introduza um nome no **Create a new namespace**. Um espaço de nome contém um ou mais centros.

    1. Selecione um valor a partir da caixa de lista seletiva de **localização.** Este valor especifica a localização em que pretende criar o centro.

    1. Selecione um grupo de recursos existente no **Grupo de Recursos,** ou crie um nome para um novo grupo de recursos.

    1. Selecione **Criar**.

        ![Portal do Azure – definir as propriedades do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Selecione **Notificações** (o ícone do sino) e, em seguida, selecione **Ir para o recurso**. Também pode atualizar a lista na página Centros de **Notificação** e selecionar o seu hub.

      ![Portal do Azure - notificações -> Ir para o recurso](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Selecione **Políticas de Acesso** na lista. Note que as duas cordas de ligação estão disponíveis para si. Precisará deles mais tarde para lidar com notificações push.

      >[!IMPORTANT]
      >*Não* utilize a política **DefaultFullSharedAccessSignature** na sua aplicação. Isto é para ser usado apenas na sua parte de trás.
      >

      ![Portal do Azure – cadeias de ligação do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
