---
title: 'Tutorial: Analise os dados da Spark Do Apache Azure HDInsight com o Power BI'
description: Tutorial - Use microsoft Power BI para visualizar os clusters HDInsight armazenados de dados do Apache Spark
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 03/02/2020
ms.openlocfilehash: d7330225ecbdc6715847821a47c140a3c2b8d1b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251957"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Tutorial: Analise os dados da Apache Spark usando o Power BI no HDInsight

Neste tutorial, aprende-se a usar o [Microsoft Power BI](https://powerbi.microsoft.com/) para visualizar dados num cluster Apache Spark em [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Utilizar o Power BI para ver dados do Spark

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Concluir o artigo [Tutorial: Load data and run queries on an Apache Spark cluster in Azure HDInsight](./apache-spark-load-data-run-query.md) (Tutorial: Carregar dados e executar consultas num cluster do Apache Spark no Azure HDInsight).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Opcional: [Subscrição](https://app.powerbi.com/signupredirect?pbi_source=web)de teste Power BI .

## <a name="verify-the-data"></a>Verificar os dados

O [Caderno Jupyter](https://jupyter.org/) que criou no [tutorial anterior](apache-spark-load-data-run-query.md) inclui código para criar uma `hvac` tabela. Esta tabela baseia-se no ficheiro CSV disponível em `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`todos os clusters HDInsight Spark em . Utilize o seguinte procedimento para verificar os dados.

1. No bloco de notas do Jupyter, cole o seguinte código e prima **SHIFT + ENTER**. O código verifica a existência das tabelas.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    O resultado tem o seguinte aspeto:

    ![Mostrar tabelas no Spark](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Se tiver fechado o bloco de notas antes de iniciar este tutorial, `hvactemptable` é limpa, pelo que não é incluída na saída.  Só as tabelas do Hive que estejam armazenadas na metastore (indicadas com **False** (Falso), na coluna **isTemporary**) podem ser acedidas a partir das ferramentas de BI. Neste tutorial, vai ligar à tabela **hvac** que criou.

2. Cole o seguinte código numa célula vazia e prima **SHIFT + ENTER**. O código verifica os dados na tabela.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    O resultado tem o seguinte aspeto:

    ![Mostrar linhas da tabela hvac no Spark](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. No menu **File (Ficheiro)** do bloco de notas, selecione **Close and Halt (Fechar e Parar)**. Encerre o bloco de notas para libertar os recursos.

## <a name="visualize-the-data"></a>Ver os dados

Nesta secção, vai utilizar o Power BI para criar visualizações, relatórios e dashboards a partir dos dados do cluster do Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop

Os primeiros passos para começar a trabalhar com o Spark são ligar ao cluster no Power BI Desktop, carregar dados a partir do cluster e criar uma visualização básica com base nesses dados.

> [!NOTE]  
> O conector demonstrado neste artigo está atualmente em pré-visualização. Se tiver comentários, envie-os através dos sites [Comunidade do Power BI](https://community.powerbi.com/) ou [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Abra o Power BI Desktop. Feche o ecrã de respingo de arranque se abrir.

2. A partir do separador **Home,** navegue para **obter dados** > **mais. . . .**

    ![Obtenha dados no Power BI Desktop a partir de HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Obtenha dados no Power BI da Apache Spark BI")

3. Introduza `Spark` na caixa de pesquisa, selecione **Azure HDInsight Spark**, e, em seguida, selecione **Connect**.

    ![Obtenha dados no Power BI da Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Obtenha dados no Power BI da Apache Spark BI")

4. Introduza o URL do `mysparkcluster.azurehdinsight.net`cluster (no formulário) na caixa de texto **do Servidor.**

5. No modo de **conectividade Data,** selecione **DirectQuery**. Em seguida, selecione **OK**.

    Pode utilizar qualquer um dos modos de conectividade de dados com o Spark. Se utilizar o DirectQuery, as alterações são refletidas nos relatórios sem atualizar o conjunto de dados completo. Se importar os dados, tem de atualizar o conjunto de dados para ver as alterações. Para obter mais informações sobre como e quando utilizar o DirectQuery, veja [Utilizar o DirectQuery no Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Introduza as informações da conta de login HDInsight e, em seguida, selecione **Connect**. O nome predefinido da conta é *admin*.

7. Selecione a `hvac` tabela, aguarde para ver uma pré-visualização dos dados e, em seguida, selecione **Carregar**.

    ![Nome e senha de utilizador do cluster de faíscas](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nome e senha de utilizador do cluster de faíscas")

    O Power BI Desktop tem as informações de que precisa para se ligar ao cluster do Spark e carregar dados da tabela `hvac`. A tabela e as colunas são apresentadas no painel **Fields** (Campos).

8. Visualize a variância entre a temperatura de destino e a temperatura real de cada edifício:

    1. No painel **VISUALIZATIONS** (VISUALIZAÇÕES), selecione **Area Chart** (Gráfico de Área).

    2. Arraste o campo **BuildingID** para **Axis** (Eixo) e os campos **ActualTemp** e **TargetTemp** para **Value** (Valor).

        ![adicionar colunas de valor](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "adicionar colunas de valor")

        O diagrama tem o seguinte aspeto:

        ![soma gráfico área](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "soma gráfico área")

        Por predefinição, a visualização mostra a soma de **ActualTemp** e **TargetTemp**. Selecione a seta para baixo ao lado de **ActualTemp** e **TragetTemp** no painel de Visualizações, pode ver que a **Soma** é selecionada.

    3. Selecione as setas para baixo ao lado de **ActualTemp** e **TragetTemp** no painel de Visualizações, selecione **Average** para obter uma média de temperaturas reais e-alvo para cada edifício.

        ![média de valores](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "média de valores")

        A visualização de dados deverá ser semelhante à da captura de ecrã. Mova o cursor sobre a visualização para obter sugestões de contexto com dados relevantes.

        ![gráfico de área](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "gráfico de área")

9. Navegue para**Guardar** **Ficheiros,** > introduza o nome `BuildingTemperature` para o ficheiro e, em seguida, selecione **Guardar**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicar o relatório no serviço Power BI (opcional)

O serviço Power BI permite-lhe partilhar relatórios e dashboards em toda a sua organização. Nesta secção, vai publicar primeiro o conjunto de dados e o relatório. Em seguida, vai afixar o relatório a um dashboard. Os dashboards são normalmente usados para se concentrar em um subconjunto de dados em um relatório. Só tens uma visualização no teu relatório, mas ainda é útil passar pelos degraus.

1. Abra o Power BI Desktop.

1. A partir do separador **Home,** **selecione Publicar**.

    ![Publicar a partir de Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publicar a partir do Power BI Desktop")

1. Selecione um espaço de trabalho para publicar o seu conjunto de dados e reportar e, em seguida, **selecione Selecione**. Na imagem seguinte, está selecionada a área de trabalho **My Workspace** predefinida.

    ![Selecione espaço de trabalho para publicar dataset e reportar para](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Selecione espaço de trabalho para publicar dataset e reportar para")

1. Depois de ter sido bem sucedida a publicação, selecione **Open 'BuildingTemperature.pbix' em Power BI**.

    ![Publicar sucesso, clique para introduzir credenciais](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publicar sucesso, clique para introduzir credenciais")

1. No serviço Power BI, selecione **credenciais De entrada**.

    ![Insira credenciais no serviço Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Insira credenciais no serviço Power BI")

1. Selecione **credenciais de Edição**.

    ![Editar credenciais no serviço Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Editar credenciais no serviço Power BI")

1. Introduza as informações da conta de login HDInsight e, em seguida, selecione **Iniciar sessão**. O nome predefinido da conta é *admin*.

    ![Inscreva-se no cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Inscreva-se no cluster Spark")

1. No painel esquerdo, vá a **Workspaces** > **My Workspace** > **REPORTS,** em seguida, selecione **BuildingTemperature**.

    ![Relatório listado sob relatórios no painel esquerdo](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Relatório listado sob relatórios no painel esquerdo")

    Também deverá ver **BuildingTemperature** em **DATASETS** (CONJUNTOS DE DADOS), no painel do lado esquerdo.

    O elemento visual que criou no Power BI Desktop está agora disponível no serviço Power BI.

1. Passe o cursor sobre a visualização e, em seguida, selecione o ícone do pino no canto superior direito.

    ![Relatório no serviço Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Relatório no serviço Power BI")

1. Selecione "Novo painel `Building temperature`de instrumentos", introduza o nome e, em seguida, selecione **Pin**.

    ![Pin para novo painel de instrumentos](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Pin para novo painel de instrumentos")

1. No relatório, selecione **Ir para o painel de instrumentos**.

O elemento visual é afixado ao dashboard. Pode adicionar outros elementos visuais ao relatório e afixá-los ao mesmo dashboard. Para obter mais informações sobre relatórios e dashboards, consulte [Relatórios em Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) e [Dashboards em Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, pode pretender eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar clusters quando não estão em uso.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o [Microsoft Power BI](https://powerbi.microsoft.com/) para visualizar dados num cluster Apache Spark em [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Avance para o próximo artigo para ver se pode criar uma aplicação de machine learning.

> [!div class="nextstepaction"]
> [Criar uma aplicação de aprendizagem automática](./apache-spark-ipython-notebook-machine-learning.md)