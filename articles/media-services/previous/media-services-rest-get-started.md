---
title: Começar com a entrega de conteúdos a pedido usando o REST [ Microsoft Docs
description: Este tutorial acompanha-o através dos passos de implementação de uma aplicação de entrega de conteúdo sonoro com a Azure Media Services utilizando a REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8989acc6d21a3c53be9d97c74ed7fbf03ba54819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773673"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Começar com a entrega de conteúdo a pedido usando o REST  

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Este quickstart acompanha-o através dos passos de implementação de uma aplicação de entrega de conteúdos video-on-demand (VoD) utilizando a Azure Media Services (AMS) REST APIs.

O tutorial apresenta o fluxo de trabalho básico dos Media Services, os objetos de programação mais comuns e as tarefas necessárias para o desenvolvimento dos Media Services. No final do tutorial, é possível transmitir ou descarregar progressivamente um ficheiro de mídia de amostra que fez upload, codificado e descarregado.

A imagem seguinte mostra alguns objetos mais frequentemente utilizados ao desenvolver aplicações VoD contra o modelo OData dos Serviços de Multimédia.

Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Pré-requisitos
São necessários os seguintes pré-requisitos para começar a desenvolver-se com os Serviços de Media com APIs REST.

* Uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta dos Media Services. Para criar uma conta dos Media Services, consulte [Como Criar uma Conta de Media Services](media-services-portal-create-account.md).
* Compreensão de como se desenvolver com a Media Services REST API. Para mais informações, consulte a Visão [Geral da API](media-services-rest-how-to-use.md)do Media Services REST .
* Uma aplicação à sua escolha que pode enviar pedidos e respostas HTTP. Este tutorial usa [Fiddler.](https://www.telerik.com/download/fiddler)

As seguintes tarefas são mostradas neste arranque rápido.

1. Iniciar pontos finais de transmissão em fluxo (com o portal do Azure).
2. Ligue-se à conta de Serviços de Media com a REST API.
3. Crie um novo ativo e faça upload de um ficheiro de vídeo com a REST API.
4. Codifique o ficheiro fonte num conjunto de ficheiros MP4 bitrate adaptativo com a API REST.
5. Publique o ativo e obtenha URLs de streaming e descarregamento progressivo com REST API.
6. Reproduzir os conteúdos.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Utilize o mesmo ID de política se estiver sempre a utilizar as mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que se destinam a permanecer no lugar por muito tempo (políticas de não upload). Para mais informações, consulte [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

Para mais detalhes sobre as entidades AMS REST utilizadas neste artigo, consulte [a Referência aPi da Azure Media Services REST API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Consulte também os [conceitos azure Media Services.](media-services-concepts.md)

>[!NOTE]
>Ao aceder a entidades em Serviços de Media, deve definir campos e valores específicos nos seus pedidos HTTP. Para mais informações, consulte [Configuração para Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Iniciar pontos finais de transmissão em fluxo com o portal do Azure

Ao trabalhar com a Azure Media Services, um dos cenários mais comuns é entregar vídeo através de streaming de bitrate adaptativo. Os Serviços de Multimédia fornecem um empacotamento dinâmico, o que lhe permite entregar os seus conteúdos codificados em MP4 de velocidade de transmissão adaptável em formatos de transmissão em fluxo suportados pelos Serviços de Multimédia (MPEG DASH, HLS, Smooth Streaming) just-in-time, sem ter de armazenar versões pré-empacotadas de cada um destes formatos de transmissão em fluxo.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

Para iniciar o ponto final de transmissão em fluxo, faça o seguinte:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Na janela Definições, clique em Pontos finais de transmissão em fluxo.
3. Clique no ponto final de transmissão em fluxo predefinido.

    A janela DETALHES DO PONTO FINAL DE TRANSMISSÃO EM FLUXO PREDEFINIDO é apresentada.

4. Clique no ícone Início.
5. Clique no botão Guardar para guardar as alterações.

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>Ligue-se à conta de Serviços de Media com a REST API

Para obter informações sobre como se conectar à AMS API, consulte [Aceda à API dos Serviços de Mídia Azure com autenticação Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>Crie um novo ativo e carregue um ficheiro de vídeo com a REST API

Nos Serviços de Multimédia, os ficheiros digitais são carregados para um elemento. A entidade **Asset** pode conter vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados (e os metadados sobre estes ficheiros.)  Uma vez que os ficheiros são enviados para o ativo, o seu conteúdo é armazenado de forma segura na nuvem para posterior processamento e streaming.

Um dos valores que tem de fornecer ao criar um ativo são as opções de criação de ativos. A propriedade **Options** é um valor de enumeração que descreve as opções de encriptação com as que um Ativo pode ser criado. Um valor válido é um dos valores da lista abaixo, e não uma combinação de valores desta lista:

* **Nenhum** = **0** - Não é utilizada encriptação. Ao utilizar esta opção, o seu conteúdo não está protegido em trânsito ou em repouso no armazenamento.
    Se planear distribuir um MP4 utilizando uma transferência progressiva, utilize esta opção.
* **ArmazenamentoEncriptado** = **1** - Encripta o seu conteúdo claro localmente usando encriptação de bitS AES-256 e, em seguida, envia-o para o Armazenamento Azure onde é armazenado encriptado em repouso. Os elementos protegidos com Encriptação do Storage são desencriptados automaticamente e colocados num sistema de ficheiros encriptados antes da codificação, sendo opcionalmente encriptados novamente antes de serem carregados novamente como um novo elemento de saída. O principal caso de utilização da Encriptação do Storage ocorre quando pretende proteger os seus ficheiros de multimédia de entrada de alta qualidade inativos no disco com uma encriptação forte.
* **CommonEncryptionProtected** = **2** - Utilize esta opção se estiver a carregar conteúdo que já foi encriptado e protegido com encriptação comum ou DRM PlayReady (por exemplo, Streaming Suave protegido com DRM PlayReady).
* **EnvelopeEncryptionProtected** = **4** – Utilize esta opção se estiver a carregar HLS encriptado com AES. Os ficheiros devem ter sido codificados e encriptados pelo Transform Manager.

### <a name="create-an-asset"></a>Criar um ativo
Um ativo é um recipiente para vários tipos ou conjuntos de objetos em Media Services, incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados. Na Rest API, a criação de um Ativo requer o envio de pedido de CORREIO para os Serviços de Media e a colocação de qualquer informação de propriedade sobre o seu ativo no organismo de pedido.

O exemplo que se segue mostra como criar um ativo.

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**Resposta HTTP**

Se for bem sucedido, o seguinte é devolvido:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>Criar um AssetFile
A entidade [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representa um ficheiro de vídeo ou áudio que é armazenado num recipiente de bolha. Um ficheiro de ativo está sempre associado a um ativo, e um ativo pode conter um ou muitos AssetFiles. A tarefa Do Codificador de Serviços de Media falha se um objeto de ficheiro de ativo não estiver associado a um ficheiro digital num recipiente de blob.

Depois de enviar o seu ficheiro de mídia digital para um recipiente blob, utiliza o pedido **MERGE** HTTP para atualizar o AssetFile com informações sobre o seu ficheiro de mídia (como mostrado mais tarde no tópico).

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Criação da Política de Acesso com permissão por escrito
Antes de enviar quaisquer ficheiros para o armazenamento de blob, delineie os direitos de política de acesso para escrever a um ativo. Para tal, PUBLIQUE um pedido http para o conjunto de entidades AccessPolicies. Defina um valor de DuraçãoInMinutes após a criação ou receba uma mensagem de erro do Servidor Interno 500 de volta em resposta. Para obter mais informações sobre AccessPolicies, consulte [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

O exemplo que se segue mostra como criar uma Política de Acesso:

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**Resposta HTTP**

Se for bem sucedido, devolve-se a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Obtenha o URL de upload

Para receber o URL de upload real, crie um Localizador SAS. Os localizadores definem o tempo de início e o tipo de ponto final de ligação para clientes que desejam aceder a Ficheiros num Ativo. Pode criar várias entidades localizadoras para um dado par accessPolicy e Asset para lidar com diferentes pedidos e necessidades do cliente. Cada um destes Localizadores utiliza o valor StartTime mais o valor DurationInMinutes da AccessPolicy para determinar o tempo que um URL pode ser usado. Para mais informações, consulte [O Localizador.](https://docs.microsoft.com/rest/api/media/operations/locator)

Um URL SAS tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

São aplicáveis algumas considerações:

* Não pode ter mais de cinco localizadores únicos associados a um dado Ativo de uma só vez. 
* Se necessitar de fazer o upload dos seus ficheiros imediatamente, deverá definir o seu valor Inicial para cinco minutos antes da hora atual. Isto porque pode haver um desvio de relógio entre a sua máquina cliente e os Serviços de Media. Além disso, o seu valor startTime deve estar no seguinte formato DateTime: YYYY-MM-DDTHH:mm:ssZ (por exemplo, "2014-05-23T17:53:50Z").    
* Pode haver um atraso de 30-40 segundos após a criação de um Localizador para quando estiver disponível para utilização. Este problema aplica-se tanto aos [localizadores de URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) como aos Localizadores de Origem.

O exemplo seguinte mostra como criar um localizador de URL SAS, tal como definido pela propriedade Tipo no organismo de pedido ("1" para um localizador SAS e "2" para um localizador de origem a pedido). A propriedade **Path** devolvida contém o URL que deve utilizar para fazer o upload do seu ficheiro.

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Resposta HTTP**

Se for bem sucedido, devolve-se a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Faça upload de um ficheiro num recipiente de armazenamento de bolhas
Uma vez que tenha o conjunto AccessPolicy e Locator, o ficheiro real é enviado para um recipiente de armazenamento de blob Azure utilizando as APIs DE REPOUSO DE ARMAZENAMENTO Azure. Tem de carregar os ficheiros como blocos. As bolhas de página não são suportadas pela Azure Media Services.  

> [!NOTE]
> Tem de adicionar o nome de ficheiro do ficheiro que pretende enviar para o valor **do Caminho** do Localizador recebido na secção anterior. Por exemplo, `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Para obter mais informações sobre o trabalho com as bolhas de armazenamento Azure, consulte [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Atualizar o Ficheiro de Ativos
Agora que fez o upload do seu ficheiro, atualize a informação do tamanho do FileAsset (e outras). Por exemplo:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta HTTP**

Se for bem sucedido, o seguinte é devolvido:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Eliminar o Localizador e a Política de Acesso
**Pedido de HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta HTTP**

Se for bem sucedido, o seguinte é devolvido:

    HTTP/1.1 204 No Content
    ...

**Pedido de HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

**Resposta HTTP**

Se for bem sucedido, o seguinte é devolvido:

    HTTP/1.1 204 No Content
    ...

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>Codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável

Depois de ingerir ativos nos Serviços de Media, os meios de comunicação podem ser codificados, transmuxados, marcados com água, e assim por diante, antes de serem entregues aos clientes. Estas atividades são agendadas e executadas em várias instâncias de função de segundo plano para assegurar um elevado desempenho e disponibilidade. Estas atividades são chamadas Jobs e cada Trabalho é composto por Tarefas atómicas que [Task](https://docs.microsoft.com/rest/api/media/operations/task) fazem o trabalho real no ficheiro Ativo (para mais informações, ver [Descrições de Tarefas, Trabalho).](https://docs.microsoft.com/rest/api/media/operations/job)

Como já foi referido, ao trabalhar com a Azure Media Services um dos cenários mais comuns é entregar streaming de bitrate adaptativo aos seus clientes. Os Serviços de Media podem embalar dinamicamente um conjunto de ficheiros MP4 bitrate adaptativo num dos seguintes formatos: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

A secção seguinte mostra como criar um trabalho que contenha uma tarefa de codificação. A tarefa especifica transcodificar o ficheiro mezanino num conjunto de MP4 bitrate adaptativo utilizando **o Media Encoder Standard**. A secção mostra também como monitorizar o progresso do processamento de postos de trabalho. Quando o trabalho estiver concluído, poderá criar localizadores que sejam necessários para ter acesso aos seus bens.

### <a name="get-a-media-processor"></a>Obtenha um processador de mídia
Nos Serviços de Media, um processador de mídia é um componente que lida com uma tarefa de processamento específica, como codificação, conversão de formato, encriptação ou desencriptação de conteúdos de mídia. Para a tarefa de codificação mostrada neste tutorial, vamos usar a Norma Media Encoder.

O código seguinte solicita a identificação do codificador.

**Pedido de HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Criar uma tarefa
Cada Trabalho pode ter uma ou mais Tarefas dependendo do tipo de processamento que pretende realizar. Através da API REST, pode criar Jobs e as suas tarefas relacionadas de uma de duas formas: As tarefas podem ser definidas inline através da propriedade de navegação de Tarefas em entidades de Trabalho, ou através do processamento de lotes OData. O Media Services SDK utiliza o processamento de lotes. No entanto, para a legibilidade dos exemplos de código neste artigo, as tarefas são definidas inline. Para obter informações sobre o processamento de lotes, consulte [o Processamento de Lote de Dados Abertos (OData).](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)

O exemplo que se segue mostra-lhe como criar e publicar um Trabalho com uma Tarefa definida para codificar um vídeo numa resolução e qualidade específicas. A seguinte secção de documentação contém a lista de todas as [predefinições](https://msdn.microsoft.com/library/mt269960) de tarefa suportadas pelo processador Media Encoder Standard.  

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**Resposta HTTP**

Se for bem sucedido, devolve-se a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


Há algumas coisas importantes a notar em qualquer pedido de emprego:

* As propriedades do TaskBody DEVEM utilizar o XML literal para definir o número de ativos de entrada ou de saída utilizados pela Tarefa. O artigo task contém a Definição De Schema XML para o XML.
* Na definição TaskBody, cada `<inputAsset>` `<outputAsset>` valor interior para e deve ser definido como JobInputAsset(valor) ou JobOutputAsset (valor).
* Uma tarefa pode ter vários ativos de produção. Um JobOutputAsset(x) só pode ser usado uma vez como uma saída de uma tarefa num trabalho.
* Pode especificar JobInputAsset ou JobOutputAsset como um ativo de entrada de uma tarefa.
* As tarefas não devem formar um ciclo.
* O parâmetro de valor que passa para JobInputAsset ou JobOutputAsset representa o valor de índice para um Ativo. Os Ativos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets na definição da entidade Job.

> [!NOTE]
> Uma vez que os Serviços de Media são construídos no OData v3, os ativos individuais nas coleções de propriedades de navegação InputMediaAssets e OutputMediaAssets são referenciados através de um par de nomes "__metadata : uri".
>
>

* O InputMediaAssets mapeia um ou mais Ativos que criou nos Serviços de Media. OutputMediaAssets são criados pelo sistema. Não referem um ativo existente.
* OutputMediaAssets pode ser nomeado usando o atributo assetName. Se este atributo não estiver presente, então o nome do OutputMediaAsset é qualquer que seja o valor de texto interno do `<outputAsset>` elemento com um sufixo do valor do Nome de Trabalho, ou o valor do Id de Trabalho (no caso em que a propriedade Nome não está definida). Por exemplo, se definir um valor para o nome do ativo para "Sample", então a propriedade OutputMediaAsset Name será definida para "Sample". No entanto, se não definisse um valor para o assetName, mas definisse o nome de trabalho para "NewJob", então o nome OutputMediaAsset seria "JobOutputAsset(valor)_NewJob".

    O exemplo que se segue mostra como definir o atributo do nome do ativo:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Para permitir o acorrentamento de tarefas:

  * Um trabalho deve ter pelo menos duas tarefas.
  * Deve haver pelo menos uma tarefa cujo contributo é o resultado de outra tarefa no trabalho.

Para mais informações consulte, [Criar um Trabalho de Codificação com os Serviços de Media REST API](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Monitorizar o progresso do processamento
Pode recuperar o estatuto de Trabalho utilizando a propriedade do Estado, como mostra o seguinte exemplo:

**Pedido de HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Resposta HTTP**

Se for bem sucedido, devolve-se a seguinte resposta:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Cancelar um trabalho
A Media Services permite-lhe cancelar trabalhos de funcionamento através da função CancelJob. Esta chamada devolve um código de erro de 400 se tentar cancelar um Trabalho quando o seu estado for cancelado, cancelando, error ou terminado.

O exemplo que se segue mostra como ligar para o CancelJob.

**Pedido de HTTP**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Se for bem sucedido, um código de resposta 204 é devolvido sem corpo de mensagem.

> [!NOTE]
> Deve codificar o id de trabalho (normalmente nb:jid:UUID: somevalue) ao passá-lo como parâmetro para o CancelJob.
>
>

### <a name="get-the-output-asset"></a>Obtenha o ativo de saída
O código seguinte mostra como solicitar o ativo de saída Id.

**Pedido de HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>Publique o ativo e obtenha URLs de streaming e descarregamento progressivo com REST API

Para transmitir ou transferir um elemento, primeiro tem de o "publicar" através da criação de um localizador. Os localizadores fornecem acesso aos ficheiros contidos no elemento. Os Media Services suportam dois tipos de localizadores: localizadores OnDemandOrigin, utilizados para transmitir multimédia (por exemplo, MPEG DASH, HLS ou Transmissão em Fluxo Uniforme) e localizadores de Assinatura de Acesso (SAS), utilizados para transferir ficheiros de multimédia. 

Assim que criar os localizadores, pode construir os URLs que são usados para transmitir ou descarregar os seus ficheiros.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

Um URL de transmissão em fluxo para uma Transmissão em Fluxo Uniforme tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Um URL de transmissão em fluxo para HLS tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Um URL de transmissão em fluxo para MPEG DASH tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Um URL de SAS utilizado para transferir ficheiros tem o seguinte formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Esta secção mostra como executar as seguintes tarefas necessárias para "publicar" os seus ativos.  

* Criação da Política de Acesso com permissão de leitura
* Criação de um URL SAS para descarregar conteúdo
* Criar um URL de origem para streaming de conteúdos

### <a name="creating-the-accesspolicy-with-read-permission"></a>Criação da Política de Acesso com permissão de leitura
Antes de descarregar ou transmitir qualquer conteúdo de mídia, primeiro defina uma Política de Acesso com permissões de leitura e crie a entidade localizador apropriada que especifica o tipo de mecanismo de entrega que pretende ativar para os seus clientes. Para obter mais informações sobre os imóveis disponíveis, consulte [AccessPolicy Entity Properties](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

O exemplo que se segue mostra como especificar a Política de Acesso para permissões de leitura para um determinado Ativo.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Se for bem sucedido, um código de sucesso de 201 é devolvido descrevendo a entidade AccessPolicy que criou. Em seguida, utiliza o Id accessPolicy juntamente com o Id de ativo do ativo que contém o ficheiro que pretende entregar (como um ativo de saída) para criar a entidade Locator.

> [!NOTE]
> Este fluxo de trabalho básico é o mesmo que carregar um ficheiro ao ingerir um Ativo (como foi discutido anteriormente neste tópico). Além disso, como o upload de ficheiros, se você (ou seus clientes) precisar de aceder imediatamente aos seus ficheiros, detete o seu valor StartTime para cinco minutos antes da hora atual. Esta ação é necessária porque pode haver um desvio de relógio entre o cliente e os Serviços de Media. O valor startTime deve estar no seguinte formato DateTime: YYYY-MM-DDTHH:mm:ssZ (por exemplo, "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Criação de um URL SAS para descarregar conteúdo
O código que se segue mostra como obter um URL que pode ser usado para descarregar um ficheiro de mídia criado e carregado anteriormente. O AccessPolicy tem conjunto de permissões de leitura e o caminho do Localizador refere-se a um URL de descarregamento SAS.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Se for bem sucedido, devolve-se a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

A propriedade **"Caminho** Devolvido" contém o URL SAS.

> [!NOTE]
> Se descarregar conteúdo encriptado de armazenamento, deve descografá-lo manualmente antes de o renderizar, ou utilizar o Processador de Desencriptação de Armazenamento numa tarefa de processamento para obter ficheiros processados de forma clara para um OutputAsset e, em seguida, transferir a partir desse Ativo. Para obter mais informações sobre o processamento, consulte Criar um Trabalho de Codificação com a Media Services REST API. Além disso, os localizadores de URL SAS não podem ser atualizados depois de terem sido criados. Por exemplo, não é possível reutilizar o mesmo Localizador com um valor inicial atualizado. Isto deve-se à forma como os URLs SAS são criados. Se pretender aceder a um ativo para download depois de expirado um Locator, então terá de criar um novo com um novo StartTime.
>
>

### <a name="download-files"></a>Transferir ficheiros
Assim que tiver o conjunto AccessPolicy e Locator, pode descarregar ficheiros utilizando as APIs REST REST de Armazenamento Azure.  

> [!NOTE]
> Tem de adicionar o nome de ficheiro do ficheiro que pretende transferir para o valor **Do** Caminho do Localizador recebido na secção anterior. Por exemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Para obter mais informações sobre o trabalho com as bolhas de armazenamento Azure, consulte [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Como resultado do trabalho de codificação que realizou anteriormente (codificação em conjunto DEMP4 adaptativo), tem vários ficheiros MP4 que pode descarregar progressivamente. Por exemplo:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Criar um URL de streaming para streaming de conteúdos
O seguinte código mostra como criar um localizador de URL de streaming:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Se for bem sucedido, devolve-se a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Para transmitir um URL de origem smooth streaming num leitor de streaming de mídia, deve anexar a propriedade Path com o nome do ficheiro manifesto Smooth Streaming, seguido de "/manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Para transmitir HLS, apêndice (formato=m3u8-aapl) após o "/manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Para transmitir mPEG DASH, apêndice (formato=mpd-time-csf) após o "/manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a name="play-your-content"></a><a id="play"></a>Jogue o seu conteúdo
Para transmitir o seu vídeo, utilize o [Leitor dos Media Services do Azure](https://aka.ms/azuremediaplayer).

Para testar o download progressivo, colhe um URL num browser (por exemplo, IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Passos Seguintes: percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
