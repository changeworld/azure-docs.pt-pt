---
title: Publique o conteúdo da Azure Media Services utilizando .NET [ Microsoft Docs
description: Aprenda a criar um localizador que é usado para construir um URL de streaming. As amostras de código são escritas em C# e utilizam o SDK de Serviços de Media para .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 615a6afb0f7a3e133603db10e7c79add3322070c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476703"
---
# <a name="publish-media-services-content-using-net"></a>Publique conteúdo dos Media Services usando .NET  
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Descrição geral
Pode transmitir um BIT4 bitrate adaptativo, criando um localizador de streaming OnDemand e construindo um URL de streaming. A [codificação de um](media-services-encode-asset.md) tópico de ativo mostra como codificar um conjunto de MP4 bitrate adaptativo. 

> [!NOTE]
> Se o seu conteúdo estiver encriptado, configure a política de entrega de ativos (conforme descrito [neste](media-services-dotnet-configure-asset-delivery-policy.md) tópico) antes de criar um localizador. 
> 
> 

Também pode utilizar um localizador de streaming OnDemand para construir URLs que apontam para ficheiros MP4 que podem ser descarregados progressivamente.  

Este tópico mostra como criar um localizador de streaming OnDemand para publicar o seu ativo e construir um URLs de streaming Smooth, MPEG DASH e HLS. Também mostra quente para construir URLs de download progressivo. 

## <a name="create-an-ondemand-streaming-locator"></a>Criar um localizador de streaming OnDemand
Para criar o localizador de streaming OnDemand e obter URLs, precisa fazer as seguintes coisas:

1. Se o conteúdo estiver encriptado, defina uma política de acesso.
2. Crie um localizador de streaming OnDemand.
3. Se planeia transmitir, obtenha o ficheiro manifesto de streaming (.ism) no ativo. 
   
   Se planeia descarregar progressivamente, obtenha os nomes dos ficheiros MP4 no ativo.  
4. Construa URLs para o ficheiro manifesto ou ficheiros MP4. 


>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Utilize o mesmo ID de política se estiver sempre a utilizar as mesmas permissões de acesso/ dias. Por exemplo, políticas para localizadores que se destinam a permanecer em vigor por muito tempo (políticas de não upload). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

### <a name="use-media-services-net-sdk"></a>Utilizar serviços de mídia .NET SDK
Construir URLs de Streaming 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

As saídas:

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> Também pode transmitir o seu conteúdo através de uma ligação TLS. Para fazer esta abordagem, certifique-se de que os seus URLs de streaming começam com HTTPS. Atualmente, a AMS não suporta TLS com domínios personalizados.
> 
> 

Construir URLs de descarregamento progressivo 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
As saídas:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Utilize os Serviços de Mídia .NET SDK Extensões
Os seguintes códigos chama métodos de extensões .NET SDK que criam um localizador e geram os URLs de Streaming Suave, HLS e MPEG-DASH para streaming adaptativo.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes
* [Descarregue os ativos](media-services-deliver-asset-download.md)
* [Configurar a política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md)

