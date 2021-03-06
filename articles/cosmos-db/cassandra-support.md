---
title: Funcionalidades do Apache Cassandra suportadas pela API para Cassandra do Azure Cosmos DB
description: Saiba mais sobre o suporte de funcionalidades do Apache Cassandra na API para Cassandra do Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 223544f7ceddce6bc2071d561da1cff1c0d4b53b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420154"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funcionalidades do Apache Cassandra suportadas pela API para Cassandra do Azure Cosmos DB 

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode comunicar com a API para Cassandra do Azure Cosmos DB através de [controladores](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) open source de cliente do Cassandra compatíveis com o [protocolo de invocação](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) da Linguagem de Consulta do Cassandra (CQL) v4. 

Ao utilizar a API para Cassandra do Azure Cosmos DB, pode desfrutar dos benefícios das APIs para Apache Cassandra, bem como das funcionalidades proporcionadas pelo Azure Cosmos DB. As funcionalidades empresariais incluem [distribuição global](distribute-data-globally.md), [criação automática de partições de aumento horizontal](partition-data.md), garantias de disponibilidade e latência, encriptação de dados inativos, cópias de segurança e mais.

## <a name="cassandra-protocol"></a>Protocolo do Cassandra 

A API para Cassandra do Azure Cosmos DB é compatível com a versão **v4** do CQL. Os comandos, as ferramentas, as limitações e as exceções de CQL suportados encontram-se listados abaixo. Qualquer controlador de cliente que entenda estes protocolos deverá conseguir ligar à API para Cassandra do Azure Cosmos DB.

## <a name="cassandra-driver"></a>Controlador do Cassandra

As seguintes versões de controladores do Cassandra são suportadas pela API para Cassandra do Azure Cosmos DB:

* [Java 3.5 e posterior](https://github.com/datastax/java-driver)  
* [C# 3.5 e posterior](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5 e posterior](https://github.com/datastax/nodejs-driver)  
* [Python 3.15 e posterior](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>Tipos de dados de CQL 

A API para Cassandra do Azure Cosmos DB suporta os seguintes tipos de dados de CQL:

* ascii  
* bigint  
* blob  
* boolean  
* counter  
* date  
* decimal  
* double  
* float  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* texto  
* hora  
* carimbo de data/hora  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>Funções de CQL

A API para Cassandra do Azure Cosmos DB suporta as seguintes funções de CQL:

* Certificado de  
* Funções de agregação
  * min, max, avg, contagem
* Funções de conversão de blobs 
  * typeAsBlob(valor)  
  * blobAsType(valor)
* Funções UUID e timeuuid 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(carimbo de data/hora)  
  * toUnixTimestamp(carimbo de data/hora)  
  * toTimestamp(data)  
  * toUnixTimestamp(data) 
  


## <a name="cassandra-api-limits"></a>Limites da API para Cassandra

A API para Cassandra do Azure Cosmos DB não tem limites quanto ao tamanho dos dados armazenados nas tabelas. É possível armazenar centenas de terabytes ou de petabytes de dados sem desrespeitar os limites da chave de partição. Da mesma forma, todas as entidades ou equivalentes de linha não têm limites no número de colunas. No entanto, a dimensão total da entidade não deve exceder 2 MB. Os dados por chave de partição não podem exceder 20 GB como em todas as outras APIs.

## <a name="tools"></a>Ferramentas 

A API para Cassandra do Azure Cosmos DB é uma plataforma de serviço gerida. Não necessita de gestão nem de utilitários como o Recoletor de Lixo, a Máquina Virtual de Java (JVM) e o nodetool para gerir o cluster. Esta API suporta ferramentas como o cqlsh, que utiliza a compatibilidade com CQLv4 Binária. 

* O explorador de dados do portal Azure, métricas, diagnósticos de registo, PowerShell e CLI são outros mecanismos suportados para gerir a conta.

## <a name="cql-shell"></a>Shell de CQL  

O utilitário da linha de comando CQLSH vem com Apache Cassandra 3.1.1 e funciona fora da caixa, definindo algumas variáveis ambientais.

**Janelas:**

Se utilizar as janelas, recomendamos que ative o sistema de [ficheiros Windows para o Linux](https://docs.microsoft.com/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Em seguida, pode seguir os comandos linux abaixo.

**Unix/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

## <a name="cql-commands"></a>Comandos de CQL

O Azure Cosmos DB suporta os seguintes comandos de base de dados nas contas da API para Cassandra.

* CRIAR ESPAÇO CHAVE (As definições de replicação para este comando são ignoradas)
* CREATE TABLE 
* ÍNDICE DE CRIAÇÃO (sem especificar o nome do índice e índices congelados completos ainda não suportados)
* PERMITIR A FILTRAGEM
* ALTER TABLE 
* USE 
* INSERT 
* SELECIONAR 
* UPDATE 
* BATCH – só são suportados comandos arquivados 
* DELETE

Todas as operações crud que são executadas através de um SDK compatível com CQL v4 devolverão informações extra sobre unidades de erro e pedidos consumidas. Os comandos DELETE e UPDATE devem ser tratados com a governação dos recursos tomada em consideração, a fim de garantir a utilização mais eficiente da entrada aprovisionada.

* Tenha em atenção que, caso seja especificado, o valor gc_grace_seconds tem de ser zero.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Mapeamento de consistência 

A API para Cassandra do Azure Cosmos DB permite que haja consistência em operações de leitura.  O mapeamento de consistência é detalhado [aqui.](consistency-levels-across-apis.md#cassandra-mapping)

## <a name="permission-and-role-management"></a>Gestão de permissões e funções

A Azure Cosmos DB suporta o controlo de acesso baseado em funções (RBAC) para o fornecimento, teclas rotativas, métricas de visualização e leitura e palavras-passe apenas de leitura que podem ser obtidas através do [portal Azure](https://portal.azure.com). A Azure Cosmos DB não apoia funções para atividades crud.

## <a name="keyspace-and-table-options"></a>Opções de espaço-chave e mesa

As opções para nome, classe, replication_fator e datacenter no comando "Criar Keyspace" são ignoradas atualmente. O sistema utiliza o método de replicação global de [distribuição](global-dist-under-the-hood.md) da Azure Cosmos DB para adicionar as regiões. Se precisar da presença transversal de dados, pode capacitá-lo ao nível da conta com powerShell, CLI ou portal, para saber mais, ver como adicionar artigo [de regiões.](how-to-manage-database-account.md#addremove-regions-from-your-database-account) Durable_writes não pode ser desativada porque o Azure Cosmos DB garante que cada escrita é durável. Em todas as regiões, o Azure Cosmos DB replica os dados através do conjunto de réplicas que é composto por quatro réplicas e esta [configuração](global-dist-under-the-hood.md) de conjunto de réplicas não pode ser modificada.
 
Todas as opções são ignoradas na criação da tabela, exceto gc_grace_seconds, que deve ser definida para zero.
O Keyspace e a tabela têm uma opção extra chamada "cosmosdb_provisioned_throughput" com um valor mínimo de 400 RU/s. A entrada keyspace permite a partilha de entrada em várias tabelas e é útil para cenários quando todas as tabelas não estão a utilizar a entrada provisionada. O comando Alter Table permite alterar a entrada prevista em todas as regiões. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Utilização da política de ligação de repetição Cassandra

Azure Cosmos DB é um sistema governado por recursos. Isto significa que pode fazer um certo número de operações num dado segundo com base nas unidades de pedido consumidas pelas operações. Se um pedido exceder esse limite num segundo, os pedidos são limitados à taxa e serão lançadas exceções. A Cassandra API em Azure Cosmos DB traduz estas exceções a erros sobrecarregados no protocolo nativo de Cassandra. Para garantir que a sua aplicação pode intercetar e retentar pedidos no caso de limitação da taxa, a [faísca](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) e as extensões [java](https://github.com/Azure/azure-cosmos-cassandra-extensions) são fornecidas. Se utilizar outros SDKs para aceder à Cassandra API em Azure Cosmos DB, crie uma política de ligação para voltar a tentar estas exceções.

## <a name="next-steps"></a>Passos seguintes

- Começar com [a criação de uma conta Cassandra API, base de dados e uma tabela](create-cassandra-api-account-java.md) usando uma aplicação Java

