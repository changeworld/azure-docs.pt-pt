---
title: 'Tutorial: Operações Da ETL com Consulta Interativa - Azure HDInsight'
description: Tutorial - Aprenda a extrair dados de um conjunto de dados CSV cru. Transforme-o utilizando consulta interativa no HDInsight. Em seguida, carregue os dados transformados na base de dados Azure SQL utilizando o Apache Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 07/02/2019
ms.openlocfilehash: 7413a32fdddb579bad61c9cfe539be6aaeae9881
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313748"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Tutorial: Extrair, transformar e carregar dados usando consulta interativa no Azure HDInsight

Neste tutorial, você descarrega um ficheiro de dados CSV cru de dados de voo disponíveis publicamente. Importe-o para armazenamento de cluster HDInsight e, em seguida, transforme os dados usando a Consulta Interativa em Azure HDInsight. Uma vez transformados os dados, você carrega esses dados numa base de dados Azure SQL usando [Apache Sqoop](https://sqoop.apache.org/).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Transferir os dados de voos de exemplo
> * Carregar os dados para um cluster do HDInsight
> * Transforme os dados usando consulta interativa
> * Crie uma tabela numa base de dados Azure SQL
> * Use sqoop para exportar dados para uma base de dados Azure SQL

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de consulta interativa no HDInsight. Consulte os [clusters Create Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Consulta Interativa** para **tipo cluster**.

* Uma base de dados Azure SQL. Vai utilizar uma base de dados SQL do Azure como arquivo de dados de destino. Se não tiver uma base de dados SQL, veja [Criar uma base de dados SQL do Azure no portal do Azure](/azure/sql-database/sql-database-single-database-get-started).

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Transferir os dados de voos

1. Navegue para [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (Administração de Investigação e Inovação Tecnológica, Instituto de Estatísticas de Transportes).

2. Na página, limpe todos os campos e, em seguida, selecione os seguintes valores:

   | Nome | Valor |
   | --- | --- |
   | Filtrar Ano |2019 |
   | Filtrar Período |Janeiro |
   | Campos |`Year, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay`. |

3. Selecione **Transferir**. Vai obter um ficheiro .zip com os campos de dados que selecionou.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Carregar os dados para um cluster do HDInsight

Existem muitas formas de carregar dados para o armazenamento associado a um cluster do HDInsight. Nesta secção, vai utilizar `scp` para carregar os dados. Para ver outras formas de carregar dados, veja [Upload data to HDInsight](../hdinsight-upload-data.md) (Carregar dados para o HDInsight).

1. Faça upload do ficheiro .zip para o nó de cabeça de cluster HDInsight. Editar o comando abaixo `FILENAME` substituindo pelo nome do `CLUSTERNAME` ficheiro .zip e com o nome do cluster HDInsight. Em seguida, abra um pedido de comando, coloque o seu diretório de trabalho para o local do ficheiro e, em seguida, insira o comando.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Introduza sim ou não para continuar se solicitado. O texto não é visível na janela como escreve.

2. Depois de concluído o carregamento, utilize SSH para ligar ao cluster. Editar o comando abaixo `CLUSTERNAME` substituindo pelo nome do cluster HDInsight. Em seguida, introduza o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Configurar a variável ambiental uma vez estabelecida uma ligação SSH. Substitua, `SQL_DATABASE` `FILE_NAME` `SQL_SERVERNAME` `SQL_USER`, `SQL_PASWORD` e com os valores apropriados. Em seguida, insira o comando:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Desinserto o ficheiro .zip inserindo o comando abaixo:

    ```bash
    unzip $FILENAME.zip
    ```

5. Crie um diretório no armazenamento HDInsight e, em seguida, copie o ficheiro .csv para o diretório introduzindo o comando abaixo:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Utilizar uma consulta do Hive para transformar os dados

Existem muitas formas de executar um trabalho do Hive num cluster do HDInsight. Nesta secção, você usa [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) para executar um trabalho de Colmeia. Para obter informações sobre outros métodos de execução de um trabalho de Colmeia, consulte [Use Apache Hive no HDInsight](../hadoop/hdinsight-use-hive.md).

Como parte do trabalho do Hive, importe os dados do ficheiro .csv para uma tabela do Hive com o nome **Delays** (Atrasos).

1. A partir da solicitação sSH que já tem para o cluster HDInsight, use o seguinte comando para criar, e editar um novo ficheiro chamado **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Utilize o seguinte texto como o conteúdo desse ficheiro:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Para guardar o ficheiro, prima **Ctrl + X,** em seguida **y**, e depois introduza.

4. Para iniciar o Hive e executar o ficheiro **flightdelays.hql**, utilize o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Quando a execução do script **flightdelays.hql** for concluída, utilize o seguinte comando para abrir uma sessão interativa do Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. Quando receber a linha de comandos `jdbc:hive2://localhost:10001/>`, utilize a seguinte consulta para obter dados a partir dos dados importados de atrasos de voos:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Esta consulta obtém uma lista de cidades em que os voos se atrasaram devido às condições atmosféricas, juntamente com o tempo de atraso médio e guarda-a em `/tutorials/flightdelays/output`. Depois, o Sqoop lê os dados a partir dessa localização e exporta-os para a Base de Dados SQL do Azure.

7. Para sair do Beeline, introduza `!quit` na linha de comandos.

## <a name="create-a-sql-database-table"></a>Criar uma base de dados SQL

Existem muitas formas de ligar à Base de Dados SQL e criar uma tabela. Os passos seguintes utilizam [FreeTDS](http://www.freetds.org/) do cluster do HDInsight.

1. Para instalar o FreeTDS, utilize o seguinte comando da ligação SSH aberta ao cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Após a conclusão da instalação, utilize o seguinte comando para ligar ao servidor da Base de Dados SQL.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Receberá um resultado semelhante ao seguinte texto:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. Na linha de comandos `1>`, introduza as seguintes linhas:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas. Esta declaração cria uma tabela com o nome de **atrasos,** com um índice agrupado.

    Utilize a seguinte consulta para verificar se a tabela foi criada:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    O resultado é semelhante ao seguinte texto:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Introduza `exit` na linha de comandos `1>` para sair do utilitário tsql.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Dados de exportação para base de dados SQL usando Apache Sqoop

Nas secções anteriores, copiou os dados transformados em `/tutorials/flightdelays/output`. Nesta secção, vai utilizar o Sqoop para exportar os dados de `/tutorials/flightdelays/output` para a tabela que criou na base de dados SQL do Azure.

1. Verifique se o SQoop pode ver a sua base de dados SQL inserindo o comando abaixo:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Este comando devolve uma lista de bases de dados, incluindo a base de dados em que criou a `delays` tabela anteriormente.

2. Exportar dados `/tutorials/flightdelays/output` da `delays` tabela, entrando no comando abaixo:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    O Sqoop liga-se à `delays` base de dados `/tutorials/flightdelays/output` que contém `delays` a tabela e exporta dados do diretório para a mesa.

3. Depois de terminar o comando sqoop, utilize o utilitário tsql para ligar à base de dados, inserindo o comando abaixo:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Utilize as instruções seguintes para verificar se os dados foram exportados para a tabela de atrasos:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Deverá ver uma lista dos dados na tabela. A tabela inclui o nome da cidade e o tempo médio dos atrasos dos voos.

    Introduza `exit` para sair do utilitário tsql.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, pode pretender eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar clusters quando não estão em uso.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, pegou num ficheiro de dados CSV cru, importou-o para um armazenamento de cluster HDInsight e depois transformou os dados usando a Consulta Interativa no Azure HDInsight.  Avance para o próximo tutorial para saber sobre o Conector apache Hive Warehouse.

> [!div class="nextstepaction"]
> [Integre a Apache Spark e a Apache Hive com o Conector de Armazém da Colmeia](./apache-hive-warehouse-connector.md)
