---
title: Criar análises de vídeo usando .NET - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Este artigo fornece amostras de informações e códigos para ajudá-lo a começar rapidamente a usar o SDK moderador de conteúdo com C# para criar análises de vídeo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 7130ed43183d64b00f8f5ef1697b9a3b456ad396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72931680"
---
# <a name="create-video-reviews-using-net"></a>Criar críticas de vídeo usando .NET

Este artigo fornece informações e amostras de código para ajudá-lo a começar rapidamente a usar o [SDK moderador](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) de conteúdo com C# para:

- Crie uma revisão em vídeo para moderadores humanos
- Adicione quadros a uma revisão
- Obtenha os quadros para a revisão
- Obtenha o estado e os detalhes da revisão
- Publicar a revisão

## <a name="prerequisites"></a>Pré-requisitos

- Inscreva-se ou crie uma conta no site da ferramenta Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)
- Este artigo assume que [moderou o vídeo (ver quickstart)](video-moderation-api.md) e tem os dados de resposta. Precisa dele para criar avaliações baseadas em quadros para moderadores humanos.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Certifique-se de que a chave de API pode chamar a API de revisão para a criação de revisões

Depois de concluir os passos anteriores, pode ficar com duas chaves do Content Moderator, se tiver iniciado a partir do portal do Azure.

Se planear utilizar a chave de API dada pelo Azure no seu exemplo de SDK, siga os passos mencionados na secção [Utilizar a chave do Azure com a API de revisão](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) para permitir que a aplicação chame a API de revisão e crie as revisões.

Se utilizar a chave de avaliação gratuita gerada pela ferramenta de revisão, a sua conta da ferramenta de revisão já conhece a chave e, por conseguinte, não são precisos passos adicionais.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Prepare o seu vídeo e os quadros de vídeo para revisão

Os quadros de vídeo de vídeo e vídeo de vídeo para revisão devem ser publicados online porque você precisa dos seus URLs.

> [!NOTE]
> O programa utiliza imagens manualmente guardadas do vídeo com pontuações aleatórias de adulto/picante para ilustrar o uso da API de revisão. Numa situação real, você usa a saída de [moderação](video-moderation-api.md#run-the-program-and-review-the-output) de vídeo para criar imagens e atribuir pontuações. 

Para o vídeo, precisa de um ponto final de streaming para que a ferramenta de revisão reproduza o vídeo na vista do jogador.

![Miniatura de demonstração de vídeo](images/ams-video-demo-view.PNG)

- Copie o **URL** nesta página de demonstração da [Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) para o url manifesto.

Para os quadros de vídeo (imagens), utilize as seguintes imagens:

![Miniatura de moldura de vídeo 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatura de moldura de vídeo 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatura de moldura de vídeo 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Quadro 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Quadro 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Quadro 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Criar o projeto do Visual Studio

1. Adicione um novo projeto **Aplicação de consola (.NET Framework)** à sua solução.

1. Nomeie o projeto **VideoReviews**.

1. Selecione este projeto como o projeto de arranque único para a solução.

### <a name="install-required-packages"></a>Instalar pacotes necessários

Instale os seguintes pacotes NuGet para o projeto TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Atualizar as instruções de utilização do programa

Modificar o programa está a usar declarações da seguinte forma.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Adicionar propriedades privadas

Adicione as seguintes propriedades privadas ao nome space **VideoReviews,** **programa**de classe . Atualize `AzureEndpoint` `CMSubscriptionKey` os campos e campos com os valores do URL final e chave de subscrição. Pode encontrá-los no **separador De arranque rápido** do seu recurso no portal Azure.


```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Criar objeto de cliente moderador de conteúdo

Adicione a seguinte definição de método ao nomespace **VideoReviews**, **programa**de classe .

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Criar uma revisão de vídeo

Crie uma análise de vídeo com **ContentModeratorClient.Reviews.CreateVideoReviews**. Para obter mais informações, veja a [Referência à API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** tem os seguintes parâmetros necessários:
1. Uma corda que contém um tipo MIME, que deve ser "aplicação/json". 
1. O nome da sua equipa moderadorde conteúdo.
1. Um **iList\<CreateVideoReviewsBodyItem>** objeto. Cada objeto **CreateVideoReviewsBodyItem** representa uma revisão de vídeo. Este quickstart cria uma revisão de cada vez.

**CreateVideoReviewsBodyItem** tem várias propriedades. No mínimo, define as seguintes propriedades:
- **Conteúdo**. O URL do vídeo a ser revisto.
- **Contentid**. Uma identificação para atribuir à revisão de vídeo.
- **Estado**. Desloque o valor para "Não publicado". Se não o definir, não se encontra em "Pendente", o que significa que a revisão em vídeo é publicada e pendente de revisão humana. Uma vez publicada uma revisão de vídeo, já não se pode adicionar quadros de vídeo, uma transcrição ou um resultado de moderação da transcrição.

> [!NOTE]
> **CreateVideoReviews** devolve um\<> de cadeiaIList. Cada uma destas cordas contém um ID para uma revisão de vídeo. Estes IDs são GUIDs e não são os mesmos que o valor da propriedade **ContentId.** 

Adicione a seguinte definição de método ao nomespace VideoReviews, programa de classe.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> A chave de serviço do Content Moderator tem um limite de velocidade de pedidos por segundo (RPS) e, se ultrapassar o limite, o SDK emite uma exceção com o código de erro 429.
>
> Uma chave de escalão gratuito tem um limite de velocidade de um RPS.

## <a name="add-video-frames-to-the-video-review"></a>Adicione quadros de vídeo à análise de vídeo

Adiciona quadros de vídeo a uma análise de vídeo com **ContentModeratorClient.Reviews.AddVideoFrameUrl** (se os seus quadros de vídeo forem apresentados online) ou **ContentModeratorClient.Reviews.AddVideoFrameStream** (se os seus quadros de vídeo forem apresentados localmente). Este quickstart pressupõe que os seus quadros de vídeo são hospedados online, e assim utiliza **AddVideoFrameUrl**. Para obter mais informações, veja a [Referência à API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** tem os seguintes parâmetros necessários:
1. Uma corda que contém um tipo MIME, que deve ser "aplicação/json".
1. O nome da sua equipa moderadorde conteúdo.
1. O ID da análise de vídeo devolvido pela **CreateVideoReviews**.
1. Um **iList\<VideoFrameBodyItem>** objeto. Cada objeto **VideoFrameBodyItem** representa uma moldura de vídeo.

**VideoFrameBodyItem** tem as seguintes propriedades:
- **Marca de tempo.** Uma corda que contém, em segundos, o tempo no vídeo a partir do qual a moldura de vídeo foi tirada.
- **FrameImage**. O URL da moldura de vídeo.
- **Metadados.** Um IList\<VideoFrameBodyItemMetadataItem>. **VideoFrameBodyItemMetadataItem** é simplesmente um par chave/valor. As chaves válidas incluem:
- **revisãoRecomendado**. É verdade que recomenda-se uma revisão humana da moldura de vídeo.
- **adultScore**. Um valor de 0 a 1 que classifica a gravidade do conteúdo adulto no quadro de vídeo.
- **a**. É verdade que o vídeo contém conteúdo adulto.
- **racyScore**. Um valor de 0 a 1 que classifica a gravidade dos conteúdos picantes no quadro de vídeo.
- **r**. É verdade que a moldura do vídeo contém conteúdo picante.
- **ReviewerResultTags**. Um IList\<VideoFrameBodyItemReviewerResultTagsItem>. **VideoFrameBodyItemReviewerResultTagsItem** é simplesmente um par chave/valor. Uma aplicação pode usar estas tags para organizar molduras de vídeo.

> [!NOTE]
> Este quickstart gera valores aleatórios para as propriedades **adultScore** e **racyScore.** Numa aplicação de produção, obteria estes valores do serviço de moderação de [vídeo,](video-moderation-api.md)implantado como serviço de mídia azure.

Adicione as seguintes definições de método ao nome space VideoReviews, programa de classe.

```csharp
<summary>
/// Create a video frame to add to a video review after the video review is created.
/// </summary>
/// <param name="url">The URL of the video frame image.</param>
/// <returns>The video frame.</returns>
private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
{
    // We generate random "adult" and "racy" scores for the video frame.
    Random rand = new Random();

    var frame = new VideoFrameBodyItem
    {
        // The timestamp is measured in milliseconds. Convert from seconds.
        Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
        FrameImage = url,

        Metadata = new List<VideoFrameBodyItemMetadataItem>
        {
            new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
            new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("a", "false"),
            new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("r", "false")
        },

        ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
        {
            new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
        }
    };

    return frame;
}
```

```csharp
/// <summary>
/// Add a video frame to the indicated video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="url">The URL of the video frame image.</param>
static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
{
    Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

    var frames = new List<VideoFrameBodyItem>()
    {
        CreateFrameToAddToReview(url, timestamp_seconds)
    };
        
    client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

    Thread.Sleep(throttleRate);
```

## <a name="get-video-frames-for-video-review"></a>Obtenha molduras de vídeo para revisão de vídeo

Pode obter os quadros de vídeo para uma análise de vídeo com **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** tem os seguintes parâmetros necessários:
1. O nome da sua equipa moderadorde conteúdo.
1. O ID da análise de vídeo devolvido pela **CreateVideoReviews**.
1. O índice de base zero do primeiro quadro de vídeo a obter.
1. O número de molduras de vídeo para obter.

Adicione a seguinte definição de método ao nomespace VideoReviews, programa de classe.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Obtenha informações sobre análise de vídeo

Obtém informações para uma revisão de vídeo com **ContentModeratorClient.Reviews.GetReview**. **GetReview** tem os seguintes parâmetros necessários:
1. O nome da sua equipa moderadorde conteúdo.
1. O ID da análise de vídeo devolvido pela **CreateVideoReviews**.

Adicione a seguinte definição de método ao nomespace VideoReviews, programa de classe.

```csharp
/// <summary>
/// Get the information for the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void GetReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

    var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Publicar revisão de vídeo

Publica uma análise de vídeo com **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** tem os seguintes parâmetros necessários:
1. O nome da sua equipa moderadorde conteúdo.
1. O ID da análise de vídeo devolvido pela **CreateVideoReviews**.

Adicione a seguinte definição de método ao nomespace VideoReviews, programa de classe.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Juntar tudo

Adicione a definição de método **principal** ao nome space VideoReviews, programa de classe. Por fim, feche a aula de Programa e o espaço de nome sinuoso de VideoReviews.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
        var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
        var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

        // Add the frames from 17, 64, and 144 seconds.
        AddFrame(client, review_id, frame1_url, "17");
        AddFrame(client, review_id, frame2_url, "64");
        AddFrame(client, review_id, frame3_url, "144");

        // Get frames information and show
        GetFrames(client, review_id);
        GetReview(client, review_id);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Executar o programa e rever o resultado
Ao executar a aplicação, vê uma saída nas seguintes linhas:

```json
Creating a video review.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "VideoFrames": [
    {
        "Timestamp": "17000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.808312381528463"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.846378884206702"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "64000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.576078300166912"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.244768953064815"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "144000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.664480847150311"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.933817870418456"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
        ]
    }
    ]
}

Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "SubTeam": "public",
    "Status": "UnPublished",
    "ReviewerResultTags": [],
    "CreatedBy": "testreview6",
    "Metadata": [
    {
        "Key": "FrameCount",
        "Value": "3"
    }
    ],
    "Type": "Video",
    "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    "ContentId": "review1",
    "CallbackEndpoint": null
}

Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="check-out-your-video-review"></a>Confira a sua análise de vídeo

Por fim, vê a análise de vídeo na sua conta de análise de Moderador de Conteúdo no ecrã de**Vídeo** **de Revisão.**>

![Revisão em vídeo para moderadores humanos](images/ams-video-review.PNG)

## <a name="next-steps"></a>Passos seguintes

Obtenha o Moderador de [Conteúdo .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e a [solução Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros quickstarts de Moderador de Conteúdo para .NET.

Saiba como adicionar moderação da [transcrição](video-transcript-moderation-review-tutorial-dotnet.md) à análise de vídeo. 

Confira o tutorial detalhado sobre como desenvolver uma [solução completa](video-transcript-moderation-review-tutorial-dotnet.md)de moderação de vídeo .
