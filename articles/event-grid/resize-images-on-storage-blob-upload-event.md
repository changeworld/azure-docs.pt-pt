---
title: 'Tutorial: Use a Grelha de Eventos Azure para automatizar imagens carregadas'
description: 'Tutorial: A Grelha de Eventos Azure pode desencadear uploads de blob no Armazenamento Azure. Pode utilizá-lo para enviar ficheiros de imagem carregados para o Armazenamento do Azure para outros serviços, como as Funções do Azure, para redimensionar e outras melhorias.'
services: event-grid, functions
author: spelluru
manager: jpconnoc
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 868c7e3956f20837b3774c0958842a7835579a04
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607505"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Tutorial: Automatizar imagens carregadas usando a Grelha de Eventos

O [Azure Event Grid](overview.md) é um serviço de eventos para a cloud. O Event Grid permite criar subscrições para eventos gerados pelos serviços do Azure ou recursos de terceiros.  

Este tutorial é a segunda parte de uma série de tutoriais sobre Armazenamento. Complementa o [tutorial de Armazenamento anterior][previous-tutorial] para adicionar a geração automática de miniaturas sem servidor com o Azure Event Grid e as Funções do Azure. O Event Grid permite às [Funções do Azure](../azure-functions/functions-overview.md) responder a eventos do [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md) e gerar miniaturas das imagens carregadas. É criada uma subscrição de evento relativamente ao evento de criação de armazenamento de Blobs. Quando é adicionado um blob a um contentor de armazenamento de Blobs específico, é chamado um ponto final de função. Os dados transmitidos ao enlace de função a partir do Event Grid são utilizados para aceder ao blob e gerar a imagem em miniatura.

Utilize a CLI do Azure e o portal do Azure para adicionar a funcionalidade de redimensionamento a uma aplicação de carregamento de imagens existente.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

![Aplicativo web publicado no navegador](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

![Aplicativo web publicado no navegador](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta de Armazenamento do Azure
> * Implementar código sem servidor com as Funções do Azure
> * Criar uma subscrição de evento de armazenamento de Blobs no Event Grid

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial:

Tem de ter concluído o tutorial de armazenamento de Blobs anterior: [Carregar dados de imagem na cloud com o Armazenamento do Azure][previous-tutorial].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tutorial requer a execução da versão 2.0.14 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Se não estiver a utilizar o Cloud Shell, tem primeiro de iniciar sessão com o `az login`.

Se não registou o fornecedor de recursos do Event Grid na sua subscrição anteriormente, confirme que está registado.

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

As Funções do Azure requerem uma conta de armazenamento geral. Além da conta de armazenamento Blob que criou no tutorial anterior, crie uma conta de armazenamento geral separada no grupo de recursos utilizando a conta de [armazenamento az criar](/cli/azure/storage/account) comando. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.

1. Delineie uma variável para manter o nome do grupo de recursos que criou no tutorial anterior.

    ```azurecli-interactive
    resourceGroupName="myResourceGroup"
    ```
2. Delineie uma variável para o nome da nova conta de armazenamento que as Funções Azure exigem.
    ```azurecli-interactive
    functionstorage="<name of the storage account to be used by the function>"
    ```
3. Crie a conta de armazenamento para a função Azure.

    ```azurecli-interactive
    az storage account create --name $functionstorage --location southeastasia \
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

## <a name="create-a-function-app"></a>Criar uma aplicação de função  

Precisa de uma aplicação de funções para alojar a execução da sua função. A aplicação Function App proporciona um ambiente para a execução sem servidor do código da sua função. Utilize o comando [az functionapp create](/cli/azure/functionapp) para criar uma aplicação Function App.

No seguinte comando, forneça o seu nome único de aplicação de função. O nome da aplicação de funções vai ser utilizado como o domínio DNS predefinido para a aplicação de funções, por isso o nome tem de ser exclusivo em todas as aplicações no Azure.

1. Especifique um nome para a aplicação de função que está a ser criada.

    ```azurecli-interactive
    functionapp="<name of the function app>"
    ```
2. Criar a função Azure.

    ```azurecli-interactive
    az functionapp create --name $functionapp --storage-account $functionstorage \
      --resource-group $resourceGroupName --consumption-plan-location southeastasia \
      --functions_version 2
    ```

Agora configure a aplicação de funções para se ligar à conta de armazenamento Blob que criou no [tutorial anterior][previous-tutorial].

## <a name="configure-the-function-app"></a>Configurar a aplicação de funções

A função necessita de credenciais para a conta de armazenamento Blob, que são adicionadas às definições de aplicação da aplicação da aplicação usando o conjunto de aplicações de config de [az functionapp.](/cli/azure/functionapp/config/appsettings)

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

```azurecli-interactive
blobStorageAccount="<name of the Blob storage account you created in the previous tutorial>"
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

```azurecli-interactive
blobStorageAccount="<name of the Blob storage account you created in the previous tutorial>"

blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
  -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

A definição `FUNCTIONS_EXTENSION_VERSION=~2` determina que a aplicação de funções seja executada na versão 2.x do runtime das Funções do Azure.

Agora, pode implementar um projeto de código de função nesta aplicação de funções.

## <a name="deploy-the-function-code"></a>Implementar o código de função 

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

A função de redimensionar a amostra C# está disponível no [GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Implemente este projeto de código na aplicação de funções utilizando o comando de origem config de fonte de implementação de [az functionapp.](/cli/azure/functionapp/deployment/source)

```azurecli-interactive
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

A função de redimensionamento do Node.js de exemplo está disponível no [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node). Implemente este projeto de código de Funções na aplicação de funções com o comando [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node
```
---

A função de redimensionamento da imagem é acionada por pedidos de HTTP enviados a partir do serviço Event Grid. Indica ao Event Grid que pretende obter estas notificações no URL da sua função ao criar uma subscrição de evento. Neste tutorial, subscreve eventos criados no blob.

Os dados transmitidos para a função a partir da notificação do Event Grid incluem o URL do blob. Esse URL é transmitido para o enlace de entrada para obter a imagem carregada a partir do armazenamento de Blobs. A função gera uma imagem em miniatura e escreve o fluxo resultante num contentor separado no armazenamento de Blobs.

Este projeto utiliza `EventGridTrigger` para o tipo de acionador. É recomendado utilizar o acionador do Event Grid através de acionadores HTTP genéricos. O Event Grid valida automaticamente os acionadores de função do Event Grid. Com os acionadores HTTP genéricos, tem de implementar a [resposta de validação](security-authentication.md).

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Para obter mais informações sobre esta função, veja os [ficheiros function.json e run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Para saber mais sobre esta função, consulte os [ficheiros function.json e index.js](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail).

---

O código de projeto de função é implementado diretamente a partir do repositório de exemplo público. Para saber mais sobre as opções de implementação para as Funções do Azure, veja [Implementação contínua para Funções do Azure](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Criar uma subscrição de evento

Uma subscrição de evento indica que eventos gerados pelo fornecedor quer que sejam enviados para um ponto final específico. Neste caso, o ponto final é exposto pela sua função. Utilize os passos seguintes para criar uma subscrição de evento que envia notificações para a sua função no portal do Azure:

1. No [portal Azure,](https://portal.azure.com)selecione **Todos os Serviços** no menu esquerdo e, em seguida, selecione **Aplicações de Função**.

    ![Navegue para funcionar apps no portal Azure](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Expanda a sua aplicação de função, escolha a função **Miniatura** e, em seguida, selecione **adicionar subscrição de Rede de Eventos**.

    ![Navegue para adicionar subscrição da Grelha de Eventos no portal Azure](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Utilize as definições de subscrição de evento especificadas na tabela.
    
    ![Criar uma subscrição de evento a partir da função no portal do Azure](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **Nome** | imageresizersub | Nome que identifica a nova subscrição de evento. |
    | **Tipo de tópico** | Contas de armazenamento | Selecione o fornecedor de eventos da conta de Armazenamento. |
    | **Subscrição** | A sua subscrição do Azure | Por predefinição, a subscrição do Azure atual está selecionada. |
    | **Grupo de recursos** | myResourceGroup | Selecione **Utilizar existente** e selecione o grupo de recursos que tem utilizado neste tutorial. |
    | **Recurso** | A sua conta de armazenamento de Blobs | Selecione a conta de armazenamento de Blobs que criou. |
    | **Tipos de evento** | Criado pelo Blob | Desmarque todos os tipos diferentes de **Criado pelo Blob**. Apenas os tipos de evento de `Microsoft.Storage.BlobCreated` são transmitidos à função. |
    | **Tipo endpoint** | gerado automaticamente | Pré-definida como **Função Azure**. |
    | **Ponto Final** | gerado automaticamente | Nome da função. Neste caso, é **miniatura.** |

4. Mude para o separador Filtros e faça as **seguintes** ações:
    1. Selecione ativar a opção de filtragem do **assunto.**
    2. Para **o Assunto começa com**, insira o seguinte valor : **/blobServices/default/containers/images/blobs/**.

        ![Especificar filtro para a subscrição do evento](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

5. Selecione **Criar** para adicionar a subscrição do evento. Isto cria uma subscrição `Thumbnail` de evento que desencadeia `images` a função quando uma bolha é adicionada ao recipiente. A função redimensiona as imagens e adiciona-as ao `thumbnails` recipiente.

Agora que os serviços de back-end estão configurados, teste a funcionalidade de redimensionamento de imagens na aplicação Web de exemplo.

## <a name="test-the-sample-app"></a>Testar a aplicação de exemplo

Para testar o redimensionamento de imagens na aplicação Web, navegue para o URL da aplicação publicada. O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Clique na região **Carregar fotografias** para selecionar e carregar um ficheiro. Também pode arrastar uma fotografia para esta região.

Note que após o desaparecimento da imagem carregada, uma cópia da imagem carregada é exibida no carrossel **das Miniaturas Geradas.** Esta imagem foi redimensionada pela função, adicionada ao contentor de *miniaturas* e transferida pelo cliente Web.

![Aplicativo web publicado no navegador](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Clique **em Escolher 'Escolher'** para selecionar um ficheiro e, em seguida, clique em **Enviar imagem**. Quando o upload é bem sucedido, o navegador navega para uma página de sucesso. Clique no link para voltar à página inicial. Uma cópia da imagem carregada é exibida na área de **Miniaturas Geradas.** (Se a imagem não aparecer no início, tente recarregar a página.) Esta imagem foi redimensionada pela função, adicionada ao recipiente de *miniaturas,* e descarregada pelo cliente web.

![Aplicativo web publicado no navegador](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma conta de Armazenamento do Azure geral
> * Implementar código sem servidor com as Funções do Azure
> * Criar uma subscrição de evento de armazenamento de Blobs no Event Grid

Avance para a terceira parte da série do tutorial de Armazenamento para saber como proteger o acesso à conta de armazenamento.

> [!div class="nextstepaction"]
> [Proteger o acesso aos dados de aplicações na cloud](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Para saber mais sobre o Event Grid, veja [Introdução ao Azure Event Grid](overview.md).
+ Para experimentar outro tutorial que inclua Funções do Azure, veja [Criar uma função que se integra no Azure Logic Apps](../azure-functions/functions-twitter-email.md).

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
