---
title: 'Quickstart: Criar um oleoduto CI/CD para java - Projetos Azure DevOps'
description: A DevOps Projects facilita o início do Azure. Ajuda-o a utilizar o seu próprio código e o repositório GitHub para lançar uma aplicação num serviço do Azure à sua escolha com alguns passos rápidos.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
monikerRange: vsts
ms.openlocfilehash: 1a276770887bee39972ba8630fb13f52bcbe802d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77049947"
---
# <a name="quickstart-set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-projects"></a>Quickstart: Criar um pipeline CI/CD para uma app Java com projetos Azure DevOps

Neste arranque rápido, utiliza a experiência simplificada de Projetos Azure DevOps para criar um pipeline de integração contínua (CI) e entrega contínua (CD) para a sua aplicação Java em Pipelines Azure. Pode utilizar projetos Azure DevOps para configurar tudo o que precisa para desenvolver, implementar e monitorizar a sua aplicação. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- Uma conta [azure DevOps](https://azure.microsoft.com/services/devops/) e organização.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

A DevOps Projects cria um oleoduto CI/CD em Pipelines Azure. Você pode criar uma nova organização Azure DevOps ou usar uma organização existente. A DevOps Projects também cria recursos Azure na subscrição Azure à sua escolha.

1. Inscreva-se no [portal Azure](https://portal.azure.com), e no painel esquerdo, selecione **Criar um recurso**. 

   ![Criar um recurso Azure no portal Azure](_img/azure-devops-project-java/continuous-delivery-configuration-full-browser.png)

1. Procure e selecione **Projetos DevOps,** e, em seguida, **selecione Criar**.

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e serviço do Azure

1. Selecione o exemplo de aplicação Java.  
Os exemplos de Java incluem várias opções de arquiteturas de aplicações.

1. A arquitetura de exemplo predefinida é Spring. Deixe a definição predefinida e, em seguida, selecione **Seguinte**.  A Aplicação Web Para Contentores é o destino de implementação predefinido. O quadro de aplicação, que escolheu anteriormente, dita o tipo de alvo de implementação de serviço seleções Azure aqui disponível. 

2. Deixe o serviço predefinido e, em seguida, selecione **Seguinte**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configure Azure DevOps e uma subscrição Azure 

1. Crie uma organização nova do Azure DevOps ou utilize uma organização existente. 
   
   1. Escolha um nome para o projeto. 
   
   1. Selecione a sua subscrição e localização Azure, escolha um nome para a sua aplicação e, em seguida, selecione **Done**.  
   Após alguns minutos, o painel de instrumentos DevOps Projects é apresentado no portal Azure. Uma aplicação de amostra é configurada num repositório na sua organização Azure DevOps, uma construção é executada, e a sua aplicação é implantada para o Azure. Este dashboard proporciona visibilidade no seu repositório de código, no pipeline CI/CD e na sua aplicação em Azure.
   
2. **Selecione Browse** para visualizar a sua aplicação de execução.
   
   ![Ver painel de aplicações no portal Azure](_img/azure-devops-project-java/azure-devops-application-dashboard.png) 

Os Projetos DevOps configuraram automaticamente um gatilho de construção e libertação de CI.  Agora, está pronto para colaborar com uma equipa na sua aplicação Java com um processo de CI/CD que implementa automaticamente o seu trabalho mais recente no seu site.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

A DevOps Projects cria um repositório Git em Azure Repos ou GitHub. Para visualizar o repositório e fazer alterações de código na sua aplicação, faça o seguinte:

1. À esquerda do painel de projetos DevOps, selecione o link para o seu ramo principal.  
Esta ligação abre uma vista para o repositório Git recentemente criado.

1. Para ver o URL do clone repositório, selecione **Clone** no canto superior direito do navegador.   
    Pode clonar o repositório Git no seu IDE preferido. Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. Do lado esquerdo do navegador, vá ao ficheiro **src/main/webapp/index.html.**

1. Selecione **Editar**e, em seguida, fazer uma alteração em alguns dos textos.
    Por exemplo, altere algum texto para uma das etiquetas div.

1. Selecione **'Cometer'** e, em seguida, guardar as suas alterações.

1. No seu navegador, vá ao painel de projetos DevOps.   
Agora deve ver uma construção em andamento. As alterações que acabou de fazer são automaticamente construídas e implantadas através de um oleoduto CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examinar o gasoduto CI/CD

 No passo anterior, os Projetos DevOps configuraram automaticamente um pipeline CI/CD completo. Explore e personalize o pipeline, conforme necessário. Tome os seguintes passos para se familiarizar com os oleodutos de construção e libertação.

1. No topo do painel de projetos DevOps, selecione **Build Pipelines**.  
Este link abre um separador de navegador e o pipeline de construção para o seu novo projeto.

1. Aponte para o campo **Status** e, em seguida, selecione a elipse (...).  
    Esta ação abre um menu onde pode iniciar várias atividades como fazer fila de uma nova construção, fazer uma pausa na construção e editar o pipeline de construção.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de construção.  
A construção executa uma variedade de tarefas como obter fontes do repositório Git, restaurar dependências e publicar saídas que são usadas para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu oleoduto de construção para algo mais descritivo, selecione **Guardar & fila**e, em seguida, selecione **Guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.   
No painel **história,** você vê um rasto de auditoria das suas recentes mudanças para a construção.  A Azure Pipelines acompanha quaisquer alterações que sejam feitas no pipeline de construção, e permite comparar versões.

1. Selecione **Triggers**.   
 A DevOps Projects criou automaticamente um gatilho ci, e cada compromisso com o repositório inicia uma nova construção.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.   
Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

1. Selecione **Construir e Soltar**e, em seguida, selecione **Lançamentos**.  
 A DevOps Projects cria um oleoduto de libertação para gerir as implantações para o Azure.

1. À esquerda, selecione a elipse (...) junto ao seu gasoduto de libertação e, em seguida, **selecione Editar**.  
O pipeline de lançamento contém um pipeline, que define o processo de lançamento.  
    
12. Em **Artefactos**, selecione **Remover**.  
O oleoduto de construção que examinou nos passos anteriores produz a saída que é usada para o artefacto. 

1. Ao lado do ícone **Drop,** selecione o **gatilho de implantação Contínua**.  
Este oleoduto de libertação tem um gatilho de CD ativado, que executa uma implantação sempre que há um novo artefacto de construção disponível. Opcionalmente, pode desativar o gatilho de modo a que as suas implementações exijam execução manual. 

1. À esquerda, selecione **Tarefas**.   
As tarefas são as atividades que o seu processo de implantação realiza. Neste exemplo, foi criada uma tarefa para implantar no Azure App Service.

1. À direita, selecione **versões**.  
Esta vista mostra um histórico das versões.

1. Selecione a elipse (...) ao lado de um dos seus lançamentos e, em seguida, selecione **Open**.  
Existem vários menus para explorar, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.   
Esta visão mostra os compromissos de código que estão associados à implantação específica. 

1. Selecionar **Registos**.  
Os registos contêm informações úteis sobre o processo de implementação. Podem ser vistos durante e após as implementações.

## <a name="clean-up-resources"></a>Limpar recursos

Pode eliminar o Serviço de Aplicações Azure e outros recursos relacionados quando já não precisa deles. Utilize a funcionalidade **Eliminar** no painel de instrumentos de Projetos DevOps.

## <a name="next-steps"></a>Passos seguintes

Quando configurao o seu processo CI/CD, os gasodutos de construção e de libertação foram automaticamente criados. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Para saber mais sobre o oleoduto CI/CD, consulte:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
