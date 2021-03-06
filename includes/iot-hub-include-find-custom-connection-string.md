---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050383"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Para criar uma política de acesso partilhado que conceda ao **serviço a ligação** e **o registo leia** permissões e obtenha uma cadeia de ligação para esta política, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Grupos de Recursos.** Selecione o grupo de recursos onde o seu hub está localizado e, em seguida, selecione o seu hub a partir da lista de recursos.

1. No painel do lado esquerdo do seu hub, selecione políticas de **acesso partilhado**.

1. A partir do menu superior acima da lista de políticas, selecione **Adicionar**.

1. Em Adicionar uma política de **acesso partilhado,** introduza um nome descritivo para a sua política, como *serviçoAndRegistryRead*. Em **Permissões,** selecione **registo ler** e ligar o **serviço**e, em seguida, selecione **Criar**.

    ![Mostre como adicionar uma nova política de acesso partilhado](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Selecione a sua nova política a partir da lista de políticas.

1. Em **teclas de acesso partilhadas,** selecione o ícone de cópia para a **cadeia de ligação -- chave primária** e guarde o valor.

    ![Mostre como recuperar a corda de ligação](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre as políticas e permissões de acesso partilhadas do IoT Hub, consulte [o controlo de acesso e as permissões.](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)
