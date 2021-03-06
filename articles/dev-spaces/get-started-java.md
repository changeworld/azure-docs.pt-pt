---
title: 'Crie um espaço de v Kubernetes: Visual Studio Code & Java'
services: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 09/26/2018
ms.topic: tutorial
description: Este tutorial mostra-lhe como usar o Azure Dev Spaces e o Visual Studio Code para depurar e iterar rapidamente uma aplicação Java no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.openlocfilehash: c71471d1a89188a065bafef2c5b6372aeff35851
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80240543"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-code-and-java-with-azure-dev-spaces"></a>Crie um espaço Kubernetes dev: Visual Studio Code e Java com Espaços Azure Dev

Neste guia, vai aprender a:

- Criar um ambiente baseado no Kubernetes no Azure otimizado para o desenvolvimento - um _espaço de programação_.
- Utilizar o VS Code e a linha de comandos para desenvolver iterativamente código em contentores.
- Desenvolver e testar de forma produtiva o seu código num ambiente de equipa.

> [!Note]
> **Se ficar preso** a qualquer momento, consulte a secção de [resolução de problemas.](troubleshooting.md)

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
O Azure Dev Spaces só precisa de configuração mínima do computador local. A maior parte da configuração do espaço de desenvolvimento é armazenada na cloud e é partilhável com outros utilizadores. Comece por transferir e executar a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Iniciar sessão na CLI do Azure
Inicie sessão no Azure. Escreva o seguinte comando numa janela de terminal:

```azurecli
az login
```

> [!Note]
> Se não tiver uma subscrição Azure, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Se tiver várias subscrições do Azure...
Pode ver as suas subscrições ao executar: 

```azurecli
az account list --output table
```

Localize a subscrição que tem *True* for *IsDefault*.
Se esta não for a subscrição que pretende utilizar, pode alterar a subscrição predefinida:

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Criar um cluster do Kubernetes ativado para os Espaços de Programador do Azure

No pedido de comando, crie o grupo de recursos numa região que apoie o [Azure Dev Spaces.][supported-regions]

```azurecli
az group create --name MyResourceGroup --location <region>
```

Crie um cluster do Kubernetes com o seguinte comando:

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

A criação do cluster demora alguns minutos.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurar o seu cluster do AKS para utilizar os espaços de desenvolvimento do Azure

Introduza o seguinte comando da CLI do Azure com o grupo de recursos que contém o cluster do AKS e o nome do cluster do AKS. O comando configura o seu cluster com suporte para espaços de desenvolvimento do Azure.

   ```azurecli
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

> [!IMPORTANT]
> O processo de configuração dos `azds` Espaços Azure Dev removerá o espaço de nome no cluster, se existir.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Depurar o Kubernetes para o VS Code
Estão disponíveis funcionalidades avançadas, como a depuração do Kubernetes, para programadores de .NET Core e o Node.js com o VS Code.

1. Se não tiver, instale o [VS Code](https://code.visualstudio.com/Download).
1. Transfira e instale a [extensão do Azure Dev Spaces do VS](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Clique em Instalar uma vez na página do Marketplace da extensão e, novamente, no VS Code. 

Para depurar aplicações em Java com os Espaços de Programador do Azure, transfira e instale a extensão [Java Debugger for Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) para o VS Code. Clique em Instalar uma vez na página do Marketplace da extensão e, novamente, no VS Code.

## <a name="create-a-web-app-running-in-a-container"></a>Criar uma aplicação Web em execução num contentor

Nesta secção, vai criar uma aplicação Web em Java e executá-la num contentor no Kubernetes.

### <a name="create-a-java-web-app"></a>Criar uma aplicação Web em Java
Descarregue o código do [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) GitHub navegando para e selecione **Clone ou Download** para baixar o repositório GitHub para o seu ambiente local. O código para este guia está em `samples/java/getting-started/webfrontend`.

## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Preparar o código para implementação do Docker e Kubernetes
Neste momento, tem uma aplicação Web básica que pode ser executada localmente. Agora, irá colocá-la em contentor ao criar recursos que definem o contentor da aplicação e como esta será implementada no Kubernetes. É fácil executar esta tarefa com o Azure Dev Spaces: 

1. Inicie o VS Code e abra a pasta `webfrontend`. (Pode ignorar os pedidos predefinidos para adicionar recursos de erro ou restaurar o projeto.)
1. Abra o Terminal Integrado no VS Code (através do menu **Ver > Terminal Integrado**).
1. Execute este comando (certifique-se de que **webfrontend** é a sua pasta atual):

    ```cmd
    azds prep --enable-ingress
    ```

O comando `azds prep` da CLI do Azure gera recursos do Docker e Kubernetes com as predefinições:
* `./Dockerfile` descreve a imagem de contentor da aplicação e como o código-fonte é construído e executado no contentor.
* Um [gráfico Helm](https://docs.helm.sh) em `./charts/webfrontend` descreve como implementar o contentor no Kubernetes.

> [!TIP]
> O [gráfico Dockerfile e Helm](how-dev-spaces-works-prep.md#prepare-your-code) para o seu projeto é usado pela Azure Dev Spaces para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

Por enquanto, não é necessário entender o conteúdo completo desses ficheiros. No entanto, importa realçar que **os mesmos recursos de configuração como código do Kubernetes e do Docker podem ser utilizados do desenvolvimento à produção, dando maior consistência em diferentes ambientes.**
 
É também geral um ficheiro com o nome `./azds.yaml` pelo comando `prep`, que é o ficheiro de configuração do Azure Dev Spaces. Este complementa os artefactos do Docker e do Kubernetes com uma configuração adicional que permite uma experiência de desenvolvimento iterativa no Azure.

## <a name="build-and-run-code-in-kubernetes"></a>Criar e executar códigos no Kubernetes
Vamos executar o nosso código! Na janela do terminal, execute este comando a partir da **pasta de código raiz**, webfrontend:

```cmd
azds up
```

Fique de olho no resultado do comando, vai reparar em várias coisas à medida que progride:
- O código fonte é sincronizado com o espaço de programação no Azure.
- É criada uma imagem de contentor no Azure, conforme especificado pelos ativos do Docker na sua pasta de códigos.
- São criados objetos do Kubernetes que utilizam a imagem do contentor, conforme especificado pelo gráfico Helm na sua pasta de códigos.
- São apresentadas as informações sobre o(s) ponto(s) final(ais) do contentor. No nosso caso, contamos com um URL HTTP público.
- Assumindo que os estágios acima sejam concluídos com sucesso, deve começar a ver o resultado `stdout` (e `stderr`), à medida que o contentor é iniciado.

> [!Note]
> Estes passos vão demorar mais tempo quando o comando `up` é executado pela primeira vez, mas as execuções seguintes deverão ser mais rápidas.

### <a name="test-the-web-app"></a>Testar a aplicação Web
Analise o resultado da consola para obter informações sobre o URL público que foi criado pelo comando `up`. Estará na forma: 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
```

Identifique o URL público para o `up` serviço na saída a partir do comando. Acaba em. `.azds.io` No exemplo acima, o `http://webfrontend.1234567890abcdef1234.eus.azds.io/`URL público é .

Para ver a sua aplicação web, abra o URL público num browser. Além `stdout` disso, o aviso e `stderr` a saída são transmitidos para a janela de rastreio de *azds* enquanto interage com a sua aplicação web. Também verá informações de rastreio para pedidos HTTP à medida que passam pelo sistema. Isto facilita-lhe o rastreio de chamadas complexas de vários serviços durante o desenvolvimento. A instrumentação adicionada pela Dev Spaces fornece este rastreio de pedido.

> [!Note]
> Além do URL público, pode `http://localhost:<portnumber>` utilizar o URL alternativo que é apresentado na saída da consola. Se utilizar o URL de anfitrião local, poderá parecer que o contentor está a ser executado localmente, contudo, está a ser executado no AKS. A Azure Dev Spaces utiliza a funcionalidade kubernetes *para* mapear o porto de acolhimento local para o contentor que funciona em AKS. Isto facilita a interação com o serviço da sua máquina local.

### <a name="update-a-content-file"></a>Atualizar um ficheiro de conteúdo
O Azure Dev Spaces não se limita apenas a pôr o código em execução no Kubernetes. Tem que ver com permitir-lhe ver, de forma rápida e iterativa, as alterações ao código serem aplicadas num ambiente do Kubernetes na cloud.

1. Na janela de terminal, prima `Ctrl+C` (para parar `azds up`).
1. Abra, `src/main/java/com/ms/sample/webfrontend/Application.java`e edite a mensagem de saudação na [linha 19:](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19)

    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Guarde o ficheiro.
1. Execute `azds up` na janela de terminal.

Este comando recria a imagem do contentor e reimplementa o gráfico Helm. Para ver as alterações ao código entrarem em vigor na aplicação em execução, basta atualizar o browser.

Mas existe um *método ainda mais rápido* para programar código, que vai explorar na próxima secção. 

## <a name="debug-a-container-in-kubernetes"></a>Depurar um contentor no Kubernetes

Nesta secção, ai utilizar o VS Code para depurar diretamente o nosso contentor em execução no Azure. Também vai aprender como obter um ciclo de edição-execução-teste mais rápido.

![](media/common/edit-refresh-see.png)

> [!Note]
> **Se ficar bloqueado** em qualquer altura, veja a secção [Resolução de problemas](troubleshooting.md) ou publique um comentário nesta página.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicializar ativos de depuração com a extensão do VS Code
Primeiro tem de configurar o seu projeto de código para que o VS Code comunique com o nosso espaço de programação no Azure. A extensão do VS Code para Espaços de Programação do Azure oferece um comando auxiliar para contribuir para a configuração da depuração. 

Abra a **Paleta de Comandos** (através do menu **Ver | Paleta de Comandos**), e utilize o preenchimento automático para escrever e selecione este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Esta ação adiciona a configuração de depuração para os Espaços de Programação do Azure na pasta `.vscode`. Este comando não deve ser confundido com o comando `azds prep`, que configura o projeto para implementação.

![](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Selecionar a configuração de depuração do AZDS
1. Para abrir a vista Debug (Depuração), clique no ícone Debug em **Activity Bar** (Barra de Atividades), no lado do VS Code.
1. Selecione **Iniciar Programa Java (AZDS)** como a configuração de depuração ativa.

![](media/get-started-java/debug-configuration.png)

> [!Note]
> Se não vir nenhum comando do Azure Dev Spaces em Command Palette (Paleta de Comandos), confirme que tem instalada a extensão do VS Code para o Azure Dev Spaces. Certifique-se de que o espaço de trabalho `azds.yaml`que abriu no Código VS é a pasta que contém .

### <a name="debug-the-container-in-kubernetes"></a>Depurar o contentor no Kubernetes
Prima **F5** para depurar o código no Kubernetes.

Tal como sucede com o comando `up`, o código é sincronizado com o espaço de programador e é criado e implementado um contentor no Kubernetes. Desta vez, obviamente, o depurador está ligado ao contentor remoto.

> [!Tip]
> A barra de estado do Código VS ficará laranja, indicando que o desordeiro está ligado. Também apresentará um URL clicável, que pode utilizar para abrir a sua aplicação.

![](media/common/vscode-status-bar-url.png)

Defina um ponto de interrupção num ficheiro de código do lado do servidor, como, por exemplo, a função `greeting()` no ficheiro de origem `src/main/java/com/ms/sample/webfrontend/Application.java`. Atualizar a página do browser faz com que o ponto de interrupção seja atingido.

Tem acesso total às informações da depuração, tal como aconteceria se o código estivesse a ser executado localmente, como, por exemplo, a pilha de chamadas, as variáveis locais, as informações de exceção, etc.

### <a name="edit-code-and-refresh"></a>Editar o código e atualizar
Com o depurador ativo, faça uma edição ao código. Por exemplo, modifique a saudação em `src/main/java/com/ms/sample/webfrontend/Application.java`. 

```java
public String greeting()
{
    return "I'm debugging Java code in Azure!";
}
```

Guarde o ficheiro e, no painel de ações do **Debug,** clique no botão **Reiniciar.**

![](media/common/debug-action-refresh.png)

Em vez de reconstruir e reimplementar uma imagem de contentor nova sempre que forem feitas edições ao código, o que, muitas vezes, irá demorar um tempo considerável, o Azure Dev Spaces recompilará incrementalmente o código dentro do contentor existente para proporcionar um ciclo de edição/depuração mais rápido.

Atualize a aplicação Web no browser. Deverá ver a mensagem personalizada apresentada na IU.

**Agora, tem um método para iterar rapidamente no código e depurar diretamente no Kubernetes.** Em seguida, irá ver como pode criar e chamar um segundo contentor.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça o desenvolvimento de vários serviços](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
