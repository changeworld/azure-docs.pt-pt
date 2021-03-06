---
title: Enviar MapReduce empregos usando HDInsight .NET SDK - Azure
description: Saiba como submeter mapReduce jobs ao Azure HDInsight Apache Hadoop usando HDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/15/2020
ms.openlocfilehash: e50510f2420d69be37af584a2648a794e1561ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157056"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Executar tarefas MapReduce com o SDK .NET do HDInsight

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como submeter mapReduce jobs usando HDInsight .NET SDK. Os clusters HDInsight vêm com um ficheiro de frasco com algumas amostras MapReduce. O ficheiro `/example/jars/hadoop-mapreduce-examples.jar`do frasco é.  Uma das amostras é **a contagem de palavras.** Desenvolve-se uma aplicação de consola C# para submeter um trabalho de contagem de palavras.  O trabalho `/example/data/gutenberg/davinci.txt` lê o ficheiro e `/example/data/davinciwordcount`produz os resultados para .  Se quiser reproduzir a aplicação, tem de limpar a pasta de saída.

> [!NOTE]  
> Os passos deste artigo devem ser realizados a partir de um cliente windows. Para obter informações sobre a utilização de um cliente Linux, OS X ou Unix para trabalhar com a Hive, utilize o seletor de separadores mostrado na parte superior do artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Estúdio Visual.](https://visualstudio.microsoft.com/vs/community/)

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Enviar MapReduzir postos de trabalho usando HDInsight .NET SDK

O HDInsight .NET SDK fornece bibliotecas de clientes .NET, que facilitam o trabalho com clusters HDInsight de .NET.

1. Inicie o Visual Studio e crie uma aplicação de consola C#.

1. Navegue para **ferramentas** > **NuGet Package Manager** > **Manager Console** e introduza o seguinte comando:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Copie o código abaixo em **Program.cs**. Em seguida, editar o código `existingClusterName` `existingClusterPassword`definindo os valores para: , , `defaultStorageAccountName`, `defaultStorageAccountKey`e `defaultStorageContainerName`.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);
    
                System.Console.WriteLine("Waiting for the job completion ...");
    
                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }
    
                // Get job output
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. Prima **F5** para executar a aplicação.

Para voltar a executar o trabalho, tem de alterar o `/example/data/davinciwordcount`nome da pasta de saída de emprego, na amostra é .

Quando o trabalho termina com sucesso, a aplicação `part-r-00000`imprime o conteúdo do ficheiro de saída .

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu várias formas de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* Para submeter um trabalho de Colmeia, consulte [consultas de Hive Run Apache utilizando HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* Para criar clusters HDInsight, consulte [Create Linux-based Apache Hadoop clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Para gerir os clusters HDInsight, consulte [Gerir os clusters Apache Hadoop em HDInsight](../hdinsight-administer-use-portal-linux.md).
* Para aprender o HDInsight .NET SDK, consulte [a referência HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* Para autenticar não interactivamente o Azure, consulte Criar aplicações de [autenticação não interativa .NET HDInsight](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).