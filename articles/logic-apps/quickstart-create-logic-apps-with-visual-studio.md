---
title: Automatizar fluxos de trabalho de tarefas em Estúdio Visual
description: Criar, programar e executar fluxos de trabalho recorrentes para integração empresarial utilizando aplicações da Lógica Azure e Estúdio Visual
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.openlocfilehash: 693b2fd8ac7440b67f53de0aedb9a8268a90de76
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241655"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Quickstart: Criar tarefas automatizadas, processos e fluxos de trabalho com aplicações lógicas azure - Estúdio Visual

Com o [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o Visual Studio, pode criar fluxos de trabalho para automatizarem tarefas e processos que integram aplicações, dados, sistemas e serviços nas empresas e organizações. Este quickstart mostra como você pode projetar e construir estes fluxos de trabalho, criando aplicações lógicas no Visual Studio e implementando essas aplicações para o Azure. Apesar de poder executar estas tarefas no portal Azure, o Visual Studio permite-lhe adicionar as suas aplicações lógicas ao controlo de fontes, publicar diferentes versões e criar modelos de Gestor de Recursos Azure para diferentes ambientes de implementação.

Se é novo em Apps Lógicas Azure e apenas quer os conceitos básicos, experimente o [quickstart para criar uma aplicação lógica no portal Azure.](../logic-apps/quickstart-create-first-logic-app-workflow.md) O Logic App Designer funciona da mesma forma tanto no portal Azure como no Visual Studio.

Neste arranque rápido, cria-se a mesma aplicação lógica com o Visual Studio que o portal Azure quickstart. Esta aplicação lógica monitoriza o feed RSS de um website e envia e-mail para cada novo item nesse feed. A sua aplicação lógica acabada parece com este fluxo de trabalho de alto nível:

![Aplicação lógica concluída](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Transfira e instale estas ferramentas, se ainda não as tiver:

  * [Estúdio Visual 2019, 2017 ou 2015 - Edição comunitária ou maior.](https://aka.ms/download-visual-studio) Este quickstart usa visual studio community 2017.

    > [!IMPORTANT]
    > Quando instalar o Visual Studio 2019 ou 2017, certifique-se de que seleciona a carga de trabalho de **desenvolvimento do Azure.**

  * [Microsoft Azure SDK para .NET (2.9.1 ou posterior)](https://azure.microsoft.com/downloads/). Saiba mais sobre o [SDK do Azure para .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * As mais recentes ferramentas de aplicações da Azure Logic apps para a extensão do Estúdio Visual para a versão que deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Estúdio Visual 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Pode transferir e instalar as Ferramentas do Azure Logic Apps diretamente a partir do Visual Studio Marketplace ou saiba [como instalar esta extensão a partir do Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Certifique-se de que reinicia o Visual Studio depois de concluir a instalação.

* Acesso à Web durante a utilização do Estruturador da Aplicação Lógica incorporado

  O designer precisa de uma ligação à Internet para criar recursos no Azure e ler propriedades e dados de conectores na sua aplicação lógica. Por exemplo, para ligações Dynamics CRM Online, o designer verifica a sua instância de CRM por padrão e propriedades personalizadas.

* Uma conta de e-mail suportada pelo Logic Apps, como o Outlook do Office 365, o Outlook.com ou o Gmail. Para outros fornecedores, reveja a [lista de conectores aqui](https://docs.microsoft.com/connectors/). Este exemplo utiliza o Office 365 Outlook. Se utilizar outro fornecedor, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Criar projeto do Grupo de Recursos do Azure

Para começar, crie um [Projeto do Grupo de Recursos do Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Saiba mais sobre [recursos e grupos de recursos do Azure](../azure-resource-manager/management/overview.md).

1. Inicie o Visual Studio. Inscreva-se na sua conta Azure.

1. No menu **Ficheiro**, selecione **Novo** > **Projeto**. (Teclado: CTRL + Turno + N)

   ![No menu "Ficheiro", selecione "Novo" > "Projeto"](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. Em **Instalado**, selecione **Visual C#** ou **Visual Basic**. Selecione **Cloud** > **Azure Resource Group**. Atribua um nome ao projeto, por exemplo:

   ![Criar projeto do Grupo de Recursos do Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Os nomes de grupos de`.`recursos podem`_`conter apenas`-`letras, números,`(`períodos , sublinha ( ), hífenes ( ) e parênteses ( ), `)`mas não podem *terminar* com períodos (`.`).
   >
   > Se **Cloud** cloud ou **Azure Resource Group** não aparecerem, certifique-se de que instala o Azure SDK para Estúdio Visual.

   Se estiver a usar o Visual Studio 2019, siga estes passos:

   1. Na **Create a new project** box, selecione o projeto do Grupo de **Recursos Azure** para Visual C# ou Visual Basic. Selecione **Next**.

   1. Forneça um nome para o grupo de recursos Azure que pretende utilizar e outras informações sobre o projeto. Selecione **Criar**.

1. A partir da lista de modelos, selecione o modelo de **Aplicação Lógica.** Selecione **OK**.

   ![Selecionar o modelo Aplicação Lógica](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Depois de o Visual Studio criar o projeto, o Explorador de Soluções é aberto e mostra a sua solução. Na sua solução, o ficheiro **LogicApp.json** não só armazena a definição de aplicação lógica, como também é um modelo de Gestor de Recursos Azure que pode utilizar para implementação.

   ![O Explorador de Soluções mostra a nova solução de aplicação lógica e o ficheiro de implementação](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Criar uma aplicação lógica em branco

Quando tiver o seu projeto do Grupo de Recursos Azure, crie a sua aplicação lógica com o modelo **de Aplicação Lógica Em Branco.**

1. No Solution Explorer, abra o menu de atalho do ficheiro **LogicApp.json.** Selecione **Abrir com o Estruturador da Aplicação Lógica**. (Teclado: CTRL + L)

   ![Abrir o ficheiro .json da aplicação lógica com o Estruturador da Aplicação Lógica](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

   O Visual Studio pede-lhe a sua subscrição Azure e um grupo de recursos Azure para criar e implementar recursos para a sua aplicação lógica e conexões.

1. Para **subscrição,** selecione a sua subscrição Azure. Para **o grupo Recursos,** selecione **Create New** para criar outro grupo de recursos Azure.

   ![Selecionar a subscrição do Azure, o grupo de recursos e a localização dos recursos](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Definição | Valor de exemplo | Descrição |
   | ------- | ------------- | ----------- |
   | Conta de utilizador | Fabrikam <br> sophia-owen@fabrikam.com | A conta que usou quando inscreveu-se no Visual Studio |
   | **Assinatura** | "Pay As You Go" <br> (sophia-owen@fabrikam.com) | O nome da sua subscrição do Azure e a conta associada |
   | **Grupo de Recursos** | MyLogicApp-RG <br> (E.U.A. Oeste) | O grupo de recursos Azure e localização para armazenar e implementar os recursos da sua aplicação lógica |
   | **Localização** | **O mesmo que o Grupo de Recursos** | O tipo de localização e a localização específica para a implementação da sua aplicação lógica. O tipo de localização é ou uma região azure ou um ambiente de serviço de integração existente [(ISE)](connect-virtual-network-vnet-isolated-environment.md). <p>Para este arranque rápido, mantenha o tipo de localização definido para **região** e a localização definida para **o Mesmo grupo de recursos**. <p>**Nota:** Depois de criar o seu projeto de grupo de recursos, pode alterar o tipo de [localização e a localização,](manage-logic-apps-with-visual-studio.md#change-location)mas diferente tipo de localização afeta a sua aplicação lógica de várias maneiras. |
   ||||

1. O Logic Apps Designer abre uma página que mostra um vídeo de introdução e gatilhos comumente usados. Percorra o vídeo e despolete-se para **Os Modelos**e selecione **A aplicação de lógica em branco**.

   ![Selecionar "Aplicação Lógica em Branco"](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Criar fluxo de trabalho da aplicação lógica

Em seguida, adicione um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) RSS que dispara quando um novo item de alimentação aparece. Cada aplicação lógica começa com um gatilho, que dispara quando critérios específicos são cumpridos. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que executa o fluxo de trabalho.

1. No Logic App Designer, sob a caixa de pesquisa, selecione **All**. Na caixa de pesquisa, insira "rss". A partir da lista de gatilhos, selecione este gatilho: **Quando um item de alimentação for publicado**

   ![Criar a sua aplicação lógica com a adição de um acionador e ações](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Depois de o gatilho aparecer no designer, termine de construir a app lógica seguindo os passos de fluxo de trabalho no [portal Azure quickstart,](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)e depois volte a este artigo. Quando tiver terminado, a aplicação lógica terá o aspeto deste exemplo:

   ![Aplicação lógica concluída](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Guarde a sua solução De Estúdio Visual. (Teclado: Ctrl+S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Implementar a aplicação lógica no Azure

Antes de poder executar e testar a sua aplicação lógica, implemente a aplicação para o Azure do Visual Studio.

1. No Solution Explorer, no menu de atalho do seu projeto, selecione **Implementar** > **Novo**. Se tal lhe for pedido, inicie sessão com a sua conta do Azure.

   ![Criar a implementação da aplicação lógica](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Para esta implementação, mantenha a subscrição padrão do Azure, grupo de recursos e outras definições. Selecione **Implementar**.

   ![Implementar a aplicação lógica no grupo de recursos do Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Se aparecer a caixa **de parâmetros de edição,** forneça um nome de recurso para a sua aplicação lógica. Guarde as suas definições.

   ![Fornecer nome da implementação para a aplicação lógica](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Quando a implementação é iniciada, o estado de implementação da sua aplicação é apresentado na janela **Saída** do Visual Studio. Se o estado não for apresentado, abra a lista **Mostrar saída de** e selecione o seu grupo de recursos do Azure.

   ![Saída do estado de implementação](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Se os seus conectores selecionados necessitarem de entrada de si, uma janela PowerShell abre-se em segundo plano e solicita quaisquer senhas ou chaves secretas necessárias. Após introduzir estas informações, a implementação continua.

   ![Janela PowerShell](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Após a implementação termina, a sua aplicação lógica está ao vivo no portal Azure e funciona na sua programação especificada (a cada minuto). Se o gatilho encontrar novos itens de alimentação, o gatilho dispara, o que cria uma instância de fluxo de trabalho que executa as ações da sua aplicação lógica. A sua aplicação lógica envia e-mail para cada novo item. Caso contrário, se o gatilho não encontrar novos itens, o gatilho não dispara e "salta" instantaneamente o fluxo de trabalho. A sua aplicação lógica aguarda até ao intervalo seguinte antes de verificar.

   Aqui estão os e-mails de amostra que esta aplicação lógica envia. Se não receber nenhum e-mail, veja a pasta de e-mail de lixo.

   ![O Outlook envia uma mensagem de e-mail por cada item de RSS novo](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Parabéns, construíste e implementaste com sucesso a tua aplicação lógica com o Visual Studio. Para gerir a sua aplicação lógica e analisar o respetivo histórico de execuções, consulte [Gerir aplicações lógicas com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Adicione nova aplicação lógica

Quando tiver um projeto existente do Grupo de Recursos Azure, pode adicionar uma nova aplicação lógica em branco a esse projeto utilizando a janela de contorno jSON.

1. No Solution Explorer, `<logic-app-name>.json` abra o ficheiro.

1. No menu **'Ver',** selecione Outros**Contornos JSON** **do Windows** > .

1. Para adicionar um recurso ao ficheiro de modelo, **selecione Adicionar Recurso** na parte superior da janela de contorno jSON. Ou na janela de contorno jSON, abra o menu de atalho de **recursos** e selecione **Adicionar Novo Recurso**.

   ![Janela de contorno JSON](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. Na caixa de diálogo **Add Resource,** na `logic app`caixa de pesquisa, encontre e selecione **Logic App**. Nomeie a sua aplicação lógica e selecione **Adicionar**.

   ![Adicionar recurso](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar com a sua aplicação lógica, elimine o grupo de recursos que contém a sua aplicação lógica e recursos relacionados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com a mesma conta utilizada para criar a aplicação lógica.

1. No menu do portal Azure, selecione **Grupos de Recursos,** ou procure e selecione **grupos de Recursos** a partir de qualquer página. Selecione o grupo de recursos da sua aplicação lógica.

1. Na página **'Visão Geral',** selecione **Eliminar o grupo de recursos**. Introduza o nome do grupo de recursos como confirmação e selecione **Eliminar**.

   !["Grupos de recursos" > "Descrição geral" > "Eliminar grupo de recursos"](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Elimine a solução do Visual Studio do seu computador local.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou, implementou e executou a sua aplicação lógica com o Visual Studio. Para aprender sobre gestão e realização de implementação avançada para aplicações lógicas com Visual Studio, consulte estes artigos:

> [!div class="nextstepaction"]
> [Gerir aplicações lógicas com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
