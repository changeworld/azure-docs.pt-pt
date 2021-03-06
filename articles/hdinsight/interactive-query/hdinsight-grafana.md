---
title: Use Grafana no Azure HDInsight
description: Saiba como aceder ao dashboard Grafana com clusters Apache Hadoop em Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082868"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Aceder ao Grafana no Azure HDInsight

[Grafana](https://grafana.com/) é um popular, construtor de gráficos e dashboards. Grafana é rica em recursos; Não só permite que os utilizadores criem dashboards personalizáveis e partilháveis, como também oferece dashboards modelo/scripted, integração LDAP, múltiplas fontes de dados e muito mais.

Atualmente, em Azure HDInsight, grafana é suportada com os tipos de cluster Spark, HBase, Kafka e Interactive Query. Não é suportado para clusters com Enterprise Security Pack habilitado.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-apache-hadoop-cluster"></a>Criar um aglomerado de Hadoop Apache

Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md). Para **o tipo cluster**, selecione **Spark,** **Kafka,** **HBase**ou **Interactive Query**.

## <a name="access-the-grafana-dashboard"></a>Aceda ao painel grafana

1. A partir de um `https://CLUSTERNAME.azurehdinsight.net/grafana/` navegador web, navegue até onde clusterNAME é o nome do seu cluster.

1. Introduza as credenciais de utilizador do cluster Hadoop.

1. O painel grafana aparece e parece este exemplo:

    ![Painel web HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Painel hDInsight Grafana")

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine o cluster que criou com os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Na caixa **de pesquisa** na parte superior, digite **HDInsight**.

1. Selecione **clusters HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparecem, selecione o **...** ao lado do cluster que criou.

1. Selecione **Eliminar**. Selecione **Sim**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como analisar os dados com o HDInsight, veja os seguintes artigos:

* [Utilize a Colmeia Apache com HDInsight](../hadoop/hdinsight-use-hive.md).

* [Utilizar mapeiaReduzir com HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Começar a usar ferramentas Visual Studio Hadoop para HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
