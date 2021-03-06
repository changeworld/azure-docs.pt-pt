---
title: Guia para manutenção preditiva para aeroespacial - Processo de Ciência de Dados da Equipa
description: Um guia técnico para o Modelo de Solução para manutenção preditiva em aeroespacial, utilitários e transporte.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 0542106f70e96b6c2f63e8ca03d2532de191d365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477175"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Guia técnico do Modelo de Solução para manutenção preditiva em aeroespacial

> [!Important]
> Este artigo foi depreciado. A discussão sobre a Manutenção Preditiva em Aeroespacial ainda é relevante, mas para informação atual, consulte a Visão Geral da [Solução para o Público Empresarial.](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace)


Os modelos de solução são projetados para acelerar o processo de construção de uma demonstração E2E. Um modelo implantado prevê a sua subscrição com componentes necessários e, em seguida, constrói as relações entre eles. Também sede o pipeline de dados com dados de amostra saem de uma aplicação geradora de dados, que descarrega e instala na sua máquina local depois de implementar o modelo de solução. Os dados do gerador hidratam o gasoduto de dados e começam a gerar previsões de aprendizagem automática, que podem ser visualizadas no painel power BI.

O processo de implementação guia-o através de vários passos para configurar as suas credenciais de solução. Certifique-se de que regista as credenciais, tais como nome de solução, nome de utilizador e palavra-passe que fornece durante a implementação. 


Os objetivos deste artigo são:
- Descreva a arquitetura de referência e os componentes aprovisionados na sua subscrição.
- Demonstre como substituir os dados da amostra por dados próprios. 
- Mostre como modificar o modelo de solução.  

## <a name="overview"></a>Descrição geral
![Arquitetura de manutenção preditiva](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Ao implementar a solução, ativa serviços Azure, incluindo Hub de Eventos, Stream Analytics, HDInsight, Data Factory e Machine Learning. O diagrama de arquitetura mostra como o modelo de manutenção preditiva para solução aeroespacial é construído. Você pode investigar estes serviços no portal Azure clicando-os no diagrama do modelo de solução criado com a implementação da solução (exceto hDInsight, que é provisionado a pedido quando as atividades de pipeline relacionadas são necessárias para executar e são apagado depois).
Descarregue uma [versão em tamanho real do diagrama](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

As seguintes secções descrevem as partes da solução.

## <a name="data-source-and-ingestion"></a>Fonte de dados e ingestão
### <a name="synthetic-data-source"></a>Fonte de dados sintéticos
Para este modelo, a fonte de dados utilizada é gerada a partir de uma aplicação de ambiente de trabalho descarregada que executa localmente após uma implementação bem sucedida.

Para encontrar as instruções para descarregar e instalar esta aplicação, selecione o primeiro nó, Gerador de Dados de Manutenção Preditiva, no diagrama do modelo de solução. As instruções encontram-se na barra de Propriedades. Esta aplicação alimenta o serviço [Azure Event Hub](#azure-event-hub) com pontos de dados, ou eventos, utilizados no resto do fluxo de solução. Esta fonte de dados é derivada de dados disponíveis publicamente do [repositório](https://c3.nasa.gov/dashlink/resources/139/) de dados da NASA utilizando o Conjunto de Dados de Simulação de [Degradação do Motor Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

A aplicação de geração de eventos povoa o Azure Event Hub apenas enquanto está a ser executado no seu computador.  

### <a name="azure-event-hub"></a>Hub de Eventos do Azure  
O serviço [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela Fonte de Dados Sintéticos.

## <a name="data-preparation-and-analysis"></a>Preparação e análise de dados  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Utilize o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) para fornecer análises quase em tempo real no fluxo de entrada do serviço [Azure Event Hub.](#azure-event-hub) Em seguida, publica os resultados num dashboard [Power BI,](https://powerbi.microsoft.com) bem como arquiva todos os eventos de entrada cruas no serviço [de Armazenamento Azure](https://azure.microsoft.com/services/storage/) para posterior processamento pelo serviço [Azure Data Factory.](https://azure.microsoft.com/documentation/services/data-factory/)

### <a name="hdinsight-custom-aggregation"></a>Agregação personalizada HDInsight
Executar scripts [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orquestrado por Azure Data Factory) usando hDInsight para fornecer agregados sobre os eventos brutos arquivados usando o recurso Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Faça previsões sobre a vida útil restante (RUL) de um determinado motor de avião utilizando as inputs recebidas com o [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning/) (orquestrado pela Azure Data Factory). 

## <a name="data-publishing"></a>Publicação de dados
### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Utilize a Base de [Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) para armazenar as previsões recebidas pelo Azure Machine Learning, que são depois consumidas no painel power [BI.](https://powerbi.microsoft.com)

## <a name="data-consumption"></a>Consumo de dados
### <a name="power-bi"></a>Power BI
Use [o Power BI](https://powerbi.microsoft.com) para mostrar um dashboard que contém agregações e alertas fornecidos pela [Azure Stream Analytics,](https://azure.microsoft.com/services/stream-analytics/)bem como previsões rul armazenadas na Base de [Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) que foram produzidas usando [O Machine Learning Azure](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Como trazer os seus próprios dados
Esta secção descreve como trazer os seus próprios dados para o Azure, e quais as áreas que requerem alterações para os dados que traz para esta arquitetura.

É pouco provável que o seu conjunto de dados corresponda ao conjunto de dados utilizado pelo Conjunto de Dados de Simulação de Degradação do [Motor Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) utilizado para este modelo de solução. Compreender os seus dados e os requisitos são cruciais na forma como modifica este modelo para trabalhar com os seus próprios dados. 

As seguintes secções discutem as partes do modelo que requerem modificações quando um novo conjunto de dados é introduzido.

### <a name="azure-event-hub"></a>Hub de Eventos do Azure
O Azure Event Hub é genérico; os dados podem ser publicados no centro em formato CSV ou JSON. Não ocorre nenhum tratamento especial no Azure Event Hub, mas é importante que compreenda os dados que lhe são alimentados.

Este documento não descreve como ingerir os seus dados, mas pode facilmente enviar eventos ou dados para um Hub de Eventos Azure utilizando as APIs do Hub de Eventos.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Utilize o recurso Azure Stream Analytics para fornecer análises quase em tempo real, lendo a partir de fluxos de dados e divulgando dados para qualquer número de fontes.

Para o modelo de manutenção preditiva para soluçõaeroespacial, a consulta Azure Stream Analytics é composta por quatro consultas sub, cada consulta consumindo eventos do serviço Azure Event Hub, com saídas para quatro locais distintos. Estas saídas consistem em três conjuntos de dados Power BI e um local de armazenamento Azure.

A consulta Azure Stream Analytics pode ser encontrada por:

* Ligue-se ao portal Azure
* Localização dos trabalhos ![stream](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) analytics Stream Analytics que foram gerados quando a solução foi implementada (*por exemplo,* **manutençãosa02asapbi** e **manutençãoa02asablob** para a solução de manutenção preditiva)
* Seleção
  
  * ***INPUTS*** para ver a entrada de consulta
  * ***CONSULTA*** para ver a consulta em si
  * ***SAÍDAS*** para ver as diferentes saídas

Informações sobre a construção de consulta sonorizadora Azure Stream Analytics podem ser encontradas na Referência de [Consulta de Análise de Fluxo](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) no MSDN.

Nesta solução, as consultas fornecem três conjuntos de dados com informações de análise quase em tempo real sobre o fluxo de dados que está a chegar a um dashboard Power BI fornecido como parte deste modelo de solução. Como existe conhecimento implícito sobre o formato de dados que chegam, estas consultas devem ser alteradas com base no seu formato de dados.

A consulta no segundo Stream Analytics manutenção de **trabalhosa02asablob** simplesmente produz todos os eventos [do Event Hub](https://azure.microsoft.com/services/event-hubs/) para o Armazenamento [Azure](https://azure.microsoft.com/services/storage/) e, portanto, não requer qualquer alteração, independentemente do seu formato de dados, uma vez que a informação completa do evento é transmitida para armazenamento.

### <a name="azure-data-factory"></a>Azure Data Factory
O serviço [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orquestra o movimento e o processamento de dados. No Modelo de Manutenção Preditiva para Solução Aeroespacial, a fábrica de dados é composta por três [oleodutos](../../data-factory/concepts-pipelines-activities.md) que movem e processam os dados utilizando várias tecnologias.  Aceda à sua fábrica de dados abrindo o nó da Fábrica de Dados na parte inferior do diagrama do modelo de solução criado com a implementação da solução. Os erros nos seus conjuntos de dados devem-se à implantação da fábrica de dados antes do início do gerador de dados. Esses erros podem ser ignorados e não impedem que a sua fábrica de dados funcione.

![Erros de conjunto de dados da fábrica de dados](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

Esta secção discute os [gasodutos e atividades necessários contidos](../../data-factory/concepts-pipelines-activities.md) na Fábrica de [Dados Azure.](https://azure.microsoft.com/documentation/services/data-factory/) Aqui está uma visão diagrama da solução.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Dois dos oleodutos desta fábrica contêm scripts [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) usados para dividir e agregar os dados. Quando notado, os scripts estão localizados na conta [De armazenamento Azure](https://azure.microsoft.com/services/storage/) criada durante a configuração. A sua localização é:\\\\\\\\hive\\ \\ script de manutenção (ou https://[O seu nome de solução].blob.core.windows.net/maintenancesascript).

Semelhante suplicou as consultas [do Azure Stream Analytics,](#azure-stream-analytics-1) os scripts [da Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) têm conhecimento implícito sobre o formato de dados que está a chegar e devem ser alterados com base no seu formato de dados.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Este [pipeline](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade - uma atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para dividir os dados colocados no [Armazenamento Azure](https://azure.microsoft.com/services/storage/) durante o trabalho de [Azure Stream Analytics.](https://azure.microsoft.com/services/stream-analytics/)

O script [da Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarefa de partição é ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MlScoringPipeline*
Este [oleoduto](../../data-factory/concepts-pipelines-activities.md) contém várias atividades cujo resultado final são as previsões pontuadas da experiência [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) associada a este modelo de solução.

As atividades incluídas são:

* A atividade [do HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) utilizando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script [da Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para realizar agregações e engenharia de recursos necessária para a experiência [de Aprendizagem automática azure.](https://azure.microsoft.com/services/machine-learning/)
  O script [da Colmeia](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarefa de partição é ***PrepareMLInput.hql***.
* [Copiar](https://msdn.microsoft.com/library/azure/dn835035.aspx) atividade que move os resultados da atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) para uma única bolha de [armazenamento Azure](https://azure.microsoft.com/services/storage/) acedida pela atividade [AzureMLBatchScoring.](https://msdn.microsoft.com/library/azure/dn894009.aspx)
* A atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) chama a experiência [Azure Machine Learning,](https://azure.microsoft.com/services/machine-learning/) com resultados colocados numa única bolha de [armazenamento Azure.](https://azure.microsoft.com/services/storage/)

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Este [pipeline](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade - uma atividade [de Cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados da experiência [Azure Machine Learning](#azure-machine-learning) do ***MLScoringPipeline*** para a Base de [Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) aprovisionada como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A experiência [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) utilizada para este modelo de solução fornece a vida útil remanescente (RUL) de um motor de avião. A experiência é específica do conjunto de dados consumido e requer modificação ou substituição específica dos dados introduzidos.

## <a name="monitor-progress"></a>Monitorizar o progresso
Uma vez lançado o Gerador de Dados, o gasoduto começa a desidratar-se e os diferentes componentes da sua solução começam a entrar em ação seguindo os comandos emitidos pela fábrica de dados. Há duas maneiras de monitorizar o oleoduto.

* Um dos trabalhos da Stream Analytics escreve os dados brutos de entrada para o armazenamento de blob. Se clicar no componente de armazenamento blob da sua solução a partir do ecrã, implementou com sucesso a solução e, em seguida, clique em Open no painel certo, leva-o ao [portal Azure](https://portal.azure.com/). Uma vez lá, clique em Blobs. No próximo painel, vê-se uma lista de contentores. Clique em dados de **manutenção.** No painel seguinte está a pasta **rawdata.** Dentro da pasta rawdata estão pastas com nomes como hora=17 e hora=18. A presença destas pastas indica que os dados brutos estão a ser gerados no seu computador e armazenados no armazenamento de bolhas. Deve ver ficheiros csv com tamanhos finitos em MB nessas pastas.
* O último passo do oleoduto é escrever dados (por exemplo, previsões de machine learning) para a Base de Dados SQL. Pode ter de esperar um máximo de três horas para que os dados apareçam na Base de Dados SQL. Uma forma de monitorizar a quantidade de dados disponíveis na sua Base de Dados SQL é através do [portal Azure](https://portal.azure.com/). No painel esquerdo, localize o ![ícone](./media/predictive-maintenance-technical-guide/icon-SQL-databases.png) SQL DATABASES SQL e clique nele. Em seguida, localize o **seu pmaintenancedb** da sua base de dados e clique nele. Na página seguinte na parte inferior, clique no MANAGE.
   
    ![Gerir ícone](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Aqui, pode clicar em New Consulta e consulta para o número de linhas (por exemplo, selecione contagem (*) a partir de PMResult). À medida que a sua base de dados cresce, o número de linhas na tabela deve aumentar.

## <a name="power-bi-dashboard"></a>Dashboard do Power BI

Instale um dashboard Power BI para visualizar os dados do Azure Stream Analytics (caminho quente) e a previsão do lote resulta da aprendizagem automática Azure (caminho frio).

### <a name="set-up-the-cold-path-dashboard"></a>Configurar o painel de instrumentos de caminho frio
No gasoduto de dados do caminho frio, o objetivo é obter o RUL preditivo (vida útil restante) de cada motor de avião uma vez que termine um voo (ciclo). O resultado da previsão é atualizado a cada 3 horas para prever os motores dos aviões que terminaram um voo nas últimas 3 horas.

O Power BI liga-se a uma Base de Dados SQL Azure como fonte de dados, onde os resultados da previsão são armazenados. 

Nota: 
1.    Ao implementar a sua solução, uma previsão aparecerá na base de dados dentro de 3 horas. O ficheiro pbix que veio com o download do Gerador contém alguns dados de sementes para que possa criar o painel power BI imediatamente. 
2.    Neste passo, o pré-requisito é descarregar e instalar o software gratuito [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Os seguintes passos guiam-no sobre como ligar o ficheiro pbix à Base de Dados SQL que foi fiada no momento da implementação da solução contendo dados (por exemplo, resultados de previsão) para visualização.

1. Pegue as credenciais da base de dados.
   
   Necessitará do nome do servidor de base de **dados, nome da base de dados, nome do utilizador e palavra-passe** antes de passar para os próximos passos. Aqui estão os passos para guiá-lo como encontrá-los.
   
   * Uma vez que **'Azure SQL Database'** no seu modelo de modelo de solução fique verde, clique nele e clique em **'Abrir'**.
   * Verá um novo separador/janela do navegador que exibe a página do portal Azure. Clique em **"Grupos** de recursos" no painel esquerdo.
   * Selecione a subscrição que está a utilizar para implementar a solução e, em seguida, selecione **'YourSolutionName\_ResourceGroup'**.
   * No novo painel pop out, clique no ícone do ![ícone](./media/predictive-maintenance-technical-guide/icon-sql.png) SQL para aceder à sua base de dados. O nome da sua base de dados está ao lado deste ícone (por exemplo, **'pmaintenancedb'),** e o nome do servidor de base de **dados** está listado na propriedade do nome Server e deve ser semelhante ao **YourSolutionName.database.windows.net**.
   * O **nome** de utilizador e a **palavra-passe** da base de dados são os mesmos que o nome de utilizador e a palavra-passe previamente registados durante a implementação da solução.
2. Atualize a fonte de dados do ficheiro de relatório do caminho frio com o Power BI Desktop.
   
   * Na pasta onde descarregou e desfechou o ficheiro Generator, clique duas vezes no ficheiro **\\PowerBI PredictiveMaintenanceAerospace.pbix.** Se vir mensagens de aviso quando abrir o ficheiro, ignore-as. Na parte superior do ficheiro, clique em **'Editar Consultas'.**
     
     ![Editar Consultas](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Verá duas mesas, **RemainingUsefulLife** e **PMResult**. Selecione a ![primeira tabela](./media/predictive-maintenance-technical-guide/icon-query-settings.png) e clique no ícone de definições de consulta ao lado de **'Source'** em **'PASSOS APLICADOS'** no painel **'Definições** de consulta' direito. Ignore quaisquer mensagens de aviso que apareçam.
   * Na janela pop out, substitua **'Server'** e **'Database'** pelos nomes do seu próprio servidor e base de dados e, em seguida, clique em **'OK'**. Para o nome do servidor, certifique-se de especificar a porta 1433 **(YourSolutionName.database.windows.net, 1433**). Deixe o campo base de dados como **pmaintenancedb**. Ignore as mensagens de aviso que aparecem no ecrã.
   * Na próxima janela, verá duas opções no painel esquerdo **(Windows** e **Database).** Clique em **'Database'**( preencha o seu nome de **utilizador'** e **'Password'** (o nome de utilizador e a palavra-passe que introduziu quando implementou a solução pela primeira vez e criou uma Base de Dados Azure SQL). ***Selecione em que nível aplicar estas definições***, verifique a opção de nível de base de dados. Em seguida, clique em **'Ligar'**.
   * Clique na segunda tabela **PMResult** e, em seguida, clique no ![ícone](./media/predictive-maintenance-technical-guide/icon-navigation.png) de navegação ao lado de **'Source'** em **'PASSOS APLICADOS'** no painel **'Definições** de Consulta' direito, e atualize os nomes do servidor e da base de dados como nos passos acima e clique em OK.
   * Assim que for guiado de volta para a página anterior, feche a janela. Uma mensagem exibe - clique **Em Aplicar**. Por último, clique no botão **Guardar** para guardar as alterações. O ficheiro Power BI estabeleceu agora a ligação ao servidor. Se as suas visualizações estiverem vazias, certifique-se de que limpa as seleções das visualizações para visualizar todos os dados clicando no ícone de borracha no canto superior direito das lendas. Utilize o botão de atualização para refletir novos dados sobre as visualizações. Inicialmente, basta ver os dados de sementes nas suas visualizações, uma vez que a fábrica de dados está programada para refrescar a cada 3 horas. Após 3 horas, verá novas previsões refletidas nas suas visualizações quando atualizar os dados.
3. (Opcional) Publique online o painel de instrumentos de via fria para [Power BI](https://www.powerbi.com/). Este passo precisa de uma conta Power BI (ou conta office 365).
   
   * Clique em **'Publicar'** e poucos segundos depois aparece uma janela exibindo "Publishing to Power BI Success!" com uma marca de verificação verde. Clique no link abaixo "Open PredictiveMaintenanceAerospace.pbix in Power BI". Para encontrar instruções detalhadas, consulte [Publicar a partir do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Para criar um novo **+** dashboard: clique no sinal ao lado da secção **Dashboards** no painel esquerdo. Introduza o nome "Demo de Manutenção Preditiva" para este novo painel de instrumentos.
   * Assim que abrir o ![relatório, clique no ícone](./media/predictive-maintenance-technical-guide/icon-pin.png) PIN para fixar todas as visualizações no seu painel de instrumentos. Para encontrar instruções detalhadas, consulte [pin um azulejo para um painel power BI a partir de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Vá à página do painel de instrumentos e ajuste o tamanho e localização das suas visualizações e edite os seus títulos. Para encontrar instruções detalhadas sobre como editar os seus azulejos, consulte [Editar um azulejo -- redimensionar, mover, mudar, mudar o nome, pin, eliminar, adicionar hiperligação](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Aqui está um painel de exemplo com algumas visualizações de caminho frio presas nele.  Dependendo do tempo que executa o seu gerador de dados, os seus números nas visualizações podem ser diferentes.
     <br/>
     ![Vista final](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Para agendar a atualização dos dados, passe o rato sobre ![o conjunto](./media/predictive-maintenance-technical-guide/icon-elipsis.png) de dados **PredictiveMaintenanceAerospace,** clique no ícone Ellipsis e, em seguida, escolha **'Agenda Refresh**' .
     <br/>
     > [!NOTE]
     > Se vir uma mensagem de aviso, clique em **Editar Credenciais** e certifique-se de que as suas credenciais de base de dados são as mesmas descritas no passo 1.
     <br/>
     ![Atualizar horários](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Expandir a secção **'Atualização de** Horários'. Ligue "mantenha os seus dados atualizados".
     <br/>
   * Agende a atualização com base nas suas necessidades. Para obter mais informações, consulte [data refresh in Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Painel de instrumentos de caminho quente de configuração
Os seguintes passos guiam-no como visualizar a produção de dados a partir de trabalhos do Stream Analytics que foram gerados no momento da implementação da solução. É necessária uma conta [online Power BI](https://www.powerbi.com/) para realizar os seguintes passos. Se não tiver uma conta, pode [criar uma.](https://powerbi.microsoft.com/pricing)

1. Adicione a saída power BI no Azure Stream Analytics (ASA).
   
   * Deve seguir as instruções no [Azure Stream Analytics & Power BI: Um painel de análise para visibilidade em tempo real dos dados de streaming](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para configurar a saída do seu trabalho de Azure Stream Analytics como painel power BI.
   * A consulta da ASA tem três saídas que são de vigilância de **aeronaves,** **alerta**de aeronaves e **voos por hora.** Pode ver a consulta clicando no separador de consulta. Correspondente a cada uma destas tabelas, precisa de adicionar uma saída à ASA. Quando adicionar a primeira saída **(monitor**de aeronaves) certifique-se de que o nome de **saída,** **o nome** do conjunto de dados e o nome da **tabela** são os mesmos **(monitor de aeronaves).** Repita os passos para adicionar saídas para alerta de **aeronaves**e **voos por hora.** Depois de ter adicionado as três tabelas de saída e iniciado o trabalho da ASA, deve receber uma mensagem de confirmação ("Starting Stream Analytics job tenancetenancesa02asapbi conseguiu").
2. Inicie sessão [no Power BI online](https://www.powerbi.com)
   
   * Na secção datasets do painel esquerdo no Meu Espaço de Trabalho, o ***DATASET*** dá nomes de **aeronaves monitorado,** alerta de **aeronaves,** e **voos de hora de viagem** devem aparecer. Estes são os dados de streaming que empurrou do Azure Stream Analytics no passo anterior. Os **voos por hora** podem não aparecer ao mesmo tempo que os outros dois conjuntos de dados devido à natureza da consulta SQL por trás. No entanto, deve aparecer depois de uma hora.
   * Certifique-se de que o painel de ***visualizações*** está aberto e é mostrado no lado direito do ecrã.
3. Assim que tiver os dados a fluir para o Power BI, pode começar a visualizar os dados de streaming. Abaixo está um painel de exemplo com algumas visualizações de caminho quente presas nele. Pode criar outros azulejos do dashboard com base em conjuntos de dados apropriados. Dependendo do tempo que executa o seu gerador de dados, os seus números nas visualizações podem ser diferentes.

    ![Vista do Dashboard](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Aqui ficam alguns passos para criar um dos azulejos acima – o azulejo "Fleet View of Sensor 11 vs. Threshold 48.26":
   
   * Clique no monitor de **aeronaves** dataset na secção datasets do painel esquerdo.
   * Clique no ícone **do Gráfico de Linha.**
   * Clique no painel **"Campos"** **para** que mostre em "Eixo" no painel de **Visualizações.**
   * Clique em "s11" e\_"alerta s11" para que ambos apareçam em "Valores". Clique na pequena seta junto ao alerta **S11** e **\_S11**, mude "Soma" para "Média".
   * Clique em **SAVE** na parte superior e nomeie o relatório "aircraftmonitor". O relatório denominado "monitor de aeronaves" é mostrado na secção **relatórios** no painel **do Navegador** à esquerda.
   * Clique no ícone **Pin Visual** no canto superior direito desta tabela de linhas. Uma janela "Pin to Dashboard" pode aparecer para escolher um painel de instrumentos. Selecione "Demo de Manutenção Preditiva", em seguida, clique em "Pin".
   * Passe o rato sobre este azulejo no painel de instrumentos, clique no ícone "editar" no canto superior direito para alterar o seu título para "Fleet View of Sensor 11 vs. Threshold 48.26" e legenda para "Average across fleet over time".

## <a name="delete-your-solution"></a>Elimine a sua solução
Certifique-se de que para o gerador de dados quando não estiver a utilizar ativamente a solução, uma vez que o funcionamento do gerador de dados incorrerá em custos mais elevados. Elimine a solução se não estiver a usá-la. A eliminação da sua solução elimina todos os componentes aprovisionados na sua subscrição quando implementou a solução. Para eliminar a solução, clique no nome da solução no painel esquerdo do modelo de solução e, em seguida, clique em **Eliminar**.

## <a name="cost-estimation-tools"></a>Ferramentas de estimativa de custos
As seguintes duas ferramentas estão disponíveis para ajudá-lo a entender melhor os custos totais envolvidos na execução do Modelo de Manutenção Preditiva para Solução Aeroespacial na sua subscrição:

* [Ferramenta de estimativa de custos do Microsoft Azure (online)](https://azure.microsoft.com/pricing/calculator/)
* [Ferramenta de estimativa de custos do Microsoft Azure (ambiente de trabalho)](https://www.microsoft.com/download/details.aspx?id=43376)

