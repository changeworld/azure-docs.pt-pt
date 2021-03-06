---
title: 'Quickstart: Reconheça o discurso de um microfone, Swift - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer o discurso em Swift sobre o macOS usando o SDK de Discurso
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: cbasoglu
ms.openlocfilehash: 7c5611a142087cff06eefb0277b12ff786074e1a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446837"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Quickstart: Reconheça o discurso em Swift sobre o macOS usando o SDK de Discurso

Os quickstarts também estão disponíveis para [síntese de fala.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-macos.md)

Neste artigo, aprende-se a criar uma aplicação macOS em Swift utilizando o Cognitive Services Speech SDK para transcrever a fala gravada de um microfone para texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, aqui está uma lista de pré-requisitos:

* Uma [chave de subscrição](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço De Fala.
* Uma máquina macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou mais tarde e [CocoaPods instalados.](https://cocoapods.org/)

## <a name="get-the-speech-sdk-for-macos"></a>Obtenha o SDK de Fala para macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Note que este tutorial não funcionará com a versão do SDK antes de 1.6.0.

O SDK de Discurso dos Serviços Cognitivos para macOS é distribuído como um pacote-quadro.
Pode ser usado em projetos Xcode como um https://aka.ms/csspeech/macosbinary [CocoaPod,](https://cocoapods.org/)ou descarregado de e ligado manualmente. Este guia utiliza um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto Xcode

Inicie o Xcode e inicie um novo projeto clicando em **File** > **New** > **Project**.
No diálogo de seleção do modelo, escolha o modelo "App de Cacau".

Nas caixas de diálogo que se seguem, faça as seleções seguintes:

1. Caixa de Diálogo Project Options (Opções do Projeto)
    1. Introduza um nome para a aplicação de início rápido, por exemplo `helloworld`.
    1. Introduza um nome de organização apropriado e um identificador de organização, se já tiver uma conta de desenvolvimento da Apple. Para fins de teste, pode escolher qualquer nome, como `testorg`. Para assinar a aplicação, precisa de um perfil de provisionamento adequado. Consulte o site de desenvolvimento da [Apple](https://developer.apple.com/) para mais detalhes.
    1. Certifique-se de que Swift é escolhido como o idioma para o projeto.
    1. Desative as caixas de verificação para utilizar storyboards e criar uma aplicação baseada em documentos. A simples UI para a aplicação de amostras será criada programáticamente.
    1. Desative todas as caixas de verificação para testes e dados principais.
1. Selecionar o diretório do projeto
    1. Escolha um diretório para colocar o projeto. Isto cria `helloworld` um diretório no diretório escolhido que contém todos os ficheiros para o projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. Detete os direitos de acesso à rede e ao microfone. Clique no nome da aplicação na primeira linha na visão geral à esquerda para chegar à configuração da aplicação e, em seguida, escolha o separador "Capabilities".
    1. Ative a definição "App sandbox" para a aplicação.
    1. Ative as caixas de verificação para acesso "Ligações de saída" e "Microfone".
    ![Definições de caixa de areia](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-sandbox.png)
1. A aplicação também precisa de declarar `Info.plist` a utilização do microfone no ficheiro. Clique no ficheiro na visão geral e adicione a tecla "Privacidade - Descrição do Uso do Microfone", com um valor como "O microfone é necessário para reconhecimento de voz".
    ![Definições em Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-info-plist.png)
1. Feche o projeto Xcode. Utilizará uma instância diferente depois de configurar os CocoaPods.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Coloque um novo ficheiro `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` de `helloworld` cabeçalho com o nome no diretório dentro do projeto Helloworld e colhe o seguinte código nele:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Adicione o `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` caminho relativo ao cabeçalho de ponte para as definições do projeto ![Swift para o alvo helloworld nas propriedades do cabeçalho de *ponte Objetivo-C*](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Substitua o conteúdo do ficheiro `AppDelegate.swift` gerado automaticamente por:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Em `AppDelegate.swift`, substitua a corda `YourSubscriptionKey` com a sua chave de subscrição.
1. Substitua a cadeia de carateres `YourServiceRegion` pela região associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="install-the-sdk-as-a-cocoapod"></a>Instale o SDK como um CocoaPod

1. Instale o gestor de dependência do CocoaPod conforme descrito nas suas instruções de [instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue para o diretório`helloworld`da sua aplicação de amostras ( ). Coloque um ficheiro de `Podfile` texto com o nome e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/Podfile)]
1. Navegue `helloworld` para o diretório num `pod install`terminal e dirija o comando. Isto gerará `helloworld.xcworkspace` um espaço de trabalho Xcode contendo tanto a aplicação da amostra como o SDK de Fala como uma dependência. Este espaço de trabalho será utilizado no seguinte.

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Abra `helloworld.xcworkspace` o espaço de trabalho em Xcode.
1. Tornar visível a saída de depuração **(Ver** > **debug Area** > **Activate Console**).
1. Construa e execute o código de exemplo selecionando o **Produto** > **Executar** a partir do menu ou clicando no botão **Reproduzir.**
1. Depois de clicar no botão "Reconhecer" na aplicação e dizer algumas palavras, deve ver o texto que falou na parte inferior da janela da aplicação.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as nossas amostras no GitHub](https://aka.ms/csspeech/samples)

