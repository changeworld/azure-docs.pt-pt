---
title: 'Quickstart: Configurar Apache Kafka no HDInsight usando o portal Azure'
description: Neste guia de início rápido, irá saber como criar um cluster do Apache Kafka no Azure HDInsight através do portal do Azure. Também irá saber mais sobre tópicos, subscritores e consumidores do Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/24/2020
ms.openlocfilehash: 90f7010970f70379c8adecc4214c44d896a1beaf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130271"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Quickstart: Criar o cluster Apache Kafka no Azure HDInsight usando o portal Azure

[Apache Kafka](./apache-kafka-introduction.md) é uma plataforma de streaming distribuída de código aberto. É frequentemente utilizado como mediador de mensagens, uma vez que fornece funcionalidades semelhantes a uma fila de mensagens de publicação-subscrição.

Neste início rápido, vai aprender a criar um cluster do Apache Kafka com o portal do Azure. Também vai saber como utilizar utilitários incluídos para enviar e receber mensagens com o Apache Kafka. Para obter explicações aprofundadas das configurações disponíveis, consulte [Conjunto de clusters em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter informações adicionais sobre a utilização do portal para criar clusters, consulte [Criar clusters no portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Só os recursos dentro da mesma rede virtual podem aceder à API do Apache Kafka. Neste guia de início rápido, irá aceder ao cluster diretamente através de SSH. Para ligar outros serviços, redes ou máquinas virtuais ao Apache Kafka, tem primeiro de criar uma rede virtual e, em seguida, criar os recursos dentro da rede. Para obter mais informações, veja o documento [Ligar ao Apache Kafka com uma rede virtual](apache-kafka-connect-vpn-gateway.md).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Criar um cluster do Apache Kafka

Para criar um cluster Apache Kafka no HDInsight, utilize os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. A partir do menu superior, selecione **+ Criar um recurso**.

    ![Portal Azure cria recurso HDInsight](./media/apache-kafka-get-started/azure-portal-create-resource.png)

1. Selecione **Analytics** > **Azure HDInsight** para ir à página de **cluster Create HDInsight.**

1. A partir do separador **Basics,** forneça as seguintes informações:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Subscrição    |  A partir da lista de lançamentos, selecione a subscrição Azure que é usada para o cluster. |
    |Grupo de recursos     | Crie um grupo de recursos ou selecione crie um existente.  Um grupo de recursos é um contentor de componentes do Azure.  Neste caso, o grupo de recursos contém o cluster do HDInsight e a conta de armazenamento do Azure dependente. |
    |Nome do cluster   | Introduza um nome globalmente exclusivo. O nome pode ser composto por até 59 caracteres, incluindo letras, números e hífenes. O primeiro e último carateres do nome não podem ser hífenes. |
    |Região    | A partir da lista de abandono, selecione uma região onde o cluster é criado.  Escolha uma região mais próxima de si para um melhor desempenho. |
    |Tipo de cluster| Selecione o tipo de **cluster selecione** para abrir uma lista. Da lista, selecione **Kafka** como o tipo de cluster.|
    |Versão|Será especificada a versão predefinida para o tipo de cluster. Selecione na lista de drop-down se pretender especificar uma versão diferente.|
    |Nome de utilizador e palavra-passe de início de sessão do cluster    | O nome de login predefinido é **administrador**. A palavra-passe deve ter pelo menos 10 caracteres de comprimento e deve conter pelo menos um dígito, uma maiúscula e uma letra minúscula, um carácter não alfanumérico (exceto os caracteres ' ' ' \). Certifique-se de que **não escolhe** uma palavra-passe comum, tal como "Pass@word1".|
    |Nome de utilizador de Secure Shell (SSH) | O nome de utilizador predefinido é **sshuser**.  Pode indicar outro nome de utilizador SSH. |
    |Utilize senha de login de cluster para SSH| Selecione esta caixa de verificação para utilizar a mesma palavra-passe para o utilizador SSH como a que forneceu para o utilizador de login do cluster.|

   ![Portal Azure cria fundamentos de cluster](./media/apache-kafka-get-started/azure-portal-cluster-basics.png)

    Cada região do Azure (localização) fornece _domínios de falha_. Um domínio de falha é um agrupamento lógico de hardware subjacente num centro de dados do Azure. Cada domínio de falha partilha um comutador de rede e uma fonte de alimentação. As máquinas virtuais e os discos geridos que implementam os nós num cluster HDInsight são distribuídos por esses domínios de falha. Esta arquitetura limita o possível impacto de falhas físicas de hardware.

    Para obter uma elevada disponibilidade de dados, selecione uma região (localização) que contenha __três domínios de falha__. Para obter informações sobre o número de domínios de falha numa região, consulte o documento [Disponibilidade das máquinas virtuais Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

    Selecione o **seguinte: >>** aba de armazenamento para avançar para as definições de armazenamento.

1. A partir do separador **Armazenamento,** forneça os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Tipo de armazenamento primário|Utilize o valor predefinido **do Armazenamento Azure**.|
    |Método de seleção|Utilize o valor predefinido **Selecione a partir da lista**.|
    |Conta de armazenamento primária|Utilize a lista de drop-down para selecionar uma conta de armazenamento existente, ou selecione **Criar nova**. Se criar uma nova conta, o nome deve ter entre 3 e 24 caracteres de comprimento, e pode incluir apenas números e letras minúsculas|
    |Contentor|Use o valor autopovoado.|

    ![HDInsight Linux começar a fornecer valores de armazenamento de cluster](./media/apache-kafka-get-started/azure-portal-cluster-storage.png "Fornecer valores de armazenamento para a criação de um cluster HDInsight")

    Selecione o separador **De Segurança + networking.**

1. Neste início rápido, mantenha as predefinições de segurança. Para saber mais sobre o pacote de Segurança Enterprise, visite [Configurar um cluster do HDInsight com Pacote de Segurança Enterprise com o Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Para aprender a usar a sua própria chave para encriptação de disco Apache Kafka, visite [encriptação de disco de chave gerida pelo Cliente](../disk-encryption.md)

   Se quiser ligar o seu cluster a uma rede virtual, selecione uma rede virtual na lista pendente **Rede Virtual**.

   ![Adicionar o cluster a uma rede virtual](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png)

    Selecione o separador de **preços Configuração +.**

1. Para garantir a disponibilidade de Apache Kafka no HDInsight, o __número de nós__ de entrada para o nó **operário** deve ser definido para 3 ou maior. O valor predefinido é 4.

    Os **discos Standard por entrada de nó de trabalhador** configura maqueta a escalabilidade de Apache Kafka no HDInsight. O Apache Kafka no HDInsight utiliza o disco local das máquinas virtuais no cluster para armazenar dados. O Apache Kafka recebe um fluxo intensivo de dados de E/S, pelo que o [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) é utilizado para garantir um elevado débito e uma maior capacidade de armazenamento por nó. O tipo de disco gerido pode ser __Standard__ (HDD) ou __Premium__ (SSD). O tipo de disco depende do tamanho da VM utilizado pelos nós de trabalho (mediadores do Apache Kafka). Os discos Premium são utilizados automaticamente com as VMs das séries DS e GS. Todos os outros tipos de VM utilizam discos Standard.

   ![Definir o tamanho do cluster do Apache Kafka](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Selecione o **separador Review + criar.**

1. Reveja a configuração do cluster. Altere quaisquer definições que estejam incorretas. Finalmente, selecione **Criar** para criar o cluster.

    ![resumo de configuração de cluster kafka](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    A criação do cluster pode demorar até 20 minutos.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Quando lhe for pedido, introduza a palavra-passe do utilizador SSH.

    Quando estiver ligado, verá informações semelhantes ao texto seguinte:

    ```output
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    83 packages can be updated.
    37 updates are security updates.


    Welcome to Apache Kafka on HDInsight.

    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Obtenha a informação do anfitrião do Zoológico apache e corretor

Ao trabalhar com Kafka, deve conhecer os anfitriões do *Zoológico* Apache e do *Corretor.* Estes anfitriões são utilizados com a API do Apache Kafka e com muitos dos utilitários que são enviados com o Kafka.

Nesta secção, obtém-se a informação do anfitrião da API do Apache Ambari REST no cluster.

1. Instale [jq,](https://stedolan.github.io/jq/)um processador JSON de linha de comando. Este utilitário é usado para analisar documentos JSON, e é útil para analisar a informação do anfitrião. A partir da ligação SSH `jq`aberta, introduza o seguinte comando para instalar:

    ```bash
    sudo apt -y install jq
    ```

1. Configurar a variável de senha. Substitua-a `PASSWORD` pela senha de login do cluster e introduza o comando:

    ```bash
    export password='PASSWORD'
    ```

1. Extrair o nome do cluster corretamente aparado. O invólucro real do nome do cluster pode ser diferente do que se espera, dependendo de como o cluster foi criado. Este comando obterá o invólucro real, e depois armazená-lo-á numa variável. Introduza o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Se está a fazer este processo de fora do cluster, há um procedimento diferente para armazenar o nome do cluster. Obtenha o nome do cluster em maiúsculas do portal Azure. Em seguida, substitua `<clustername>` o nome do cluster `export clusterName='<clustername>'`no seguinte comando e execute-o: .


1. Para definir uma variável ambiental com informações do anfitrião zookeeper, use o comando abaixo. O comando recupera todos os anfitriões do Zookeeper, e depois devolve apenas as duas primeiras entradas. Isto acontece porque é desejável que exista alguma redundância para o caso de um anfitrião estar inacessível.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Este comando requer acesso ambari. Se o seu cluster estiver por trás de um NSG, execute este comando a partir de uma máquina que possa aceder a Ambari. 

1. Para verificar se a variável de ambiente está definida corretamente, utilize o comando seguinte:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    Este comando devolve informações semelhantes ao texto seguinte:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. Para definir uma variável de ambiente com as informações do anfitrião do mediador do Apache Kafka, utilize o comando seguinte:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Este comando requer acesso ambari. Se o seu cluster estiver por trás de um NSG, execute este comando a partir de uma máquina que possa aceder a Ambari. 

1. Para verificar se a variável de ambiente está definida corretamente, utilize o comando seguinte:

    ```bash
    echo $KAFKABROKERS
    ```

    Este comando devolve informações semelhantes ao texto seguinte:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Gerir tópicos do Apache Kafka

O Kafka armazena fluxos de dados em *tópicos*. Pode utilizar o utilitário `kafka-topics.sh` para gerir os tópicos.

* **Para criar um tópico**, utilize o seguinte comando na ligação SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Este comando liga ao Zookeeper com as informações do anfitrião armazenadas em `$KAFKAZKHOSTS`. Em seguida, cria um tópico do Apache Kafka denominado **test**.

    * Os dados armazenados neste tópico estão particionados em oito partições.

    * Cada partição é replicada em três nós de trabalho no cluster.

        Se tiver criado o cluster numa região do Azure que fornece três domínios de falha, utilize um fator de replicação de 3. Caso contrário, utilize um fator de replicação de 4.
        
        Nas regiões com três domínios de falha, um fator de replicação de 3 permite que as réplicas sejam distribuídas pelos domínios de falha. Nas regiões com dois domínios de falha, um fator de replicação de 4 distribui as réplicas uniformemente pelos domínios.
        
        Para obter informações sobre o número de domínios de falha numa região, consulte o documento [Disponibilidade das máquinas virtuais Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        O Apache Kafka não está ciente dos domínios de falha do Azure. Durante a criação de réplicas de partição para tópicos, poderá não distribuir as réplicas corretamente para fins de elevada disponibilidade.

        Para garantir uma elevada disponibilidade, utilize a ferramenta de reequilíbrio da [divisória Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Esta ferramenta deve ser executada a partir de uma ligação SSH ao nó principal do cluster do Apache Kafka.

        Para garantir a maior disponibilidade dos seus dados do Apache Kafka, deve reequilibrar as réplicas de partições do tópico quando:

        * Criar um novo tópico ou partição

        * Aumentar verticalmente um cluster

* **Para listar os tópicos**, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Este comando apresenta uma lista de tópicos disponíveis no cluster do Apache Kafka.

* **Para eliminar um tópico**, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Este comando elimina o tópico com o nome `topicname`.

    > [!WARNING]  
    > Se eliminar o tópico `test` criado anteriormente, tem de recriá-lo. O mesmo é utilizado por passos mais adiante neste documento.

Para obter mais informações sobre os comandos disponíveis com o utilitário `kafka-topics.sh`, utilize o seguinte comando:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Produzir e consumir registos

O Kafka armazena *registos* nos tópicos. Os registos são produzidos por *produtores* e consumidos por *consumidores*. Os produtores e consumidores comunicam com o serviço do *mediador do Kafka*. Cada nó de trabalho no cluster do HDInsight é um anfitrião do mediador do Apache Kafka.

Utilize os seguintes passos para armazenar os registos no tópico de teste criado anteriormente e, em seguida, leia-os através de um consumidor:

1. Para escrever registos no tópico, utilize o utilitário `kafka-console-producer.sh` a partir da ligação SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Após este comando, chega a uma linha vazia.

2. Introduza uma mensagem de texto na linha vazia e carregue em Enter. Introduza algumas mensagens desta forma e, em seguida, utilize **Ctrl + C** para regressar à linha de comandos normal. Cada linha é enviada como um registo separado para o tópico do Apache Kafka.

3. Para ler registos do tópico, utilize o utilitário `kafka-console-consumer.sh` a partir da ligação SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Este comando obtém os registos do tópico e apresenta-os. A utilização de `--from-beginning` indica ao consumidor para iniciar a partir do início da transmissão em fluxo, para que todos os registos sejam obtidos.

    Se estiver a utilizar uma versão antiga do Kafka, substitua `--bootstrap-server $KAFKABROKERS` por `--zookeeper $KAFKAZKHOSTS`.

4. Utilize __Ctrl + C__ para parar o consumidor.

Também podem criar programaticamente produtores e consumidores. Para um exemplo de utilização desta API, consulte o Apache Kafka Producer e a Consumer API com o documento [HDInsight.](apache-kafka-producer-consumer-api.md)

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este início rápido, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

> [!WARNING]  
> A eliminação de um cluster Apache Kafka no HDInsight elimina todos os dados armazenados em Kafka.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Use Apache Spark com Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
