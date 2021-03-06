---
title: 'Tutorial: Integração do Diretório Ativo Azure com keylight LockPath [ LockPath Keylight ] Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 148c2c46a911088d01ab83fe2d16e8ca81d272ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098781"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Tutorial: Integração de Diretório Ativo Azure com keylight LockPath

Neste tutorial, aprende-se a integrar o LockPath Keylight com o Azure Ative Directory (Azure AD).
Integrar o Keylight LockPath com o Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao LockPath Keylight.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no LockPath Keylight (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Keylight LockPath, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada por sinal de teclado LockPath

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* LockPath Keylight suporta **SP** iniciado SSO
* LockPath Keylight suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Adicionar luz de chave LockPath da galeria

Para configurar a integração do LockPath Keylight em Azure AD, precisa de adicionar o LockPath Keylight da galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o Keylight LockPath da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o **teclado LockPath,** selecione **o teclado LockPath** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

    ![Luz-chave LockPath na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com o LockPath Keylight com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no LockPath Keylight.

Para configurar e testar o único sinal de Acesso Azure AD com o LockPath Keylight, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. Configure o sinal único do **[lockPath keylight](#configure-lockpath-keylight-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create LockPath Keylight user](#create-lockpath-keylight-test-user)** - para ter uma contrapartida de Britta Simon no LockPath Keylight que está ligado à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o LockPath Keylight, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **LockPath Keylight,** selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![LockPath Keylight Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<company name>.keylightgrc.com/`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<company name>.keylightgrc.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identifier e URL de Resposta real. Contacte a equipa de suporte do [Cliente LockPath Keylight](https://www.lockpath.com/contact/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

6. Na secção **'Luz de chave LockPath',** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-lockpath-keylight-single-sign-on"></a>Configure LockPath Keylight Single Sign-On

1. Para ativar o SSO no teclado LockPath, execute os seguintes passos:

    a. Inscreva-se na sua conta LockPath Keylight como administrador.

    b. No menu em cima, clique em **Pessoa**, e selecione **Keylight Setup**.

    ![Configurar um único sinal](./media/keylight-tutorial/401.png)

    c. Na vista da árvore à esquerda, clique em **SAML**.

    ![Configurar um único sinal](./media/keylight-tutorial/402.png)

    d. No diálogo de **definições SAML,** clique em **Editar**.

    ![Configurar um único sinal](./media/keylight-tutorial/404.png)

1. Na página de diálogo **de definições SAML de edição,** execute os seguintes passos:

    ![Configurar um único sinal](./media/keylight-tutorial/405.png)

    a. Deteto a **autenticação SAML** para **Ativa**.

    b. Na caixa de texto URL do Fornecedor de **Identidade,** colá o valor URL de **Login** que copiou do portal Azure.

    c. Na caixa de texto URL do Fornecedor de **Identidade,** colá o valor URL de **Logout** que copiou do portal Azure.

    d. Clique **em Escolher Ficheiro** para selecionar o certificado de teclado LockPath descarregado e, em seguida, clique em **Abrir** para carregar o certificado.

    e. Detete a localização id do **utilizador SAML** para **o elemento identificador de nome da declaração do assunto**.

    f. Forneça ao Fornecedor de **Serviços Keylight** utilizando o seguinte padrão: `https://<CompanyName>.keylightgrc.com`.

    g. Defina **os utilizadores de fornecimento automático** para **Ative**.

    h. Defina **o tipo de conta de prestação automática** para O Utilizador **Completo**.

    i. Definir **função de segurança de fornecimento automático**, selecione Utilizador Standard com **SAML**.

    j. Defina o **config de segurança de fornecimento automático,** selecione **a configuração padrão do utilizador**.

    k. Na caixa de texto de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` **atributo e-mail,** escreva .

    l. No **primeiro nome atributo** caixa `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`de texto, escreva .

    m. No **último nome atributo** caixa `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`de texto, escreva .

    n. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sinal de Azure, concedendo acesso ao LockPath Keylight.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **o Keylight LockPath**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LockPath Keylight**.

    ![O link de luz de chave LockPath na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-lockpath-keylight-test-user"></a>Criar o utilizador de teste lockPath Keylight

Nesta secção, um utilizador chamado Britta Simon é criado em LockPath Keylight. O LockPath Keylight suporta o fornecimento de utilizadores just-in-time, que está ativado por predefinição. Não há nenhum item de ação para si nesta secção. Se um utilizador ainda não existir no LockPath Keylight, um novo é criado após a autenticação. Se precisar de criar um utilizador manualmente, tem de contactar a equipa de suporte ao [Cliente LockPath Keylight](https://www.lockpath.com/contact/).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo lockPath keylight no Painel de Acesso, deve ser automaticamente inscrito no teclado LockPath para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)