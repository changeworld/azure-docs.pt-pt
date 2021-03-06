---
title: Gerir os clusters Apache Hadoop em HDInsight com .NET SDK - Azure
description: Aprenda a executar tarefas administrativas para os clusters Apache Hadoop em HDInsight utilizando HDInsight .NET SDK.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 36a77d49b507d3d0158d1b4b492d0141350de50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240644"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Gerir clusters do Apache Hadoop no HDInsight com o SDK .NET

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Aprenda a gerir os clusters HDInsight usando [HDInsight.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).

**Pré-requisitos**

Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Ligue-se ao Azure HDInsight

Precisa dos seguintes pacotes NuGet:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

A seguinte amostra de código mostra-lhe como se conectar ao Azure antes de poder administrar clusters HDInsight sob a sua subscrição Azure.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

Verá um aviso quando executar este programa.  Se não quiser ver o pedido, consulte [Create autenticação não interativa .NET HDInsight aplicações](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


## <a name="list-clusters"></a>Aglomerados de listas

O seguinte código de corte lista clusters e algumas propriedades:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Eliminar aglomerados

Utilize o seguinte código para eliminar um cluster sincronizada ou assincronicamente: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Aglomerados de escala

A função de dimensionamento do cluster permite alterar o número de nós de trabalhador usados por um cluster que está a funcionar no Azure HDInsight sem ter de recriar o cluster.

> [!NOTE]  
> Apenas são suportados clusters com a versão 3.1.3 ou superior do HDInsight. Se não tiver a certeza da versão do seu cluster, pode consultar a página Propriedades.  Ver [Lista e mostrar clusters.](hdinsight-administer-use-portal-linux.md#showClusters)

O impacto de alterar o número de nós de dados para cada tipo de cluster suportado pela HDInsight:

* Apache Hadoop
  
    Pode aumentar perfeitamente o número de nós dos trabalhadores num aglomerado hadoop que está a funcionar sem ter impacto em quaisquer postos de trabalho pendentes ou em funcionamento. Novos postos de trabalho também podem ser submetidos enquanto a operação estiver em curso. Falhas numa operação de escala são manuseadas graciosamente de modo a que o cluster seja sempre deixado em estado funcional.
  
    Quando um cluster Hadoop é reduzido reduzindo o número de nós de dados, alguns dos serviços do cluster são reiniciados. Isto faz com que todos os postos de trabalho em funcionamento e pendentes falhem no final da operação de escala. No entanto, pode reenviar os trabalhos assim que a operação estiver concluída.
* Apache HBase
  
    Pode adicionar ou remover os nós do seu cluster HBase enquanto estiver em funcionamento. Os Servidores Regionais são automaticamente equilibrados dentro de poucos minutos após o completar a operação de escala. No entanto, também pode equilibrar manualmente os servidores regionais iniciando sessão no headnode do cluster e executando os seguintes comandos a partir de uma janela de solicitação de comando:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    Pode adicionar ou remover os nós de dados do seu aglomerado de tempestades enquanto estiver em execução. Mas depois de uma conclusão bem sucedida da operação de escala, terá de reequilibrar a topologia.
  
    O reequilíbrio pode ser realizado de duas formas:
  
  * UI web tempestade
  * Ferramenta de interface de linha de comando (CLI)
    
    Por favor, consulte a [documentação](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) da Tempestade Apache para mais detalhes.
    
    O UI web storm está disponível no cluster HDInsight:
    
    ![Reequilíbrio da escala de tempestade HDInsight](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Aqui está um exemplo de como usar o comando CLI para reequilibrar a topologia da tempestade:
    

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

O seguinte código de corte mostra como redimensionar um cluster sincronizada ou assíncrona:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Conceder/revogar o acesso

Os clusters HDInsight têm os seguintes serviços web HTTP (todos estes serviços têm pontos finais RESTful):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Templo de Apache

Por predefinição, estes serviços são concedidos para acesso. Pode revogar/conceder o acesso. Revogar:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Para conceder:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> Ao conceder/revogar o acesso, irá redefinir o nome do utilizador do cluster e a palavra-passe.

Isto também pode ser feito através do Portal. Ver [Gerir os clusters Apache Hadoop em HDInsight utilizando o portal Azure](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Atualizar credenciais de utilizador HTTP

É o mesmo procedimento que o acesso grant/revogar http.  Se o cluster tiver acesso http, deve primeiro revogá-lo.  E, em seguida, conceder o acesso com novas credenciais de utilizador HTTP.

## <a name="find-the-default-storage-account"></a>Encontre a conta de armazenamento por defeito

O seguinte código de corte demonstra como obter o nome da conta de armazenamento predefinido e a chave de armazenamento predefinido para um cluster.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Submeter postos de trabalho

**Para submeter mapReduce jobs**

Ver [Executar MapReduzir amostras em HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Para submeter empregos apache hive** 

Consulte [as consultas de Hiv-Hiv e Hive Run Apache utilizando .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Para submeter os empregos apache Sqoop**

Consulte [O Apache Sqoop com HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Para submeter empregos apache Oozie**

Consulte [O Apache Oozie com Hadoop para definir e executar um fluxo de trabalho em HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Enviar dados para armazenamento de Blob Azure

Consulte [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data].

## <a name="see-also"></a>Veja também

* [HDInsight .NET SDK documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Gerir os clusters Apache Hadoop em HDInsight utilizando o portal Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight utilizando uma interface de linha de comando][hdinsight-admin-cli]
* [Criar clusters do HDInsight][hdinsight-provision]
* [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data]
* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
