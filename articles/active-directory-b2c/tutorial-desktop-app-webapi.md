---
title: 'Tutorial: Conceder acesso a uma API web Node.js a partir de uma aplicação de ambiente de trabalho'
description: Tutorial sobre como usar o Ative Directory B2C para proteger uma API web Node.js e chamá-lo de uma aplicação de ambiente de trabalho .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 59670cda68f54e4c0b20b361f0688e6766acba61
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183393"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API Web Node.js a partir de uma aplicação de ambiente de trabalho com o Azure Active Directory B2C

Este tutorial mostra-lhe como chamar um API web Node.js protegido pelo Azure Ative Directory B2C (Azure AD B2C) de uma aplicação de ambiente de trabalho da Windows Presentation Foundation (WPF), também protegida pelo Azure AD B2C.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar os âmbitos para uma API web
> * Conceder permissões à Web API
> * Atualizar a amostra para utilizar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos e pré-requisitos no [Tutorial: Autenticar os utilizadores num cliente nativo de ambiente](tutorial-desktop-app.md)de trabalho.

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurar os âmbitos

Os âmbitos proporcionam uma forma de reger o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. Neste tutorial, vai definir as permissões de leitura e escrita para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registe o valor em `demo.read` **SCOPES** para que o âmbito seja utilizado num passo posterior quando configurar a aplicação de ambiente de trabalho. O valor total do `https://contosob2c.onmicrosoft.com/api/demo.read`âmbito é semelhante a .

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida de uma aplicação de cliente nativo, você precisa conceder as permissões de pedido de cliente nativo registrado para a Web API que registrou em Azure AD B2C.

No tutorial pré-requisito, registou uma aplicação de cliente nativo chamada *nativeapp1*. Os seguintes passos configuram o registo de aplicação nativa com os âmbitos da API que expôs para *webapi1* na secção anterior. Isto permite que a aplicação de ambiente de trabalho obtenha um sinal de acesso do Azure AD B2C que a API web pode usar para verificar e fornecer acesso acessível aos seus recursos. Configura e executa tanto a aplicação de ambiente de trabalho como as amostras de código API web mais tarde no tutorial.

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Selecione **Aplicações**e, em seguida, selecione *nativeapp1*.
1. Selecione **o acesso API,** e, em seguida, selecione **Adicionar**.
1. No dropdown **Select API,** selecione *webapi1*.
1. No **dropdown Select Scopes,** selecione os âmbitos que definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **OK**.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Selecione registos de **aplicativos (Pré-visualização)** e, em seguida, selecione a aplicação do cliente nativo que deve ter acesso à API. Por exemplo, *nativeapp1*.
1. Em **Gerir,** selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicionar uma permissão**.
1. Selecione o separador **My APIs.**
1. Selecione a API a que a aplicação do cliente nativo deve ter acesso. Por exemplo, *webapi1*.
1. Sob **Permissão,** expanda a **demonstração**e, em seguida, selecione os âmbitos que definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. **Selecione Adicionar permissões**. Como foi dirigido, aguarde alguns minutos antes de passar para o próximo passo.
1. Selecione **Grant administrador consentimento para (o nome do seu inquilino)**.
1. Selecione a sua conta de administrador atualmente assinada ou inscreva-se com uma conta no seu inquilino Azure AD B2C que tenha sido atribuída pelo menos a função de administrador de *aplicação Cloud.*
1. Selecione **Aceitar**.
1. Selecione **Refresh**, e, em seguida, verifique se "Granted for ..." aparece em **Status** para ambos os âmbitos. Pode levar alguns minutos para as permissões se propagarem.

* * *

Para utilizar a aplicação de ambiente de trabalho WPF, os utilizadores autenticam-se com o Azure AD B2C. A aplicação de ambiente de trabalho obtém uma concessão de autorização do Azure AD B2C para aceder à API Web protegida.

## <a name="configure-the-samples"></a>Configure as amostras

Agora que a Web API está registada e tem âmbitos e permissões configurados, configura a aplicação de ambiente de trabalho e as amostras de API web para utilizar o seu inquilino Azure AD B2C.

### <a name="update-the-desktop-application"></a>Atualizar a aplicação de ambiente de trabalho

Num pré-requisito para este artigo, modificou uma [aplicação de ambiente](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) de trabalho WPF para permitir o início de sessão com um fluxo de utilizador no seu inquilino Azure AD B2C. Nesta secção, atualiza essa mesma aplicação para fazer referência à Web API que registou anteriormente, *webapi1*.

1. Abra a solução **active-directy-b2c-wpf** ()`active-directory-b2c-wpf.sln`no Estúdio Visual.
1. No projeto **active-directy-b2c-wpf,** abra o ficheiro *App.xaml.cs* e encontre as seguintes definições variáveis.
    1. Substitua o `ApiScopes` valor da variável pelo valor que registou anteriormente quando definiu o âmbito **de demo.read.**
    1. Substitua o `ApiEndpoint` valor da variável pelo **URI redirecionamento** que gravou anteriormente quando registou a Web API (por exemplo, *webapi1*) no seu inquilino.

    Segue-se um exemplo:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Obtenha e atualize a amostra de API nonó.js

Em seguida, obtenha a amostra de código API web Node.js do GitHub e configure-a para usar a Web API que registou no seu inquilino Azure AD B2C.

[Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clone a aplicação Web de exemplo a partir do GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

O exemplo de API Web Node.js utiliza a biblioteca Passport.js para ativar o Azure AD B2C no sentido de proteger as chamadas à API.

1. Abra o ficheiro `index.js`.
1. Atualize estas definições variáveis com os seguintes valores. Alterar `<web-API-application-ID>` para a Identificação de **Aplicação (cliente)** da Web API que registou anteriormente *(webapi1*). Mude `<your-b2c-tenant>` para o nome do seu inquilino Azure AD B2C.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Uma vez que está a executar a API localmente, `/` atualize o caminho na rota `/hello`para o método GET para, em vez da localização da aplicação de demonstração de:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Executar os exemplos

### <a name="run-the-nodejs-web-api"></a>Executar a API web Node.js

1. Inicie uma linha de comandos Node.js.
2. Mude para o diretório que contém o exemplo de Node.js. Por exemplo, `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Execute os seguintes comandos:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Executar a aplicação de ambiente de trabalho

1. Abra a solução **active-directy-b2c-wpf** no Visual Studio.
2. Prima **F5** para executar a aplicação de ambiente de trabalho.
3. Inicie sessão com o endereço de e-mail e a palavra-passe utilizados no tutorial [Autenticar utilizadores com o Azure Active Directory B2C numa aplicação de ambiente de trabalho](tutorial-desktop-app.md).
4. Selecione o botão **Call API.**

A aplicação de ambiente de trabalho faz um pedido à API web de execução local, e após verificação de um token de acesso válido, mostra o nome de exibição do utilizador inscrito.

![Nome de exibição mostrado no painel superior da aplicação de ambiente de trabalho WPF](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

A sua aplicação de ambiente de trabalho, protegida por Azure AD B2C, está a chamar a API web de execução local que também está protegida pelo Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar os âmbitos para uma API web
> * Conceder permissões à Web API
> * Atualizar a amostra para utilizar a aplicação

> [!div class="nextstepaction"]
> [Tutorial: Adicione fornecedores de identidade às suas aplicações no Diretório Ativo Azure B2C](tutorial-add-identity-providers.md)
