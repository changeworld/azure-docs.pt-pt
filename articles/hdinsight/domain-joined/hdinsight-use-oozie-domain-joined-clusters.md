---
title: Apache Oozie fluxos de trabalho & Segurança Empresarial - Azure HDInsight
description: Fluxos de trabalho De Apache Oozie seguros utilizando o Pacote de Segurança Empresarial Azure HDInsight. Aprenda a definir um fluxo de trabalho oozie e submeta um trabalho oozie.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/09/2019
ms.openlocfilehash: 9ef54707f7fac3dd1328e29f6d05f62c1dee2561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194908"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Executar Apache Oozie em clusters Hadoop HDInsight com pacote de segurança empresarial

Apache Oozie é um sistema de fluxo de trabalho e coordenação que gere os empregos da Apache Hadoop. Oozie está integrado com a pilha hadoop, e apoia os seguintes trabalhos:

- Apache MapReduce
- Porco Apache
- Colmeia Apache
- Apache Sqoop

Você também pode usar Oozie para agendar trabalhos específicos para um sistema, como programas Java ou scripts de concha.

## <a name="prerequisite"></a>Pré-requisito

Um cluster hadoop Azure HDInsight com pacote de segurança empresarial (ESP). Consulte [os clusters HDInsight de Configuração com ESP](./apache-domain-joined-configure-using-azure-adds.md).

> [!NOTE]  
> Para obter instruções detalhadas sobre como utilizar o Oozie em clusters não ESP, consulte Utilize os fluxos de [trabalho apache Oozie em Azure HDInsight baseado em Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Ligar a um cluster ESP

Para obter mais informações sobre a Secure Shell (SSH), consulte [Connect to HDInsight (Hadoop) utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ligue-se ao cluster HDInsight utilizando o SSH:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. Para verificar a autenticação kerberos bem sucedida, utilize o `klist` comando. Caso contrário, `kinit` utilize para iniciar a autenticação kerberos.

1. Inscreva-se na porta de entrada hDInsight para registar o símbolo OAuth necessário para aceder ao Armazenamento do Lago De dados Azure:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    Um código de resposta ao estado de **200 OK** indica registo bem sucedido. Verifique o nome de utilizador e a palavra-passe se for recebida uma resposta não autorizada, como o 401.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho

As definições de fluxo de trabalho oozie são escritas na Linguagem de Definição de Processo de Hadoop Apache (hPDL). hPDL é uma linguagem de definição de processo XML. Tome os seguintes passos para definir o fluxo de trabalho:

1. Configurar o espaço de trabalho de um utilizador de domínio:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   Substitua-a `DomainUser` com o nome de utilizador do domínio.
   Substitua-a `DomainUserPath` pelo percurso de diretório inicial para o utilizador do domínio.
   Substitua-a `ClusterVersion` pela versão da plataforma de dados do cluster.

2. Utilize a seguinte declaração para criar e editar um novo ficheiro:

   ```bash
   nano workflow.xml
   ```

3. Depois de o editor nano abrir, introduza o seguinte XML como conteúdo do ficheiro:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
    ```

4. Substitua-o `clustername` pelo nome do cluster.

5. Para guardar o ficheiro, selecione **Ctrl+X**. Insira **Y**. Em seguida, selecione **Enter**.

    O fluxo de trabalho é dividido em duas partes:

   - **Credencial.** Esta secção tem as credenciais utilizadas para autenticar as ações da Oozie:

     Este exemplo utiliza a autenticação para ações da Colmeia. Para saber mais, consulte [a Autenticação de Ação.](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html)

     O serviço de credencial permite que as ações da Oozie se personiquem pelo utilizador por aceder aos serviços Hadoop.

   - **Ação.** Esta secção tem três ações: redução de mapas, servidor hive 2 e servidor hive 1:

     - A ação de redução de mapas é um exemplo de um pacote Oozie para reduzir o mapa que produz a contagem de palavras agregada.

     - As ações do servidor hive 2 e hive 1 executam uma consulta numa tabela de colmeia sinuosa fornecida com HDInsight.

     As ações da Colmeia utilizam as credenciais definidas na `cred` secção de credenciais para autenticação utilizando a palavra-chave no elemento de ação.

6. Utilize o seguinte comando `workflow.xml` para `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`copiar o ficheiro para:

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. Substitua-a `domainuser` pelo seu nome de utilizador para o domínio.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Defina o ficheiro de propriedades para o trabalho de Oozie

1. Utilize a seguinte declaração para criar e editar um novo ficheiro para propriedades de emprego:

    ```bash
    nano job.properties
    ```

2. Depois de o editor nano abrir, utilize o seguinte XML como conteúdo do ficheiro:

   ```bash
   nameNode=adl://home
   jobTracker=headnodehost:8050
   queueName=default
   examplesRoot=examples
   oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
   hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
   hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
   oozie.use.system.libpath=true
   user.name=[domainuser]
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - Utilize `adl://home` o URI `nameNode` para a propriedade se tiver o Azure Data Lake Storage Gen1 como o seu armazenamento principal de cluster. Se estiver a utilizar o Armazenamento Azure `wasb://home`Blob, mude-o para . Se estiver a utilizar o Azure Data Lake `abfs://home`Storage Gen2, então mude-o para .
   - Substitua-a `domainuser` pelo seu nome de utilizador para o domínio.  
   - Substitua `ClusterShortName` com o nome curto para o cluster. Por exemplo, se o nome do cluster for `clustershortname` https:// *[link exemplo]* sechadoopcontoso.azurehdisnight.net, são os primeiros seis caracteres do cluster: **secha**.  
   - Substitua-a `jdbcurlvalue` com o URL JDBC da configuração da Colmeia. Um exemplo é jdbc:hive2://headnodehost:10001/;transportMode=http.
   - Para guardar o ficheiro, selecione `Y`Ctrl+X, introduza , e, em seguida, selecione **Enter**.

   Este ficheiro de propriedades precisa estar presente localmente quando executar empregos Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Crie scripts personalizados da Hive para empregos oozie

Pode criar os dois scripts Hive para o servidor hive 1 e hive servidor 2, como mostrado nas seguintes secções.

### <a name="hive-server-1-file"></a>Ficheiro do servidor hive 1

1. Criar e editar um ficheiro para as ações do servidor da Hive 1:

    ```bash
    nano countrowshive1.hql
    ```

2. Criar o guião:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Guarde o ficheiro para o Sistema de Ficheiros Distribuídos Apache Hadoop (HDFS):

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Ficheiro hive server 2

1. Criar e editar um campo para as ações do servidor hive 2:

    ```bash
    nano countrowshive2.hql
    ```

2. Criar o guião:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Guarde o ficheiro para hDFS:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Submeta empregos de Oozie

A apresentação de postos de trabalho da Oozie para agrupamentos de ESP é como a apresentação de postos de trabalho da Oozie em clusters não ESP.

Para mais informações, consulte [Oozie Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado em Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Resultados de uma submissão de emprego de Oozie

Os empregos da Oozie são geridos para o utilizador. Assim, tanto os registos de auditoria Apache Hadoop YARN como Apache Ranger mostram os trabalhos a serem executados como o utilizador personificado. A saída da interface da linha de comando de um trabalho oozie parece ser o seguinte código:

```output
Job ID : 0000015-180626011240801-oozie-oozi-W
------------------------------------------------------------------------------------------------
Workflow Name : map-reduce-wf
App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
Status        : SUCCEEDED
Run           : 0
User          : alicetest
Group         : -
Created       : 2018-06-26 19:25 GMT
Started       : 2018-06-26 19:25 GMT
Last Modified : 2018-06-26 19:30 GMT
Ended         : 2018-06-26 19:30 GMT
CoordAction ID: -

Actions
------------------------------------------------------------------------------------------------
ID                      Status  Ext ID          ExtStatus   ErrCode
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
-----------------------------------------------------------------------------------------------
```

Os registos de auditoria do Ranger para as ações do servidor hive 2 mostram o Oozie a executar a ação para o utilizador. As vistas ranger e yARN são visíveis apenas para o administrador do cluster.

## <a name="configure-user-authorization-in-oozie"></a>Configure a autorização do utilizador em Oozie

A Oozie por si só tem uma configuração de autorização de utilizador que pode impedir os utilizadores de parar ou apagar o emprego de outros utilizadores. Para ativar esta `oozie.service.AuthorizationService.security.enabled` configuração, detete o para `true`. 

Para mais informações, consulte [a Instalação e Configuração Apache Oozie.](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html)

Para componentes como o servidor Hive 1 onde o plug-in Ranger não está disponível ou suportado, apenas é possível a autorização hDFS de grãos grossos. A autorização de grãos finos só está disponível através de plug-ins ranger.

## <a name="get-the-oozie-web-ui"></a>Obtenha a Teia Oozie UI

A Oozie web UI fornece uma visão baseada na web sobre o estado dos empregos oozie no cluster. Para obter a UI web, dê os seguintes passos em clusters ESP:

1. Adicione um [nó de borda](../hdinsight-apps-use-edge-node.md) e ative a [autenticação SSH Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Siga os passos [de UI web oozie](../hdinsight-use-oozie-linux-mac.md) para permitir o túnel sSH até ao nó de borda e aceder à UI web.

## <a name="next-steps"></a>Passos seguintes

- [Use Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho em Azure HDInsight baseado em Linux](../hdinsight-use-oozie-linux-mac.md).
- [Ligue-se ao HDInsight (Apache Hadoop) utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
