---
title: Apache Kafka aumentar escala - Azure HDInsight
description: Saiba como configurar discos geridos para o cluster do Apache Kafka no Azure HDInsight para aumentar a escalabilidade.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 56c25b7c77809a5cb7f4e539cff8e1815cd9976f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031715"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Configurar o armazenamento e a escalabilidade para o Apache Kafka no HDInsight

Saiba como configurar o número de discos geridos utilizados por [Apache Kafka](https://kafka.apache.org/) no HDInsight.

O Kafka no HDInsight utiliza o disco local das máquinas virtuais no cluster do HDInsight. Uma vez que o Kafka recebe um fluxo bastante pesado de dados de E/S, os [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) são utilizados para permitir um débito de transferência mais elevado e oferecer mais capacidade de armazenamento por nó. Se forem utilizadas unidades de disco rígido virtuais (VHD) tradicionais para o Kafka, cada nó tem um limite de 1 TB. Com os discos geridos, pode utilizar vários discos até alcançar 16 TB para cada nó do cluster.

O diagrama seguinte estabelece uma comparação entre o Kafka no HDInsight antes dos discos geridos e o Kafka no HDInsight com os discos geridos:

![kafka com arquitetura de discos geridos](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Configurar discos geridos: portal do Azure

1. Siga os passos em [Create an HDInsight cluster](../hdinsight-hadoop-create-linux-clusters-portal.md) (Criar um cluster no HDInsight) para compreender os passos comuns de criação de um cluster com o portal. Não complete o processo de criação do portal.

2. A partir da secção **de configuração & preços,** utilize o campo __Número de Nós__ para configurar o número de discos.

    > [!NOTE]  
    > O tipo de disco gerido pode ser __Standard__ (HDD) ou __Premium__ (SSD). Os discos Premium são utilizados com as VMs das séries DS e GS. Todos os outros tipos de VM utilizam discos Standard.

    ![secção de tamanho de cluster com os discos por nó de trabalhador destacado](./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Configurar discos geridos: modelo do Resource Manager

Para controlar o número de discos utilizados por nós de trabalho num cluster do Kafka, utilize a seguinte secção do modelo:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Pode encontrar um modelo completo que demonstre como configurar [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json)discos geridos em .

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o trabalho com Apache Kafka no HDInsight, consulte os seguintes documentos:

* [Use mirrormaker para criar uma réplica de Apache Kafka no HDInsight](apache-kafka-mirroring.md)
* [Use Apache Storm com Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Use Apache Spark com Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Ligue-se a Apache Kafka através de uma Rede Virtual Azure](apache-kafka-connect-vpn-gateway.md)

* [Blog HDInsight em discos geridos com Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
