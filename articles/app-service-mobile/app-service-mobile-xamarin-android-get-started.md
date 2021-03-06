---
title: Começar com aplicativos Xamarin.Android
description: Siga este tutorial para começar a usar aplicativos Azure Mobile para o desenvolvimento do Android Xamarin.
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: b42205436c88f9075423bfcaf9e5a9fd931ee4f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461373"
---
# <a name="create-a-xamarinandroid-app"></a>Criar uma Aplicação Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Xamarin.Android. Para obter mais informações, consulte [O que são Mobile Apps](app-service-mobile-value-prop.md).

Abaixo é exibida uma captura de ecrã da aplicação concluída:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Mobile Apps para aplicações Xamarin.Android.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Uma conta ativa do Azure. Se não tiver uma conta, inscreva-se para uma versão de avaliação do Azure e obtenha até 10 Aplicações Móveis gratuitas. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio com Xamarin. Para obter instruções, consulte [Configuração e instalação do Visual Studio e Xamarin](/visualstudio/cross-platform/setup-and-install).

## <a name="create-an-azure-mobile-app-backend"></a>Criar um back-end da Aplicação Móvel do Azure
Siga estes passos para criar um back-end da Aplicação Móvel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Acabou de aprovisionar um back-end da Aplicação Móvel do Azure que pode ser utilizado pelas suas aplicações cliente móveis. Em seguida, transfira um projeto de servidor para um back-end simples de uma “lista de tarefas” e publique-o no Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma ligação à base de dados e configurar o projeto cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Executar a aplicação Xamarin.Android
1. Abra o projeto Xamarin.Android.

2. Vá ao [portal Azure](https://portal.azure.com/) e navegue para a aplicação móvel que criou. Na `Overview` lâmina, procure o URL que é o ponto final público da sua aplicação móvel. Exemplo - o nome de site para o meu https://test123.azurewebsites.netnome de aplicação "test123" será .

3. Abra o `ToDoActivity.cs` ficheiro nesta pasta - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs. O nome `ZUMOAPPNAME`da aplicação é .

4. Na `ToDoActivity` aula, `ZUMOAPPURL` substitua a variável por ponto final público acima.

    `const string applicationURL = @"ZUMOAPPURL";`

    torna-se
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Pressione a tecla F5 para implementar e executar a aplicação.

6. Na aplicação, digite texto significativo, como *completar o tutorial* e, em seguida, clique no botão **Adicionar.**

    ![][10]

    Os dados do pedido são inseridos na tabela TodoItem. Os itens armazenados na tabela são devolvidos pelo back-end da aplicação móvel e os dados são apresentados na lista.

   > [!NOTE]
   > Pode rever o código que acede ao seu back-end da aplicação móvel para consultar e inserir dados (presente no ficheiro ToDoActivity.cs c#).
   
## <a name="troubleshooting"></a>Resolução de problemas
Se tiver algum problema ao criar a solução, execute o gestor de pacotes NuGet e atualize os pacotes de suporte `Xamarin.Android`. Os projetos do início rápido nem sempre incluem as versões mais recentes.

Note que todos os pacotes de suporte referenciados no projeto têm de ter a mesma versão. O [pacote NuGet de Aplicações Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) tem dependência `Xamarin.Android.Support.CustomTabs` para a plataforma Android. Portanto, se o seu projeto utilizar os pacotes de suporte mais recentes, precisará de instalar diretamente este pacote com a versão necessária para evitar conflitos.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
