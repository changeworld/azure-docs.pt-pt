---
title: Problemas de conectividade Apache Phoenix no Azure HDInsight
description: Problemas de conectividade entre Apache HBase e Apache Phoenix no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887296"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Cenário: Problemas de conectividade Apache Phoenix no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Incapaz de ligar a Apache HBase com Apache Phoenix. As razões podem variar.

## <a name="cause-incorrect-ip"></a>Causa: IP incorreto

IP incorreto do nó de Zookeeper ativo.

### <a name="resolution"></a>Resolução

O IP do nó de Zookeeper ativo pode ser identificado a partir do Ambari UI seguindo as ligações com **HBase** > **Quick Links** > **ZK (Ative)** > **Zookeeper Info**. Corrija o IP conforme necessário.

---

## <a name="cause-systemcatalog-table-offline"></a>Causa: SISTEMA. Tabela CATALOG offline

Ao executar comandos `!tables`como, recebe uma mensagem de erro semelhante a:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Ao executar comandos `count 'SYSTEM.CATALOG'`como, recebe uma mensagem de erro semelhante a:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Resolução

A partir do Apache Ambari UI, complete os seguintes passos para reiniciar o serviço HMaster em todos os nós do ZooKeeper:

1. A partir da secção **resumo** da HBase, vá para **HBase** > **Ative HBase Master**.

1. A partir da secção **Componentes,** reinicie o serviço HBase Master.

1. Repita estes passos para todos os restantes serviços **De Standby HBase Master.**

Pode levar até cinco minutos para o serviço HBase Master estabilizar e terminar a recuperação. Depois `SYSTEM.CATALOG` de a tabela voltar ao normal, o problema de conectividade com a Apache Phoenix deve ser resolvido automaticamente.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
