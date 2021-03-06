---
title: Configure o streaming playReady offline com o Azure Media Services v3
description: Este artigo mostra como configurar a sua conta Azure Media Services para o streaming PlayReady para o Windows 10 offline.
services: media-services
keywords: DASH, DRM, Widevine Offline Mode, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2019
ms.author: willzhan
ms.openlocfilehash: 151aadadb5674f7f144d42b1f9d5115501ed381d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887236"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Offline PlayReady Streaming para Windows 10 com Media Services v3

A Azure Media Services suporta o download/reprodução offline com proteção DRM. Este artigo cobre o suporte offline dos Serviços Azure Media para clientes Windows 10/PlayReady. Pode ler sobre o suporte ao modo offline para dispositivos iOS/FairPlay e Android/Widevine nos seguintes artigos:

- [Offline FairPlay Streaming for iOS](offline-fairplay-for-ios.md) (Transmissão Offline do FairPlay para iOS)
- [Offline Widevine Streaming para Android](offline-widevine-for-android.md)

> [!NOTE]
> A DRM offline só é cobrada por fazer um único pedido de licença quando descarrega o conteúdo. Quaisquer erros não são contabilizados.

## <a name="overview"></a>Descrição geral

Esta secção dá algum pano de fundo na reprodução do modo offline, especialmente por que:

* Em alguns países/regiões, a disponibilidade da Internet e/ou largura de banda ainda é limitada.Os utilizadores podem optar por descarregar primeiro para poderem ver os conteúdos em resolução suficientemente elevada para uma experiência de visualização satisfatória. Neste caso, mais frequentemente, o problema não é a disponibilidade da rede, mas sim a largura de banda limitada da rede. Os fornecedores OTT/OVP estão a pedir suporte ao modo offline.
* Conforme divulgado na conferência de acionistas do Netflix 2016 Q3, o download de conteúdos é uma "funcionalidade solicitada", e "estamos abertos a isso" disse Reed Hastings, CEO da Netflix.
* Alguns fornecedores de conteúdos podem proibir a entrega de licença sem condutor para além da fronteira entre um país e uma região. Se um utilizador precisa de viajar para o estrangeiro e ainda quiser ver conteúdo, é necessário descarregar offline.
 
O desafio que enfrentamos na implementação do modo offline é o seguinte:

* Mp4 é suportado por muitos jogadores, ferramentas codificadoras, mas não há ligação entre o contentor MP4 e a DRM;
* A longo prazo, cFF com CENC é o caminho a seguir. No entanto, hoje em dia, o ecossistema de apoio às ferramentas/jogadores ainda não está presente. Precisamos de uma solução, hoje.
 
A ideia é: o formato de ficheiro de streaming suave[(PIFF)](https://docs.microsoft.com/iis/media/smooth-streaming/protected-interoperable-file-format)com h264/AAC tem uma ligação com o PlayReady (AES-128 CTR). O ficheiro de streaming suave individual .ismv (assumindo que o áudio é muxed em vídeo) é em si um fMP4 e pode ser usado para reprodução. Se um conteúdo de streaming suave passar pela encriptação PlayReady, cada ficheiro .ismv torna-se um MP4 fragmentado protegido playReady. Podemos escolher um ficheiro .ismv com o bitrate preferido e renomeá-lo como .mp4 para download.

Existem duas opções para hospedar o MP4 protegido PlayReady para download progressivo:

* Pode colocar este MP4 no mesmo ativo de serviço de contentor/media e alavancar o ponto final de streaming da Azure Media Services para download progressivo;
* Pode utilizar o localizador SAS para download progressivo diretamente do Azure Storage, contornando os Serviços Azure Media.
 
Pode utilizar dois tipos de entrega de licença PlayReady:

* Serviço de entrega de licençaS PlayReady nos Serviços Azure Media;
* Servidores de licença PlayReady hospedados em qualquer lugar.

Abaixo estão dois conjuntos de ativos de teste, o primeiro que usa a entrega da licença PlayReady na AMS enquanto o segundo que usa o meu servidor de licença PlayReady hospedado num VM Azure:

#1 de ativos:

* URL de descarregamento progressivo:[https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4")
* PlayReady LA_URL (AMS):[https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

#2 de ativos:

* URL de descarregamento progressivo:[https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (nas instalações):[https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

Para testes de reprodução, usamos uma Aplicação Universal Windows no Windows 10. Nas [amostras universais do Windows 10,](https://github.com/Microsoft/Windows-universal-samples)existe uma amostra básica do leitor chamada [Amostra de Streaming Adaptativo](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Tudo o que temos de fazer é adicionar o código para escolhermos o vídeo descarregado e usá-lo como fonte, em vez de adaptar a fonte de streaming. As alterações estão no manipulador de eventos de clique do botão:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![Reprodução do modo offline do PlayReady protegido fMP4](./media/offline-playready-for-windows/offline-playready1.jpg)

Uma vez que o vídeo está sob a proteção PlayReady, a imagem não poderá incluir o vídeo.

Em resumo, alcançámos o modo offline nos Serviços De Mídia Azure:

* A transcodificação de conteúdos e a encriptação PlayReady podem ser feitas em Serviços De Mídia Azure ou outras ferramentas;
* Os conteúdos podem ser hospedados em Azure Media Services ou Azure Storage para download progressivo;
* A entrega da licença PlayReady pode ser da Azure Media Services ou de qualquer outro lugar;
* O conteúdo de streaming liso preparado ainda pode ser utilizado para streaming on-line via DASH ou suave com o PlayReady como DRM.

## <a name="next-steps"></a>Passos seguintes

[Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)
