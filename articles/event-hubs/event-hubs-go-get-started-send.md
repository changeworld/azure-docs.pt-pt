---
title: 'Quickstart: Enviar e receber eventos usando Go - Azure Event Hubs'
description: 'Quickstart: Este artigo fornece um walkthrough para criar uma aplicação Go que envia eventos de Azure Event Hubs.'
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: e5f52d0ddbf9a66d974732d6d98ca8a5b09cc2d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720584"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Quickstart: Envie eventos ou receba eventos de Centros de Eventos usando Go
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como escrever aplicações Go para enviar eventos para ou receber eventos de um centro de eventos. 

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste tutorial para criar a sua própria.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Vá instalado localmente. Siga [estas instruções,](https://golang.org/doc/install) se necessário.
- Uma conta ativa do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita][] antes de começar.
- **Crie um espaço de nome sinuoso do Event Hubs e um centro de eventos.** Utilize o [portal Azure](https://portal.azure.com) para criar um espaço de nome de Hubs de Eventos tipo, e obtenha as credenciais de gestão que a sua aplicação precisa para comunicar com o hub do evento. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo.](event-hubs-create.md)

## <a name="send-events"></a>Enviar eventos
Esta secção mostra-lhe como criar uma aplicação Go para enviar eventos para um centro de eventos. 

### <a name="install-go-package"></a>Instalar pacote Go

Obtenha o pacote Go para `go get` `dep`Centros de Eventos com ou . Por exemplo:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Importar pacotes no seu ficheiro de código

Para importar os pacotes Go, utilize o seguinte exemplo de código:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Criar um principal de serviço

Crie um novo diretor de serviço seguindo as instruções em [Create a Azure service principal com o Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Guarde as credenciais fornecidas no seu ambiente com os seguintes nomes. Tanto os pacotes Azure SDK for Go como os Event Hubs são reconfigurados para procurar estes nomes variáveis:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Agora, crie um provedor de autorização para o seu cliente Event Hubs que utilize estas credenciais:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Criar cliente de Hubs de Eventos

O seguinte código cria um cliente do Event Hubs:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Escrever código para enviar mensagens

No seguinte corte, utilize (1) para enviar mensagens interativamente a partir de um terminal, ou (2) para enviar mensagens dentro do seu programa:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Adicionais

Obtenha as iDs das divisórias no seu centro de eventos:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Execute a aplicação para enviar eventos para o centro do evento. 

Parabéns! Enviou agora mensagens para um hub de eventos.

## <a name="receive-events"></a>Receber eventos

### <a name="create-a-storage-account-and-container"></a>Criar uma conta de armazenamento e um recipiente

Estados como arrendamentos em divisórias e postos de controlo no fluxo de eventos são partilhados entre recetores usando um recipiente de armazenamento Azure. Pode criar uma conta de armazenamento e um recipiente com o Go SDK, mas também pode criar um seguindo as instruções nas contas de [armazenamento sobre o Azure.](../storage/common/storage-create-storage-account.md)

As amostras para a criação de artefactos de armazenamento com o Go SDK estão disponíveis no repo de [amostras Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) e na amostra correspondente a este tutorial.

### <a name="go-packages"></a>Ir pacotes

Para receber as mensagens, obtenha os pacotes Go para Centros de Eventos com `go get` ou: `dep`

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Importar pacotes no seu ficheiro de código

Para importar os pacotes Go, utilize o seguinte exemplo de código:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Criar um principal de serviço

Crie um novo diretor de serviço seguindo as instruções em [Create a Azure service principal com o Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Guarde as credenciais fornecidas no seu ambiente com os seguintes nomes: Tanto o pacote Azure SDK para Go e Event Hubs são reconfigurados para procurar estes nomes variáveis.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Em seguida, crie um provedor de autorização para o seu cliente Event Hubs que utilize estas credenciais:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Obter estrutura de metadados

Obtenha uma estrutura com metadados sobre o seu ambiente Azure usando o Azure Go SDK. As operações posteriores utilizam esta estrutura para encontrar pontos finais corretos.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Criar ajudante credencial 

Crie um ajudante credencial que utilize as credenciais anteriores do Azure Ative Directory (AAD) para criar uma credencial de assinatura de acesso partilhado (SAS) para armazenamento. O último parâmetro diz a este construtor para usar as mesmas variáveis ambientais utilizadas anteriormente:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Crie um ponteiro de verificação e um leaser 

Crie um **leaser**, responsável por alugar uma divisória a um determinado recetor, e um ponteiro de **verificação,** responsável pela escrita de pontos de verificação para o fluxo de mensagens para que outros recetores possam começar a ler a partir da compensação correta.

Atualmente, está disponível um único **StorageLeaserCheckpointer** que utiliza o mesmo recipiente de armazenamento para gerir tanto os alugueres como os pontos de verificação. Para além da conta de armazenamento e dos nomes dos contentores, o **StorageLeaserCheckpointer** necessita da credencial criada na etapa anterior e do ambiente Azure para aceder corretamente ao recipiente.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Construir anfitrião de processador de eventos

Tem agora as peças necessárias para construir um Anfitrião de Processadorde Eventos, da seguinte forma. O mesmo **StorageLeaserCheckpointer** é utilizado como um leaser e um ponteiro de verificação, como descrito anteriormente:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Criar manipulador 

Agora crie um manipulador e registe-o com o Anfitrião do Processador de Eventos. Quando o anfitrião é iniciado, aplica-se este e quaisquer outros manipuladores especificados às mensagens de entrada:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Escrever código para receber mensagens

Com tudo configurado, pode iniciar o `Start(context)` Anfitrião do Processador de `StartNonBlocking(context)` Eventos com para mantê-lo em funcionamento permanente, ou com para funcionar apenas enquanto as mensagens estiverem disponíveis.

Este tutorial começa e corre da seguinte forma; ver a amostra GitHub, `StartNonBlocking`por exemplo, utilizando:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
