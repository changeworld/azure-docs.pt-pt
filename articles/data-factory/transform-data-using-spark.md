---
title: Transformar dados usando a atividade do Spark
description: Aprenda a transformar dados executando programas Spark a partir de um oleoduto de fábrica de dados Azure utilizando a Atividade spark.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/31/2018
ms.openlocfilehash: eb887a7d9081875c28964ddb1e3d1b2e609862fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912959"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transforme dados utilizando a atividade da Spark na Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-spark.md)
> * [Versão atual](transform-data-using-spark.md)

A atividade spark em um [pipeline](concepts-pipelines-activities.md) Data Factory executa um programa Spark por [conta própria](compute-linked-services.md#azure-hdinsight-linked-service) ou a [pedido](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do cluster HDInsight. Este artigo baseia-se no artigo sobre atividades de transformação de [dados,](transform-data.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas. Quando utiliza um serviço ligado à Spark a pedido, a Data Factory cria automaticamente um cluster Spark para que apenas em tempo para processar os dados e, em seguida, apague o cluster assim que o processamento estiver concluído. 


## <a name="spark-activity-properties"></a>Propriedades de atividade de faísca
Aqui está a definição json da amostra de uma Atividade de Faísca:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

O quadro seguinte descreve as propriedades JSON utilizadas na definição JSON:

| Propriedade              | Descrição                              | Necessário |
| --------------------- | ---------------------------------------- | -------- |
| nome                  | Nome da atividade no oleoduto.    | Sim      |
| descrição           | Texto descrevendo o que a atividade faz.  | Não       |
| tipo                  | Para a Atividade de Faísca, o tipo de atividade é HDInsightSpark. | Sim      |
| linkedServiceName     | Nome do HDInsight Spark Linked Service em que o programa Spark funciona. Para conhecer este serviço ligado, consulte o artigo de [serviços ligados à Compute.](compute-linked-services.md) | Sim      |
| SparkJobLinkedService | O serviço ligado ao Armazenamento Azure que detém o ficheiro de trabalho spark, dependências e registos.  Se não especificar um valor para esta propriedade, o armazenamento associado ao cluster HDInsight é utilizado. O valor deste imóvel só pode ser um serviço ligado ao Armazenamento Azure. | Não       |
| rootPath              | O recipiente e pasta Azure Blob que contém o ficheiro Spark. O nome do ficheiro é sensível ao caso. Consulte a secção de estrutura de pastas (secção seguinte) para obter detalhes sobre a estrutura desta pasta. | Sim      |
| entradaFilePath         | Caminho relativo para a pasta raiz do código/embalagem Spark. O ficheiro de entrada deve ser um ficheiro Python ou um ficheiro .jar. | Sim      |
| className             | Classe principal java/faísca da aplicação      | Não       |
| argumentos             | Uma lista de argumentos de linha de comando para o programa Spark. | Não       |
| proxyUser             | A conta de utilizador para personificar para executar o programa Spark | Não       |
| sparkConfig           | Especifique valores para as propriedades de configuração de Faíscalistas listadas no tópico: [Configuração de faíscas - Propriedades de aplicação](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Não       |
| getDebugInfo          | Especifica quando os ficheiros de registo Spark são copiados para o armazenamento Azure utilizado pelo cluster HDInsight (ou) especificado pelo sparkJobLinkedService. Valores permitidos: Nenhum, sempre ou falha. Valor predefinido: Nenhum. | Não       |

## <a name="folder-structure"></a>Estrutura de pasta
Os trabalhos de faísca são mais extensíveis do que os empregos de porco/colmeia. Para trabalhos spark, você pode fornecer múltiplas dependências, tais como pacotes de frascos (colocados no java CLASSPATH), ficheiros python (colocados no PYTHONPATH) e quaisquer outros ficheiros.

Crie a seguinte estrutura de pasta no armazenamento Azure Blob referenciado pelo serviço ligado ao HDInsight. Em seguida, carregue ficheiros dependentes para as subpastas apropriadas na pasta raiz representada pela **entradaFilePath**. Por exemplo, faça upload de ficheiros python para a subpasta pyFiles e ficheiros de frascos para a subpasta dos frascos da pasta raiz. No prazo de execução, o serviço Data Factory espera a seguinte estrutura de pasta no armazenamento do Blob Azure:     

| Caminho                  | Descrição                              | Necessário | Tipo   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.`(raiz)            | O percurso raiz do trabalho de Spark no serviço ligado ao armazenamento | Sim      | Pasta |
| &lt;utilizador definido&gt; | O caminho que aponta para o arquivo de entrada do spark job | Sim      | Ficheiro   |
| ./frascos                | Todos os ficheiros sob esta pasta são carregados e colocados no caminho de classe java do cluster | Não       | Pasta |
| ./pyFiles             | Todos os ficheiros sob esta pasta são carregados e colocados no PYTHONPATH do cluster | Não       | Pasta |
| ./ficheiros               | Todos os ficheiros desta pasta são carregados e colocados no diretório de trabalho do executor | Não       | Pasta |
| ./arquivos            | Todos os ficheiros desta pasta não estão comprimidos | Não       | Pasta |
| ./registos                | A pasta que contém registos do cluster Spark. | Não       | Pasta |

Aqui está um exemplo para um armazenamento contendo dois ficheiros de trabalho Spark no Armazenamento De Blob Azure referenciado pelo serviço ligado hDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade de porco](transform-data-using-hadoop-pig.md)
* [MapReduzir a atividade](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote de aprendizagem automática](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
