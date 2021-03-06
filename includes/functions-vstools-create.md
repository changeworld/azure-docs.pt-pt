---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 477f3cf270377bc1341e65ab500093e05277afa8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657548"
---
O modelo de projeto Funções Azure no Estúdio Visual cria um projeto que pode publicar numa aplicação de função no Azure. Você pode usar uma aplicação de função para agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação, escalagem e partilha de recursos.

1. A partir do menu do Estúdio Visual, selecione **File** > **New** > **Project**.

1. Em **Criar um novo projeto,** introduza *funções* na caixa de pesquisa, escolha o modelo **funções Azure** e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto,** insira um **nome de Projeto** para o seu projeto e, em seguida, selecione **Criar**. O nome da aplicação de funções deve ser válido como um espaço de nomes C#. Portanto, não use sublinhados, hífenes ou outros carateres não alfanuméricos.

1. Para criar uma nova definição de aplicação de **função Azure,** utilize os valores no quadro seguinte:

    | Definição      | Valor  | Descrição                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Funções tempo de funcionamento** | **Funções Azure <br />v2 (.NÚCLEO NET)** | Este valor cria um projeto de função que utiliza a versão 2.x runtime das Funções Azure, que suporta o Núcleo .NET. As Funções do Azure 1.x suportam o .NET Framework. Para mais informações, consulte a visão geral das [versões de execução do Funcionamento do Azure.](../articles/azure-functions/functions-versions.md)   |
    | **Modelo de função** | **Gatilho HTTP** | Este valor cria uma função desencadeada por um pedido HTTP. |
    | **Conta de Armazenamento**  | **Emulador do Armazenamento** | Como uma Função Azure requer uma conta de armazenamento, uma é atribuída ou criada quando publica o seu projeto para o Azure. Um gatilho HTTP não utiliza uma cadeia de ligação à conta De armazenamento Azure; todos os outros tipos de gatilho requerem uma cadeia de ligação de conta Azure Storage válida.  |
    | **Direitos de acesso** | **Anónimo** | A função criada pode ser acionada por qualquer cliente sem fornecer uma chave. Esta definição de autorização torna mais fácil testar a função nova. Para obter mais informações sobre chaves e autorização, consulte [as chaves de autorização](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) e http e as [encadernações webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    

    
    ![Definições do projeto Funções Azure](./media/functions-vs-tools-create/functions-project-settings.png)

    Certifique-se de definir os **direitos** de acesso ao **Anonymous**. Se escolher o nível de **função**predefinido, é necessário apresentar a chave de [função](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) nos pedidos para aceder ao ponto final da sua função.

1. Selecione **Criar** para criar o projeto de função e função de gatilho HTTP.
