---
title: Analisar conteúdo de vídeo para material censurável em C# - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Como analisar conteúdo de vídeo para vários materiais censuráveis utilizando o SDK moderador de conteúdo para .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 71858755fe31823d4d7ef8623b915db851530116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72755243"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analisar conteúdo de vídeo para material censurável em C #

Este artigo fornece informações e amostras de código para ajudá-lo a começar a usar o [SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) moderador de conteúdo para .NET para digitalizar conteúdo sonoro para conteúdos adultos ou picantes.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure

A capacidade de moderação de vídeo do Moderador de Conteúdo está disponível como um processador de **visualização** pública gratuito em Azure Media Services (AMS). O Azure Media Services é um serviço Azure especializado para armazenar e transmitir conteúdos de vídeo. 

### <a name="create-an-azure-media-services-account"></a>Criar uma conta dos Media Services do Azure

Siga as instruções na [Create a Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) para subscrever a AMS e criar uma conta de armazenamento Azure associada. Nessa conta de armazenamento, crie um novo recipiente de armazenamento Blob.

### <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação azure ative diretório

Navegue para a sua nova subscrição AMS no portal Azure e selecione **o acesso a API** a partir do menu lateral. Selecione **Connect to Azure Media Services com o diretor de serviço**. Note o valor no campo final da **API REST;** Vai precisar disto mais tarde.

Na secção de **aplicações Azure AD,** selecione **Create New** e nomeie o seu novo registo de aplicação Azure AD (por exemplo, "VideoModADApp"). Clique em **Guardar** e aguarde alguns minutos enquanto a aplicação estiver configurada. Em seguida, deverá ver o seu novo registo de aplicações na secção de **aplicações Azure AD** da página.

Selecione o registo da sua aplicação e clique no botão **'Gerir'** abaixo. Note o valor no campo de ID da **aplicação;** Vai precisar disto mais tarde. Selecione **Teclas** > de**Definições**e introduza uma descrição para uma nova tecla (como "VideoModKey"). Clique em **Guardar**e, em seguida, note o novo valor-chave. Copie esta corda e guarde-a em algum lugar seguro.

Para uma passagem mais completa do processo acima, Veja [Começar com a autenticação Azure AD](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

Uma vez feito isto, pode usar o processador de mídia de moderação de vídeo de duas maneiras diferentes.

## <a name="use-azure-media-services-explorer"></a>Utilizar o Azure Media Services Explorer

O Azure Media Services Explorer é um frontend amigável para a AMS. Use-a para navegar na sua conta AMS, fazer upload de vídeos e digitalizar conteúdos com o processador de mídia Moderador de Conteúdo. Descarregue e instale-o a partir do [GitHub,](https://github.com/Azure/Azure-Media-Services-Explorer/releases)ou consulte o post do [blog Azure Media Services Explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) para obter mais informações.

![Explorador de Serviços De Mídia Azure com Moderador de Conteúdo](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. No Estúdio Visual, crie um novo projeto de **aplicação de consola (.NET Framework)** e nomeie-o **VideoModeration**. 
1. Se houver outros projetos na sua solução, selecione esta como o único projeto de arranque.
1. Obtenha os pacotes NuGet necessários. Clique com o botão direito do rato no seu projeto no Explorador de Soluções e selecione **Manage NuGet Packages** (Gerir Pacotes NuGet); em seguida, localize e instale os pacotes seguintes:
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Adicione código de moderação de vídeo

Depois, vai copiar e colar o código neste guia no projeto, para implementar um cenário de moderação de conteúdos simples.

### <a name="update-the-programs-using-statements"></a>Atualizar as instruções de utilização do programa

Adicione as declarações `using` seguintes à parte superior do ficheiro _Program.cs_.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>Configurar referências de recursos

Adicione os seguintes campos estáticos à classe **Programa** em _Program.cs_. Estes campos possuem as informações necessárias para a ligação à sua subscrição AMS. Preencha-os com os valores que obteve nos degraus acima. Tenha em `CLIENT_ID` anote que é o valor de `CLIENT_SECRET` ID de **aplicação** da sua aplicação Azure AD, e é o valor do "VideoModKey" que criou para essa aplicação.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Se desejar utilizar um ficheiro de vídeo local (caso mais simples), adicione-o ao projeto e entre no seu caminho como valor `INPUT_FILE` (os caminhos relativos são relativos ao diretório de execução).

Também terá de criar o ficheiro _preset.json_ no diretório atual e usá-lo para especificar um número de versão. Por exemplo:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Carregar os vídeos de entrada(s)

O método **principal** da classe **Program** criará um Contexto Azure Media e, em seguida, um Contexto de Armazenamento Azure (caso os seus vídeos estejam no armazenamento de blob). O código restante digitaliza um vídeo de uma pasta local, blob ou múltiplas bolhas dentro de um recipiente de armazenamento Azure. Pode experimentar todas as opções comentando as outras linhas de código.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Criar um contexto azure media

Adicione o seguinte método à classe **Programa**. Isto utiliza as suas credenciais AMS para permitir a comunicação com a AMS.

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Adicione o código para criar um contexto de armazenamento azure

Adicione o seguinte método à classe **Programa**. Utiliza o Contexto de Armazenamento, criado a partir das suas credenciais de armazenamento, para aceder ao seu armazenamento de blob.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Adicione o código para criar ativos azure media a partir de arquivo local e blob

O processador de mídia Content Moderator gere empregos em **Ativos** dentro da plataforma Azure Media Services.
Estes métodos criam os Ativos a partir de um ficheiro local ou de uma bolha associada.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Adicione o código para digitalizar uma coleção de vídeos (como bolhas) dentro de um recipiente

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Adicione o método para executar o Trabalho de Moderador de Conteúdo

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>Adicionar funções de ajudante

Estes métodos descarregam o ficheiro de saída do Moderador de Conteúdo (JSON) do ativo azure Media Services e ajudam a rastrear o estado do trabalho de moderação para que o programa possa registar um estado de execução na consola.

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>Executar o programa e rever o resultado

Após a conclusão do trabalho de moderação de conteúdo, analise a resposta json. É constituído por estes elementos:

- Resumo da informação de vídeo
- **Tiros** como "**fragmentos**"
- **Quadros-chave** como "**eventos**" com uma **revisãoRecomendado " (= verdadeiro ou falso)"** bandeira baseada em pontuações **de Adulto** e **Picante**
- **início,** **duração,** **duração total,** e carimbo de **tempo** estão em "tiques". Divida por **tempo** para obter o número em segundos.
 
> [!NOTE]
> - `adultScore`representa a presença potencial e a pontuação de previsão de conteúdo que pode ser considerada sexualmente explícita ou adulta em determinadas situações.
> - `racyScore`representa a presença potencial e a pontuação de previsão de conteúdo que pode ser considerada sexualmente sugestiva ou madura em determinadas situações.
> - `adultScore`e `racyScore` estão entre 0 e 1. Quanto maior for a pontuação, maior é o modelo que prevê que a categoria pode ser aplicável. Esta pré-visualização baseia-se num modelo estatístico e não em resultados codificados manualmente. Recomendamos testar com o seu próprio conteúdo para determinar como cada categoria se alinha com os seus requisitos.
> - `reviewRecommended`é verdadeiro ou falso dependendo dos limiares de pontuação internos. Os clientes devem avaliar se utilizam este valor ou decidem sobre os limiares personalizados com base nas suas políticas de conteúdo.

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>Passos seguintes

Saiba como gerar [críticas](video-reviews-quickstart-dotnet.md) de vídeo a partir da sua saída de moderação.

Adicione moderação da transcrição às suas [análises](video-transcript-moderation-review-tutorial-dotnet.md) de vídeo.

Confira o tutorial detalhado sobre como construir uma solução completa de moderação de [vídeo e transcrição](video-transcript-moderation-review-tutorial-dotnet.md).

[Baixe a solução Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros quickstarts de Moderador de Conteúdo para .NET.
