---
title: Cenários e disponibilidade das funcionalidades dos Serviços de Multimédia do Microsoft Azure em datacenters | Microsoft Docs
description: Este tópico apresenta uma descrição geral dos cenários e da disponibilidade dos serviços e funcionalidades dos Serviços de Multimédia do Microsoft Azure em datacenters.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: fa0cf5d698bc2186928e0db19be173ec725485e8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985937"
---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>Cenários e disponibilidade das funcionalidades dos Serviços de Multimédia em datacenters

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Os Serviços de Multimédia do Microsoft Azure (AMS) permitem-lhe carregar, armazenar, codificar e empacotar de forma segura conteúdos de vídeo ou áudio, para a entrega de transmissões em fluxo, tanto a pedido, como em direto, para vários clientes (por exemplo, TV, PC e dispositivos móveis).

O AMS funciona em vários datacenters em todo o mundo. Estes datacenters são agrupados por regiões geográficas, dando-lhe a flexibilidade de escolher onde pretende criar as suas aplicações. Pode rever a [lista de regiões e as respetivas localizações](https://azure.microsoft.com/regions/). 

Este tópico mostra cenários comuns para entregar o seu conteúdo [ao vivo](#live_scenarios) ou a pedido. Também mostra detalhes sobre a disponibilidade dos serviços e funcionalidades de multimédia nos datacenters.

## <a name="overview"></a>Descrição geral

### <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar os Media Services do Azure, deve ter o seguinte:

* Uma conta do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com).
* Uma conta de Media Services do Azure Para obter mais informações, consulte [Criar Conta](media-services-portal-create-account.md).
* O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

    Quando a sua conta AMS é criada, um ponto final de streaming **predefinido** é adicionado à sua conta no estado **Deter.** Para começar a transmitir o seu conteúdo em fluxo e tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo tem de estar no estado **Em execução**.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Objetos utilizados normalmente no desenvolvimento com o modelo OData do AMS

A imagem seguinte mostra alguns objetos mais frequentemente utilizados ao desenvolver contra o modelo OData dos Serviços de Multimédia.

Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

Pode ver o modelo de todo [aqui](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Proteger conteúdos em armazenamento e distribuir multimédia de transmissão em fluxo em segurança (não encriptadas)

![Fluxo de trabalho do VoD](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Carregue um ficheiro multimédia de alta qualidade para um elemento.

    É recomendado aplicar uma opção de encriptação de armazenamento ao seu elemento para proteger os seus conteúdos durante o carregamento e enquanto estes permanecem inativos em armazenamento.
2. Codifique para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável.

    É recomendado aplicar uma opção de encriptação de armazenamento ao elemento de saída para proteger os seus conteúdos enquanto estes permanecem.
3. Configure a política de entrega de elementos (utilizada pelo empacotamento dinâmico).

    Se o seu elemento estiver armazenamento encriptado, **deve** configurar a política de distribuição do elemento.
4. Publique o elemento através da criação de um localizador OnDemand.
5. Transmita os conteúdos publicados.

Para obter informações sobre a disponibilidade em datacenters, veja a secção [Disponibilidade](#availability).

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteger os conteúdos em armazenamento, distribuir multimédia de transmissão em fluxo encriptada de forma dinâmica

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Carregue um ficheiro multimédia de alta qualidade para um elemento. Aplique a opção de encriptação de armazenamento ao elemento.
2. Codifique para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável. Aplique a opção de encriptação de armazenamento ao elemento de saída.
3. Crie uma chave de conteúdo de encriptação para o elemento que pretende que seja encriptado dinamicamente durante a reprodução.
4. Configure a política de autorização da chave de conteúdo.
5. Configure a política de entrega de elementos (utilizada pelo empacotamento dinâmico e a encriptação dinâmica).
6. Publique o elemento através da criação de um localizador OnDemand.
7. Transmita os conteúdos publicados.

Para obter informações sobre a disponibilidade em datacenters, veja a secção [Disponibilidade](#availability).

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Utilize a Análise de Multimédia para derivar conhecimentos acionáveis dos seus vídeos

A Análise de Multimédia é um conjunto de componentes de voz e visão que facilitam a derivação de conhecimentos acionáveis das organizações e empresas dos seus respetivos ficheiros de vídeo. Para obter mais informações, consulte [Descrição Geral da Análise dos Media Services do Azure](media-services-analytics-overview.md).

1. Carregue um ficheiro multimédia de alta qualidade para um elemento.
2. Processe os seus vídeos com um dos serviços de Análise de Multimédia descritos na secção [Media Analytics overview](media-services-analytics-overview.md) (Descrição geral da Análise de Multimédia).
3. Os processadores de multimédia de Análise de Multimédia produzem ficheiros MP4 ou ficheiros JSON. Se um processador de multimédia produzir um ficheiro MP4, pode transferir progressivamente o ficheiro. Se um processador de multimédia produzir um ficheiro JSON, pode transferir o ficheiro a partir do Blob Storage do Azure.

Para obter informações sobre a disponibilidade em datacenters, veja a secção [Disponibilidade](#availability).

## <a name="deliver-progressive-download"></a>Fornecer transferência progressiva

1. Carregue um ficheiro multimédia de alta qualidade para um elemento.
2. Codifique para um único ficheiro MP4.
3. Publique o elemento através da criação de um localizador OnDemand ou SAS.

    Se utilizar o localizador SAS, o conteúdo é transferido a partir do Blob Storage do Azure. Neste caso, não precisa de ter pontos finais de transmissão em fluxo no estado iniciado.
4. Transferir progressivamente os conteúdos.

## <a name="delivering-live-streaming-events"></a><a id="live_scenarios"></a>Entregar eventos de transmissão em fluxo 

1. Ingira conteúdos em direto com vários protocolos de transmissão em fluxo em direto (por exemplo, RTMP ou Smooth Streaming).
2. (opcionalmente) Codifique a transmissão numa transmissão em fluxo de velocidade de transmissão adaptável.
3. Pré-visualize a sua transmissão em fluxo em direto.
4. Distribua os conteúdos através de protocolos de transmissão em fluxo comuns (por exemplo, MPEG DASH, Smooth, HLS) diretamente para os seus clientes ou para uma Rede de Entrega de Conteúdos (CDN), para uma maior distribuição.

    -ou-

    Grave e armazene os conteúdos ingeridos, de forma a transmiti-los em fluxo posteriormente (Vídeo a Pedido).

Ao realizar a transmissão em fluxo em direto, pode escolher uma das rotas seguintes:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhar com canais que recebem transmissões em fluxo em direto com velocidade de transmissão múltipla a partir de codificadores no local (pass-through)

O diagrama seguinte mostra as principais partes da plataforma de AMS envolvidas no fluxo de trabalho de **pass-through**.

![Fluxo de trabalho em direto](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Para obter mais informações, consulte [Trabalhar com Canais que recebem transmissões em Fluxo em Direto de Múltipla Velocidade de Transmissão a partir de Codificadores no Local](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhar com canais ativados para realizar live encoding com os Serviços de Multimédia do Azure

O diagrama seguinte mostra as partes principais da plataforma do AMS envolvidas no fluxo de trabalho de Transmissão em Fluxo em Direto onde um Canal é ativado para realizar live encoding comMedia Services.

![Fluxo de trabalho em direto](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Para obter informações sobre a disponibilidade em datacenters, veja a secção [Disponibilidade](#availability).

## <a name="consuming-content"></a>Consumo de conteúdos

Os Media Services do Azure fornecem as ferramentas necessárias para criar aplicações avançadas e dinâmicas de leitor de cliente para a maioria das plataformas, incluindo: Dispositivos iOS dispositivos, Dispositivos Android, Windows, Windows Phone, Xbox e Caixas descodificadoras. 

## <a name="enabling-azure-cdn"></a>Ativar a CDN do Azure

Os Media Services suportam a integração com a CDN do Azure. Para obter mais informações sobre como ativar a CDN do Azure, consulte [Como Gerir Transmissão em Fluxo de Pontos Finais numa Conta de Media Services](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a><a id="scaling"></a>Escalar uma conta dos Media Services

Os clientes do AMS podem dimensionar pontos finais de transmissões, o processamento de multimédia e o armazenamento nas respetivas contas do AMS.

* Os clientes dos Serviços de Multimédia podem escolher um ponto final de transmissão em fluxo **Standard** ou **Premium**. O ponto final de transmissão em fluxo **Standard** é ideal para a maioria das cargas de trabalho de transmissão em fluxo. Inclui as mesmas funcionalidades dos pontos finais **Premium** e dimensiona a largura de banda de saída automaticamente. 

    Os pontos finais de transmissão em fluxo **Premium** são adequadas para cargas de trabalho avançadas, ao fornecer uma capacidade de largura de banda dimensionável e dedicada. Por predefinição, os clientes que têm os pontos finais de transmissão em fluxo **Premium** recebem uma unidade de transmissão em fluxo (SU). O ponto final de transmissão em fluxo pode ser dimensionado mediante a adição de mais SUs. Cada SU fornece capacidade de largura de banda adicional à aplicação. Para obter mais informações sobre o dimensionamento de pontos finais de transmissões em fluxo **Premium** veja o tópico [Scaling streaming endpoints](media-services-portal-scale-streaming-endpoints.md) (Dimensionar pontos finais de transmissões em fluxo).

* As contas dos Serviços de Multimédia estão associadas a um Tipo de Unidade Reservada, que determina a velocidade do processamento das suas tarefas de processamento de mulitmédia. Pode escolher entre os seguintes tipos de unidades reservados: **S1,** **S2,** ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**.

    Além de especificar o tipo de unidade reservado, pode especificar para fornecer a sua conta com **Unidades Reservadas** (RUs). O número de RUs aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta.

    >[!NOTE]
    >As RUs destinam-se a paralelizar todos os processamentos de multimédia, incluindo trabalhos de indexação com o Azure Media Indexer. No entanto, ao contrário da codificação, os trabalhos de indexação não são processados mais depressa com unidades reservadas mais rápidas.

    Para mais informações consulte, Dimensione o [processamento dos meios de comunicação.](media-services-portal-scale-media-processing.md)
* Também pode escalar a sua conta dos Media Services adicionando-lhe contas deo Storage. Cada conta do Storage está limitada a 500 TB. Para expandir o limite predefinido do seu armazenamento, pode optar por ligar várias contas do Storage numa única conta de Media Services. Para obter mais informações, veja [Manage storage accounts](meda-services-managing-multiple-storage-accounts.md) (Gerir contas de armazenamento).

## <a name="availability-of-media-services-features-across-datacenters"></a><a id="availability"></a> Disponibilidade das funcionalidades dos Serviços de Multimédia em datacenters

Esta secção mostra detalhes sobre a disponibilidade das funcionalidades dos Serviços de Multimédia em datacenters.

### <a name="ams-accounts"></a>Contas do AMS

#### <a name="availability"></a>Disponibilidade

Utilize [produtos Azure por Região](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) para determinar se os Serviços de Media estão disponíveis num datacenter específico.

### <a name="streaming-endpoints"></a>Pontos finais de transmissões em fluxo 

Os clientes dos Serviços de Multimédia podem escolher um ponto final de transmissão em fluxo **Standard** ou **Premium**. Para obter mais informações, veja a secção [Dimensionamento](#scaling).

#### <a name="availability"></a>Disponibilidade

|Nome|Estado|Datacenters
|---|---|---|
|Standard|GA|Todos|
|Premium|GA|Todos|

### <a name="live-encoding"></a>Live Encoding

#### <a name="availability"></a>Disponibilidade

Disponível em todos os dataenters, exceto Alemanha, Sul do Brasil, Oeste da Índia, Sul da Índia e Índia Central. 

### <a name="encoding-media-processors"></a>Processadores de multimédia de codificação

O AMS oferece dois codificadores a pedido, o **Media Encoder Standard** e o **Media Encoder Premium Workflow**. Para obter mais informações, veja [Overview and comparison of Azure on-demand media encoders](media-services-encode-asset.md) (Descrição geral e comparação dos codificadores de multimédia a pedido). 

#### <a name="availability"></a>Disponibilidade

|Nome do processador de mulitmédia|Estado|Datacenters
|---|---|---|
|Media Encoder Standard|GA|Todos|
|Fluxo de Trabalho Premium de Codificador de Multimédia|GA|Tudo, exceto a China|

### <a name="analytics-media-processors"></a>Processadores de multimédia de análise

A Análise de Multimédia é um conjunto de componentes de voz e visão que facilitam a derivação de conhecimentos acionáveis por parte das organizações e empresas dos respetivos ficheiros de vídeo. Para obter mais informações, consulte [Descrição Geral da Análise dos Media Services do Azure](media-services-analytics-overview.md).

> [!NOTE]
> Alguns processadores de meios de comunicação de análise serão retirados. Para as datas de aposentadoria, consulte o tema dos [componentes do legado.](legacy-components.md)

#### <a name="availability"></a>Disponibilidade

|Nome do processador de mulitmédia|Estado|Datacenters
|---|---|---|
|Azure Media Face Detector|Pré-visualização|Todos|
|Azure Media Indexer|GA|Todos|
|Azure Media Motion Detector|Pré-visualização|Todos|
|Azure Media OCR|Pré-visualização|Todos|
|Azure Media Redactor|GA|Todos|
|Azure Media Video Thumbnails|Pré-visualização|Todos|

### <a name="protection"></a>Proteção

Os Serviços de Multimédia do Microsoft Azure permite-lhe proteger os seus elementos multimédia desde o momento em que saem do seu computador para armazenamento, processamento e entrega. Para obter mais informações, veja [Protecting AMS content](media-services-content-protection-overview.md) (Proteger conteúdos do AMS).

#### <a name="availability"></a>Disponibilidade

|Encriptação|Estado|Datacenters|
|---|---|---| 
|Armazenamento|GA|Todos|
|Chaves AES-128|GA|Todos|
|Fairplay|GA|Todos|
|PlayReady|GA|Todos|
|Widevine|GA|Todos, exceto Alemanha, Federal Government e China.

### <a name="reserved-units-rus"></a>Unidades reservadas (RUs)

O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. 

Para obter mais informações, veja a secção [Dimensionamento](#scaling).

#### <a name="availability"></a>Disponibilidade

Disponível em todos os datacenters.

### <a name="reserved-unit-ru-type"></a>Tipo de unidade reservada (RU)

As contas dos Serviços de Multimédia estão associadas a um tipo de Unidade Reservada, que determina a velocidade do processamento das suas tarefas de processamento de multimédia. Pode escolher de entre os tipos de unidades reservadas S1, S2 ou S3.

Para obter mais informações, veja a secção [Dimensionamento](#scaling).

#### <a name="availability"></a>Disponibilidade

|Nome do tipo do RU|Estado|Datacenters
|---|---|---|
|S1|GA|Todos|
|S2|GA|Todos, exceto Sul do Brasil e Oeste da Índia|
|S3|GA|Todos, exceto Oeste da Índia|

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

