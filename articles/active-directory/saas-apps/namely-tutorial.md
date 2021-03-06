---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Nomeadamente [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e, nomeadamente.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: a9ec54ce27b4d058938e688ec671709e09391cce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160376"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Tutorial: Integração do Diretório Ativo Azure com a Nomeadamente

Neste tutorial, aprende-se a integrar nomeadamente com o Azure Ative Directory (Azure AD).
Integrando nomeadamente com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Nomeadamente.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Nomeadamente (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Nomeadamente, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Nomeadamente subscrição ativada por um único sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Nomeadamente apoia **SP** iniciado SSO

## <a name="adding-namely-from-the-gallery"></a>Adicionando nomeadamente a partir da galeria

Para configurar a integração da Nomeadamente no Anúncio Azure, precisa de adicionar a Nomeadamente da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Nomeadamente a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **a**saber , selecione **nomeadamente** a partir do painel de resultados e, em seguida, clique em adicionar o botão **adicionar** a aplicação.

     ![Nomeadamente na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com a Namely com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Nomeadamente.

Para configurar e testar o único sinal de Azure AD com a Nomeadamente, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar nomeadamente o sign-on único](#configure-namely-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Criar nomeadamente o utilizador](#create-namely-test-user)** do teste - para ter uma contraparte de Britta Simon em Nomeadamente que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da AD Azure com a Nomeadamente, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **nomeadamente,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Nomeadamente informações de inscrição única de domínio e URLs](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<subdomain>.namely.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte [nomeadamente a equipa](https://www.namely.com/contact/) de suporte ao cliente para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar Nomeadamente,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-namely-single-sign-on"></a>Configurar nomeadamente um único sign-on

1. Noutra janela do navegador, inscreva-se no site da empresa nomeadamente como administrador.

2. Na barra de ferramentas por cima, clique em **Empresa.**
   
    ![Configurar um único sinal](./media/namely-tutorial/tutorial_namely_06.png) 

3. Clique no separador **Definições**.
   
    ![Configurar um único sinal](./media/namely-tutorial/tutorial_namely_07.png) 

4. Clique em **SAML**.
   
    ![Configurar um único sinal](./media/namely-tutorial/tutorial_namely_08.png) 

5. Na página **Definições SAML,** execute os seguintes passos:
   
    ![Configurar um único sinal](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Clique em **ativar saml**. 

    b. Na caixa de texto url **SSO** do fornecedor de identidade, colá o valor do URL de **Login,** que copiou do portal Azure.
    
    c. Abra o seu certificado descarregado no Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa de texto do certificado do **fornecedor de identidade.**
     
    d. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Nomeadamente.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Nomeadamente**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Nomeadamente**.

    ![O link nomeadamente na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-namely-test-user"></a>Criar nomeadamente o utilizador de teste

O objetivo desta secção é criar um utilizador chamado Britta Simon em nomeadamente.

**Para criar um utilizador chamado Britta Simon em nomeadamente, execute os seguintes passos:**

1. Inscreva-se no site da empresa, nomeadamente, como administrador.

2. Na barra de ferramentas por cima, clique em **Pessoas**.
   
    ![Configurar um único sinal](./media/namely-tutorial/tutorial_namely_10.png) 

3. Clique no separador **Diretório.**
   
    ![Configurar um único sinal](./media/namely-tutorial/tutorial_namely_11.png) 

4. Clique **em adicionar nova pessoa**.

    ![Configurar um único sinal](./media/namely-tutorial/tutorial_namely_12.png)

5. No diálogo **Adicionar Nova Pessoa,** execute os seguintes passos:

    a. Na caixa de texto **de primeiro nome,** digite **Britta**.

    b. Na caixa de texto **de apelido,** escreva **Simon.**

    c. Na caixa de texto **por e-mail,** digite o endereço de **e-mail** da BrittaSimon.

    d. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Nomeadamente no Painel de Acesso, deve ser automaticamente inscrito no Nomeadamente para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

