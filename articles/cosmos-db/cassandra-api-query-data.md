---
title: 'Tutorial: Dados de consulta de uma conta da API cassandra em Azure Cosmos DB'
description: Este tutorial mostra como consultar os dados dos utilizadores de uma conta API Da API da Azure Cosmos DB, utilizando uma aplicação Java.
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
Customer intent: As a developer, I want to build a Java application to query data stored in a Cassandra API account of Azure Cosmos DB so that customers can manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 69a9bc912f2cd52e52ca6403187f993413539ecd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "60899904"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>Tutorial: Dados de consulta de uma conta da API cassandra em Azure Cosmos DB

Como desenvolvedor, pode ter aplicações que usam pares chave/valor. Pode utilizar uma conta Cassandra API no Azure Cosmos DB para armazenar e consultar os dados chave/valor. Este tutorial mostra como consultar os dados dos utilizadores de uma conta Cassandra API em Azure Cosmos DB utilizando uma aplicação Java. A aplicação Java utiliza o [controlador Java](https://github.com/datastax/java-driver) e consulta os dados do utilizador, tais como id do utilizador, nome do utilizador e cidade utilizadora. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Dados de consulta de uma mesa de Cassandra
> * Executar a aplicação

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Este artigo pertence a um tutorial com várias partes. Antes de começar, certifique-se de completar os passos anteriores para criar a conta Cassandra API, espaço de chave, tabela e dados de amostra de [carga na tabela](cassandra-api-load-data.md). 

## <a name="query-data"></a>Consultar dados

Utilize os seguintes passos para consultar dados da sua conta de API para Cassandra:

1. Abra o ficheiro `UserRepository.java` na pasta `src\main\java\com\azure\cosmosdb\cassandra`. Anexe o seguinte bloco de código. Este código fornece três métodos: 

   * Para consultar todos os utilizadores na base de dados
   * Para consultar um utilizador específico, filtrado por ID de utilizador
   * Para apagar uma tabela

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. Abra o ficheiro `UserProfile.java` na pasta `src\main\java\com\azure\cosmosdb\cassandra`. Esta classe contém o método principal que chama os métodos createKeyspace, createTable e inserir dados definidos anteriormente. Agora, anexe o código seguinte, que consulta todos os utilizadores ou um utilizador específico:

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>Executar a aplicação Java
1. Abra uma linha de comandos ou janela de terminal. Cole o seguinte bloco de código. 

   Este código altera o diretório (cd) para o caminho da pasta onde criou o projeto. Em seguida, executa o comando `mvn clean install` para gerar o ficheiro `cosmosdb-cassandra-examples.jar` na pasta de destino. Por fim, executa a aplicação Java.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Agora, no portal do Azure, abra o **Data Explorer** e verifique se a tabela de utilizador foi eliminada.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não são necessários, pode eliminar o grupo de recursos, a conta Azure Cosmos e todos os recursos relacionados. Para isso, selecione o grupo de recursos para a máquina virtual, selecione **Eliminar**, e, em seguida, confirmar o nome do grupo de recursos para apagar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a consultar dados de uma conta da Cassandra API em Azure Cosmos DB. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [Migrar dados para a conta da API para Cassandra](cassandra-import-data.md)


