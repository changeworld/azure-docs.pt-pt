---
title: 'Tutorial: Integração de Diretório Sonárgico Azure com QuickHelp [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o QuickHelp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 655c9ad3-2076-4e2c-8e47-9ed3bf04be56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f4b41098a3b374506e655bf90f972b57195e0958
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093383"
---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Tutorial: Integração de Diretório Ativo Azure com QuickHelp

Neste tutorial, aprende-se a integrar quickHelp com o Azure Ative Directory (Azure AD).
Integrar quickHelp com AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a QuickHelp.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no QuickHelp (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com quickHelp, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de sinal único QuickHelp ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* QuickHelp suporta **SP** iniciado SSO

* QuickHelp suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-quickhelp-from-the-gallery"></a>Adicionando QuickHelp da galeria

Para configurar a integração do QuickHelp no Azure AD, precisa de adicionar QuickHelp da galeria à sua lista de aplicações saaS geridas.

**Para adicionar QuickHelp da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **QuickHelp**, selecione **QuickHelp** do painel de resultados e, em seguida, clique em adicionar o botão **adicionar** a aplicação.

     ![QuickHelp na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com quickHelp com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no QuickHelp.

Para configurar e testar o único sinal de Azure AD com quickHelp, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o QuickHelp Single Sign-On](#configure-quickhelp-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-quickhelp-test-user)** do teste QuickHelp - para ter uma contrapartida da Britta Simon no QuickHelp que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com quickHelp, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **QuickHelp,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![QuickHelp Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://quickhelp.com/<ROUTEURL>`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL:`https://auth.quickhelp.com`

    > [!NOTE]
    > O valor do URL de inscrição não é real. Atualize o valor com o URL de Sign-On real. Contacte o administrador quickHelp da sua organização ou o seu Gestor de Sucesso do Cliente BrainStorm para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **Configurar QuickHelp,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-quickhelp-single-sign-on"></a>Configure o sinal único de quickhelp

1. Inscreva-se no site da empresa QuickHelp como administrador.

2. No menu em cima, clique **em Admin**.
   
    ![Configurar um único sinal][21]

3. No menu **QuickHelp Admin,** clique em **Definições**.
   
    ![Configurar um único sinal][22]

4. Clique em **Definições de Autenticação**.

5. Na página Definições de **Autenticação,** execute os seguintes passos
   
    ![Configurar um único sinal][23]
   
    a. Como **Tipo SSO,** selecione **WSFederation**.
   
    b. Para fazer o upload do ficheiro de metadados do Azure descarregado, clique em **Navegar,** navegar para o ficheiro e terminar e clicar em **Enviar Metadados**.
   
    c. Na caixa de texto `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` **e-mail,** escreva .
   
    d. Na caixa de texto `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`De Primeiro **Nome,** .
   
    e. Na caixa de texto `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`De Último **Nome,** .
   
    f. Na Barra de **Ação,** clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo brittasimon@yourcompanydomain.extensionde campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao QuickHelp.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **QuickHelp**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **QuickHelp**.

    ![O link QuickHelp na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-quickhelp-test-user"></a>Criar o utilizador de teste QuickHelp

Nesta secção, um utilizador chamado Britta Simon é criado em QuickHelp. QuickHelp suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no QuickHelp, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo QuickHelp no Painel de Acesso, deve ser automaticamente inscrito no QuickHelp para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[21]: ./media/quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/quickhelp-tutorial/tutorial_quickhelp_07.png