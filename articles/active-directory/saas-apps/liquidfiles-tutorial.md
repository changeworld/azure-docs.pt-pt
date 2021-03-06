---
title: 'Tutorial: Integração de Diretório Ativo Azure com LiquidFiles [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LiquidFiles.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cb517134-0b34-4a74-b40c-5a3223ca81b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6514594d3119ebf8fab774c3e84c85e34bdfeaf4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67097936"
---
# <a name="tutorial-azure-active-directory-integration-with-liquidfiles"></a>Tutorial: Integração de Diretório Ativo Azure com LiquidFiles

Neste tutorial, aprende-se a integrar os LiquidFiles com o Azure Ative Directory (Azure AD).
Integrar Ficheiros Líquidos com AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a Ficheiros Líquidos.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no LiquidFiles (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o LiquidFiles, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada por um único sinal de LiquidFiles

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* LiquidFiles suporta **SP** iniciado SSO

## <a name="adding-liquidfiles-from-the-gallery"></a>Adicionar Ficheiros Líquidos da galeria

Para configurar a integração dos LiquidFiles em Azure AD, é necessário adicionar Ficheiros Líquidos da galeria à sua lista de aplicações SaaS geridas.

**Para adicionar LiquidFiles da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **LiquidFiles**, selecione **LiquidFiles** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

    ![Ficheiros líquidos na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com LiquidFiles com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em LiquidFiles.

Para configurar e testar o único sinal de Azure AD com o LiquidFiles, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure liquidfiles single sign-on](#configure-liquidfiles-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-liquidfiles-test-user)** de teste LiquidFiles - para ter uma contraparte da Britta Simon em LiquidFiles que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o LiquidFiles, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **LiquidFiles,** selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Informação de domínio e URLs de liquidfiles e URLs](common/sp-identifier-reply.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<YOUR_SERVER_URL>/saml/init`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<YOUR_SERVER_URL>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<YOUR_SERVER_URL>/saml/consume`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte ao [Cliente LiquidFiles](https://www.liquidfiles.com/support.html) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

6. Na secção **Certificado de Assinatura SAML,** **copie** a IMPRESSÃO DIGITAL e guarde-a no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

7. Na secção **Configurar LiquidFiles,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-liquidfiles-single-sign-on"></a>Configure LiquidFiles Single Sign-On

1. Inscreva-se no site da empresa LiquidFiles como administrador.

1. Clique em **Iniciar sessão individual** no **> Configuração** do menu.

1. Na página **de configuração de um único sinal,** execute os seguintes passos

    ![Configurar um único sinal](./media/liquidfiles-tutorial/tutorial_single_01.png)

    a. Como **método de sinal único,** selecione **SAML 2**.

    b. Na caixa de texto **IDP Login URL,** colá o valor do URL de **Login,** que copiou do portal Azure.

    c. Na caixa de texto **IDP Logout URL,** colá o valor do URL de **Logout,** que copiou do portal Azure.

    d. Na caixa de texto **IDP Cert Fingerprint,** colá o valor **THUMBPRINT** que copiou do portal Azure..

    e. Na caixa de texto formato identificador de nome, escreva o valor `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Na caixa de texto authn `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport`Context, escreva o valor .

    g. Clique em **Guardar**.

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

Nesta secção, permite que a Britta Simon utilize um único sign-on Azure, concedendo acesso aos LiquidFiles.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **LiquidFiles**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LiquidFiles**.

    ![A ligação LiquidFiles na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-liquidfiles-test-user"></a>Criar o utilizador de teste LiquidFiles

O objetivo desta secção é criar um utilizador chamado Britta Simon em LiquidFiles. Trabalhe com o administrador do servidor LiquidFiles para ser adicionado como utilizador antes de iniciar sessão na sua aplicação LiquidFiles.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo LiquidFiles no Painel de Acesso, deve ser automaticamente inscrito nos Ficheiros Líquidos para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

