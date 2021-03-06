---
title: Começar com armazenamento de mesa usando Visual Studio (serviços na nuvem)
description: Como começar a usar o armazenamento da Mesa Azure num projeto de serviço em nuvem no Estúdio Visual depois de se ligar a uma conta de armazenamento usando serviços conectados do Estúdio Visual
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c42d65b5e2c46fcdbe1b0725f2ebce881722db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299988"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução aos serviços ligados (projetos de serviços cloud) Armazenamento de Tabelas do Azure e o Visual Studio
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a usar o armazenamento de mesa Azure no Visual Studio depois de ter criado ou referenciado uma conta de armazenamento Azure num projeto de serviços na nuvem utilizando o diálogo Visual Studio **Add Connected Services.** A operação **Add Connected Services** instala os pacotes NuGet apropriados para aceder ao armazenamento do Azure no seu projeto e adiciona o fio de ligação para a conta de armazenamento aos seus ficheiros de configuração do projeto.

O serviço de armazenamento de mesa azure permite armazenar grandes quantidades de dados estruturados. O serviço é uma loja de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Para começar, primeiro precisa criar uma mesa na sua conta de armazenamento. Vamos mostrar-lhe como criar uma tabela Azure em código, e também como realizar operações básicas de tabela e entidade, tais como adicionar, modificar, ler e ler entidades de mesa. As amostras estão\# escritas em código C e utilizam a [biblioteca de clientes do Microsoft Azure Storage para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**NOTA:** Algumas das APIs que realizam chamadas para o armazenamento do Azure são assíncronas. Consulte [a programação assíncrona com Async e aguarde](https://msdn.microsoft.com/library/hh191443.aspx) mais informações. O código abaixo pressupõe que estão a ser utilizados métodos de programação assinizadores.

* Consulte [O Get começou com o armazenamento da Mesa Azure utilizando .NET](../storage/storage-dotnet-how-to-use-tables.md) para obter mais informações sobre tabelas de manipulação programática.
* Consulte [a documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Armazenamento Azure.
* Consulte [a documentação dos Serviços Cloud](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços de nuvem Azure.
* Consulte [ASP.NET](https://www.asp.net) para obter mais informações sobre a programação ASP.NET aplicações.

## <a name="access-tables-in-code"></a>Tabelas de acesso em código
Para aceder a tabelas em projetos de serviço na nuvem, você precisa incluir os seguintes itens para quaisquer ficheiros de origem C# que acedam ao armazenamento de mesa Azure.

1. Certifique-se de que as declarações de espaço de nome no topo do ficheiro C# incluem estas **utilizações.**
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de ligação de armazenamento e conta de armazenamento da configuração do serviço Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Utilize todo o código acima em frente ao código nas seguintes amostras.
   > 
   > 
3. Obtenha um objeto **CloudTableClient** para fazer referência aos objetos de mesa na sua conta de armazenamento.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Obtenha um objeto de referência **CloudTable** para fazer referência a uma tabela e entidades específicas.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Criar uma tabela em código
Para criar a tabela Azure, basta adicionar uma chamada para **CreateIfNotExistsAsync** ao seguinte obter um objeto **CloudTable,** tal como descrito na secção "Tabelas de Acesso em código".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. O código seguinte define uma classe de entidade chamada **CustomerEntity** que usa o primeiro nome do cliente como chave de linha e o último nome como chave de partição.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

As operações de mesa envolvendo entidades são feitas usando o objeto **CloudTable** que criou anteriormente em "Tabelas de Acesso em código". O objeto **TableOperation** representa a operação a ser feita. O exemplo de código que se segue mostra como criar um objeto **CloudTable** e um objeto **CustomerEntity.** Para preparar a operação, é criada uma **TableOperation** para inserir a entidade cliente na tabela. Finalmente, a operação é executada através do chamado **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades
Pode inserir várias entidades numa tabela numa única operação de escrita. O exemplo de código seguinte cria dois objetos de entidade ("Jeff Smith" e "Ben Smith"), adiciona-os a um objeto **tableBatchOperation** utilizando o método Insert e, em seguida, inicia a operação chamando **CloudTable.ExecuteBatchAsync**.

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";

    // Create another customer entity and add it to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";

    // Add both customer entities to the batch insert operation.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);

    // Execute the batch operation.
    await peopleTable.ExecuteBatchAsync(batchOperation);

## <a name="get-all-of-the-entities-in-a-partition"></a>Obter todas as entidades em uma partição
Para consultar uma tabela para todas as entidades numa partição, utilize um objeto **tablequery.** O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
            Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


## <a name="get-a-single-entity"></a>Obter uma única entidade
Pode escrever uma consulta para obter uma entidade única e específica. O código seguinte utiliza um objeto **TableOperation** para especificar um cliente chamado 'Ben Smith'. Este método devolve apenas uma entidade, em vez de uma coleção, e o valor devolvido em **TableResult.Result** é um objeto **CustomerEntity.** Especificar as teclas de partição e de linha numa consulta é a forma mais rápida de recuperar uma única entidade do serviço **De Mesa.**

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Eliminar uma entidade
Pode apagar uma entidade depois de a encontrar. O código seguinte procura uma entidade cliente chamada "Ben Smith", e se o encontrar, elimina-o.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation and then execute it.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       await peopleTable.ExecuteAsync(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

