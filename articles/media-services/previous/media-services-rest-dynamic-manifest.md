---
title: Criação de filtros com serviços de mídia azure REST API [ Microsoft Docs
description: Este tópico descreve como criar filtros para que o seu cliente possa usá-los para transmitir secções específicas de um fluxo. A Media Services cria manifestos dinâmicos para alcançar este streaming seletivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewr: cenkdin
ms.openlocfilehash: b778ad8c59cf51f92584cd3590f7d99244f37b2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774959"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Criação de filtros com serviços de mídia azure REST API 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

A partir do lançamento de 2.17, o Media Services permite-lhe definir filtros para os seus ativos. Estes filtros são regras do lado do servidor que permitem que os seus clientes escolham fazer coisas como: reprodução apenas uma secção de um vídeo (em vez de reproduzir todo o vídeo), ou especificar apenas um subconjunto de representações de áudio e vídeo que o dispositivo do seu cliente pode manusear (em vez de todas as representações que estão associadas ao ativo). Esta filtragem dos seus ativos é arquivada através do **Dynamic Manifest**s que são criados a pedido do seu cliente para transmitir um vídeo com base em filtros especificados.

Para obter informações mais detalhadas relacionadas com filtros e manifesto dinâmico, consulte a visão geral dos [manifestos dinâmicos.](media-services-dynamic-manifest-overview.md)

Este artigo mostra como usar APIs REST para criar, atualizar e eliminar filtros. 

## <a name="types-used-to-create-filters"></a>Tipos usados para criar filtros
Os seguintes tipos são utilizados na criação de filtros:  

* [Filtro](https://docs.microsoft.com/rest/api/media/operations/filter)
* [Filtro de ativos](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [ApresentaçãoTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect e FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> Ao aceder a entidades em Serviços de Media, deve definir campos e valores específicos nos seus pedidos HTTP. Para mais informações, consulte [Configuração para Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como se conectar à AMS API, consulte [Aceda à API dos Serviços de Mídia Azure com autenticação Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-filters"></a>Criar filtros
### <a name="create-global-filters"></a>Criar filtros globais
Para criar um Filtro Global, utilize os seguintes pedidos HTTP:  

#### <a name="http-request"></a>Pedido de HTTP
Cabeçalhos dos Pedidos

    POST https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host:media.windows.net 

Corpo do pedido 

    {  
       "Name":"GlobalFilter",
       "PresentationTimeRange":{  
          "StartTimestamp":"0",
          "EndTimestamp":"9223372036854775807",
          "PresentationWindowDuration":"12000000000",
          "LiveBackoffDuration":"0",
          "Timescale":"10000000"
       },
       "Tracks":[  
          {  
             "PropertyConditions":
                  [  
                {  
                   "Property":"Type",
                   "Value":"audio",
                   "Operator":"Equal"
                },
                {  
                   "Property":"Bitrate",
                   "Value":"0-2147483647",
                   "Operator":"Equal"
                }
             ]
          }
       ]
    }




#### <a name="http-response"></a>Resposta HTTP
    HTTP/1.1 201 Created 

### <a name="create-local-assetfilters"></a>Criar AssetFilters locais
Para criar um AssetFilter local, utilize os seguintes pedidos HTTP:  

#### <a name="http-request"></a>Pedido de HTTP
Cabeçalhos dos Pedidos

    POST https://media.windows.net/API/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net  

Corpo do pedido 

    {   
       "Name":"AssetFilter", 
       "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
       "PresentationTimeRange":{   
          "StartTimestamp":"0", 
          "EndTimestamp":"9223372036854775807", 
          "PresentationWindowDuration":"12000000000", 
          "LiveBackoffDuration":"0", 
          "Timescale":"10000000" 
       }, 
       "Tracks":[   
          {   
             "PropertyConditions": 
                  [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

#### <a name="http-response"></a>Resposta HTTP
    HTTP/1.1 201 Created 
    . . . 

## <a name="list-filters"></a>Filtros de lista
### <a name="get-all-global-filters-in-the-ams-account"></a>Obtenha todos os **Filtros**globais na conta AMS
Para listar filtros, utilize os seguintes pedidos HTTP: 

#### <a name="http-request"></a>Pedido de HTTP
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Obtenha **AssetFilter**s associado a um ativo
#### <a name="http-request"></a>Pedido de HTTP
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>Obtenha um **AssetFilter** com base no seu Id
#### <a name="http-request"></a>Pedido de HTTP
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Atualizar filtros
Utilize patch, PUT ou MERGE para atualizar um filtro com novos valores de propriedade.  Para obter mais informações sobre estas operações, consulte [PATCH, PUT, MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

Se atualizar um filtro, pode demorar até dois minutos para o ponto final de streaming atualizar as regras. Se o conteúdo foi servido utilizando este filtro (e cached em proxies e caches CDN), a atualização deste filtro pode resultar em falhas dos jogadores. Limpe a cache depois de atualizar o filtro. Se esta opção não for possível, considere utilizar um filtro diferente.  

### <a name="update-global-filters"></a>Atualizar filtros globais
Para atualizar um filtro global, utilize os seguintes pedidos http: 

#### <a name="http-request"></a>Pedido de HTTP
Cabeçalhos de pedido: 

    MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 
    Content-Length: 384

Corpo do pedido: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

### <a name="update-local-assetfilters"></a>Atualizar os AssetFilters locais
Para atualizar um filtro local, utilize os seguintes pedidos HTTP: 

#### <a name="http-request"></a>Pedido de HTTP
Cabeçalhos de pedido: 

    MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

Corpo do pedido: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 


## <a name="delete-filters"></a>Eliminar filtros
### <a name="delete-global-filters"></a>Eliminar filtros globais
Para eliminar um Filtro Global, utilize os seguintes pedidos HTTP:

#### <a name="http-request"></a>Pedido de HTTP
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN>  
    x-ms-version: 2.19 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Eliminar os AssetFilters locais
Para eliminar um AssetFilter local, utilize os seguintes pedidos HTTP:

#### <a name="http-request"></a>Pedido de HTTP
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Construa URLs de streaming que usam filtros
Para obter informações sobre como publicar e entregar os seus ativos, consulte [A Entrega de Conteúdos à Visão Geral](media-services-deliver-content-overview.md)dos Clientes .

Os seguintes exemplos mostram como adicionar filtros aos seus URLs de streaming.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Transmissão em Fluxo Uniforme**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Manifestos dinâmicos visão geral](media-services-dynamic-manifest-overview.md)

