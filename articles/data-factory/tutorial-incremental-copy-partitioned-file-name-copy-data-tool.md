---
title: Copie gradualmente novos ficheiros com base no nome de ficheiro dividido pelo tempo
description: Crie uma fábrica de dados Azure e, em seguida, utilize a ferramenta Copy Data para carregar gradualmente novos ficheiros apenas com base no nome de ficheiro dividido no tempo.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/17/2020
ms.openlocfilehash: 50e82362f39da2d3f3ecfad2de1ed07b68f8a6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501640"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Copie gradualmente novos ficheiros com base no nome de ficheiro dividido pelo tempo utilizando a ferramenta Copy Data

Neste tutorial, irá utilizar o portal do Azure para criar uma fábrica de dados. Em seguida, utiliza a ferramenta Copy Data para criar um pipeline que copia gradualmente novos ficheiros com base no nome de ficheiro dividido pelo tempo do armazenamento do Azure Blob para o armazenamento do Azure Blob.

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure**: Utilize o armazenamento blob como _fonte_ e _sumidouro_ da loja de dados. Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Criar dois recipientes no armazenamento blob

Prepare o seu armazenamento Blob para o tutorial executando estes passos.

1. Criar um recipiente chamado **fonte.**  Crie um caminho de pasta como **2020/03/17/03** no seu recipiente. Crie um ficheiro de texto vazio e nomeie-o como **file1.txt**. Faça upload do ficheiro1.txt para a fonte do caminho da **pasta/2020/03/17/03** na sua conta de armazenamento.  Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).

    ![upload de ficheiros](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Por favor, ajuste o nome da pasta com o tempo UTC.  Por exemplo, se o atual tempo utc for de 3:38 AM em 17 de março de 2020, pode criar o caminho da pasta como **fonte/2020/03/17/03/** pela regra de **fonte/{Year}/{Month}/{Day}/{Hour}/**.

2. Criar um recipiente chamado **destino.** Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, selecione **Criar um recurso** > **Data + Analytics** > **Data Factory**:

   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

    O nome da fábrica de dados tem de ser _globalmente exclusivo_. Poderá receber a seguinte mensagem de erro:

   ![Mensagem de erro de nova fábrica de dados](./media/doc-common-process/name-not-available-error.png)

   Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_**ADFTutorialDataFactory**. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Selecione a **subscrição** do Azure na qual quer criar a nova fábrica de dados.
4. Em **Grupo de Recursos**, efetue um destes passos:

    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).

5. Em **Versão**, selecione **V2** para indicar a versão.
6. Sob **localização,** selecione a localização para a fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados (por exemplo, o Armazenamento do Azure e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) utilizados pela fábrica de dados podem estar noutras localizações e regiões.
7. Selecione **Criar**.
8. Depois de concluída a criação, é apresentada a home page **Fábrica de Dados**.
9. Para iniciar a interface de utilizador (IU) do Azure Data Factory num separador à parte, selecione o mosaico **Criar e Monitorizar**.

    ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utilizar a ferramenta Copiar Dados para criar um pipeline

1. Na página **Let's start,** selecione o título **Copy Data** para lançar a ferramenta Copy Data.

   ![Mosaico ferramenta Copiar Dados](./media/doc-common-process/get-started-page.png)

2. Na página **Propriedades,** tome os seguintes passos:

    a. Sob **o nome de tarefa,** introduza **DeltaCopyFromBlobPipeline**.

    b. Sob **a cadência de tarefaou no calendário**de tarefas, selecione Executar **regularmente no horário**.

    c. No **tipo de gatilho,** selecione **Janela tumbling**.

    d. Em **Recorrência,** insira **1 hora(s)**.

    e. Selecione **Next**.

    A IU do Data Factory cria um pipeline com o nome de tarefa especificado.

    ![Página Propriedades](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação
    
    b. Selecione Armazenamento Azure Blob na galeria e, em seguida, selecione Continuar.
    
    c. Na página **New Linked Service (Azure Blob Storage),** introduza um nome para o serviço ligado. Selecione a sua subscrição Azure e selecione a sua conta de armazenamento na lista de nomes da **conta de armazenamento.** Teste a ligação e, em seguida, **selecione Criar**.

    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Selecione o serviço ligado recém-criado na página da loja de **dados Source** e, em seguida, clique em **Next**.

4. Na página **Escolher o ficheiro ou pasta de entrada**, execute os seguintes passos:

    a. Navegue e selecione o recipiente de **origem** e, em seguida, selecione **Escolher**.

    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. No **comportamento de carregamento de ficheiros,** selecione carga **incremental: nomes de pasta/ficheiro seletivas a tempo**.

    c. Escreva o caminho dinâmico da pasta como **fonte/{year}/{month}/{day}/{hour}/** e altere o formato como mostrado na seguinte imagem. Verifique **a cópia binária** e clique **em Next**.

    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. Na página da loja de **dados Destination,** selecione o **AzureBlobStorage**, que é a mesma conta de armazenamento que a loja de fontede dados, e, em seguida, clique em **Next**.

    ![Página arquivo de dados de destino](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Na página **Escolha o ficheiro de saída ou página de pasta,** faça os seguintes passos:

    a. Navegue e selecione a pasta de **destino** e, em seguida, clique **em Escolher**.

    b. Escreva o caminho dinâmico da pasta como **destino/{year}/{month}/{day}/{hour}/** e altere o formato como seguintes:

    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Clique em **Seguinte**.

    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Na página **Definições**, selecione **Seguinte**.

8. Na página **Resumo**, reveja as definições e depois selecione **Seguinte**.

    ![Página de resumo](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).
    ![Página de implementação](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente.  É necessário esperar pelo funcionao do gasoduto quando este for acionado automaticamente (cerca de uma hora). Quando funcionar, clique no link de nome do pipeline **DeltaCopyFromBlobPipeline** para visualizar detalhes de execução de atividade ou reexecutar o pipeline. Selecione **Atualizar** para atualizar a lista.

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Ajuste a largura da coluna das colunas de **origem** e **destino** (se necessário) para apresentar mais detalhes, pode ver que o ficheiro fonte (file1.txt) foi copiado da *fonte/2020/03/17/03/* para *o destino/2020/03/17/03/* com o mesmo nome de ficheiro. 

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Também pode verificar o mesmo utilizando ohttps://storageexplorer.com/) Azure Storage Explorer ( para digitalizar os ficheiros.

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Crie outro ficheiro de texto vazio com o novo nome como **file2.txt**. Faça upload do ficheiro2.txt para a fonte do caminho da **pasta/2020/03/17/04** na sua conta de armazenamento. Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).

    > [!NOTE]
    > Pode estar ciente de que é necessário criar um novo caminho de pasta. Por favor, ajuste o nome da pasta com o tempo UTC.  Por exemplo, se o atual tempo utc for de 4:20 AM em Mar. 17th, 2020, pode criar o caminho da pasta como **fonte/2020/03/17/04/** pela regra de **{Year}/{Month}/{Day}/{Hour}/**.

13. Para voltar à vista **Pipeline Runs,** selecione **Todas as condutas de gasodutos**, e aguarde que o mesmo gasoduto seja acionado automaticamente após mais uma hora.  

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Selecione o novo link **DeltaCopyFromBlobPipeline** para a segunda execução do gasoduto quando chegar, e faça o mesmo para rever detalhes. Verá que o ficheiro fonte (file2.txt) foi copiado da **fonte/2020/03/17/04/** para **o destino/2020/03/17/04/** com o mesmo nome de ficheiro. Também pode verificar o mesmo utilizando ohttps://storageexplorer.com/) Azure Storage Explorer ( para digitalizar os ficheiros no recipiente de **destino.**


## <a name="next-steps"></a>Passos seguintes
Avance para o tutorial seguinte para saber como transformar dados através de um cluster do Spark no Azure:

> [!div class="nextstepaction"]
>[Transformar dados com um cluster do Spark na cloud](tutorial-transform-data-spark-portal.md)
