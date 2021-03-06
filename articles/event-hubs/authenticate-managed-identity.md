---
title: Autenticação de uma identidade gerida com Diretório Ativo Azure
description: Este artigo fornece informações sobre a autenticação de uma identidade gerida com o Azure Ative Directory para aceder aos recursos do Azure Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 672b663a9cab72d465ea00e0a5ade364eadbf64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251534"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticar uma identidade gerida com o Diretório Ativo Azure para aceder aos Recursos hubs do Evento
O Azure Event Hubs apoia a autenticação azure Ative Directory (Azure AD) com [identidades geridas para os recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md) Identidades geridas para recursos Azure podem autorizar o acesso aos recursos do Event Hubs utilizando credenciais de AD Azure de aplicações em execução em Máquinas Virtuais Azure (VMs), aplicações de função, conjuntos de escala de máquina virtual e outros serviços. Ao utilizar identidades geridas para recursos Azure juntamente com a autenticação da AD Azure, pode evitar armazenar credenciais com as suas aplicações que funcionam na nuvem.

Este artigo mostra como autorizar o acesso a um centro de eventos utilizando uma identidade gerida a partir de um Azure VM.

## <a name="enable-managed-identities-on-a-vm"></a>Ativar identidades geridas num VM
Antes de poder utilizar identidades geridas para a Azure Resources para autorizar os recursos do Event Hubs a partir do seu VM, tem primeiro de ativar identidades geridas para os Recursos Azure no VM. Para aprender a ativar identidades geridas para os Recursos Azure, consulte um destes artigos:

- [Portal Azure](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes do Gestor de Recursos Azure](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerida em Azure AD
Para autorizar um pedido ao serviço Event Hubs a partir de uma identidade gerida na sua aplicação, configurar as definições de controlo de acesso baseado em funções (RBAC) para essa identidade gerida. O Azure Event Hubs define funções RBAC que englobam permissões para envio e leitura a partir de Centros de Eventos. Quando a função RBAC é atribuída a uma identidade gerida, a identidade gerida é dada acesso aos dados do Event Hubs no âmbito apropriado.

Para obter mais informações sobre a atribuição de funções RBAC, consulte [Authenticate com Diretório Ativo Azure para acesso aos recursos do Event Hubs.](authorize-access-azure-active-directory.md)

## <a name="use-event-hubs-with-managed-identities"></a>Utilizar Hubs de Eventos com identidades geridas
Para utilizar os Centros de Eventos com identidades geridas, é necessário atribuir à identidade o papel e o âmbito adequado. O procedimento nesta secção utiliza uma aplicação simples que funciona sob uma identidade gerida e acessa recursos do Event Hubs.

Aqui estamos usando uma aplicação web de amostra alojada no [Azure App Service](https://azure.microsoft.com/services/app-service/). Para instruções passo a passo para criar uma aplicação web, consulte [Criar uma aplicação web core ASP.NET em Azure](../app-service/app-service-web-get-started-dotnet.md)

Uma vez criada a aplicação, siga estes passos: 

1. Vá a **Definições** e selecione **Identidade**. 
1. Selecione o **Estado** a **estar ligado**. . 
1. Selecione **Guardar** para guardar a definição. 

    ![Identidade gerida para uma aplicação web](./media/authenticate-managed-identity/identity-web-app.png)

Uma vez ativado esta configuração, uma nova identidade de serviço é criada no seu Diretório Ativo Azure (Azure AD) e configurada no anfitrião do Serviço de Aplicações.

Agora, atribua esta identidade de serviço a um papel no âmbito exigido nos recursos do seu Evento Hubs.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções RBAC utilizando o portal Azure
Para atribuir um papel aos recursos do Event Hubs, navegue para esse recurso no portal Azure. Mostrar as definições de Controlo de Acesso (IAM) para o recurso e seguir estas instruções para gerir as atribuições de funções:

> [!NOTE]
> Os seguintes passos atribuem uma função de identidade de serviço aos espaços de nome do Seu Event Hubs. Pode seguir os mesmos passos para atribuir uma função de recurso do Event Hubs. 

1. No portal Azure, navegue para o espaço de nome dos Hubs de Eventos e exiba a **visão geral** para o espaço de nome. 
1. Selecione **Control de Acesso (IAM)** no menu esquerdo para visualizar as definições de controlo de acesso para o centro do evento.
1.  Selecione o separador de **atribuições de funções** para ver a lista de atribuições de papéis.
3.  Selecione **Adicionar** para adicionar um novo papel.
4.  Na página de atribuição de **funções Add,** selecione as funções de Event Hubs que pretende atribuir. Em seguida, procure localizar a identidade de serviço que tinha registado para atribuir o papel.
    
    ![Adicionar página de atribuição de papéis](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Selecione **Guardar**. A identidade a quem atribuiu o papel aparece listada nesse papel. Por exemplo, a imagem que se segue mostra que a identidade do serviço tem o proprietário de Dados do Event Hubs.
    
    ![Identidade atribuída a um papel](./media/authenticate-managed-identity/role-assigned.png)

Uma vez atribuído o papel, a aplicação web terá acesso aos recursos do Event Hubs sob o âmbito definido. 

### <a name="test-the-web-application"></a>Testar a aplicação Web
1. Crie um espaço de nome sinuoso do Event Hubs e um centro de eventos. 
2. Implemente a aplicação web para o Azure. Consulte a seguinte secção de separadores para obter links para a aplicação web no GitHub. 
3. Certifique-se de que o EnvioReceber.aspx é definido como o documento predefinido para a aplicação web. 
3. Ativar **a identidade** para a aplicação web. 
4. Atribuir esta identidade ao papel de Proprietário de Dados do **Event Hubs** ao nível do espaço de nome ou ao nível do hub do evento. 
5. Executar a aplicação web, introduzir o nome do espaço de nome e o nome do centro de eventos, uma mensagem, e selecionar **Enviar**. Para receber o evento, selecione **Receber**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs (mais recente)](#tab/latest)
Agora pode lançar a sua aplicação web e apontar o seu navegador para a página de aspx da amostra. Pode encontrar a aplicação web da amostra que envia e recebe dados dos recursos do Event Hubs no [repo GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Instale o mais recente pacote a partir de [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/), e comece a enviar eventos para Event **HubProducerClient** e receber eventos usando **EventHubConsumerClient**. 

> [!NOTE]
> Para uma amostra de Java que usa uma identidade gerida para publicar eventos num centro de eventos, consulte publicar eventos com amostra de [identidade Azure no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (legado)](#tab/old)
Agora pode lançar a sua aplicação web e apontar o seu navegador para a página de aspx da amostra. Pode encontrar a aplicação web da amostra que envia e recebe dados dos recursos do Event Hubs no [repo GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Instale o mais recente pacote a partir do [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)e comece a enviar e receber dados dos hubs do Evento utilizando o EventHubClient, como mostra o seguinte código: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Hubs de Eventos para Kafka
Pode utilizar aplicações Apache Kafka para enviar mensagens e receber mensagens de Azure Event Hubs utilizando a identidade gerida OAuth. Consulte a seguinte amostra no GitHub: [Event Hubs para Kafka - envie e receba mensagens usando a identidade gerida OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Amostras
- **Amostras de Azure.Messaging.EventHubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Microsoft.Azure.EventHubs amostras](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Estas amostras utilizam a antiga biblioteca **Microsoft.Azure.EventHubs,** mas pode facilmente atualizá-la para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.** Para mover a amostra da utilização da antiga biblioteca para uma nova, consulte o [Guia para migrar de Microsoft.Azure.EventHubs para Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).
    Esta amostra foi atualizada para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.**
- [Hubs de eventos para Kafka - envie e receba mensagens usando identidade gerida OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Passos seguintes
- Veja o seguinte artigo para conhecer identidades geridas para os recursos do Azure: [O que são as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Consulte os seguintes artigos relacionados:
    - [Autenticação solicita aos Hubs de Eventos Azure de uma aplicação que usa o Diretório Ativo Azure](authenticate-application.md)
    - [Autenticar pedidos aos Hubs de Eventos Azure usando assinaturas de acesso partilhado](authenticate-shared-access-signature.md)
    - [Autorizar acesso aos recursos do Event Hubs utilizando o Diretório Ativo Azure](authorize-access-azure-active-directory.md)
    - [Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md)