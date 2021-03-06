---
title: Servidor Apache ZooKeeper falha em formar quórum no Azure HDInsight
description: Servidor Apache ZooKeeper falha em formar quórum no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250228"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Servidor Apache ZooKeeper falha em formar quórum no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

O servidor Apache ZooKeeper não é saudável, os sintomas podem incluir: ambos os Gestores de Recursos/Nódeos de Nome estão em modo de espera, as operações simples de HDFS não funcionam, `zkFailoverController` é interrompida e não pode ser iniciada, os trabalhos de Yarn/Spark/Livy falham devido a erros do Zookeeper. Os Daemons LLAP também podem não conseguir iniciar os clusters Secure Spark ou Interactive Hive. Pode ver uma mensagem de erro semelhante a:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

No Zookeeper Server regista qualquer hospedeiro zookeeper em /var/log/zookeeper/zookeeper-zookeeper-zookeeper-server-\*.out, você também pode ver o seguinte erro:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Causa

Quando o volume de ficheiros instantâneos é grande ou os ficheiros instantâneos são corrompidos, o servidor ZooKeeper não formará um quórum, o que causa serviços relacionados com zooKeeper pouco saudáveis. O servidor ZooKeeper não removerá ficheiros instantâneos antigos do seu diretório de dados, pelo contrário, é uma tarefa periódica a ser realizada pelos utilizadores para manter a saúde do ZooKeeper. Para mais informações, consulte [Os Pontos Fortes e Limitações do ZooKeeper](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Resolução

Consulte o diretório `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2` de dados do ZooKeeper e descubra se o tamanho do ficheiro de instantâneos é grande. Tome os seguintes passos se existirem grandes instantâneos:

1. Recua as fotos `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2`e.

1. Limpe as `/hadoop/zookeeper/version-2` fotos `/hadoop/hdinsight-zookeeper/version-2`dentro e.

1. Reinicie todos os servidores do ZooKeeper da Apache Ambari UI.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
