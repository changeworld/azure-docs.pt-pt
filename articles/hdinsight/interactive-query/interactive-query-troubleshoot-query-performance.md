---
title: Mau desempenho em consultas apache hive LLAP em Azure HDInsight
description: As consultas em Apache Hive LLAP estão a executar mais lentamente do que o esperado no Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895129"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Cenário: Mau desempenho em consultas apache hive LLAP em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de Consulta Interativa em clusters Azure HDInsight.

## <a name="issue"></a>Problema

As configurações de cluster predefinidas não estão suficientemente sintonizadas para a sua carga de trabalho. As consultas na Hive LLAP estão a executar mais lentamente do que o esperado.

## <a name="cause"></a>Causa

Isto pode acontecer devido a uma variedade de razões.

## <a name="resolution"></a>Resolução

LlAP está otimizado para consultas que envolvem juntas e agregados. Consultas como as seguintes não têm um bom desempenho num aglomerado de Colmeia Interativa:

```
select * from table where column = "columnvalue"
```

Para melhorar o desempenho da consulta de pontos na Hive LLAP, detete as seguintes configurações:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Também pode aumentar a utilização da cache LLAP para melhorar o desempenho com a seguinte alteração de configuração:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
