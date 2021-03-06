---
title: Criar recurso Personalizer
description: A configuração do serviço inclui como o serviço trata recompensas, quantas vezes o serviço explora, quantas vezes o modelo é retreinado e quanto dados são armazenados.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336066"
---
# <a name="create-a-personalizer-resource"></a>Criar um recurso Personalizer

Um recurso Personalizer é a mesma coisa que um ciclo de aprendizagem personalizer. Um único recurso, ou ciclo de aprendizagem, é criado para cada domínio ou área de conteúdo que você tem. Não utilize múltiplas áreas de conteúdo no mesmo ciclo, pois isso irá confundir o ciclo de aprendizagem e fornecer previsões deficientes.

Se quiser que o Personalizer selecione o melhor conteúdo para mais do que uma área de conteúdo de uma página web, utilize um ciclo de aprendizagem diferente para cada um.


## <a name="create-a-resource-in-the-azure-portal"></a>Criar um recurso no portal Azure

Crie um recurso Personalizer para cada ciclo de feedback.

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). O link anterior leva-o à página **Criar** para o serviço Personalizer.
1. Insira o seu nome de serviço, selecione uma subscrição, localização, nível de preços e grupo de recursos.

    > [!div class="mx-imgBorder"]
    > ![Utilize o portal Azure para criar recurso Personalizer, também chamado ciclo de aprendizagem.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Selecione **Criar** para criar o recurso.

1. Depois de o seu recurso ter sido implantado, selecione o botão **'Ir a Recurso'** para ir ao seu recurso Personalizer.

1. Selecione a página **de arranque Quick** para o seu recurso e, em seguida, copie os valores para o seu ponto final e chave. Você precisa tanto do ponto final de recursos como da chave para usar as APIs de Rank e Reward.

1. Selecione a página **de Configuração** para o novo recurso para [configurar o ciclo de aprendizagem](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Criar um recurso com o Azure CLI

1. Inscreva-se no Azure CLI com o seguinte comando:

    ```azurecli-interactive
    az login
    ```

1. Crie um grupo de recursos, um agrupamento lógico para gerir todos os recursos Azure que pretende utilizar com o recurso Personalizer.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Crie um novo recurso Personalizer, ciclo de _aprendizagem,_ com o seguinte comando para um grupo de recursos existente.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Isto devolve um objeto JSON, que inclui o seu **ponto final**de recurso .

1. Utilize o seguinte comando Azure CLI para obter a sua **chave de recursos**.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Você precisa tanto do ponto final de recursos como da chave para usar as APIs de Rank e Reward.

## <a name="next-steps"></a>Passos seguintes

* [Configurar](how-to-settings.md) Loop de aprendizagem personalizador