---
title: Cadernos Zeppelin & cluster Apache Spark - Azure HDInsight
description: Instruções passo a passo sobre como usar os cadernos Zeppelin com clusters Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 3c1369e813ba6518f6cd4b27082020ae36a24c82
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811198"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Utilizar blocos de notas do Apache Zeppelin com o cluster do Apache Spark no Azure HDInsight

Os clusters HDInsight Spark incluem os cadernos [Apache Zeppelin.](https://zeppelin.apache.org/) Usa os cadernos para gerir os empregos da [Apache Spark.](https://spark.apache.org/) Neste artigo, aprende-se a usar o caderno Zeppelin num cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* O esquema URI para o armazenamento primário dos seus clusters. O esquema `wasb://` seria para o Armazenamento `abfs://` de Blob Azure, `adl://` para o Azure Data Lake Storage Gen2 ou para o Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para `wasbs://`o Armazenamento Blob, o URI seria .  Para mais informações, consulte [Exigir transferência segura no Armazenamento Azure](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Lance um caderno Apache Zeppelin

1. A partir da **visão geral**do cluster Spark, selecione **notebook Zeppelin** a partir de **dashboards cluster**. Introduza as credenciais de administração para o cluster.  

   > [!NOTE]  
   > Também pode chegar ao Caderno Zeppelin para o seu cluster abrindo o seguinte URL no seu navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Crie um novo bloco de notas. Do painel do cabeçalho, navegue até **ao Caderno** > **Criar uma nova nota.**

    ![Crie um novo caderno Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Crie um novo caderno Zeppelin")

    Introduza um nome para o caderno e, em seguida, selecione **Criar Nota**.

3. Certifique-se de que o cabeçalho do caderno mostra um estado ligado. É denotado por um ponto verde no canto superior direito.

    ![Estatuto do caderno Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Estatuto do caderno Zeppelin")

4. Carregue dados de exemplo para uma tabela temporária. Quando cria um cluster Spark no HDInsight, `hvac.csv`o ficheiro de dados da `\HdiSamples\SensorSampleData\hvac`amostra, é copiado para a conta de armazenamento associada em .

    No parágrafo vazio que é criado por defeito no novo caderno, cola o seguinte corte.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Pressione **SHIFT + ENTER** ou selecione o botão **Reproduzir** para o parágrafo executar o corte. O estatuto no canto direito do parágrafo deve progredir de READY, PENDING, RUNNING to FINISHED. A saída aparece na parte inferior do mesmo parágrafo. A imagem parece a seguinte imagem:

    ![Criar uma tabela temporária a partir de dados brutos](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Criar uma tabela temporária a partir de dados brutos")

    Também pode fornecer um título para cada parágrafo. A partir do canto direito do parágrafo, selecione o ícone **Definições** (sprocket) e, em seguida, selecione o **título do Show**.  

    > [!NOTE]  
    > %spark2 intérprete não é suportado em cadernos Zeppelin em todas as versões HDInsight, e %sh intérprete não será suportado a partir de HDInsight 4.0 em diante.

5. Agora pode executar declarações spark `hvac` SQL na mesa. Colhe a seguinte consulta num novo parágrafo. A consulta recupera a identificação do edifício. Também a diferença entre o alvo e as temperaturas reais para cada edifício numa dada data. Turno de pressão **+ ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    A declaração **%sql,** no início, diz ao caderno para utilizar o intérprete Livy Scala.

6. Selecione o ícone **do Gráfico** de Barras para alterar o visor.  **definições,** aparecem depois de ter selecionado a Carta de **Barras,** permite-lhe escolher **Teclas**, e **Valores**.  A imagem que se segue mostra a saída.

    ![Executar uma declaração Spark SQL usando o notebook1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Executar uma declaração Spark SQL usando o notebook1")

7. Também pode executar declarações Spark SQL usando variáveis na consulta. O próximo corte mostra como definir `Temp`uma variável, na consulta com os valores possíveis com que pretende consultar. Quando executa a consulta pela primeira vez, uma queda é automaticamente povoada com os valores que especificou para a variável.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Colar este corte num novo parágrafo e prima **SHIFT + ENTER**. Em seguida, selecione **65** da lista de drop-down **temp.**

8. Selecione o ícone **do Gráfico** de Barras para alterar o visor.  Em seguida, selecione **as definições** e faça as seguintes alterações:

   * **Grupos:**  Adicione **o tema-alvo**.  
   * **Valores:** 1. Remover **a data**.  2. Adicione **temp_diff**.  3.  Mude o agregador de **SUM** para **AVG**.  

     A imagem que se segue mostra a saída.

     ![Executar uma declaração Spark SQL usando o caderno2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Executar uma declaração Spark SQL usando o caderno2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Como uso pacotes externos com o caderno?

O notebook Zeppelin no cluster Apache Spark no HDInsight pode usar pacotes externos e com contribuições comunitárias que não estão incluídos no cluster. Procure no [repositório Maven](https://search.maven.org/) a lista completa de pacotes disponíveis. Também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, uma lista completa de pacotes com contribuições comunitárias está disponível nos [Pacotes Spark](https://spark-packages.org/).

Neste artigo, você verá como usar o pacote [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o caderno Jupyter.

1. Abrir definições de intérprete. A partir do canto superior direito, selecione o sessão no nome do utilizador e, em seguida, selecione **Intérprete**.

    ![Intérprete de lançamento](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Saída da colmeia")

2. Percorra para **livy2**e, em seguida, **selecione**.

    ![Alterar as definições do intérprete1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Alterar as definições do intérprete1")

3. Navegue `livy.spark.jars.packages`para a tecla , `group:id:version`e desemque o seu valor no formato. Por isso, se quiser utilizar o pacote [spark-csv,](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) tem de `com.databricks:spark-csv_2.10:1.4.0`definir o valor da chave para .

    ![Alterar as definições do intérprete2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Alterar as definições do intérprete2")

    Selecione **Guardar** e, em seguida, **OK** para reiniciar o intérprete Livy.

4. Se quer entender como chegar ao valor da chave que entrou acima, eis como.

    a. Localize o pacote no Repositório Maven. Para este artigo, usamos [spark-csv.](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)

    b. Do repositório, recolha os valores para **GroupId,** **ArtifactId,** e **Versão**.

    ![Use pacotes externos com caderno Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Use pacotes externos com caderno Jupyter")

    c. Concatenaos os três valores, separados por um cólon (**:**).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Onde estão guardados os cadernos Zeppelin?

Os cadernos Zeppelin são guardados para os cabeçados do cluster. Assim, se apagar o cluster, os cadernos também serão eliminados. Se quiser preservar os seus cadernos para posterior utilização noutros clusters, deve exportá-los depois de terminar de gerir os trabalhos. Para exportar um caderno, selecione o ícone **Export** como mostrado na imagem abaixo.

![Descarregue o caderno](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Descarregue o caderno")

Esta ação guarda o caderno como um ficheiro JSON na sua localização de descarregamento.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Utilizar `Shiro` para configurar o acesso aos intérpretes zeppelin em clusters de pacotes de segurança empresarial (ESP)

Como referido acima, `%sh` o intérprete não é suportado a partir do HDInsight 4.0. Além disso, uma vez `%sh` que o intérprete introduz potenciais problemas de segurança, tais como chaves de acesso utilizando comandos de conchas, foi removido dos clusters HDInsight 3.6 ESP também. Significa `%sh` que o intérprete não está disponível ao clicar **Criar uma nova nota** ou na UI intérprete por padrão.

Os utilizadores de domínio `Shiro.ini` privilegiado podem usar o ficheiro para controlar o acesso à UI intérprete. Apenas estes utilizadores `%sh` podem criar novos intérpretes `%sh` e definir permissões em cada novo intérprete. Para controlar o `shiro.ini` acesso utilizando o ficheiro, utilize os seguintes passos:

1. Defina uma nova função usando um nome de grupo de domínio existente. No exemplo seguinte, `adminGroupName` está um grupo de utilizadores privilegiados em AAD. Não use caracteres especiais ou espaços brancos no nome do grupo. Os personagens depois `=` dão as permissões para este papel. `*`significa que o grupo tem permissões completas.

    ```
    [roles]
    adminGroupName = *
    ```

2. Adicione o novo papel para o acesso aos intérpretes zeppelin. No exemplo seguinte, todos `adminGroupName` os utilizadores em são tidos acesso a intérpretes Zeppelin e podem criar novos intérpretes. Pode colocar vários papéis `roles[]`entre os suportes, separados por vírgulas. Depois, os utilizadores que tenham as permissões necessárias podem aceder aos intérpretes zeppelin.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Gestão de sessões livy

O primeiro parágrafo de código no seu caderno Zeppelin cria uma nova sessão livy no seu cluster. Esta sessão é partilhada em todos os cadernos Zeppelin que mais tarde crias. Se a sessão da Livy for morta por alguma razão, os empregos não vão fugir do caderno zeppelin.

Nesse caso, deve fazer os seguintes passos antes de começar a trabalhar a partir de um caderno Zeppelin.  

1. Reinicie o intérprete Livy do caderno Zeppelin. Para isso, abra as definições do intérprete selecionando o nome de utilizador registado a partir do canto superior direito e, em seguida, selecione **Intérprete**.

    ![Intérprete de lançamento](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Saída da colmeia")

2. Percorra até **ao livy2**e, em seguida, selecione **reiniciar**.

    ![Reinicie o intérprete livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Reinicie o intérprete zeppelin")

3. Executa uma célula de código de um caderno zeppelin existente. Este código cria uma nova sessão livy no cluster HDInsight.

## <a name="general-information"></a>Informações gerais

### <a name="validate-service"></a>Validar o serviço

Para validar o serviço de `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` Ambari, navegue até onde o CLUSTERNAME é o nome do seu cluster.

Para validar o serviço de uma linha de comando, SSH para o nó da cabeça. Mude o utilizador `sudo su zeppelin`para zeppelin utilizando o comando . Comandos de estado:

|Comando |Descrição |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Estado de serviço.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Versão de serviço.|
|`ps -aux | grep zeppelin`|Identifique o PID.|

### <a name="log-locations"></a>Localizações de log

|Serviço |Caminho |
|---|---|
|zeppelin-servidor|/usr/hdp/current/zeppelin-server/|
|Registos do Servidor|/var/log/zeppelin|
|Intérprete de `Shiro`Configuração, site.xml, log4j|/usr/hdp/current/zeppelin-server/conf ou /etc/zeppelin/conf|
|Diretório PID|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Ativar a exploração de depuração

1. Navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` até onde clusterNAME é o nome do seu cluster.

1. Navegue para **CONFIGS** > **Propriedades avançadas de zeppelin-log4j** > **log4j_properties_content**.

1. Modificar `log4j.appender.dailyfile.Threshold = INFO` `log4j.appender.dailyfile.Threshold = DEBUG`para .

1. Adicione `log4j.logger.org.apache.zeppelin.realm=DEBUG`.

1. Guarde alterações e reinicie o serviço.

## <a name="next-steps"></a>Passos seguintes

[Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Análise de dados interativa utilizando Spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício utilizando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize o Plugin de Ferramentas HDInsight para intelliJ IDEA para criar e submeter aplicações Apache Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize o Plugin de Ferramentas HDInsight para intelliJ IDEA para depurar aplicações Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernels disponíveis para o portátil Jupyter no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
