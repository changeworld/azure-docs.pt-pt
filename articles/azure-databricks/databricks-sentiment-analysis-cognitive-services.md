---
title: Utilize os tijolos de dados azure para análise de sentimentos
description: Aprenda a usar Os Databricks Azure com Hubs de Eventos e Serviços Cognitivos API para executar análise de sentimento em dados de streaming em tempo quase real.
services: azure-databricks
author: lenadroid
ms.author: alehall
ms.reviewer: mamccrea
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 07/29/2019
ms.openlocfilehash: 382dff156c088f367200f0dd46c3758193ade189
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75889233"
---
# <a name="tutorial-sentiment-analysis-on-streaming-data-using-azure-databricks"></a>Tutorial: análise de sentimentos na transmissão em fluxo de dados com o Azure Databricks

Neste tutorial, aprende-se a executar análises de sentimentos num fluxo de dados usando os Pinos de Dados em tempo quase real. Configure o sistema de ingestão de dados com os Hubs de Eventos do Azure. Consuma as mensagens de Hubs de Eventos para o Azure Databricks através do conector de Hubs de Eventos do Apache Spark. Finalmente, usa APIs do Serviço Cognitivo para fazer análisede sentimento sondar os dados transmitidos.

No final deste tutorial, deverá ter transmitido tweets do Twitter em fluxo, que incluem o termo "Azure" nos mesmos, e executado análise de sentimentos nos tweets.

A ilustração seguinte mostra o fluxo da aplicação:

![Azure Databricks com Hubs de Eventos e Serviços Cognitivos](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-tutorial.png "Azure Databricks com Hubs de Eventos e Serviços Cognitivos")

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma área de trabalho do Azure Databricks
> * Criar um cluster do Spark no Azure Databricks
> * Criar uma aplicação no Twitter para aceder a dados de transmissão
> * Criar blocos de notas no Azure Databricks
> * Anexar bibliotecas aos Hubs de Eventos e à API do Twitter
> * Criar uma conta de Serviços Cognitivos e recuperar a chave de acesso
> * Enviar tweets para os Hubs de Eventos
> * Ler tweets dos Hubs de Eventos
> * Executar a análise de sentimentos em tweets

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=sparkeventhubs-docs-alehall) antes de começar.

> [!Note]
> Este tutorial não pode ser realizado utilizando **a assinatura de teste gratuito do Azure.**
> Se tiver uma conta gratuita, vá ao seu perfil e altere a sua subscrição para **pay-as-you-go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [remova o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-azure-portal), e [solicite um aumento](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) de quota para vCPUs na sua região. Ao criar o seu espaço de trabalho Azure Databricks, pode selecionar o nível de preços **Trial (Premium - 14 dias grátis)** para dar ao espaço de trabalho acesso a DBUs de dados Premium Azure gratuitos durante 14 dias.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, certifique-se de que cumpre os seguintes requisitos:
- Um espaço de nomes dos Hubs de Eventos do Azure.
- Um Hub de Eventos no espaço de nomes.
- Cadeia de ligação para aceder ao espaço de nomes dos Hubs de Eventos. A cadeia de ligação deve ter um formato semelhante a `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.
- O nome da política de acesso partilhado e a chave da política dos Hubs de Eventos.

Pode satisfazer estes requisitos, efetuando os passos no artigo, [Criar um espaço de nomes e um hub de eventos dos Hubs de Eventos do Azure](../event-hubs/event-hubs-create.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall).

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta secção, vai criar uma área de trabalho do Azure Databricks com o portal do Azure.

1. No portal Azure, selecione **Criar um recurso** > **Data + Analytics** > **Azure Databricks**.

    ![Tijolos de dados no portal Azure](./media/databricks-sentiment-analysis-cognitive-services/azure-databricks-on-portal.png "Tijolos de dados no portal Azure")

3. Em **Serviço Azure Databricks**, forneça os valores para criar uma área de trabalho do Databricks.

    ![Criar uma área de trabalho do Azure Databricks](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-workspace.png "Criar uma área de trabalho do Azure Databricks")

    Forneça os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks        |
    |**Assinatura**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../azure-resource-manager/management/overview.md). |
    |**Localização**     | Selecione **E.U.A. Leste 2**. Para outras regiões disponíveis, veja [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/?WT.mc_id=sparkeventhubs-docs-alehall).        |
    |**Nível de Preços**     |  Escolha entre **Standard** ou **Premium**. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall).       |

    Selecione **Afixar ao dashboard** e, em seguida, selecione **Criar**.

4. A criação da conta demora alguns minutos. Durante a criação da conta, o portal apresenta o mosaico **Submeter a implementação para o Azure Databricks** no lado direito. Pode ter de se deslocar para a direita no dashboard para ver o mosaico. É também apresentada uma barra de progresso junto à parte superior do ecrã. Pode ver qualquer área de progresso.

    ![Telha de implantação de tijolos de dados](./media/databricks-sentiment-analysis-cognitive-services/databricks-deployment-tile.png "Telha de implantação de tijolos de dados")

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

1. No portal do Azure, aceda à área de trabalho do Databricks que criou e, em seguida, selecione **Iniciar Área de Trabalho**.

2. Será redirecionado para o portal do Azure Databricks. No portal, selecione **Cluster**.

    ![Tijolos de dados em Azure](./media/databricks-sentiment-analysis-cognitive-services/databricks-on-azure.png "Tijolos de dados em Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar cluster de faíscas databricks em Azure](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-spark-cluster.png "Criar cluster de faíscas databricks em Azure")

    Aceite todos os outros valores predefinidos que não sejam os seguintes:

   * Introduza um nome para o cluster.
   * Para este artigo, crie um cluster com **6.0** tempo de execução.
   * Certifique-se de que seleciona o **'Terminar' \_ \_ após minutos de caixa de verificação de inatividade.** Indique uma duração (em minutos) para terminar o cluster, caso não esteja a ser utilizado.

   Selecione o tamanho do trabalhador do cluster e do nó do condutor adequado para os seus critérios técnicos e [orçamento](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall).

     Selecione **Criar cluster**. Depois de o cluster estar em execução, pode anexar blocos de notas ao cluster e executar tarefas do Spark.

## <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter

Para receber um fluxo de tweets, crie uma aplicação no Twitter. Siga as instruções para criar uma aplicação no Twitter e registe os valores que precisa para concluir este tutorial.

1. A partir de um navegador web, vá ao [Twitter Para Desenvolvedores](https://developer.twitter.com/en/apps), e selecione **Criar uma aplicação**. Pode ver uma mensagem a dizer que precisa de se candidatar a uma conta de desenvolvedordo no Twitter. Sinta-se à vontade para o fazer, e depois de a sua candidatura ter sido aprovada, deverá consultar um e-mail de confirmação. Pode levar vários dias para ser aprovado para uma conta de desenvolvedor.

    ![Confirmação da conta de desenvolvedor do Twitter](./media/databricks-sentiment-analysis-cognitive-services/databricks-twitter-dev-confirmation.png "Confirmação da conta de desenvolvedor do Twitter")

2. Na página **Criar uma aplicação**, forneça os detalhes da nova aplicação e, em seguida, selecione **Criar a sua aplicação do Twitter**.

    ![Detalhes da aplicação do Twitter](./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details.png "Detalhes da aplicação do Twitter")

    ![Detalhes da aplicação do Twitter](./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details-create.png "Detalhes da aplicação do Twitter")

3. Na página de aplicação, selecione o separador **Keys and Tokens** e copie os valores para **chave api** do consumidor e chave secreta do consumidor **API**Secret . Além disso, selecione **Create** under **Access Token e Access Token Secret** para gerar as fichas de acesso. Copie os valores de **Token de Acesso** e **Segredo de Token de Acesso**.

    ![Detalhes da aplicação do Twitter](./media/databricks-sentiment-analysis-cognitive-services/twitter-app-key-secret.png "Detalhes da aplicação do Twitter")

Guarde os valores que obteve da aplicação do Twitter. Vai precisar dos valores mais tarde no tutorial.

## <a name="attach-libraries-to-spark-cluster"></a>Anexar bibliotecas ao cluster do Spark

Neste tutorial, vai utilizar as APIs do Twitter para enviar tweets para os Hubs de Eventos. Também pode utilizar o [Conector de Hubs de eventos do Spark](https://github.com/Azure/azure-event-hubs-spark?WT.mc_id=sparkeventhubs-docs-alehall) para ler e escrever dados em Hubs de Eventos do Azure. Para utilizar estas APIs como parte do seu cluster, adicione-as como bibliotecas aos Bricks Azure e associe-as ao seu cluster Spark. As seguintes instruções mostram como adicionar uma biblioteca.

1. No espaço de trabalho Azure Databricks, selecione **Clusters,** e escolha o seu cluster Spark existente. Dentro do menu cluster, escolha **Bibliotecas** e clique **em Instalar Nova**.

   ![Adicione a caixa de diálogo da biblioteca](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-locate-cluster.png "Adicionar aglomerado de localização de biblioteca")

   ![Adicione a caixa de diálogo da biblioteca](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-install-new.png "Adicionar biblioteca instalar novo")

2. Na página da Nova Biblioteca, para **Fonte** selecione **Maven**. Para **coordenar,** clique em **Pacotes de Pesquisa** para o pacote que pretende adicionar. Eis as coordenadas do Maven para as bibliotecas utilizadas neste tutorial:

   * Conector de Hubs de Eventos do Spark – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * API do Twitter – `org.twitter4j:twitter4j-core:4.0.7`

     ![Forneça coordenadas Maven](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search.png "Forneça coordenadas Maven")

     ![Forneça coordenadas Maven](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search-dialogue.png "Pesquisar coordenadas Maven")

3. Selecione **Instalar**.

4. No menu do cluster, certifique-se de que ambas as bibliotecas estão instaladas e fixadas corretamente.

    ![Ver bibliotecas](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-check.png "Ver bibliotecas")

6. Repita estes passos para o pacote do Twitter, `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Obter uma chave de acesso aos Serviços Cognitivos

Neste tutorial, você usa as APIs de Análise de Texto de [Serviços Cognitivos Azure](../cognitive-services/text-analytics/overview.md) para executar análise de sentimento num fluxo de tweets em tempo quase real. Antes de utilizar as APIs, deve criar uma conta de Serviços Cognitivos Azure no Azure e recuperar uma chave de acesso para utilizar as APIs de Análise de Texto.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall).

2. Selecione **+ Criar um recurso**.

3. No Azure Marketplace, selecione **AI + Cognitive Services** > **Text Analytics API**.

    ![Criar conta de serviços cognitivos](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-text-api.png "Criar conta de serviços cognitivos")

4. Na caixa de diálogo **Criar**, forneça os valores seguintes:

    ![Criar conta de serviços cognitivos](./media/databricks-sentiment-analysis-cognitive-services/create-cognitive-services-account.png "Criar conta de serviços cognitivos")

   - Introduza um nome para a conta de Serviços Cognitivos.
   - Selecione a subscrição do Azure na qual a conta foi criada.
   - Selecione uma localização do Azure.
   - Selecione um escalão de preço para o serviço. Para obter mais informações sobre os preços dos Serviços Cognitivos, veja a [página de preços](https://azure.microsoft.com/pricing/details/cognitive-services/?WT.mc_id=sparkeventhubs-docs-alehall).
   - Especifique se pretende criar um novo grupo de recursos ou selecione um existente.

     Selecione **Criar**.

5. Depois de criada a conta, a partir do separador **Overview,** selecione **as teclas**de acesso do Show .

    ![Mostrar chaves de acesso](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-get-access-keys.png "Mostrar chaves de acesso")

    Além disso, copie uma parte do URL do ponto final, conforme mostrado na captura de ecrã. Este URL é necessário no tutorial.

6. Em **Gerir chaves**, selecione o ícone de cópia em relação à chave que pretende utilizar.

    ![Copiar chaves de acesso](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-copy-access-keys.png "Copiar chaves de acesso")

7. Guarde os valores do URL do ponto final e a chave de acesso que obteve neste passo. Serão necessários mais tarde no tutorial.

## <a name="create-notebooks-in-databricks"></a>Criar blocos de notas no Databricks

Nesta secção, vai criar dois blocos de notas na área de trabalho do Databricks, com os seguintes nomes

- **SendTweetsToEventHub** – Um bloco de notas de produtor que utiliza para obter tweets do Twitter e transmiti-los para os Hubs de Eventos.
- **AnalyzeTweetsFromEventHub** - um bloco de notas de consumidor que utiliza para ler os tweets dos Hubs de Eventos e executar a análise de sentimentos.

1. No painel esquerdo, selecione **Área de Trabalho**. No menu pendente **Área de Trabalho**, selecione **Criar** e, em seguida, selecione **Bloco de Notas**.

    ![Criar caderno em Databricks](./media/databricks-sentiment-analysis-cognitive-services/databricks-create-notebook.png "Criar caderno em Databricks")

2. Na caixa de diálogo **Criar Bloco de Notas**, introduza **SendTweetsToEventHub**, selecione **Scala** como linguagem e selecione o cluster do Spark que criou anteriormente.

    ![Criar caderno em Databricks](./media/databricks-sentiment-analysis-cognitive-services/databricks-notebook-details.png "Criar caderno em Databricks")

    Selecione **Criar**.

3. Repita os passos para criar o bloco de notas **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Enviar tweets para os Hubs de Eventos

No caderno **SendTweetsToEventHub,** cola o seguinte código e substitui os espaços reservados por valores para o espaço de nome do seu Event Hubs e aplicação do Twitter que criou anteriormente. Este bloco de notas transmite os tweets com a palavra-chave "Azure" para os Hubs de Eventos em tempo real.

> [!NOTE]
> A API do Twitter tem certas restrições e [quotas](https://developer.twitter.com/en/docs/basics/rate-limiting.html)de pedidos. Se não estiver satisfeito com a limitação da taxa padrão na API do Twitter, pode gerar conteúdo de texto sem utilizar a API do Twitter neste exemplo. Para isso, detete `test` os `twitter` dados **variáveisSource** para em vez de e povoar o teste de **listaSource** com entrada de teste preferida.

```scala
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._
    import scala.collection.immutable._
    import scala.concurrent.Future
    import scala.concurrent.ExecutionContext.Implicits.global

    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val pool = Executors.newScheduledThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sleep(time: Long): Unit = Thread.sleep(time)

    def sendEvent(message: String, delay: Long) = {
      sleep(delay)
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData)
      System.out.println("Sent event: " + message + "\n")
    }

    // Add your own values to the list
    val testSource = List("Azure is the greatest!", "Azure isn't working :(", "Azure is okay.")

    // Specify 'test' if you prefer to not use Twitter API and loop through a list of values you define in `testSource`
    // Otherwise specify 'twitter'
    val dataSource = "test"

    if (dataSource == "twitter") {

      import twitter4j._
      import twitter4j.TwitterFactory
      import twitter4j.Twitter
      import twitter4j.conf.ConfigurationBuilder

      // Twitter configuration!
      // Replace values below with you

      val twitterConsumerKey = "<CONSUMER API KEY>"
      val twitterConsumerSecret = "<CONSUMER API SECRET>"
      val twitterOauthAccessToken = "<ACCESS TOKEN>"
      val twitterOauthTokenSecret = "<TOKEN SECRET>"

      val cb = new ConfigurationBuilder()
        cb.setDebugEnabled(true)
        .setOAuthConsumerKey(twitterConsumerKey)
        .setOAuthConsumerSecret(twitterConsumerSecret)
        .setOAuthAccessToken(twitterOauthAccessToken)
        .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

      val twitterFactory = new TwitterFactory(cb.build())
      val twitter = twitterFactory.getInstance()

      // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
      val query = new Query(" #Azure ")
      query.setCount(100)
      query.lang("en")
      var finished = false
      while (!finished) {
        val result = twitter.search(query)
        val statuses = result.getTweets()
        var lowestStatusId = Long.MaxValue
        for (status <- statuses.asScala) {
          if(!status.isRetweet()){
            sendEvent(status.getText(), 5000)
          }
          lowestStatusId = Math.min(status.getId(), lowestStatusId)
        }
        query.setMaxId(lowestStatusId - 1)
      }

    } else if (dataSource == "test") {
      // Loop through the list of test input data
      while (true) {
        testSource.foreach {
          sendEvent(_,5000)
        }
      }

    } else {
      System.out.println("Unsupported Data Source. Set 'dataSource' to \"twitter\" or \"test\"")
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
```

Para executar o bloco de notas, prima **SHIFT + ENTER**. Pode ver um resultado como o fragmento abaixo. Cada evento no resultado é um tweet que é ingerido nos Hubs de Eventos com o termo "Azure".

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure

    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie

    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk

    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Ler tweets dos Hubs de Eventos

No bloco de notas **AnalyzeTweetsFromEventHub**, cole o seguinte código e substitua o marcador de posição pelos valores dos Hubs de Eventos do Azure que criou anteriormente. Este bloco de notas lê os tweets que transmitiu anteriormente para os Hubs de Eventos através do bloco de notas **SendTweetsToEventHub**.

```scala

    import org.apache.spark.eventhubs._
    import com.microsoft.azure.eventhubs._

    // Build connection string with the above information
    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new com.microsoft.azure.eventhubs.ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val customEventhubParameters =
      EventHubsConf(connStr.toString())
      .setMaxEventsPerTrigger(5)

    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

    incomingStream.printSchema

    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Obtém o resultado seguinte:


    root
     |-- body: binary (nullable = true)
     |-- offset: long (nullable = true)
     |-- seqNumber: long (nullable = true)
     |-- enqueuedTime: long (nullable = true)
     |-- publisher: string (nullable = true)
     |-- partitionKey: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+------+--------------+---------------+---------+------------+
    |body  |offset|sequenceNumber|enqueuedTime   |publisher|partitionKey|
    +------+------+--------------+---------------+---------+------------+
    |[50 75 62 6C 69 63 20 70 72 65 76 69 65 77 20 6F 66 20 4A 61 76 61 20 6F 6E 20 41 70 70 20 53 65 72 76 69 63 65 2C 20 62 75 69 6C 74 2D 69 6E 20 73 75 70 70 6F 72 74 20 66 6F 72 20 54 6F 6D 63 61 74 20 61 6E 64 20 4F 70 65 6E 4A 44 4B 0A 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 37 76 73 37 63 4B 74 76 61 68 20 0A 23 63 6C 6F 75 64 63 6F 6D 70 75 74 69 6E 67 20 23 41 7A 75 72 65]                              |0     |0             |2018-03-09 05:49:08.86 |null     |null        |
    |[4D 69 67 72 61 74 65 20 79 6F 75 72 20 64 61 74 61 62 61 73 65 73 20 74 6F 20 61 20 66 75 6C 6C 79 20 6D 61 6E 61 67 65 64 20 73 65 72 76 69 63 65 20 77 69 74 68 20 41 7A 75 72 65 20 53 51 4C 20 44 61 74 61 62 61 73 65 20 4D 61 6E 61 67 65 64 20 49 6E 73 74 61 6E 63 65 20 7C 20 23 41 7A 75 72 65 20 7C 20 23 43 6C 6F 75 64 20 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 73 4A 48 58 4E 34 74 72 44 6B]            |168   |1             |2018-03-09 05:49:24.752|null     |null        |
    +------+------+--------------+---------------+---------+------------+

    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Dado que o resultado está num modo binário, utilize o seguinte fragmento para convertê-lo numa cadeia.

```scala
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so we cast it to string to see the actual content of the message
    val messages =
      incomingStream
      .withColumn("Offset", $"offset".cast(LongType))
      .withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType))
      .withColumn("Timestamp", $"enqueuedTime".cast(LongType))
      .withColumn("Body", $"body".cast(StringType))
      .select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

O resultado assemelha-se agora ao seguinte fragmento:

    root
     |-- Offset: long (nullable = true)
     |-- Time (readable): timestamp (nullable = true)
     |-- Timestamp: long (nullable = true)
     |-- Body: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+-----------------+----------+-------+
    |Offset|Time (readable)  |Timestamp |Body
    +------+-----------------+----------+-------+
    |0     |2018-03-09 05:49:08.86 |1520574548|Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure          |
    |168   |2018-03-09 05:49:24.752|1520574564|Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |
    |0     |2018-03-09 05:49:02.936|1520574542|@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|
    |176   |2018-03-09 05:49:20.801|1520574560|4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |
    +------+-----------------+----------+-------+
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Já transmitiu dados dos Hubs de Eventos Azure para os Tecidos De dados do Azure em tempo quase real, utilizando o conector De Eventos Hubs para A Spark. Para obter mais informações sobre como utilizar o conector Hubs de Eventos para Spark, veja a [documentação do conector](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs?WT.mc_id=sparkeventhubs-docs-alehall).

## <a name="run-sentiment-analysis-on-tweets"></a>Executar a análise de sentimentos em tweets

Nesta secção, vai efetuar a análise de sentimentos dos tweets recebidos através da API do Twitter. Nesta secção, vai adicionar os fragmentos de código ao mesmo bloco de notas **AnalyzeTweetsFromEventHub**.

Comece por adicionar uma nova célula de código ao bloco de notas e colar o fragmento de código fornecido abaixo. Este fragmento de código define os tipos de dados para trabalhar com a API de Idiomas e Sentimentos.

```scala
import java.io._
import java.net._
import java.util._

case class Language(documents: Array[LanguageDocuments], errors: Array[Any]) extends Serializable
case class LanguageDocuments(id: String, detectedLanguages: Array[DetectedLanguages]) extends Serializable
case class DetectedLanguages(name: String, iso6391Name: String, score: Double) extends Serializable

case class Sentiment(documents: Array[SentimentDocuments], errors: Array[Any]) extends Serializable
case class SentimentDocuments(id: String, score: Double) extends Serializable

case class RequestToTextApi(documents: Array[RequestToTextApiDocument]) extends Serializable
case class RequestToTextApiDocument(id: String, text: String, var language: String = "") extends Serializable
```

Adicione uma nova célula de código e cole o fragmento fornecido abaixo. Este fragmento define um objeto que contém funções para chamar a API de Análise de Texto para executar a deteção de idioma e análise de sentimentos. Certifique-se de que `<PROVIDE ACCESS KEY HERE>` substitui o espaço reservado pelo valor que recuperou para a sua conta de Serviços Cognitivos.

```scala
import javax.net.ssl.HttpsURLConnection
import com.google.gson.Gson
import com.google.gson.GsonBuilder
import com.google.gson.JsonObject
import com.google.gson.JsonParser
import scala.util.parsing.json._

object SentimentDetector extends Serializable {

    // Cognitive Services API connection settings
    val accessKey = "<PROVIDE ACCESS KEY HERE>"
    val host = "https://cognitive-docs.cognitiveservices.azure.com/"
    val languagesPath = "/text/analytics/v2.1/languages"
    val sentimentPath = "/text/analytics/v2.1/sentiment"
    val languagesUrl = new URL(host+languagesPath)
    val sentimenUrl = new URL(host+sentimentPath)
    val g = new Gson

    def getConnection(path: URL): HttpsURLConnection = {
        val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
        connection.setRequestMethod("POST")
        connection.setRequestProperty("Content-Type", "text/json")
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", accessKey)
        connection.setDoOutput(true)
        return connection
    }

    def prettify (json_text: String): String = {
        val parser = new JsonParser()
        val json = parser.parse(json_text).getAsJsonObject()
        val gson = new GsonBuilder().setPrettyPrinting().create()
        return gson.toJson(json)
    }

    // Handles the call to Cognitive Services API.
    def processUsingApi(request: RequestToTextApi, path: URL): String = {
        val requestToJson = g.toJson(request)
        val encoded_text = requestToJson.getBytes("UTF-8")
        val connection = getConnection(path)
        val wr = new DataOutputStream(connection.getOutputStream())
        wr.write(encoded_text, 0, encoded_text.length)
        wr.flush()
        wr.close()

        val response = new StringBuilder()
        val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
        var line = in.readLine()
        while (line != null) {
            response.append(line)
            line = in.readLine()
        }
        in.close()
        return response.toString()
    }

    // Calls the language API for specified documents.
    def getLanguage (inputDocs: RequestToTextApi): Option[Language] = {
        try {
            val response = processUsingApi(inputDocs, languagesUrl)
            // In case we need to log the json response somewhere
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val language = g.fromJson(niceResponse, classOf[Language])
            if (language.documents(0).detectedLanguages(0).iso6391Name == "(Unknown)")
                return None
            return Some(language)
        } catch {
            case e: Exception => return None
        }
    }

    // Calls the sentiment API for specified documents. Needs a language field to be set for each of them.
    def getSentiment (inputDocs: RequestToTextApi): Option[Sentiment] = {
        try {
            val response = processUsingApi(inputDocs, sentimenUrl)
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val sentiment = g.fromJson(niceResponse, classOf[Sentiment])
            return Some(sentiment)
        } catch {
            case e: Exception => return None
        }
    }
}
```

Adicione outra célula para definir uma Ignição Spark (função definida pelo utilizador) que determina o sentimento.

```scala
// User Defined Function for processing content of messages to return their sentiment.
val toSentiment =
    udf((textContent: String) =>
        {
            val inputObject = new RequestToTextApi(Array(new RequestToTextApiDocument(textContent, textContent)))
            val detectedLanguage = SentimentDetector.getLanguage(inputObject)
            detectedLanguage match {
                case Some(language) =>
                    if(language.documents.size > 0) {
                        inputObject.documents(0).language = language.documents(0).detectedLanguages(0).iso6391Name
                        val sentimentDetected = SentimentDetector.getSentiment(inputObject)
                        sentimentDetected match {
                            case Some(sentiment) => {
                                if(sentiment.documents.size > 0) {
                                    sentiment.documents(0).score.toString()
                                }
                                else {
                                    "Error happened when getting sentiment: " + sentiment.errors(0).toString
                                }
                            }
                            case None => "Couldn't detect sentiment"
                        }
                    }
                    else {
                        "Error happened when getting language" + language.errors(0).toString
                    }
                case None => "Couldn't detect language"
            }
        }
    )
```

Adicione uma célula de código final para preparar um dataframe com o conteúdo do tweet e o sentimento associado ao tweet.

```scala
// Prepare a dataframe with Content and Sentiment columns
val streamingDataFrame = incomingStream.selectExpr("cast (body as string) AS Content").withColumn("Sentiment", toSentiment($"Content"))

// Display the streaming data with the sentiment
streamingDataFrame.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Deve ver um resultado como o seguinte fragmento:

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +--------------------------------+------------------+
    |Content                         |Sentiment         |
    +--------------------------------+------------------+
    |Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah   #cloudcomputing #Azure          |0.7761918306350708|
    |Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |0.8558163642883301|
    |@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|0.5               |
    |4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |0.5               |
    +--------------------------------+------------------+

Um valor perto de **1** na coluna **Sentimento** sugere uma excelente experiência com o Azure. Um valor perto de **0** sugere problemas sentidos pelos utilizadores ao trabalharem com o Microsoft Azure.

Já está! Utilizando os Databricks Azure, transbordou com sucesso dados para o Azure Event Hubs, consumiu os dados de streaming utilizando o conector Event Hubs e, em seguida, executou análises de sentimentos sobre dados de streaming em tempo real.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de executar o tutorial, pode terminar o cluster. Para tal, na área de trabalho do Azure Databricks, no painel esquerdo, selecione **Clusters**. Para o cluster que quer terminar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**.

![Parar um cluster de Databricks](./media/databricks-sentiment-analysis-cognitive-services/terminate-databricks-cluster.png "Parar um cluster de Databricks")

Se não terminar manualmente o cluster, para automaticamente, desde que selecione o **Terminate após \_ \_ minutos de** caixa de verificação de inatividade enquanto cria o cluster. Nesse caso, o cluster para automaticamente se tiver estado inativo durante o período de tempo especificado.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a utilizar o Azure Databricks para transmitir dados em fluxo aos Hubs de Eventos do Azure e, em seguida, ler os dados de transmissão em fluxo dos Hubs de Eventos em tempo real. Aprendeu a:
> [!div class="checklist"]
> * Criar uma área de trabalho do Azure Databricks
> * Criar um cluster do Spark no Azure Databricks
> * Criar uma aplicação no Twitter para aceder a dados de transmissão
> * Criar blocos de notas no Azure Databricks
> * Adicionar e anexar bibliotecas aos Hubs de Eventos e à API do Twitter
> * Criar uma conta dos Serviços Cognitivos da Microsoft e obter a chave de acesso
> * Enviar tweets para os Hubs de Eventos
> * Ler tweets dos Hubs de Eventos
> * Executar a análise de sentimentos em tweets

Avance para o próximo tutorial para saber mais sobre como efetuar tarefas de aprendizagem automática com o Azure Databricks.

> [!div class="nextstepaction"]
>[Machine Learning usando tijolos de dados Azure](/azure/databricks/applications/machine-learning/mllib/decision-trees)

