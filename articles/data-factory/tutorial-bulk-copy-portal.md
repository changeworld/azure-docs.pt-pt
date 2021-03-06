---
title: Copiar dados a granel utilizando o portal Azure
description: Saiba como utilizar o Azure Data Factory e a Atividade de Cópia para copiar dados de um arquivo de dados de origem para um arquivo de dados de destino em massa.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/27/2020
ms.openlocfilehash: 5108335c0b5d0a51559653d51bfe6154237731f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78162659"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Copiar várias tabelas em massa através do Azure Data Factory

Este tutorial demonstra **a cópia de uma série de tabelas da Base de Dados Azure SQL para a Azure Synapse Analytics (anteriormente SQL DW)**. Também pode aplicar o mesmo padrão noutros cenários de cópia. Por exemplo, copiar tabelas do SQL Server/Oracle para Azure SQL Database/Azure Synapse Analytics (anteriormente SQL DW)/Azure Blob, copiando diferentes caminhos de Blob para tabelas de bases de dados Azure SQL.

> [!NOTE]
> - Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

A um nível elevado, este tutorial envolve os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie a Base de Dados Azure SQL, a Azure Synapse Analytics (anteriormente SQL DW) e os serviços ligados ao Armazenamento Azure.
> * Crie conjuntos de dados Azure SQL Database e Azure Synapse Analytics (anteriormente SQL DW).
> * Criar um pipeline para procurar as tabelas a copiar e outro pipeline para executar a operação de cópia real. 
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Este tutorial utiliza o portal do Azure. Para saber mais sobre como utilizar outras ferramentas/SDKs para criar uma fábrica de dados, veja [Inícios rápidos](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Fluxo de trabalho ponto a ponto
Neste cenário, tem várias tabelas na Base de Dados Azure SQL que pretende copiar para a Azure Synapse Analytics (anteriormente SQL DW). Segue-se a sequência lógica de passos no fluxo de trabalho que ocorre nos pipelines:

![Fluxo de trabalho](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* O primeiro pipeline procura a lista de tabelas que têm de ser copiadas para os arquivos de dados de sink.  Em alternativa, pode manter uma tabela de metadados que apresenta uma lista de todas as tabelas a copiar para o arquivo de dados de sink. Em seguida, o pipeline aciona outro pipeline, que itera cada tabela na base de dados e executa a operação de cópia de dados.
* O segundo pipeline executa a cópia real. Aceita a lista de tabelas como um parâmetro. Para cada tabela da lista, copie a tabela específica na Base de Dados Azure SQL para a tabela correspondente em Azure Synapse Analytics (anteriormente SQL DW) utilizando cópia encenada através do [armazenamento Blob e da PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para melhor desempenho. Neste exemplo, o primeiro pipeline passa a lista de tabelas como um valor para o parâmetro. 

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Conta de Armazenamento Azure.** A conta de Armazenamento do Azure é utilizada como armazenamento de blobs de teste na operação de cópia em massa. 
* **Base de Dados Azure SQL**. Esta base de dados contém os dados de origem. 
* **Azure Synapse Analytics (anteriormente SQL DW)**. Este armazém de dados contém os dados copiados da Base de Dados SQL. 

### <a name="prepare-sql-database-and-azure-synapse-analytics-formerly-sql-dw"></a>Prepare a Base de Dados SQL e a Azure Synapse Analytics (anteriormente SQL DW)

**Preparar a Base de Dados SQL do Azure de origem**:

Crie uma Base de Dados SQL do Azure SQL com dados de exemplo do Adventure Works LT, seguindo o artigo [Criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md). Este tutorial copia todas as tabelas desta base de dados de amostras para um Azure Synapse Analytics (anteriormente SQL DW).

**Prepare a pia Azure Synapse Analytics (anteriormente SQL DW)**:

1. Se não tiver um Azure Synapse Analytics (anteriormente SQL DW), consulte o artigo [Create a SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) para obter passos para criar um.

1. Crie schemas de tabela correspondentes em Azure Synapse Analytics (anteriormente SQL DW). Irá utilizar o Azure Data Factory para migrar/copiar dados num passo mais à frente.

## <a name="azure-services-to-access-sql-server"></a>Serviços do Azure para aceder ao SQL Server

Tanto para a Base de Dados SQL como para a Azure Synapse Analytics (anteriormente SQL DW), os serviços Azure acedem ao servidor SQL. Certifique-se de que **os serviços e recursos do Azure acedem a esta** definição de servidor e stão **ligados** para o seu servidor Azure SQL. Esta definição permite que o serviço Data Factory leia dados da sua Base de Dados Azure SQL e escreva dados para o seu Azure Synapse Analytics (anteriormente SQL DW). 

Para verificar e ligar esta definição, aceda ao seu servidor Azure SQL > Firewalls de segurança > e redes virtuais > definir os **serviços e recursos do Allow Azure para aceder a este servidor** a **ON**.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. Vá ao [portal Azure.](https://portal.azure.com) 
1. À esquerda do menu do portal Azure, selecione **Criar um recurso** > **Analytics** > **Data Factory**. 
   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na página da nova fábrica de **dados,** introduza **a ADFTutorialBulkCopyDF** para **nome**. 
 
   O nome da fábrica de dados Azure deve ser **globalmente único.** Se vir o erro seguinte no campo de nome, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialBulkCopyDF). Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
1. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados. 
1. No **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
     Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
1. Selecione **V2** para a **versão**.
1. Selecione a **localização** da fábrica de dados. Para obter uma lista de regiões do Azure em que o Data Factory está atualmente disponível, selecione as regiões que lhe interessam na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.
1. Clique em **Criar**.
1. Depois de concluída a criação, selecione **Recorrer para** navegar na página **data Factory.** 
   
1. Clique no mosaico **Criar e Monitorizar** para iniciar a aplicação IU do Data Factory num separador à parte.
1. Na página **Let's start,** mude para o separador **Autor** no painel esquerdo, como mostra a seguinte imagem:

     ![Página Introdução](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados para ligar os seus arquivos de dados e computações a uma fábrica de dados. O serviço ligado tem as informações de ligação que o serviço Data Factory utiliza para se ligar ao arquivo de dados no runtime. 

Neste tutorial, liga as suas lojas azure SQL Database, Azure Synapse Analytics (anteriormente SQL DW) e lojas de dados de armazenamento Azure Blob à sua fábrica de dados. A Base de Dados SQL do Azure é o arquivo de dados de origem. O Azure Synapse Analytics (anteriormente SQL DW) é a loja de dados de sink/destination. O Armazenamento de Blob Azure deve ser encetado os dados antes de os dados ser carregados no Azure Synapse Analytics (anteriormente SQL DW) utilizando a PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Criar o serviço ligado da Base de Dados SQL do Azure de origem
Neste passo, vai criar um serviço ligado para ligar a sua base de dados SQL do Azure à fábrica de dados. 

1. Clique em **Ligações** na parte inferior da janela e clique **+ Novo** na barra de ferramentas ( O botão**de ligações** está localizado na parte inferior da coluna esquerda sob **recursos de fábrica).** 

1. Na janela **Novo Serviço Ligado**, selecione **Base de Dados SQL do Azure** e clique em **Continuar**. 
1. Na janela **New Linked Service (Azure SQL Database),** faça os seguintes passos: 

    a. Introduza **AzureSqlDatabaseLinkedService** em **Nome**.
    
    b. Em **Nome do servidor**, selecione o seu servidor do SQL do Azure.
    
    c. Em **Nome da base de dados**, selecione a sua base de dados SQL do Azure. 
    
    d. Introduza o **nome do utilizador** para ligar à base de dados SQL do Azure. 
    
    e. Introduza a **palavra-passe** do utilizador. 

    f. Para testar a ligação à base de dados SQL do Azure com as informações especificadas, clique em **Testar ligação**.
  
    g. Clique em **Criar** para salvar o serviço ligado.


### <a name="create-the-sink-azure-synapse-analytics-formerly-sql-dw-linked-service"></a>Crie o serviço ligado azure Synapse Analytics (anteriormente SQL DW)

1. No separador **Ligações**, clique em **+ Novo**, novamente na barra de ferramentas. 
1. Na janela **New Linked Service,** selecione **Azure Synapse Analytics (anteriormente SQL DW)** e clique **em Continuar**. 
1. Na janela **New Linked Service (Azure Synapse Analytics (anteriormente SQL DW)),** faça os seguintes passos: 
   
    a. Introduza **AzureSqlDWLinkedService** em **Nome**.
     
    b. Em **Nome do servidor**, selecione o seu servidor do SQL do Azure.
     
    c. Em **Nome da base de dados**, selecione a sua base de dados SQL do Azure. 
     
    d. Introduza **o nome de utilizador** para ligar à base de dados Azure SQL. 
     
    e. Introduza **palavra-passe** para o utilizador. 
     
    f. Para testar a ligação à base de dados SQL do Azure com as informações especificadas, clique em **Testar ligação**.
     
    g. Clique em **Criar**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Criar o serviço ligado de Armazenamento do Azure de teste
Neste tutorial, vai utilizar o armazenamento de Blobs do Azure como área de teste provisória para ativar o PolyBase para um melhor desempenho de cópia.

1. No separador **Ligações**, clique em **+ Novo**, novamente na barra de ferramentas. 
1. Na janela **Novo Serviço Ligado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**. 
1. Na janela **New Linked Service (Azure Blob Storage),** faça os seguintes passos: 

    a. Introduza **AzureStorageLinkedService** em **Nome**.                                                 
    b. Selecione a sua **conta de Armazenamento Azure** para **o nome da conta de armazenamento**.
    
    c. Clique em **Criar**.


## <a name="create-datasets"></a>Criar conjuntos de dados
Neste tutorial, vai criar conjuntos de dados de origem e sink, que especificam a localização onde os dados são armazenados. 

O conjunto de dados de entrada **AzureSqlDatabaseDataset** refere-se a **AzureSqlDatabaseLinkedService**. O serviço ligado especifica a cadeia de ligação para ligar à base de dados. O conjunto de dados especifica o nome da base de dados e a tabela que contém os dados de origem. 

O conjunto de dados de saída **AzureSqlDWDataset** refere-se a **AzureSqlDWLinkedService**. O serviço ligado especifica a cadeia de ligação para ligar ao Azure Synapse Analytics (anteriormente SQL DW). O conjunto de dados especifica a base de dados e a tabela para a qual os dados são copiados. 

Neste tutorial, as tabelas SQL de origem e destino não estão hard-coded nas definições do conjunto de dados. Em vez disso, a atividade ForEach transmite o nome da tabela no runtime à atividade Cópia. 

### <a name="create-a-dataset-for-source-sql-database"></a>Criar um conjunto de dados para a Base de Dados SQL de origem

1. Clique **+ (mais)** no painel esquerdo e, em seguida, clique em **Dataset**. 

    ![Menu Novo conjunto de dados](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Na janela **New Dataset,** selecione **Azure SQL Database**, e, em seguida, clique **em Continuar**. 
    
1. Na janela **'Propriedades definidas',** em **nome**, **introduza o Conjunto de Dados AzureSqlDatabase**. Sob **o serviço Linked,** selecione **AzureSqlDatabaseLinkedService**. Em seguida, clique em **OK**.

1. Mude para o separador **Ligação,** selecione qualquer tabela para **Tabela**. Esta é uma tabela fictícia. Vai especificar uma consulta no conjunto de dados de origem quando criar um pipeline. A consulta é utilizada para extrair dados da base de dados SQL do Azure. Em alternativa, pode clicar na caixa de verificação **editar** e introduzir **o nome dbo.dummy name** como nome de mesa. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics-formerly-sql-dw"></a>Criar um conjunto de dados para afundar a Azure Synapse Analytics (anteriormente SQL DW)

1. Clique em **+ (mais)**, no painel do lado esquerdo, e clique em **Conjunto de Dados**. 
1. Na janela **New Dataset,** selecione **Azure Synapse Analytics (anteriormente SQL DW)** e, em seguida, clique em **Continuar**.
1. Na janela **'Propriedades definidas',** em **nome**, **introduza O Conjunto AzureSqlDWDataset**. Sob **o serviço Linked,** selecione **AzureSqlDWLinkedService**. Em seguida, clique em **OK**.
1. Mude para o separador **Parâmetros**, clique em **+ Novo** e introduza **DWTableName** no nome do parâmetro. Se copiar/colar este nome a partir da página, certifique-se de que não há **nenhum personagem** espacial no final do **DWTableName**.
1. Mude para o separador **Ligação**, 

    a. Para **Tabela,** consulte a opção **Editar.** Introduza o **dbo** na primeira caixa de entrada do nome da tabela. E, em seguida, selecione na segunda caixa de entrada e clique no link de **conteúdo dinâmico Adicionar** abaixo. 

    ![Nome de tabela de ligação de conjunto de dados](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    b. Na página **Adicionar Conteúdo Dinâmico,** clique no **Nome DWTAble** em **Parâmetros**, `@dataset().DWTableName`que irá povoar automaticamente a caixa de texto de expressão superior, em seguida, clique em **Terminar**. A propriedade **tableName** do conjunto de dados está definida como o valor que é transmitido como argumento ao parâmetro **DWTableName**. A atividade ForEach itera através de uma lista de tabelas e transmite-as uma a uma à atividade Cópia. 

    ![Construtor de parâmetro do conjunto de dados](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Criar pipelines
Neste tutorial, vai criar dois pipelines, **IterateAndCopySQLTables** e **GetTableListAndTriggerCopyData**. 

O pipeline **GetTableListAndTriggerCopyData** executa duas ações:

* Procura a tabela do sistema da Base de Dados SQL do Azure para obter a lista de tabelas a copiar.
* Aciona o pipeline **IterateAndCopySQLTables** para fazer a cópia real de dados.

O oleoduto **IterateAndCopySQLTables** tem uma lista de tabelas como parâmetro. Para cada tabela da lista, copia dados da tabela em Azure SQL Database para Azure Synapse Analytics (anteriormente SQL DW) utilizando cópia encenada e PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Criar o pipeline IterateAndCopySQLTables

1. No painel do lado esquerdo, clique em **+ (mais)** e clique em **Pipeline**.

    ![Menu Novo pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. No separador **Geral**, especifique **IterateAndCopySQLTables** para o nome. 

1. Mude para o separador **Parâmetros** e execute as ações seguintes: 

    a. Clique em **+ Novo**. 
    
    b. Introduza **a tabela Lista** para o **nome**do parâmetro .
    
    c. Selecione **Matriz** para **Tipo**.

1. Na caixa de ferramentas **Atividades**, expanda **Iteração e Condições** e arraste e largue a atividade **ForEach** na superfície de desenho do pipeline. Também pode pesquisar por atividades na caixa de ferramentas **Atividades**. 

    a. No separador **Geral**, na parte inferior, introduza **IterateSQLTables** em **Nome**. 

    b. Mude para o separador **Definições,** clique na caixa de entrada para **itens**e, em seguida, clique no link de **conteúdo dinâmico Adicionar** abaixo. 

    c. Na página **Adicionar Conteúdo Dinâmico,** colapsar as secções de Variáveis e **Funções** do **Sistema,** clique na `@pipeline().parameter.tableList` **tabelaLista** em **Parâmetros**, que irá automaticamente povoar a caixa de texto de expressão superior como . Em seguida, clique em **Concluir**. 

    ![Construtor de parâmetro do Foreach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Mude para o separador **Atividades,** clique no ícone do **lápis** para adicionar uma atividade infantil à atividade **ForEach.**
    ![Construtor de atividades foreach](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. Na caixa de **ferramentas De Atividades,** expanda a **Move & Transfer**e arrasta a atividade de dados da **Cópia** para a superfície do designer de pipeline. Repare no menu de trilho na parte superior. **IterateAndCopySQLTable** é o nome do pipeline e **iterateSQLTables** é o nome de atividade ForEach. O estruturador está no âmbito da atividade. Para voltar ao editor de pipeline do editor ForEach, pode clicar no link no menu breadcrumb. 

    ![Copy em ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Mude para o separador **Origem** e siga os passos abaixo:

    1. Selecione **AzureSqlDatabaseDataset** em **Conjunto de Dados de Origem**. 
    1. Selecione opção **Consulta** para **Utilização**. 
    1. Clique na caixa de entrada **Consulta** -> selecione **Adicionar conteúdo dinâmico** abaixo -> introduza a seguinte expressão em **Consulta** -> selecione **Concluir**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Mude para o separador **Sink** e siga os passos abaixo: 

    1. Selecione **AzureSqlDWDataset** em **Conjunto de Dados de Sink**.
    1. Clique na caixa de entrada para obter o valor do parâmetro DWTableName -> selecione o **conteúdo dinâmico Adicionar** abaixo, introduza `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` a expressão como script, -> selecione **Acabamento**.
    1. Para o método Copy, selecione **PolyBase**. 
    1. Limpe a opção **por defeito do tipo Use.** 
    1. Clique na caixa de entrada **Cópia prévia do Script** -> selecione **Adicionar conteúdo dinâmico** abaixo -> introduza a seguinte expressão como um script -> selecione **Concluir**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Definições de sink de Copy](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Mude para o separador **Definições** e siga os passos abaixo: 

    1. Selecione a caixa de verificação para ativar a **encenação**.
    1. Selecione **AzureStorageLinkedService** em **Nome do Serviço Ligado da Conta de Armazenamento**.

1. Para validar as definições do pipeline, clique em **Validar**, na barra de ferramentas do mesmo. Certifique-se de que não há nenhum erro de validação. Para fechar o **Relatório de Validação do Pipeline**, clique em **>>**.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Criar o pipeline GetTableListAndTriggerCopyData

Este oleoduto faz duas ações:

* Procura a tabela do sistema da Base de Dados SQL do Azure para obter a lista de tabelas a copiar.
* Aciona o pipeline "IterateAndCopySQLTables" para executar a cópia de dados real.

1. No painel do lado esquerdo, clique em **+ (mais)** e clique em **Pipeline**.
1. No separador **Geral,** altere o nome do pipeline para **GetTableListAndTriggerCopyData**. 

1. Na caixa de **ferramentas De Atividades,** expanda a atividade de **lookup** **geral**e drag-drop para a superfície do designer de gasodutos, e faça os seguintes passos:

    1. Introduza **LookupTableList** em **Nome**. 
    1. Introduza **Obter a lista de tabelas da base de dados SQL do Azure** em **Descrição**.

1. Mude para o separador **Definições** e siga os passos abaixo:

    1. Selecione **AzureSqlDatabaseDataset** em **Conjunto de Dados de Origem**. 
    1. **Selecione Consulta** para **Utilização consulta**. 
    1. Introduza a seguinte consulta SQL em **consulta**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Desmarque a caixa de verificação do campo **Primeira linha apenas**.

        ![Atividade Lookup - página de definições](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Atividade de Pipeline de **execução** de drag-drop da caixa de ferramentas De atividades para a superfície do design de pipeline, e definir o nome para **TriggerCopy**.

1. Para **ligar** a atividade **de procuração** à atividade do **Gasoduto executar,** arraste a **caixa verde** anexa à atividade de lookup à esquerda da atividade do Pipeline executar.

    ![Ligar as atividades Lookup e Execute Pipeline](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Mude para o separador **Definições** da atividade do **Pipeline executar** e faça os seguintes passos: 

    1. Selecione **IterateAndCopySQLTables** em **Pipeline invocado**. 
    1. Expanda a secção **Avançada** e limpe a caixa de verificação para **esperar na conclusão**.
    1. Clique em **+ Novo** na secção **Parâmetros**. 
    1. Introduza **a tabela Lista** para o **nome**do parâmetro .
    1. Clique na caixa de entrada VALOR -> selecione **Adicionar conteúdo dinâmico** abaixo -> introduza `@activity('LookupTableList').output.value` como o valor do nome de tabela -> selecione **Concluir**. Está selendo a lista de resultados da atividade de Lookup como entrada para o segundo oleoduto. A lista de resultados contém a lista de tabelas cujos dados têm de ser copiados para o destino. 

        ![Atividade Execute pipeline - página de definições](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Para validar o pipeline, clique em **Validar**, na barra de ferramentas. Confirme que não há erros de validação. Para fechar o **Relatório de Validação do Pipeline**, clique em **>>**.

1. Para publicar entidades (conjuntos de dados, oleodutos, etc.) para o serviço Data Factory, clique em **Publicar tudo** em cima da janela. Aguarde até que a publicação seja bem-sucedida. 

## <a name="trigger-a-pipeline-run"></a>Acionar uma execução de pipeline

1. Vá para o pipeline **GetTableListAndTriggerCopyData,** clique em **Adicionar Gatilho** na barra de ferramentas de pipeline superior , e, em seguida, clique **em Trigger agora**. 

1. Confirme a execução na página de **execução** do Pipeline e, em seguida, selecione **Terminar**.

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Mude para o separador **Monitor.** Clique em **Refresh** até ver que corre para ambos os oleodutos da sua solução. Continue a atualizar a lista até ver o estado **Com Êxito**. 

1. Para visualizar as operações associadas ao pipeline **GetTableListAndTriggerCopyData,** clique na ligação de nome do pipeline para o pipeline. Deverá ver duas execuções de atividade para esta execução de pipeline. 
    ![Executar pipeline de monitorização](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Para visualizar a saída da atividade **de Procuração,** clique no link **'Saída'** junto à atividade sob a coluna **'NOME DE ATIVIDADE'.** Pode maximizar e restaurar a janela **Saída**. Depois de rever, clique em **X** para fechar a janela **Saída**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Para voltar à vista **Pipeline Runs,** clique em todas as ligações **do Pipeline** na parte superior do menu breadcrumb. Clique na ligação **IterateAndCopySQLTables** (sob a coluna **PIPELINE NAME)** para visualizar as execuções de atividade do gasoduto. Note que há uma atividade de **Cópia** executada para cada tabela na saída da atividade **de Lookup.** 

1. Confirme que os dados foram copiados para o alvo Azure Synapse Analytics (anteriormente SQL DW) que utilizou neste tutorial. 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie a Base de Dados Azure SQL, a Azure Synapse Analytics (anteriormente SQL DW) e os serviços ligados ao Armazenamento Azure.
> * Crie conjuntos de dados Azure SQL Database e Azure Synapse Analytics (anteriormente SQL DW).
> * Criar um pipeline para procurar as tabelas a copiar e outro pipeline para executar a operação de cópia real. 
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Avance para o tutorial seguinte para saber como copiar dados de forma incremental de uma origem para um destino:
> [!div class="nextstepaction"]
>[Copiar dados de forma incremental](tutorial-incremental-copy-portal.md)
