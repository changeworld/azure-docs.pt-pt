---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: travisw
ms.openlocfilehash: 8b187e058299f8aa8b762231c0ed1e708e5ad9d1
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659441"
---
1. Lance o Android Studio e selecione **Iniciar um novo projeto Android Studio** na janela **Welcome.**

    ![Captura de ecrã da janela de boas-vindas do Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. O **Assistente de Escolha do seu projeto** aparece. Selecione **Telefone e Tablet** e Atividade **Vazia** na caixa de seleção de atividades. Selecione **Next**.

   ![Screenshot de Escolha o seu assistente de projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. No ecrã de **configurar o seu projeto,** introduza *quickstart* como **Nome** e introduza *samples.speech.cognitiveservices.microsoft.com* como **nome de pacote**. Em seguida, selecione um diretório de projeto. Para **o nível mínimo de API**, selecione **API 23: Android 6.0 (Marshmallow)**. Deixe todas as outras caixas de verificação limpas e selecione **Terminar**.

   ![Screenshot de Configure o seu assistente de projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

O Android Studio demora algum tempo a preparar o seu novo projeto Android. Em seguida, configure o projeto para conhecer o SDK de Serviços Cognitivos Azure e para usar Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK dos Serviços Cognitivos é de 1.11.0.

O Speech SDK para Android está embalado como [AAR (Biblioteca Android),](https://developer.android.com/studio/projects/android-library)que inclui as bibliotecas necessárias e as permissões necessárias para android.
Está hospedado num repositório Maven em\/https: /csspeechstorage.blob.core.windows.net/maven/.

Configure o projeto para utilizar o SDK de Voz. Abra a janela **Project Structure** selecionando a Estrutura do**Projeto** **de Arquivo** > da barra de menus Android Studio. Na janela Estrutura do **Projeto,** efaça as seguintes alterações:

1. Na lista no lado esquerdo da janela, selecione **Project** (Projeto). Editar as definições de **Repositório da Biblioteca Predefinida,** anexando uma víreme e o nosso\/URL repositório Maven fechado em aspas únicas: 'https: /csspeechstorage.blob.core.windows.net/maven/'

   ![Captura de ecrã da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. No mesmo ecrã, no lado esquerdo, selecione **a aplicação**. Em seguida, selecione o separador **Dependencies** (Dependências) na parte superior da janela. Selecione o**+** sinal verde plus (), e selecione **a dependência** da Biblioteca a partir do menu suspenso.

   ![Screenshot da dependência da Biblioteca](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Na janela que aparece, introduza o nome e a versão do SDK de Discurso para *Android.microsoft.cognitiveservices.speech:client-sdk:1.11.0*. Em seguida, selecione **OK**.
   O SDK da fala deve ser adicionado à lista de dependências agora, como mostra:

   ![Screenshot do Speech SDK na lista de dependências](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Selecione o separador **Propriedades.** Para **a compatibilidade de origem** e **compatibilidade do alvo,** selecione **1.9**.

   ![Screenshot da Compatibilidade de Fonte e Compatibilidade do Alvo](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selecione **OK** para fechar a janela Estrutura do **Projeto** e aplique as suas alterações no projeto.
