---
title: Change feed no Azure Cosmos DB API para Cassandra
description: Aprenda a usar o feed de mudança no Azure Cosmos DB API para cassandra obter as alterações feitas aos seus dados.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74694626"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Change feed no Azure Cosmos DB API para Cassandra

O suporte [para alimentação](change-feed.md) de mudanças no Azure Cosmos DB API para Cassandra está disponível através dos predicados de consulta na Linguagem Cassandra Query (CQL). Utilizando estas condições predicadas, pode consultar a api de alimentação de alterações. As aplicações podem obter as alterações feitas numa tabela utilizando a chave primária (também conhecida como chave de partição) como é exigido no CQL. Em seguida, pode tomar mais medidas com base nos resultados. As alterações nas linhas da tabela são capturadas na ordem do seu tempo de modificação e a ordem de classificação é garantida por chave de partição.

O exemplo que se segue mostra como obter um feed de mudança em todas as linhas de uma tabela De Espaço Chave Cassandra API usando .NET. O predicado COSMOS_CHANGEFEED_START_TIME() é utilizado diretamente no CQL para consultar itens no feed de alteração a partir de um determinado tempo de início (neste caso, data de data atual). Você pode baixar a amostra completa [aqui](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

Em cada iteração, a consulta retoma no último ponto as alterações foram lidas, usando o estado de paging. Podemos ver um fluxo contínuo de novas mudanças na tabela no Keyspace. Veremos alterações nas linhas que estão inseridas ou atualizadas. Atualmente, não é suportado o cuidado de eliminar as operações utilizando o feed de mudança na Cassandra API. 

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

Para obter as alterações para uma única linha por chave primária, pode adicionar a chave primária na consulta. O exemplo que se segue mostra como rastrear as mudanças para a linha onde "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações aplicam-se ao utilizar o feed de mudança com a Cassandra API:

* Inserções e atualizações são suportadas atualmente. A eliminação da operação ainda não está suportada. Como uma suver, pode adicionar um marcador suave nas linhas que estão a ser eliminadas. Por exemplo, adicione um campo na linha chamado "apagado" e coloque-o como "verdadeiro".
* A última atualização é persistida, uma vez que no núcleo SQL API e atualizações intermédias para a entidade não estão disponíveis.


## <a name="error-handling"></a>Processamento de erros

Os seguintes códigos de erro e mensagens são suportados quando se utiliza o feed de mudança na Cassandra API:

* Código de **erro HTTP 429** - Quando o feed de alteração é limitado, devolve uma página vazia.

## <a name="next-steps"></a>Passos seguintes

* [Gerir os recursos da API da Azure Cosmos DB Cassandra utilizando modelos de Gestor de Recursos Azure](manage-cassandra-with-resource-manager.md)
