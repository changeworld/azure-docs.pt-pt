---
title: 'QuickStart: Criar uma aplicação web html estática'
description: Implemente o seu primeiro HTML Hello World para o Azure App Service em minutos. Implementa-se usando o Git, que é uma das muitas formas de se implementar para o App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 159b38962fe91cedfc8d313bef943dbc74e9974e
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520242"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Criar uma aplicação Web HTML estática no Azure

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este quickstart mostra como implementar um site básico HTML+CSS para o Serviço de Aplicações Azure. Você completará este quickstart em [Cloud Shell,](https://docs.microsoft.com/azure/cloud-shell/overview)mas também pode executar estes comandos localmente com [Azure CLI](/cli/azure/install-azure-cli).

![Página inicial da aplicação de exemplo](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

No Cloud Shell, crie um diretório de início rápido e, em seguida, altere-o.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Em seguida, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu diretório de início rápido.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>Criar uma aplicação Web

Mude para o diretório que contém o código de exemplo e execute o comando `az webapp up`.

No comando a seguir, substitua <nome_aplicação> por um nome de aplicação exclusivo.

```bash
cd html-docs-hello-world
```

```azurecli
az webapp up --location westeurope --name <app_name> 
```

O comando `az webapp up` executa as seguintes ações:

- Cria um grupo de recursos predefinido.

- Cria um plano do serviço de aplicações predefinido.

- Cria uma aplicação com o nome especificado.

- [Implementa](https://docs.microsoft.com/azure/app-service/deploy-zip) os ficheiros zip do diretório atual de trabalho para a aplicação Web.

Este comando pode demorar alguns minutos a ser executado. Ao executar, apresenta informações semelhantes ao exemplo seguinte:

```json
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "westeurope",
  "name": "<app_name>",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/<username>/quickstart/html-docs-hello-world ",
  < JSON data removed for brevity. >
}
```

Anote o valor `resourceGroup`. Vai precisar dele na secção [limpar recursos](#clean-up-resources).

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Num browser, vá ao URL `http://<app_name>.azurewebsites.net`da aplicação: .

A página está a ser executada como uma aplicação Web do Serviço de Aplicações do Azure.

![Home page da aplicação de exemplo](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

**Parabéns!** Implementou a sua primeira aplicação HTML no Serviço de Aplicações.

## <a name="update-and-redeploy-the-app"></a>Atualizar e reimplementar a aplicação

No Cloud Shell, escreva `nano index.html` para abrir o editor de texto nano. No cabeçalho da etiqueta `<h1>`, altere o "Serviço de Aplicações do Azure – Site HTML Estático de Exemplo" para -"Serviço de Aplicações do Azure", conforme mostrado abaixo.

![Nano index.html](media/app-service-web-get-started-html/nano-index-html.png)

Guarde as alterações e feche o nano. Utilize o comando `^O` para guardar e `^X` para sair.

Agora irá implementar novamente a aplicação com o mesmo comando `az webapp up`.

```azurecli
az webapp up --location westeurope --name <app_name> --html
```

Depois de concluída a implementação, volte para a janela do browser aberta que abriu no passo **Navegar para a aplicação** e atualize a página.

![Página inicial atualizada da aplicação de exemplo](media/app-service-web-get-started-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>Gerencie a sua nova app Azure

Para gerir a aplicação web que criou, no [portal Azure,](https://portal.azure.com)procure e selecione Serviços de **Aplicações.** 

![Selecione Serviços de Aplicações no portal Azure](./media/app-service-web-get-started-html/portal0.png)

Na página de Serviços de **Aplicações,** selecione o nome da sua aplicação Azure.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-get-started-html/portal1.png)

É apresentada a página de descrição geral da sua aplicação Web. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar.

![Painel Serviço de Aplicações no portal do Azure](./media/app-service-web-get-started-html/portal2.png)

O menu à esquerda fornece diferentes páginas para configurar a sua aplicação.

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de recursos. Se achar que não vai precisar destes recursos no futuro, execute o seguinte comando no Cloud Shell para eliminar o grupo de recursos. Lembre-se de que o nome do grupo de recursos foi gerado automaticamente para si no passo [criar uma aplicação Web](#create-a-web-app).

```azurecli
az group delete --name appsvc_rg_Windows_westeurope
```

Este comando pode demorar alguns minutos a ser executado.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain.md)
