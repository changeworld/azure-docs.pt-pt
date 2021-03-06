---
title: 'Quickstart: Consulta Apache HBase em Azure HDInsight - HBase Shell'
description: Neste arranque rápido, aprende-se a usar a Apache HBase Shell para executar consultas Apache HBase.
keywords: hdinsight,hadoop,HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 572262cbece26171f9a67bf073906fa2dfd4d8e1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79371074"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Quickstart: Consulta Apache HBase em Azure HDInsight com HBase Shell

Neste arranque rápido, aprende-se a usar a Apache HBase Shell para criar uma tabela HBase, inserir dados e, em seguida, consultar a tabela.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache HBase. Consulte [o cluster Create](../hadoop/apache-hadoop-linux-tutorial-get-started.md) para criar um cluster HDInsight.  Certifique-se de que escolhe o tipo de cluster **HBase.**

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Criar uma tabela e manipular dados

Para a maioria das pessoas, os dados são apresentados no formato de tabela:

![Dados tabulares HDInsight Apache HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

Em HBase (uma implementação do [Cloud BigTable),](https://cloud.google.com/bigtable/)os mesmos dados parecem:

![HDInsight Apache HBase BigTable dados](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Pode utilizar o SSH para se ligar aos clusters HBase e, em seguida, utilizar a Apache HBase Shell para criar tabelas HBase, inserir dados e consultar dados.

1. Utilize `ssh` o comando para se ligar ao seu cluster HBase. Edite o comando `CLUSTERNAME` abaixo substituindo pelo nome do seu cluster e, em seguida, introduza o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Utilize `hbase shell` o comando para iniciar a concha interativa HBase. Introduza o seguinte comando na sua ligação SSH:

    ```bash
    hbase shell
    ```

3. Use `create` o comando para criar uma tabela HBase com famílias de duas colunas. Introduza o seguinte comando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Utilize `list` o comando para listar todas as tabelas em HBase. Introduza o seguinte comando:

    ```hbase
    list
    ```

5. Utilize `put` o comando para inserir valores numa coluna especificada numa linha especificada numa determinada tabela. Introduza o seguinte comando:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Utilize `scan` o comando para `Contacts` digitalizar e devolver os dados da tabela. Introduza o seguinte comando:

    ```hbase
    scan 'Contacts'
    ```

7. Use `get` o comando para recolher o conteúdo de uma linha. Introduza o seguinte comando:

    ```hbase
    get 'Contacts', '1000'
    ```

    Vê-se resultados `scan` semelhantes como usar o comando porque só há uma linha.

8. Utilize `delete` o comando para eliminar um valor celular numa tabela. Introduza o seguinte comando:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Use `disable` o comando para desativar a mesa. Introduza o seguinte comando:

    ```hbase
    disable 'Contacts'
    ```

10. Use `drop` o comando para largar uma mesa da Base H. Introduza o seguinte comando:

    ```hbase
    drop 'Contacts'
    ```

11. Utilize `exit` o comando para parar a concha interativa HBase. Introduza o seguinte comando:

    ```hbase
    exit
    ```

Para obter mais informações sobre o esquema de tabela HBase, consulte [Introdução ao Design De Schema Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Para obter mais comandos HBase, consulte o artigo [Guia de referência Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a Apache HBase Shell para criar uma tabela HBase, inserir dados e, em seguida, consultar a tabela. Para saber mais sobre os dados armazenados na HBase, o próximo artigo irá mostrar-lhe como executar consultas com a Apache Spark.

> [!div class="nextstepaction"]
> [Utilizar o Apache Spark para ler e escrever dados do Apache HBase](../hdinsight-using-spark-query-hbase.md)