---
title: Copiar funcionalidades de otimização de desempenho de atividade
description: Conheça as principais funcionalidades que o ajudam a otimizar o desempenho da atividade de cópia na Azure Data Factory
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/09/2020
ms.openlocfilehash: d37b4648c0a37f16fe5c9d8794bd78417c5780ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257891"
---
# <a name="copy-activity-performance-optimization-features"></a>Copiar funcionalidades de otimização de desempenho de atividade

Este artigo descreve as funcionalidades de otimização de desempenho da atividade de cópia que pode alavancar na Azure Data Factory.

## <a name="data-integration-units"></a>Unidades de Integração de Dados

Uma Unidade de Integração de Dados é uma medida que representa o poder (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade na Azure Data Factory. A Unidade de Integração de Dados aplica-se apenas ao tempo de execução da [integração do Azure,](concepts-integration-runtime.md#azure-integration-runtime)mas não ao tempo de execução da [integração auto-hospedada.](concepts-integration-runtime.md#self-hosted-integration-runtime)

Os DIUs autorizados a capacitar uma execução de atividade de cópia é **entre 2 e 256**. Se não for especificado ou escolher "Auto" no UI, a Data Factory aplica dinamicamente a definição de DIU ideal com base no seu par de sumidouros e no padrão de dados. A tabela seguinte lista as gamas DIU suportadas e o comportamento predefinido em diferentes cenários de cópia:

| Cenário de cópia | Gama DIU suportada | DIUs padrão determinados por serviço |
|:--- |:--- |---- |
| Entre lojas de arquivos |- **Cópia de ou para ficheiro único:** 2-4 <br>- **Cópia de e para vários ficheiros:** 2-256 dependendo do número e tamanho dos ficheiros <br><br>Por exemplo, se copiar dados de uma pasta com 4 ficheiros grandes e optar por preservar a hierarquia, o DIU max-effective é de 16; quando optar por fundir ficheiros, o DIU max-eficaz é de 4. |Entre 4 e 32 dependendo do número e tamanho dos ficheiros |
| Da loja de ficheiros à loja sem ficheiros |- **Cópia de ficheiro único**: 2-4 <br/>- **Cópia de vários ficheiros**: 2-256 dependendo do número e tamanho dos ficheiros <br/><br/>Por exemplo, se copiar dados de uma pasta com 4 ficheiros grandes, o DIU max-effective é de 16. |- **Copiar em Azure SQL Database ou Azure Cosmos DB:** entre 4 e 16 dependendo do nível de pia (DTUs/RUs) e padrão de ficheiro de origem<br>- **Copiar para Azure Synapse Analytics** usando a declaração PolyBase ou COPY: 2<br>- Outro cenário: 4 |
| Da loja sem ficheiros à loja de ficheiros |- **Cópia de lojas de dados ativadas** por opção de partição (incluindo [oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 ao escrever para uma pasta e 2-4 ao escrever para um único ficheiro. Nota por divisória de dados de origem pode usar até 4 DIUs.<br>- **Outros cenários**: 2-4 |- **Cópia de REST ou HTTP:** 1<br/>- **Cópia da Amazon Redshift** usando UNLOAD: 2<br>- **Outro cenário**: 4 |
| Entre lojas não-arquivo |- **Cópia de lojas de dados ativadas** por opção de partição (incluindo [oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 ao escrever para uma pasta e 2-4 ao escrever para um único ficheiro. Nota por divisória de dados de origem pode usar até 4 DIUs.<br/>- **Outros cenários**: 2-4 |- **Cópia de REST ou HTTP:** 1<br>- **Outro cenário**: 4 |

Pode ver os DIUs utilizados para cada cópia executada na visão de monitorização da atividade de cópia ou na saída de atividade. Para mais informações, consulte a [monitorização da atividade do Copy](copy-activity-monitoring.md). Para anular este padrão, especifique um valor para a `dataIntegrationUnits` propriedade da seguinte forma. O *número real de DIUs* que a operação de cópia utiliza no tempo de execução é igual ou inferior ao valor configurado, dependendo do seu padrão de dados.

Será cobrado **# da duração \* da cópia DIUs \* usada preço unitário/DIU-hora**. Veja aqui os [preços](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)correntes. A moeda local e a desvalorização separada podem aplicar-se por tipo de subscrição.

**Exemplo:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Escalabilidade do tempo de execução da integração auto-hospedada

Se quiser obter uma maior entrada, pode aumentar ou aumentar o IR auto-hospedado:

- Se a CPU e a memória disponível no nó DE IR auto-hospedado não estiverem totalmente utilizadas, mas a execução de postos de trabalho simultâneos está a atingir o limite, deverá aumentar o número de postos de trabalho simultâneos que podem funcionar num nó.  Consulte [aqui](create-self-hosted-integration-runtime.md#scale-up) as instruções.
- Se, por outro lado, o CPU estiver no topo do nó DE IR auto-hospedado ou a memória disponível for baixa, pode adicionar um novo nó para ajudar a escalar a carga através dos vários nós.  Consulte [aqui](create-self-hosted-integration-runtime.md#high-availability-and-scalability) as instruções.

Nota nos seguintes cenários, a execução de uma única copy activity pode alavancar vários nós de IR auto-hospedados:

- Copiar dados de lojas baseadas em ficheiros, dependendo do número e tamanho dos ficheiros.
- Copiar dados da loja de dados ativada por oposição por partição (incluindo [Oracle,](connector-oracle.md#oracle-as-source) [Netezza,](connector-netezza.md#netezza-as-source) [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [Tabela SAP](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)dependendo do número de divisórias de dados.

## <a name="parallel-copy"></a>Cópia paralela

Pode definir cópia`parallelCopies` paralela (propriedade) na atividade da cópia para indicar o paralelismo que pretende que a atividade da cópia utilize. Pode pensar nesta propriedade como o número máximo de fios dentro da atividade de cópia que lê a partir da sua fonte ou escrever para as suas lojas de dados de pia em paralelo.

A cópia paralela é ortogonal para unidades de [integração](#data-integration-units) de dados ou [nódos de INFRAVERMELHOS auto-hospedados.](#self-hosted-integration-runtime-scalability) É contado em todos os nós DE DIUs ou auto-hospedados.

Para cada execução de atividade de cópia, por padrão, a Azure Data Factory aplique dinamicamente a definição de cópia paralela ideal com base no seu par de sumidouros e no padrão de dados. 

> [!TIP]
> O comportamento padrão da cópia paralela geralmente dá-lhe a melhor entrada, que é determinada automaticamente pela ADF com base no seu par de sumidouros, padrão de dados e número de DIUs ou na contagem de CPU/memória/nó do IR auto-hospedado. Consulte o desempenho da atividade da [cópia Troubleshoot](copy-activity-performance-troubleshooting.md) sobre quando sintonizar cópia paralela.

A tabela seguinte enumera o comportamento da cópia paralela:

| Cenário de cópia | Comportamento de cópia paralela |
| --- | --- |
| Entre lojas de arquivos | `parallelCopies`determina o paralelismo **ao nível dos ficheiros**. O pedaço dentro de cada ficheiro acontece por baixo de forma automática e transparente. Foi concebido para utilizar o melhor tamanho adequado para um determinado tipo de loja de dados para carregar dados em paralelo. <br/><br/>O número real de cópias paralelas que a atividade de cópia utiliza no tempo de execução não é mais do que o número de ficheiros que tem. Se o comportamento da cópia for fundir o **File** em sumidouro de ficheiros, a atividade da cópia não pode tirar partido do paralelismo de nível de ficheiro. |
| Da loja de ficheiros à loja sem ficheiros | - Ao copiar dados para a Base de Dados Azure SQL ou azure Cosmos DB, a cópia paralela por defeito também depende do nível de pia (número de DTUs/RUs).<br>- Ao copiar dados para a Tabela Azure, a cópia paralela por defeito é de 4. |
| Da loja sem ficheiros à loja de ficheiros | - Ao copiar dados de uma loja de dados ativada por oposição por partição (incluindo [Oráculo,](connector-oracle.md#oracle-as-source) [Netezza,](connector-netezza.md#netezza-as-source) [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [Tabela SAP](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), a cópia paralela predefinida é de 4. O número real de cópias paralelas que a atividade de cópia utiliza no tempo de execução não é mais do que o número de divisórias de dados que tem. Quando utilizar o Tempo de Funcionano de Integração Auto-hospedado e copiar para Azure Blob/ADLS Gen2, note que a cópia paralela max-eficaz é de 4 ou 5 por nó de IR.<br>- Para outros cenários, a cópia paralela não faz efeito. Mesmo que o paralelismo seja especificado, não é aplicado. |
| Entre lojas não-arquivo | - Ao copiar dados para a Base de Dados Azure SQL ou azure Cosmos DB, a cópia paralela por defeito também depende do nível de pia (número de DTUs/RUs).<br/>- Ao copiar dados de uma loja de dados ativada por oposição por partição (incluindo [Oráculo,](connector-oracle.md#oracle-as-source) [Netezza,](connector-netezza.md#netezza-as-source) [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [Tabela SAP](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), a cópia paralela predefinida é de 4.<br>- Ao copiar dados para a Tabela Azure, a cópia paralela por defeito é de 4. |

Para controlar a carga em máquinas que hospedam as suas lojas de dados, ou para `parallelCopies` afinar o desempenho da cópia, pode anular o valor predefinido e especificar um valor para a propriedade. O valor deve ser um inteiro maior ou igual a 1. No tempo de execução, para o melhor desempenho, a atividade de cópia utiliza um valor inferior ou igual ao valor que definiu.

Quando especificar um valor `parallelCopies` para a propriedade, tenha em conta o aumento de carga na sua fonte e afunde as lojas de dados. Considere também o aumento da carga para o tempo de funcionação da integração auto-hospedado se a atividade de cópia for capacitada por ela. Este aumento de carga acontece especialmente quando você tem múltiplas atividades ou executagens simultâneas das mesmas atividades que funcionam contra a mesma loja de dados. Se notar que a loja de dados ou o tempo de execução de `parallelCopies` integração auto-hospedado estão sobrecarregados com a carga, diminua o valor para aliviar a carga.

**Exemplo:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>Cópia encenada

Quando copia dados de uma loja de dados de origem para uma loja de dados de sumidouro, pode optar por utilizar o armazenamento Blob como uma loja de encenação provisória. A encenação é especialmente útil nos seguintes casos:

- **Deseja ingerir dados de várias lojas de dados no SQL Data Warehouse via PolyBase.** O SQL Data Warehouse utiliza a PolyBase como um mecanismo de alta carga para carregar uma grande quantidade de dados no Armazém de Dados SQL. Os dados de origem devem estar no armazenamento blob ou na Azure Data Lake Store, e devem satisfazer critérios adicionais. Quando carrega dados de uma loja de dados que não seja o armazenamento blob ou a Azure Data Lake Store, pode ativar a cópia de dados através do armazenamento de blob de encenação provisória. Nesse caso, a Azure Data Factory realiza as transformações de dados necessárias para garantir que satisfaz os requisitos da PolyBase. Em seguida, utiliza a PolyBase para carregar dados no SQL Data Warehouse de forma eficiente. Para mais informações, consulte Use PolyBase para carregar dados no Armazém de [Dados Azure SQL](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Por vezes demora algum tempo a realizar um movimento de dados híbrido (isto é, copiar de uma loja de dados no local para uma loja de dados em nuvem) sobre uma ligação de rede lenta.** Para melhorar o desempenho, pode utilizar cópias encenadas para comprimir os dados no local, de modo a que leve menos tempo para mover dados para a loja de dados de encenação na nuvem. Em seguida, pode descomprimir os dados na loja de preparação antes de carregar na loja de dados de destino.
- **Não quer abrir portas que não sejam o porto 80 e o porto 443 na sua firewall por causa das políticas de TI corporativas.** Por exemplo, quando copia dados de uma loja de dados no local para um lavatório de base de dados Azure SQL ou um depósito de dados Azure SQL, precisa de ativar a comunicação TCP de saída na porta 1433 para a firewall do Windows e para a sua firewall corporativa. Neste cenário, a cópia encenada pode aproveitar o tempo de execução da integração auto-hospedada para primeiro copiar dados para uma instância de armazenamento Blob sobre HTTP ou HTTPS na porta 443. Em seguida, pode carregar os dados em Base de Dados SQL ou Armazém de Dados SQL a partir da encenação de armazenamento Blob. Neste fluxo, não é necessário ativar a porta 1433.

### <a name="how-staged-copy-works"></a>Como funciona a cópia encenada

Quando ativa a função de encenação, primeiro os dados são copiados da loja de dados de origem para o armazenamento blob de encenação (traga o seu próprio). Em seguida, os dados são copiados da loja de dados de encenação para a loja de dados do lavatório. A Azure Data Factory gere automaticamente o fluxo de dois estágios para si. A Azure Data Factory também limpa dados temporários do armazenamento de encenação após o movimento de dados estar concluído.

![Cópia encenada](media/copy-activity-performance/staged-copy.png)

Quando ativa o movimento de dados utilizando uma loja de estágios, pode especificar se pretende que os dados sejam comprimidos antes de transferir os dados da loja de dados de origem para uma loja provisória ou de encenação de dados e, em seguida, descomprimido antes de mover dados de um provisório ou encenação armazenamento de dados na loja de dados do lavatório.

Atualmente, não é possível copiar dados entre duas lojas de dados que estão ligadas através de diferentes IRs auto-hospedados, nem com nem com cópia encenada. Para tal cenário, pode configurar duas atividades de cópia explicitamente acorrentadas para copiar de origem para encenação e depois de encenação a afundar.

### <a name="configuration"></a>Configuração

Configure a definição **de habilitação** Na atividade da cópia para especificar se pretende que os dados sejam encenados no armazenamento blob antes de os carregar numa loja de dados de destino. Quando definir o `TRUE` **habilitação** para especificar as propriedades adicionais listadas na tabela seguinte. Também precisa de criar um serviço de acesso partilhado azure Storage ou Storage para encenação se não tiver um.

| Propriedade | Descrição | Valor predefinido | Necessário |
| --- | --- | --- | --- |
| habilitarEncenação |Especifique se pretende copiar dados através de uma loja provisória. |Falso |Não |
| linkedServiceName |Especifique o nome de um serviço ligado ao [AzureStorage,](connector-azure-blob-storage.md#linked-service-properties) que se refere à instância de Armazenamento que utiliza como uma loja de encenação provisória. <br/><br/> Não é possível utilizar o Armazenamento com uma assinatura de acesso partilhado para carregar dados no Armazém de Dados SQL através da PolyBase. Podeusá-lo em todos os outros cenários. |N/D |Sim, quando **o habilitar É** definido para TRUE |
| path |Especifique o caminho de armazenamento blob que pretende conter os dados encenados. Se não fornecer um caminho, o serviço cria um recipiente para armazenar dados temporários. <br/><br/> Especifique um caminho apenas se utilizar o Armazenamento com uma assinatura de acesso partilhado, ou se necessitar de dados temporários para estar num local específico. |N/D |Não |
| enableCompression |Especifica se os dados devem ser comprimidos antes de serem copiados para o destino. Esta definição reduz o volume de dados transferidos. |Falso |Não |

>[!NOTE]
> Se utilizar cópia faseada com compressão ativada, o diretor de serviço ou a autenticação MSI para a realização do serviço ligado à bolha não é suportado.

Aqui está uma definição de amostra de uma atividade de cópia com as propriedades que são descritas na tabela anterior:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Impacto de faturação de cópia encenada

É cobrado com base em dois passos: duração da cópia e tipo de cópia.

* Quando utiliza a encenação durante uma cópia em nuvem, que está a copiar dados de uma loja de dados em nuvem para outra loja de dados em nuvem, ambas as fases empoderadas pelo tempo de execução da integração do Azure, é-lhe cobrada a [soma da duração da cópia para o passo 1 e passo 2] x [preço unitário de cópia em nuvem].
* Quando utiliza a encenação durante uma cópia híbrida, que está a copiar dados de uma loja de dados no local para uma loja de dados em nuvem, uma fase capacitada por um tempo de integração auto-hospedado, é cobrado pela duração [da cópia híbrida] x [preço unitário de cópia híbrida] + [duração da cópia em nuvem] x [preço unitário de cópia de nuvem].

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Copiar guia de desempenho e escalabilidade da atividade](copy-activity-performance.md)
- [Desempenho da atividade da cópia de resolução de problemas](copy-activity-performance-troubleshooting.md)
- [Utilize a Azure Data Factory para migrar dados do seu lago de dados ou armazém de dados para o Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)