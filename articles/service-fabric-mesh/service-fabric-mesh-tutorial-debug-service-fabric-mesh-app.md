---
title: Depurar uma aplicação web azure service Fabric Mesh web funcionando localmente
description: Neste tutorial, vai depurar uma aplicação do Azure Service Fabric Mesh em execução no seu cluster local.
author: dkkapur
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: c36d45919ae8a17026fc91f8e9040f3bb11d3eb0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75494953"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Tutorial: Depurar uma aplicação do Service Fabric Mesh em execução no cluster de desenvolvimento local

Este tutorial é a segunda parte de uma série e mostra-lhe como criar e depurar uma aplicação do Azure Service Fabric Mesh no cluster de desenvolvimento local.

Neste tutorial vai aprender:

> [!div class="checklist"]
> * O que acontece quando cria uma aplicação do Azure Service Fabric Mesh
> * Como definir um ponto de interrupção para observar uma chamada serviço a serviço

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação do Service Fabric Mesh no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Depurar uma aplicação do Azure Service Fabric Mesh em execução no cluster de desenvolvimento local
> * [Implementar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Atualizar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Limpar os recursos do Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Certifique-se de que [configurou o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md), que inclui a instalação do runtime do Service Fabric, o SDK, o Docker e o Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Transferir a aplicação de tarefas de exemplo

Se não criou a aplicação de amostra seleção a fazer na [primeira parte desta série tutorial,](service-fabric-mesh-tutorial-create-dotnetcore.md)pode descarregá-la. Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

A aplicação está no diretório `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Criar e depurar no seu cluster local

É automaticamente criada e implementada uma imagem do Docker no seu cluster local, assim que o projeto é carregado. Este processo pode demorar algum tempo. Para monitorizar o progresso no painel **Output** (Saída) do Visual Studio, defina o menu pendente **Show output from:** (Mostrar saída de) como **Service Fabric Tools** (Ferramentas do Service Fabric).

Prima **F5** para compilar e executar o serviço localmente. Quando o projeto é executado e depurado localmente, o Visual Studio irá:

* Garantir que o Docker para Windows está em execução e definir como utilizar o Windows como sistema operativo do contentor.
* Transferir quaisquer imagens base do Docker em falta. Esta parte pode demorar algum tempo
* Criar (ou recriar) a imagem do Docker utilizada para alojar o seu projeto de código.
* Implementar e executar o contentor no cluster de desenvolvimento local do Service Fabric.
* Executar os seus serviços e atingir os pontos de interrupção definidos.

Depois de a implementação local estar concluída e o Visual Studio estar a executar a aplicação, será aberta uma janela do browser numa página Web de exemplo predefinida.

## <a name="debugging-tips"></a>Sugestões de depuração

Faça a sua primeira corrida de depuração (F5) muito mais rápido seguindo as instruções no desempenho do [Otimize Visual Studio](service-fabric-mesh-howto-optimize-vs.md).

Há um problema que faz com `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` que a chamada falhe na ligação ao serviço. Isto pode acontecer sempre que altera o seu endereço IP do anfitrião. Para resolver isto:

1. Remova a aplicação do cluster local (em Visual Studio, **Build** > **Clean Solution).**
2. No Gestor do Cluster Local do Service Fabric, selecione **Parar Cluster Local** e, em seguida, **Iniciar Cluster Local**.
3. Reimplementar a aplicação (no Visual Studio, **F5**).

Se receber o erro **Nenhum cluster local do Service Fabric em execução**, certifique-se de que o Service Fabric Local Cluster Manager (LCM) está em execução, clique com o botão direito do rato no ícone do LCM na barra de tarefas e, em seguida, clique em **Iniciar Cluster Local**. Depois de ser iniciado, regresse ao Visual Studio e prima **F5**.

Se receber um erro **404** quando a aplicação for iniciada, pode significar que as variáveis de ambiente em **service.yaml** estão incorretas. Certifique-se de que `ApiHostPort` e `ToDoServiceName` estão definidos corretamente de acordo com as instruções em [Criar variáveis de ambiente](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore#create-environment-variables).

Se ocorrerem erros de compilação em **service.yaml**, certifique-se de que são utilizados espaços, não separadores, para avançar as linhas. Além disso, por agora, tem de criar a aplicação com o idioma inglês.

### <a name="debug-in-visual-studio"></a>Depurar no Visual Studio

Ao depurar uma aplicação de malha de tecido de serviço no Estúdio Visual, está a utilizar um cluster de desenvolvimento de Tecido de Serviço local. Para ver como os itens de tarefas são obtidos do serviço de back-end, faça a depuração para o método OnGet().
1. No projeto **WebFrontEnd,** abra o**Índice de Páginas.cshtml** >  **Pages** > **Index.cshtml.cs** e delineie um ponto de rutura no método **OnGet** (linha 17).
2. No projeto **ToDoService,** abra **TodoController.cs** e delineie um ponto de rutura no método **Get** (linha 15).
3. Regresse ao seu browser e atualize a página. Atingiu o ponto de interrupção no método de front-end da Web `OnGet()`. Pode inspecionar a variável `backendUrl` para ver como as variáveis de ambiente que definiu no ficheiro **service.yaml** são combinadas para o URL utilizado para contactar o serviço de back-end.
4. Ignore (F10) a chamada `client.GetAsync(backendUrl).GetAwaiter().GetResult())` e irá atingir o ponto de interrupção do controlador `Get()`. Neste método, pode ver como é obtida a lista de itens de tarefas a partir da lista na memória.
5. Quando terminar, pare de depurar o seu projeto no Estúdio Visual pressionando **shift+F5**.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * O que acontece quando cria uma aplicação do Azure Service Fabric Mesh
> * Como definir um ponto de interrupção para observar uma chamada serviço a serviço

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Implementar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
