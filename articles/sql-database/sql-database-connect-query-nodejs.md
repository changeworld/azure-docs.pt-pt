---
title: Use Node.js para consultar uma base de dados
description: Como usar o Node.js para criar um programa que se conecta a uma base de dados Azure SQL e consulta-lo usando declarações T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768608"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Início Rápido: Utilizar o Node.js para consultar uma base de dados SQL do Azure

Neste arranque rápido, utiliza o Node.js para se ligar a uma base de dados Azure SQL e utilizar declarações T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma [base de dados Azure SQL](sql-database-single-database-get-started.md)
- Software relacionado com [node.js](https://nodejs.org)

  # <a name="macos"></a>[macOS](#tab/macos)

  Instale homebrew e Node.js, em seguida, instale o controlador ODBC e o SQLCMD utilizando os passos **1.2** e **1.3** nas [aplicações Create Node.js utilizando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Instale o Node.js e, em seguida, instale o controlador ODBC e o SQLCMD utilizando os passos **1.2** e **1.3** nas [aplicações Create Node.js utilizando o SQL Server em Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Instale as aplicações Chocolatey e Node.js e, em seguida, instale o controlador ODBC e o SQLCMD utilizando os passos **1.2** e **1.3** nas [aplicações Create Node.js utilizando o Servidor SQL no Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar a base de dados **Adventure Works.**

> [!NOTE]
> Pode optar opcionalmente por utilizar uma instância gerida pelo Azure SQL.
>
> Para criar e configurar, utilize o [Portal Azure,](sql-database-managed-instance-get-started.md) [PowerShell,](scripts/sql-database-create-configure-managed-instance-powershell.md)ou [CLI,](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)em seguida, configurar [no local](sql-database-managed-instance-configure-p2s.md) ou [conectividade VM.](sql-database-managed-instance-configure-vm.md)
>
> Para carregar dados, consulte restaurar com o [FICHEIRO BACPAC](sql-database-import.md) com o ficheiro [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ou ver restaurar a base de dados dos [Importadores](sql-database-managed-instance-get-started-restore.md)do Mundo Wide .

## <a name="get-sql-server-connection-information"></a>Obtenha informações de ligação ao servidor SQL

Obtenha as informações de ligação que precisa para ligar à base de dados Azure SQL. Necessitará do nome do servidor ou nome do anfitrião totalmente qualificado, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Vá às bases de **dados SQL** ou página de **instâncias geridas pela SQL.**

3. Na página **Overview,** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para uma única base de dados ou o nome de servidor totalmente qualificado ao lado do **Anfitrião** para uma instância gerida. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copiar.** 

## <a name="create-the-project"></a>Criar o projeto

Abra uma linha de comandos e crie uma pasta com o nome *sqltest*. Abra a pasta que criou e execute o seguinte comando:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Adicione código à base de dados de consulta

1. No seu editor de texto favorito, crie um novo ficheiro, *sqltest.js*.

1. Substitua o seu conteúdo pelo seguinte código. Em seguida, adicione os valores apropriados para o seu servidor, base de dados, utilizador e senha.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });
      
      connection.execSql(request);
    }
    ```

> [!NOTE]
> O exemplo de código utiliza a base de dados da amostra **AdventureWorksLT** para o Azure SQL.

## <a name="run-the-code"></a>Executar o código

1. No pedido de comando, executar o programa.

    ```bash
    node sqltest.js
    ```

1. Verifique se as 20 linhas superiores são devolvidas e feche a janela de aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Controlador Microsoft Node.js para SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Conecte e consulta no Windows/Linux/macOS com [.NET core,](sql-database-connect-query-dotnet-core.md) [Visual Studio Code](sql-database-connect-query-vscode.md), ou [SSMS](sql-database-connect-query-ssms.md) (apenas windows)

- [Inicie com .NET Core no Windows/Linux/macOS utilizando a linha de comando](/dotnet/core/tutorials/using-with-xplat-cli)

- Desenhe a sua primeira base de dados Azure SQL utilizando [.NET](sql-database-design-first-database-csharp.md) ou [SSMS](sql-database-design-first-database.md)
