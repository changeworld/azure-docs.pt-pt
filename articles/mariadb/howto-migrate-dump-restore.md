---
title: Migrar com despejo e restauro - Base de Dados Azure para MariaDB
description: Este artigo explica duas formas comuns de fazer cópias de segurança e restaurar bases de dados na sua Base de Dados Azure para o MariaDB, utilizando ferramentas como mysqldump, MySQL Workbench e PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 72735e83af97fde8377e27daa45501704ef5a3c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164547"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Migrar a sua base de dados MariaDB para a Base de Dados Azure para MariaDB utilizando lixeira e restauro
Este artigo explica duas formas comuns de fazer cópias de segurança e restaurar bases de dados na sua Base de Dados Azure para mariaDB
- Despejo e restauro da linha de comando (usando mysqldump) 
- Despejo e restauro usando PHPMyAdmin

## <a name="before-you-begin"></a>Antes de começar
Para passar por este guia de como orientar, você precisa ter:
- [Criar base de dados Azure para servidor MariaDB - Portal Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)
- utilitário de linha de comando [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) instalado numa máquina.
- MySQL [Workbench MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta MySQL de terceiros para fazer despejo e restaurar comandos.

## <a name="use-common-tools"></a>Use ferramentas comuns
Utilize utilitários e ferramentas comuns, como a Bancada de Trabalho MySQL ou a mysqldump, para ligar e restaurar remotamente os dados na Base de Dados Azure para o MariaDB. Utilize estas ferramentas na sua máquina cliente com uma ligação à Internet para ligar à Base de Dados Azure para MariaDB. Utilize uma ligação encriptada SSL para as melhores práticas de segurança, consulte também a [conectividade Configure SSL na Base de Dados Azure para MariaDB](concepts-ssl-connection-security.md). Não precisa de mover os ficheiros de despejo para qualquer local especial na nuvem ao migrar para a Base de Dados Azure para o MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Usos comuns para despejo e restauro
Pode utilizar utilitários MySQL, como mysqldump e mysqlpump, para despejar e carregar bases de dados numa Base de Dados Azure para servidor MariaDB em vários cenários comuns. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Utilize lixeiras de base de dados quando estiver a migrar toda a base de dados. Esta recomendação mantém-se ao mover uma grande quantidade de dados, ou quando pretende minimizar a interrupção do serviço para sites ou aplicações ao vivo. 
-  Confirme que todas as tabelas na base de dados utilizam o motor de armazenamento InnoDB ao carregar os dados no Azure Database for MariaDB. A Base de Dados Azure para MariaDB suporta apenas o motor de armazenamento InnoDB, pelo que não suporta motores de armazenamento alternativos. Se as suas tabelas estiverem configuradas com outros motores de armazenamento, converta-as no formato do motor InnoDB antes da migração para a Base de Dados Azure para o MariaDB.
   Por exemplo, se tiver um WordPress ou WebApp utilizando as tabelas MyISAM, converta primeiro essas tabelas migrando para o formato InnoDB antes de restaurar para a Base de Dados Azure para O DND. Utilize a `ENGINE=InnoDB` cláusula para definir o motor utilizado ao criar uma nova tabela e, em seguida, transfira os dados para a tabela compatível antes da restauração. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar problemas de compatibilidade, confirme que utiliza a mesma versão do MariaDB nos sistemas de origem e de destino ao capturar as bases de dados. Por exemplo, se o seu servidor MariaDB existente for a versão 10.2, então deve migrar para a Base de Dados Azure para MariaDB configurado para executar a versão 10.2. O `mysql_upgrade` comando não funciona numa Base de Dados Azure para o servidor MariaDB, e não é suportado. Se precisar de fazer upgrade em versões MariaDB, primeiro despejar ou exportar a sua base de dados de versão inferior para uma versão mais alta do MariaDB no seu próprio ambiente. Em `mysql_upgrade`seguida, corra , antes de tentar migrar para uma Base de Dados Azure para MariaDB.

## <a name="performance-considerations"></a>Considerações de desempenho
Para otimizar o desempenho, tome nota destas considerações ao despejar grandes bases de dados:
-   Use `exclude-triggers` a opção em mysqldump ao despejar bases de dados. Exclua os gatilhos dos ficheiros de despejo para evitar que os comandos do gatilho disparem durante a restauração dos dados. 
-   Utilize `single-transaction` a opção de definir o modo de isolamento de transações para READ REPETIVEL e enviar uma declaração sQL START TRANSACTION para o servidor antes de despejar dados. Despejar muitas tabelas numa única transação faz com que algum armazenamento extra seja consumido durante a restauração. A `single-transaction` opção `lock-tables` e a opção são mutuamente exclusivas porque o LOCK TABLES faz com que quaisquer transações pendentes sejam cometidas implicitamente. Para despejar mesas `single-transaction` grandes, `quick` combine a opção com a opção. 
-   Utilize `extended-insert` a sintaxe de várias linhas que inclui várias listas VALUE. Isto resulta num ficheiro de despejo mais pequeno e acelera as inserções quando o ficheiro é recarregado.
-  Utilize `order-by-primary` a opção em mysqldump ao despejar bases de dados, de modo a que os dados sejam escritos em ordem principal.
-   Utilize `disable-keys` a opção em mysqldump ao despejar dados, para desativar os constrangimentos das chaves estrangeiras antes da carga. Desativar os controlos de chaves estrangeiras proporciona ganhos de desempenho. Ative os constrangimentos e verifique os dados após a carga para garantir a integridade referencial.
-   Utilize mesas divididas quando apropriado.
-   Carregue os dados em paralelo. Evite demasiado paralelismo que o faça atingir um limite de recursos e monitorize os recursos utilizando as métricas disponíveis no portal Azure. 
-   Utilize `defer-table-indexes` a opção na misqlpump ao despejar bases de dados, para que a criação de índices ocorra após a carga dos dados das tabelas.
-   Copie os ficheiros de backup para uma bolha/loja Azure e execute o restauro a partir daí, que deve ser muito mais rápido do que realizar o restauro através da Internet.

## <a name="create-a-backup-file"></a>Criar um ficheiro de backup
Para fazer o apoio a uma base de dados MariaDB existente no servidor local ou numa máquina virtual, execute o seguinte comando utilizando o mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os parâmetros a fornecer são:
- [uname] Nome de utilizador da sua base de dados 
- [passe] A palavra-passe para a sua base de dados (note que não há espaço entre -p e a palavra-passe) 
- [nome dbname] O nome da sua base de dados 
- [backupfile.sql] O nome de ficheiro para a sua cópia de segurança da base de dados 
- [--opt] A opção mysqldump 

Por exemplo, para fazer o backup a uma base de dados denominada 'testdb' no seu servidor MariaDB com o nome de utilizador 'testuser' e sem palavra-passe para um ficheiro testdb_backup.sql, utilize o seguinte comando. O comando recoloca `testdb` a base `testdb_backup.sql`de dados num ficheiro chamado , que contém todas as declarações SQL necessárias para recriar a base de dados. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Para selecionar tabelas específicas na sua base de dados para fazer o seu trabalho de fundo, enumere os nomes de tabelas separados por espaços. Por exemplo, para fazer o apoio apenas à tabela 1 e às tabelas 2 do 'testdb', siga este exemplo: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Para fazer um backup mais de uma base de dados ao mesmo tempo, utilize o interruptor --base de dados e enumere os nomes da base de dados separados por espaços. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Criar uma base de dados no servidor alvo
Crie uma base de dados vazia na base de dados azure-alvo para o servidor MariaDB onde pretende migrar os dados. Utilize uma ferramenta como a bancada MySQL para criar a base de dados. A base de dados pode ter o mesmo nome que a base de dados que contém os dados despejados ou pode criar uma base de dados com um nome diferente.

Para se conectar, localize as informações de ligação na **visão geral** da sua Base de Dados Azure para MariaDB.

![Encontre as informações de ligação no portal Azure](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Adicione as informações de ligação na sua bancada de trabalho MySQL.

![Cadeia de conexão de bancada de trabalho MySQL](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Restaure a sua base de dados MariaDB
Uma vez criado a base de dados de destino, pode utilizar o comando mysql ou a bancada mySQL workbench para restaurar os dados na base de dados específica recém-criada a partir do ficheiro de despejo.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Neste exemplo, restaure os dados na base de dados recém-criada na base de dados azure alvo para o servidor MariaDB.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportação usando PHPMyAdmin
Para exportar, pode utilizar a ferramenta comum phpMyAdmin, que já pode ter instalado localmente no seu ambiente. Para exportar a sua base de dados MariaDB utilizando PHPMyAdmin:
1. Abra a phpMyAdmin.
2. Selecione a sua base de dados. Clique no nome da base de dados na lista à esquerda. 
3. Clique no link **Export.** Uma nova página parece ver o despejo da base de dados.
4. Na área de Exportação, clique no link **Select All** para escolher as tabelas na sua base de dados. 
5. Na área de opções SQL, clique nas opções apropriadas. 
6. Clique no **Save como** opção de ficheiro e na opção de compressão correspondente e, em seguida, clique no botão **Go.** Deve aparecer uma caixa de diálogo que o leva a guardar o ficheiro localmente.

## <a name="import-using-phpmyadmin"></a>Importação com PHPMyAdmin
Importar a sua base de dados é semelhante à exportação. Faça as seguintes ações:
1. Abra a phpMyAdmin. 
2. Na página de configuração phpMyAdmin, clique em **Adicionar** para adicionar a sua Base de Dados Azure para o servidor MariaDB. Forneça os detalhes da ligação e informações de login.
3. Crie uma base de dados devidamente nomeada e selecione-a à esquerda do ecrã. Para reescrever a base de dados existente, clique no nome da base de dados, selecione todas as caixas de verificação ao lado dos nomes das tabelas e selecione **Drop** para eliminar as tabelas existentes. 
4. Clique no link **SQL** para mostrar a página onde pode escrever em comandos SQL ou fazer upload do seu ficheiro SQL. 
5. Utilize o botão **de navegação** para encontrar o ficheiro base de dados. 
6. Clique no botão **Go** para exportar a cópia de segurança, execute os comandos SQL e recrie a sua base de dados.

## <a name="next-steps"></a>Passos seguintes
- [Ligue aplicações à Base de Dados Azure para MariaDB.](./howto-connection-string.md)
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
