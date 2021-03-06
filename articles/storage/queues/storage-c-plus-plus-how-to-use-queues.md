---
title: Como utilizar o armazenamento de fila (C++) - Armazenamento Azure
description: Saiba como utilizar o serviço de armazenamento de fila em Azure. As amostras estão escritas em C++.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 4fe543010df9514cb2b22c56482a4b592574e917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941769"
---
# <a name="how-to-use-queue-storage-from-c"></a>Como utilizar o Armazenamento de Filas a partir do C++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia irá mostrar-lhe como realizar cenários comuns utilizando o serviço de armazenamento de fila Azure. Os exemplos são escritos no C++ e utilizam a [Biblioteca de Cliente de Armazenamento do Microsoft Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Os cenários abordados incluem **a inserção,** **espreitar,** **receber**e **apagar** mensagens de fila, bem como criar e **apagar filas.**

> [!NOTE]
> Este guia destina-se à Biblioteca de Clientes de Armazenamento do Microsoft Azure para C++ versão 1.0.0 e acima. A versão recomendada é Storage Client Library 2.2.0, que está disponível através de [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação C++
Neste guia, utilizará funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação C++.

Para tal, terá de instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++ e criar uma conta de armazenamento do Azure na sua subscrição do Azure.

Para instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++, pode utilizar os seguintes métodos:

* **Linux:** Siga as instruções dadas na Biblioteca de Clientes de [Armazenamento Azure para C++ README: Começar na página do Linux.](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)
* **Janelas:** No Windows, use [vcpkg](https://github.com/microsoft/vcpkg) como gerente de dependência. Siga o [início rápido](https://github.com/microsoft/vcpkg#quick-start) para inicializar os vcpkg. Em seguida, utilize o seguinte comando para instalar a biblioteca:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Pode encontrar um guia para como construir o código fonte e exportar para nuGet no ficheiro [README.](https://github.com/Azure/azure-storage-cpp#download--install)

## <a name="configure-your-application-to-access-queue-storage"></a>Configure a sua aplicação para aceder ao Armazenamento de Filas
Adicione as seguintes declarações no topo do ficheiro C++ onde pretende utilizar as APIs de armazenamento Azure para aceder às filas:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento Azure
Os clientes do Armazenamento do Azure utilizam uma cadeia de ligação de armazenamento para armazenar pontos finais e credenciais para aceder a serviços de gestão de dados. Ao executar uma aplicação cliente, deve fornecer a cadeia de ligação de armazenamento no seguinte formato, utilizando o nome da sua conta de armazenamento e a chave de acesso ao armazenamento para a conta de armazenamento listada no [Portal Azure](https://portal.azure.com) para os valores *De Nome* de Conta e Chave *de Conta.* Para obter informações sobre contas de armazenamento e chaves de acesso, consulte sobre as Contas de [Armazenamento Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Este exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Para testar a sua aplicação no seu computador Windows local, pode utilizar o [emulador](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) de armazenamento Microsoft Azure que está instalado com o [Azure SDK](https://azure.microsoft.com/downloads/). O emulador de armazenamento é um utilitário que simula os serviços Blob, Queue e Table disponíveis em Azure na sua máquina de desenvolvimento local. O seguinte exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação para o seu emulador local de armazenamento:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar o emulador de armazenamento Azure, selecione o botão **Iniciar** ou prima a tecla **Windows.** Comece a digitar **o Emulador**de Armazenamento Azure e selecione **o Emulador** de Armazenamento Do Microsoft Azure da lista de aplicações.

Os exemplos seguintes partem do princípio de que utiliza um destes dois métodos para obter a cadeia de ligação de armazenamento.

## <a name="retrieve-your-connection-string"></a>Obter a sua cadeia de ligação
Pode utilizar a **classe cloud_storage_account** para representar as informações da sua Conta de Armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método **analisar**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
Um objeto **cloud_queue_client** permite obter objetos de referência para filas. O código seguinte cria um objeto **cloud_queue_client.**

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Utilize **o** cloud_queue_client objeto para obter uma referência à fila que pretende utilizar. Pode criar a fila se não existir.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem numa fila
Para inserir uma mensagem numa fila existente, crie primeiro um novo **cloud_queue_message**. Em seguida, chame o **método add_message.** Um **cloud_queue_message** pode ser criado a partir de uma cadeia ou de um matriz **byte.** Eis o código que cria uma fila (se não existir) e introduz a mensagem "Olá, Mundo":

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Espreitar a próxima mensagem
Pode espreitar a mensagem na frente de uma fila sem a retirar da fila, chamando o método **peek_message.**

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O seguinte código atualiza a mensagem de filas com novos conteúdos e expande o tempo limite de visibilidade em 60 segundos. Isto guarda o estado do trabalho associado à mensagem e atribui ao cliente outro minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar fluxos de trabalho de vários passos em mensagens de filas, sem ser necessário recomeçar do início se falhar um passo de processamento devido a uma falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e se a mensagem for repetida mais do que n vezes, deveria eliminá-la. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-de-queue-the-next-message"></a>Como: Desfilar a próxima mensagem
O código remove uma mensagem da fila em dois passos. Quando **ligas get_message,** recebes a próxima mensagem numa fila. Uma mensagem devolvida de **get_message** torna-se invisível para qualquer outra mensagem de leitura de código desta fila. Para terminar de remover a mensagem da fila, também deve ligar **para delete_message**. Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seu código chama **delete_message** logo após a mensagem ter sido processada.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Como: Aproveitar opções adicionais para mensagens de desfilar
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem. O exemplo de código que se segue utiliza o método **get_messages** para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem usando um **para** loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Note que os 5 minutos começam para todas as mensagens ao mesmo tempo, pelo que após 5 minutos se passaram desde a chamada para **get_messages**, quaisquer mensagens que não tenham sido apagadas voltarão a ser visíveis.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O método **download_attributes** pede ao serviço de fila para recuperar os atributos da fila, incluindo a contagem de mensagens. O método **approximate_message_count** recebe o número aproximado de mensagens na fila.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Como: Apagar uma fila
Para eliminar uma fila e todas as mensagens contidas na seleção, ligue para o **método delete_queue_if_exists** no objeto de fila.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico do armazenamento de fila, siga estes links para saber mais sobre o Armazenamento Azure.

* [Como utilizar o Blob Storage a partir de C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como utilizar o Armazenamento de Mesa a partir de C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Lista De recursos de armazenamento Azure em C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Biblioteca do Cliente de Armazenamento para Referência C++](https://azure.github.io/azure-storage-cpp)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
