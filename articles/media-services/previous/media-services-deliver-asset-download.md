---
title: Baixe os ativos dos Media Services para o seu computador - Azure / Microsoft Docs
description: Saiba o que está prestes a transferir bens para o seu computador. As amostras de código são escritas em C# e utilizam o SDK de Serviços de Media para .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 21fcc6ae09718ffbb22e1d438926586dd3cde71d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61465665"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Como: Entregar um ativo por download  
Este artigo discute opções para a entrega de ativos de mídia enviados para os Media Services. Pode entregar conteúdo dos Media Services em inúmeros cenários de aplicação. Depois de codificar, descarregue os meios de comunicação gerados ou aceda-os utilizando um localizador de streaming. Para um melhor desempenho e escalabilidade, também pode entregar conteúdo utilizando uma Rede de Entrega de Conteúdos (CDN).

Este exemplo mostra como descarregar os meios de comunicação dos Media Services para o seu computador local. O código questiona os postos de trabalho associados à conta de Media Services por ID de emprego e acede à sua coleção **OutputMediaAssets** (que é o conjunto de um ou mais ativos de media de produção que resultam da gestão de um emprego). Este exemplo mostra como descarregar os meios de comunicação de saída de um emprego, mas pode aplicar a mesma abordagem para descarregar outros ativos.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Utilize o mesmo ID de política se estiver sempre a utilizar as mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que se destinam a permanecer no lugar por muito tempo (políticas de não upload). Para mais informações, consulte [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Entregar conteúdo de streaming](media-services-deliver-streaming-content.md)

