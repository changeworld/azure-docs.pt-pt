---
title: 'Tutorial: Tutorial de distribuição global Azure Cosmos DB para a API SQL'
description: 'Tutorial: Aprenda a configurar a distribuição global da Azure Cosmos DB utilizando a API SQL com .Net, Java, Python e vários outros SDKs'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: 177973cf766a6215453f3062c0fb206961649272
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80295530"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Tutorial: Criar distribuição global da Azure Cosmos DB utilizando a API SQL

Neste artigo, vamos mostrar como utilizar o portal do Azure para configurar a distribuição global do Azure Cosmos DB e, em seguida, ligar através da API SQL.

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configure a distribuição global utilizando as [APIs SQL](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>Ligar a uma região preferencial com a API SQL

Para tirar o máximo partido da [distribuição global](distribute-data-globally.md), as aplicações cliente podem especificar a lista de preferência ordenada de regiões a utilizar nas operações de documentos. Isto pode ser feito ao definir a política de ligação. Com base na configuração da conta do Azure Cosmos DB, disponibilidade regional atual e lista de preferência especificada, o SDK SQL selecionará o ponto final ideal para efetuar operações de escrita e leitura.

Esta lista de preferência é especificada ao inicializar uma ligação com os SDKs SQL. Os SDKs aceitam um parâmetro opcional "PreferredLocations" que é uma lista ordenada de regiões do Azure.

O SDK enviará automaticamente todas as escritas para a região de escrita atual.

Todas as leituras serão enviadas para a primeira região disponível na lista PreferredLocations. Se o pedido falhar, o cliente falhará a lista para a região seguinte e assim sucessivamente.

Os SDKs só tentarão ler a partir das regiões especificadas em PreferredLocations. Por exemplo, se a Conta de Base de Dados estiver disponível em quatro regiões, mas o cliente apenas especificar duas regiões de leitura (não escrita) para PreferredLocations, não serão fornecidas leituras fora da região de leitura não especificada em PreferredLocations. Se as regiões de leitura especificadas em PreferredLocations não estiverem disponíveis, serão fornecidas leituras fora da região de escrita.

A aplicação pode verificar o ponto final de escrita atual e o ponto final de leitura escolhido pelo SDK ao consultar duas propriedades, WriteEndpoint e ReadEndpoint, disponíveis na versão 1.8 e posterior do SDK.

Se a propriedade PreferredLocations não estiver definida, todos os pedidos serão fornecidos a partir da região de escrita atual.

## <a name="net-sdk"></a>SDK .NET
O SDK pode ser utilizado sem quaisquer alterações de código. Neste caso, o SDK direciona automaticamente as leituras e as escritas para a região de escrita atual.

Na versão 1.8 e posterior do SDK .NET, o parâmetro ConnectionPolicy do construtor DocumentClient tem uma propriedade denominada Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Esta propriedade é do tipo de Coleção `<string>` e deve conter uma lista de nomes de região. Os valores de cadeia são formatados pela coluna Nome da Região na página [Regiões do Azure][regions], sem espaços antes ou depois do primeiro e do último caráter, respetivamente.

Os pontos finais de escrita e leitura atuais estão disponíveis em DocumentClient.WriteEndpoint e DocumentClient.ReadEndpoint, respetivamente.

> [!NOTE]
> Os URLs para os pontos finais não devem ser considerados como constantes de longa duração. O serviço pode atualizá-los em qualquer momento. O SDK processa esta alteração automaticamente.
>
>

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

## <a name="nodejsjavascript"></a>Node.js/JavaScript

> [!NOTE]
> Os URLs para os pontos finais não devem ser considerados como constantes de longa duração. O serviço pode atualizá-los em qualquer momento. O SDK processará esta alteração automaticamente.
>
>

Abaixo está um exemplo de código para Node.js/Javascript.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>SDK Python

O seguinte código mostra como definir locais preferidos utilizando o Python SDK:

```python

connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v2-sdk"></a>Java V2 SDK

O seguinte código mostra como definir locais preferidos utilizando o Java SDK:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Arrays.asList("East US", "West US", "Canada Central"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKeyOrResourceToken(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy)
                .build();
```

## <a name="rest"></a>REST
Depois de uma conta de base de dados ter sido disponibilizada em várias regiões, os clientes podem consultar a respetiva disponibilidade ao efetuar um pedido GET no URI seguinte.

    https://{databaseaccount}.documents.azure.com/

O serviço devolverá uma lista de regiões e os URIs de ponto final correspondentes do Azure Cosmos DB para as réplicas. A região de escrita atual será indicada na resposta. Em seguida, o cliente pode selecionar o ponto final adequado para obter todos os pedidos da API REST da seguinte forma.

Resposta de exemplo

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Todos os pedidos PUT, POST e DELETE têm de ir para o URI de escrita indicado
* Todos os GETs e outros pedidos de leitura (por exemplo consultas) podem ir para qualquer ponto final da escolha do cliente

Os pedidos de escrita para regiões só de leitura falharão com o código de erro HTTP 403 ("Proibido").

Se a região de escrita mudar após a fase inicial de descoberta do cliente, as escritas subsequentes para a região de escrita anterior falharão com o código de erro HTTP 403 ("Proibido"). Em seguida, o cliente deve obter novamente a lista de regiões para obter a região de escrita atualizada.

Já está, isto conclui este tutorial. Pode saber como gerir a consistência da sua conta replicada globalmente ao ler [Níveis de consistência no Azure Cosmos DB](consistency-levels.md). Para obter mais informações sobre como funciona a replicação de base de dados global no Azure Cosmos DB, veja [Distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global com as APIs SQL

Agora, pode avançar para o próximo tutorial para saber como desenvolver localmente com o emulador local do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Desenvolver localmente com o emulador](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

