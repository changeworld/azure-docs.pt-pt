---
title: 'Quickstart: Configurar o início para uma aplicação web ASP.NET'
titleSuffix: Azure AD B2C
description: Neste Quickstart, execute uma amostra ASP.NET aplicação web que utiliza o Azure Ative Directory B2C para fornecer o sessão de conta.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 197155650bf7b8d113f96dbcfd6d9e2b58924b7f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78187207"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Quickstart: Instale o sinal para uma aplicação ASP.NET utilizando o Diretório Ativo Azure B2C

O Azure Ative Directory B2C (Azure AD B2C) fornece gestão de identidade na nuvem para manter a sua aplicação, negócio e clientes protegidos. O Azure AD B2C permite às aplicações fazer a autenticação em contas de redes sociais e contas empresariais, através de protocolos padrão abertos. Neste início rápido, vai utilizar uma aplicação ASP.NET para iniciar sessão com um fornecedor de identidade de redes sociais e chamar uma API Web protegida pelo Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a **carga de trabalho de ASP.NET e desenvolvimento web.**
- Uma conta social do Facebook, Google ou Microsoft.
- [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clone a aplicação Web de exemplo a partir do GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    Há dois projetos na solução de amostra:

    - **TaskWebApp** - uma aplicação Web que cria e edita uma lista de tarefas. A aplicação web utiliza o fluxo de utilizador de **inscrição ou de entrada** para se inscrever ou inscrever nos utilizadores.
    - **TaskService** - uma API Web que suporta a funcionalidade de criação, leitura, atualização e eliminação da lista de tarefas. A API Web é protegida pelo Azure AD B2C e chamada pela aplicação Web.

## <a name="run-the-application-in-visual-studio"></a>Executar a aplicação no Visual Studio

1. Na pasta de projeto da aplicação de exemplo, abra a solução **B2C-WebAPI-DotNet.sln** no Visual Studio.
2. Para este início rápido, vai executar ambos os projetos **TaskWebApp** e **TaskService** em simultâneo. Clique com o botão direito do rato na solução **B2C-WebAPI-DotNet** no Explorador de Soluções e, em seguida, selecione **Definir Projetos de Arranque**.
3. Selecione **Vários projetos de arranque** e altere a **Ação** de ambos os projetos para **Iniciar**.
4. Clique em **OK**.
5. Prima **F5** para depurar as duas aplicações. Cada aplicação abre no seu próprio separador do browser:

    - `https://localhost:44316/` - aplicação Web ASP.NET. No início rápido, interage diretamente com esta aplicação.
    - `https://localhost:44332/` - a API Web que é chamada pela aplicação Web ASP.NET.

## <a name="sign-in-using-your-account"></a>Iniciar sessão com a sua conta

1. Clique em **Inscrever-se / Iniciar sessão** na aplicação Web ASP.NET para iniciar o fluxo de trabalho.

    ![Experimente ASP.NET aplicação web no navegador com link de inscrição/sinal destacado](./media/quickstart-web-app-dotnet/web-app-sign-in.png)

    O exemplo suporta várias opções de inscrição, incluindo através de um fornecedor de identidade de redes sociais ou criando uma conta local com um endereço de e-mail. Para este arranque rápido, utilize uma conta de fornecedor de identidade social do Facebook, Google ou Microsoft.

2. O Azure AD B2C apresenta uma página de entrada para uma empresa fictícia chamada Fabrikam para a aplicação web da amostra. Para inscrever-se através de um fornecedor de identidade de redes sociais, clique no botão do fornecedor de identidade que pretende utilizar.

    ![Inscreva-se ou inscreva-se na página mostrando botões de fornecedor de identidade](./media/quickstart-web-app-dotnet/sign-in-or-sign-up-web.png)

    Autentica (iniciar sessão) usando as credenciais da sua conta social e autorizar o pedido de leitura de informação na sua conta social. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de rede social, como o nome e a localidade.

3. Conclua o processo de início de sessão para o fornecedor de identidade.

## <a name="edit-your-profile"></a>Editar o seu perfil

O Azure Active Directory B2C fornece funcionalidades para permitir que os utilizadores atualizem os seus perfis. A aplicação web da amostra utiliza um fluxo de utilizador de perfil de edição Azure AD B2C para o fluxo de trabalho.

1. Na barra de menus da aplicação, clique no nome do perfil e selecione **Editar perfil** para editar o perfil que criou.

    ![App web de amostra no navegador com link de perfil Editar destacado](./media/quickstart-web-app-dotnet/edit-profile-web.png)

2. Altere o seu **Nome a apresentar** ou **Cidade** e, em seguida, clique em **Continuar** para atualizar o seu perfil.

    A alteração é apresentada na parte superior direita da home page da aplicação Web.

## <a name="access-a-protected-api-resource"></a>Aceder a um recurso protegido da API

1. Clique em **Lista de Tarefas** para introduzir e modificar os itens da lista de tarefas.

2. Introduza texto na caixa de texto **Novo Item**. Clique em **Adicionar** para chamar a API Web protegida pelo Azure AD B2C, que adiciona um item da lista de tarefas.

    ![Sample web app no navegador com Adicionar um item de lista a fazer](./media/quickstart-web-app-dotnet/add-todo-item-web.png)

    A aplicação Web ASP.NET inclui um token de acesso do Azure AD no pedido para o recurso da API Web protegido para executar operações nos itens da lista de tarefas do utilizador.

Utilizou com êxito a conta de utilizador do Azure AD B2C para fazer uma chamada autorizada para uma API Web protegida pelo Azure AD B2C.

## <a name="clean-up-resources"></a>Limpar recursos

Pode utilizar o inquilino do Azure AD B2C se planeia experimentar outros inícios rápidos ou tutoriais do Azure AD B2C. Quando já não for necessário, pode [eliminar o inquilino do Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, utilizou uma amostra ASP.NET aplicação para:

* Inscreva-se com uma página de login personalizada
* Inscreva-se com um fornecedor de identidade social
* Criar uma conta Azure AD B2C
* Ligue para uma Web API protegida por Azure AD B2C

Comece a criar o seu próprio inquilino do Azure AD B2C.

> [!div class="nextstepaction"]
> [Criar um inquilino do Azure Active Directory B2C no portal do Azure](tutorial-create-tenant.md)
