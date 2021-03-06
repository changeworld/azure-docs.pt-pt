---
title: Tutorial - Construa uma aplicação altamente disponível com armazenamento Blob
titleSuffix: Azure Storage
description: Utilize armazenamento geo-redundante de acesso de leitura para disponibilizar os dados da sua aplicação.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 0eabd918b5f8f52049792ceb28ef8055945d6475
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77162179"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Tutorial: Construir uma aplicação altamente disponível com armazenamento Blob

Este tutorial é a primeira parte de uma série. Nele, aprende-se a disponibilizar os dados da sua aplicação em Azure.

Quando tiver concluído este tutorial, terá uma aplicação de consola que carrega e recupera uma bolha de uma conta de armazenamento [geo-redundante](../common/storage-redundancy.md) de acesso de leitura (RA-GRS).

O RA-GRS trabalha replicando transações de uma região primária para uma região secundária. Este processo de replicação garante que os dados na região secundária acabam por ser consistentes. A aplicação utiliza o padrão [de Disjuntor](/azure/architecture/patterns/circuit-breaker) para determinar a que ponto final se ligar, alternando automaticamente entre pontos finais à medida que são simuladas falhas e recuperações.

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar uma conta do Storage
> * Definir a cadeia de ligação
> * Executar a aplicação de consola

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

# <a name="net"></a>[.NET](#tab/dotnet)

* Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a carga de trabalho de **desenvolvimento do Azure.**

  ![Desenvolvimento do Azure (na Web e na Cloud)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python"></a>[Python](#tab/python)

* Instalar [Python](https://www.python.org/downloads/)
* Transfira e instale o [SDK de Armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* Instale [o Nó.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Criar uma conta do Storage

Uma conta de armazenamento fornece um espaço de nome único para armazenar e aceder aos objetos de dados do Seu Armazenamento Azure.

Siga estes passos para criar uma conta de armazenamento georredundante com acesso de leitura:

1. Selecione o botão **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Selecione **Armazenamento** a partir da **página Nova.**
3. Selecione **conta de armazenamento - blob, file, table, fila** em **Destaque**.
4. Preencha o formulário da conta de armazenamento com as seguintes informações, conforme mostrado na imagem abaixo e selecione **Criar**:

   | Definição       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome** | mystorageaccount | Um valor exclusivo para a conta de armazenamento |
   | **Modelo de implantação** | Resource Manager  | O Resource Manager contém as funcionalidades mais recentes.|
   | **Tipo de conta** | StorageV2 | Para obter detalhes sobre os tipos de contas, veja [Tipos de contas de armazenamento](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Desempenho** | Standard | O desempenho standard é suficiente para este cenário de exemplo. |
   | **Replicação**| Armazenamento georredundante com acesso de leitura (RA-GRS) | É necessário para o exemplo funcionar. |
   |**Subscrição** | A sua subscrição |Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.azure.com/Subscriptions) (Subscrições). |
   |**Grupo de Recursos** | myResourceGroup |Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições). |
   |**Localização** | E.U.A. Leste | Escolher uma localização. |

![criar conta de armazenamento](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Transferir o exemplo

# <a name="net"></a>[.NET](#tab/dotnet)

[Transfira o projeto de exemplo](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (deszipe) o ficheiro storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Também pode utilizar o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. O projeto de exemplo contém uma aplicação de consola.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python"></a>[Python](#tab/python)

[Transfira o projeto de exemplo](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (deszipe) o ficheiro storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Também pode utilizar o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. O projeto de exemplo contém uma aplicação Python básica.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Descarregue o projeto da amostra](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) e desaperte o ficheiro. Também pode utilizar o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. O projeto da amostra contém uma aplicação básica no nó.js.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Configure a amostra

# <a name="net"></a>[.NET](#tab/dotnet)

Na aplicação, tem de indicar a cadeia de ligação da sua conta de armazenamento. Pode armazenar esta cadeia de ligação numa variável de ambiente no computador local que executa a aplicação. Siga um dos exemplos abaixo, consoante o Sistema Operativo para criar a variável de ambiente.

No portal do Azure, navegue para a sua conta de armazenamento. Selecione **Chaves de acesso**, em **Definições**, na conta de armazenamento. Copie a **cadeia de ligação** da chave primária ou secundária. Execute um dos seguintes comandos com \<base no\> seu sistema operativo, substituindo a sua cadeia de ligação com a sua cadeia de ligação real. Este comando guarda uma variável de ambiente no computador local. No Windows, a variável ambiente não está disponível até recarregar o Pedido de **Comando** ou a concha que está a utilizar.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python"></a>[Python](#tab/python)

No pedido, deve fornecer as credenciais da sua conta de armazenamento. Pode armazenar esta informação em variáveis ambientais na máquina local que executa a aplicação. Siga um dos exemplos abaixo, dependendo do seu Sistema Operativo para criar as variáveis ambientais.

No portal do Azure, navegue para a sua conta de armazenamento. Selecione **Chaves de acesso**, em **Definições**, na conta de armazenamento. Colar o nome da **conta de armazenamento** e os valores-chave nos seguintes comandos, substituindo o seu nome **Key** \<\> de conta e \<os seus\> espaços reservados. Este comando salva as variáveis ambientais para a máquina local. No Windows, a variável ambiente não está disponível até recarregar o Pedido de **Comando** ou a concha que está a utilizar.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Para executar esta amostra, deve adicionar as `.env.example` credenciais da sua `.env`conta de armazenamento ao ficheiro e, em seguida, rebatizar para .

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Pode encontrar esta informação no portal Azure navegando na sua conta de armazenamento e selecionando **as teclas de Acesso** na secção **Definições.**

Instale as dependências necessárias. Para isso, abra um pedido de comando, navegue para a pasta da amostra e, em seguida, introduza `npm install`.

---

## <a name="run-the-console-application"></a>Executar a aplicação de consola

# <a name="net"></a>[.NET](#tab/dotnet)

No Estúdio Visual, prima **F5** ou selecione **Começar** a depurar a aplicação. O estúdio visual restaura automaticamente os pacotes NuGet em falta se configurado, visite [Instalar e reinstalar pacotes com restauro](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) de pacotes para saber mais.

É iniciada uma janela de consola e a aplicação começa a ser executada. A aplicação carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. A aplicação verifica para garantir que a imagem foi replicada para o ponto final de RA-GRS secundário. Em seguida, começa a transferir a imagem até 999 vezes. Cada leitura é representada por um **P** ou um **S**. Onde **P** representa o ponto final primário e **S** representa o ponto final secundário.

![Aplicação de consola em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, a tarefa `RunCircuitBreakerAsync` no ficheiro `Program.cs` é utilizada para transferir uma imagem da conta de armazenamento através do método [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync). Antes do download, é definido um [OperationContext.](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) O contexto da operação define os processadores de eventos que são acionados se uma transferência for concluída com êxito ou se falhar e estiver a repetir a operação.

# <a name="python"></a>[Python](#tab/python)

Para executar a aplicação num terminal ou numa linha de comandos, aceda ao diretório **circuitbreaker.py** e introduza `python circuitbreaker.py`. A aplicação carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. A aplicação verifica para garantir que a imagem foi replicada para o ponto final de RA-GRS secundário. Em seguida, começa a transferir a imagem até 999 vezes. Cada leitura é representada por um **P** ou um **S**. Onde **P** representa o ponto final primário e **S** representa o ponto final secundário.

![Aplicação de consola em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, o método `run_circuit_breaker` no ficheiro `circuitbreaker.py` é utilizado para transferir uma imagem da conta de armazenamento através do método [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html).

A função de repetição do objeto de armazenamento está definida como uma política de repetição linear. A função de repetição determina se deve repetir um pedido e especifica o número de segundos a aguardar antes da repetição. Defina o valor **retry\_to\_secondary** como verdadeiro se pretender repetir o pedido para o ponto final secundário, caso o pedido inicial para o primário falhe. A aplicação de exemplo, é definida uma política de repetição personalizada na função `retry_callback` do objeto de armazenamento.

Antes do download, o objeto de serviço [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) e [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) função é definido. Estas funções definem os processadores de eventos que são acionados se uma transferência for concluída com êxito ou se falhar e estiver a repetir a operação.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Para executar a amostra, abra um pedido de comando, navegue para a pasta da amostra e, em seguida, introduza `node index.js`.

A amostra cria um recipiente na sua conta de armazenamento Blob, envia **HelloWorld.png** para o recipiente e verifica repetidamente se o recipiente e a imagem se replicaram para a região secundária. Após a replicação, pede-lhe que entre em **D** ou **Q** (seguido de ENTER) para descarregar ou desistir. A sua saída deve ser semelhante ao seguinte exemplo:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

### <a name="net"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Processador de eventos de repetição

O processador de eventos `OperationContextRetrying` é chamado quando a transferência da imagem falha e está definida para repetir. Se for atingido o número máximo de repetições definidas na aplicação, [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) do pedido é alterado para `SecondaryOnly`. Esta definição força a aplicação a tentar transferir a imagem do ponto final secundário. Esta configuração reduz o tempo que demora a pedir a imagem, porque o ponto final primário não é repetido indefinidamente.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Processador de eventos de pedido concluído

O processador de eventos `OperationContextRequestCompleted` é chamado quando a transferência da imagem é bem-sucedida. Se a aplicação estiver a utilizar o ponto final secundário, continua a utilizar este ponto final até 20 vezes. Ao fim dessas 20 vezes, a aplicação define [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) novamente como `PrimaryThenSecondary` e repete o ponto final primário. Se um pedido for bem-sucedido, a aplicação continua a ler a partir do ponto final primário.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Processador de eventos de repetição

O processador de eventos `retry_callback` é chamado quando a transferência da imagem falha e está definida para repetir. Se for atingido o número máximo de repetições definidas na aplicação, [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) do pedido é alterado para `SECONDARY`. Esta definição força a aplicação a tentar transferir a imagem do ponto final secundário. Esta configuração reduz o tempo que demora a pedir a imagem, porque o ponto final primário não é repetido indefinidamente.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Processador de eventos de pedido concluído

O processador de eventos `response_callback` é chamado quando a transferência da imagem é bem-sucedida. Se a aplicação estiver a utilizar o ponto final secundário, continua a utilizar este ponto final até 20 vezes. Ao fim dessas 20 vezes, a aplicação define [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) novamente como `PRIMARY` e repete o ponto final primário. Se um pedido for bem-sucedido, a aplicação continua a ler a partir do ponto final primário.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Com o Node.js V10 SDK, os manipuladores de chamadas são desnecessários. Em vez disso, a amostra cria um pipeline configurado com opções de retry e um ponto final secundário. Isto permite que a aplicação mude automaticamente para o gasoduto secundário se não conseguir atingir os seus dados através do pipeline primário.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Passos seguintes

Na primeira parte da série, aprendeu a disponibilizar uma aplicação altamente disponível com contas de armazenamento RA-GRS.

Avance para a parte dois da série para saber como simular uma falha e forçar a aplicação a utilizar o ponto final RA-GRS secundário.

> [!div class="nextstepaction"]
> [Simular uma falha na leitura da região primária](storage-simulate-failure-ragrs-account-app.md)
