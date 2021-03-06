---
title: Phoenix Query Server REST SDK - Azure HDInsight
description: Instale e utilize o REST SDK para o Servidor de Consulta Phoenix em Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 84c2bad1004029fe61dcfc19321957a170284587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612262"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Apache Phoenix Consulta Server REST SDK

[Apache Phoenix](https://phoenix.apache.org/) é uma camada de base de dados relacional massivamente paralela em cima de [Apache HBase](apache-hbase-overview.md). A Phoenix permite-lhe utilizar consultas semelhantes a SQL com HBase através de ferramentas SSH, como [o SQLLine](apache-hbase-query-with-phoenix.md). A Phoenix também fornece um servidor HTTP chamado Phoenix Query Server (PQS), um cliente fino que suporta dois mecanismos de transporte para a comunicação do cliente: JSON e Protocol Buffers. O Protocolo Buffers é o mecanismo padrão, e oferece uma comunicação mais eficiente do que a JSON.

Este artigo descreve como usar o PQS REST SDK para criar tabelas, linhas de upsert individualmente e a granel, e selecionar dados usando declarações SQL. Os exemplos usam o [controlador Microsoft .NET para Apache Phoenix Query Server](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Este SDK é construído com as [APIs Avatica da Apache Calcite,](https://calcite.apache.org/avatica/) que utilizam exclusivamente buffers protocolares para o formato de serialização.

Para mais informações, consulte [Apache Calcite Avatica Protocol Buffers Reference](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Instalar o SDK

O controlador Microsoft .NET para apache Phoenix Query Server é fornecido como um pacote NuGet, que pode ser instalado a partir da Consola de **Pacotes NuGet** do Estúdio Visual com o seguinte comando:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Instantiate novo objeto PhoenixClient

Para começar a usar a `PhoenixClient` biblioteca, instantaneamente um novo objeto, passando `ClusterCredentials` contendo o `Uri` seu cluster e o nome de utilizador e senha de utilizador Apache Hadoop do cluster.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Substitua o CLUSTERNAME pelo nome do cluster HDInsight HBase e userNAME e PASSWORD pelas credenciais Hadoop especificadas na criação do cluster. O nome de utilizador de Hadoop predefinido é **administrador**.

## <a name="generate-unique-connection-identifier"></a>Gerar identificador de ligação único

Para enviar um ou mais pedidos para PQS, você precisa incluir um identificador de ligação único para associar o(s) pedido com a conexão.

```csharp
string connId = Guid.NewGuid().ToString();
```

Cada exemplo primeiro faz `OpenConnectionRequestAsync` uma chamada para o método, passando no identificador de ligação único. Em seguida, defina `ConnectionProperties` e, `RequestOptions`passando esses objetos e o identificador de ligação gerado ao `ConnectionSyncRequestAsync` método. O objeto do `ConnectionSyncRequest` PQS ajuda a garantir que tanto o cliente como o servidor têm uma visão consistente das propriedades da base de dados.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest e as suas Propriedades de Conexão

Para `ConnectionSyncRequestAsync`ligar, passe `ConnectionProperties` um objeto.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Aqui estão algumas propriedades de interesse:

| Propriedade | Descrição |
| -- | -- |
| AutoCommit | Uma booleana denotando se `autoCommit` está habilitado para transações phoenix. |
| ReadOnly | Um booleano denotando se a ligação é apenas leitura. |
| Isolamento de Transações | Um inteiro que denote o nível de isolamento de transações de acordo com a especificação JDBC - consulte a tabela seguinte.|
| Catálogo | O nome do catálogo a utilizar ao recolher propriedades de ligação. |
| Esquema | O nome do esquema a utilizar ao recolher propriedades de ligação. |
| IsDirty | Um booleano denotando se as propriedades foram alteradas. |

Aqui estão os `TransactionIsolation` valores:

| Valor de isolamento | Descrição |
| -- | -- |
| 0 | As transações não são apoiadas. |
| 1 | Leituras sujas, leituras não repetíveis e leituras fantasma podem ocorrer. |
| 2 | São evitadas leituras sujas, mas podem ocorrer leituras não repetíveis e leituras fantasma. |
| 4 | Leituras sujas e leituras não repetíveis são evitadas, mas podem ocorrer leituras fantasma. |
| 8 | Leituras sujas, leituras não repetíveis e leituras fantasma são todas evitadas. |

## <a name="create-a-new-table"></a>Criar uma nova tabela

HBase, como qualquer outro RDBMS, armazena dados em tabelas. Phoenix usa consultas Padrão SQL para criar novas tabelas, ao mesmo tempo que define a chave primária e os tipos de colunas.

Este exemplo e todos os exemplos posteriores, use o `PhoenixClient` objeto instantâneo como definido em [Instantiate um novo objeto PhoenixClient](#instantiate-new-phoenixclient-object).

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

O exemplo anterior cria uma `Customers` nova `IF NOT EXISTS` tabela chamada usando a opção. A `CreateStatementRequestAsync` chamada cria uma nova declaração no servidor Avitica (PQS). O `finally` quarteirão fecha `CreateStatementResponse` o `OpenConnectionResponse` devolvido e os objetos.

## <a name="insert-data-individually"></a>Inserir dados individualmente

Este exemplo mostra uma inserção individual de dados, referindo uma `List<string>` coleção de abreviaturas de estado e território americanos:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

O valor `StateProvince` da coluna da tabela será utilizado numa operação posteriormente selecionada.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

A estrutura para executar uma declaração de inserção é semelhante à criação de uma nova tabela. No final do `try` bloco, a transação é explicitamente comprometida. Este exemplo repete uma transação de inserção 300 vezes. O exemplo seguinte mostra um processo de inserção de lote mais eficiente.

## <a name="batch-insert-data"></a>Dados de inserção do lote

O código que se segue é quase idêntico ao código de inserção individual dos dados. Este exemplo `UpdateBatch` utiliza o objeto `ExecuteBatchRequestAsync`numa chamada para, em vez de ligar `ExecuteRequestAsync` repetidamente com uma declaração preparada.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Num ambiente de teste, inserir individualmente 300 novos registos demorou quase 2 minutos. Em contraste, inserir 300 discos como lote requer apenas 6 segundos.

## <a name="select-data"></a>Selecionar dados

Este exemplo mostra como reutilizar uma ligação para executar múltiplas consultas:

1. Selecione todos os registos e, em seguida, obter os registos restantes após a dposição do máximo de 100.
2. Utilize uma declaração selecionada para a contagem total da linha para recuperar o resultado único do escalar.
3. Execute uma declaração selecionada que deretenha o número total de clientes por estado ou território.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

A saída `select` das declarações deve ser o seguinte resultado:

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Passos seguintes

* [Apache Phoenix no HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Usando o Apache HBase REST SDK](apache-hbase-rest-sdk.md)
