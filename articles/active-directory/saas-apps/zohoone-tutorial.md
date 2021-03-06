---
title: 'Tutorial: Integração do Diretório Ativo Azure com zoho One [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086237"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Tutorial: Integração do Diretório Ativo Azure com zoho One

Neste tutorial, aprende-se a integrar o Zoho One com o Azure Ative Directory (Azure AD).
Integrar o Zoho One com o Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Zoho One.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Zoho One (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Zoho One, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Zoho One única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Zoho One apoia **SP** e **IDP** iniciado SSO

## <a name="adding-zoho-one-from-the-gallery"></a>Adicionando Zoho One da galeria

Para configurar a integração do Zoho One em Azure AD, você precisa adicionar Zoho One da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Zoho One da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Zoho One**, selecione **Zoho One** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Zoho Um na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com zoho One com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Zoho One.

Para configurar e testar o único sinal de Azure AD com zoho One, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure zoho One Single Sign-On](#configure-zoho-one-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie](#create-zoho-one-test-user)** o utilizador de teste Zoho One - para ter uma contrapartida de Britta Simon no Zoho One que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com zoho One, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Zoho One,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Zoho One Domain e URLs informações únicas de inscrição](common/idp-relay.png)

    a. Na caixa de texto **identificador,** digite um URL:`one.zoho.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > O valor url de **resposta** anterior não é real. Você receberá `<saml-identifier>` o valor de #step4 da secção **Configure Zoho One Single Sign-On** , o que é explicado mais tarde no tutorial.

    c. Clique em **definir URLs adicionais**.

    d. Na caixa de texto **do Estado relé,** escreva um URL:`https://one.zoho.com`

5. Se desejar configurar a aplicação no modo iniciado **sp,** execute o seguinte passo:


    ![Zoho One Domain e URLs informações únicas de inscrição](common/both-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > O valor de URL de **sign-on** anterior não é real. Atualizará o valor com o URL de Sign-On real da secção **Configure Zoho One Single Sign-On,** o que é explicado mais tarde no tutorial. 

6. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

7. Na secção **Configurar zoho One,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-zoho-one-single-sign-on"></a>Configure Zoho Um único signo

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Zoho One como administrador.

2. No separador **Organização,** clique em **configurar** sob **autenticação SAML**.

    ![Zoho One org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Na página Pop-up executa os seguintes passos:

    ![Zoho One sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Na caixa de texto **url de entrada,** cola o valor do URL de **Login,** que copiou do portal Azure.

    b. Na caixa de texto **URL sign-out,** colhe o valor do URL de **Logout,** que copiou do portal Azure.

    c. Clique em **Navegar** para carregar o **Certificado (Base64)** que descarregou do portal Azure.

    d. Clique em **Guardar**.

4. Depois de salvar a configuração da autenticação SAML, copie o valor **Do Identificador SAML** e asente-o com o URL de **resposta** no lugar `<saml-identifier>`de , como `https://accounts.zoho.com/samlresponse/one.zoho.com` e cole o valor gerado na caixa de texto URL de **resposta** sob a secção **de configuração SAML Básica.**

    ![Zoho One saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Vá ao separador **Domínios** e, em seguida, clique em **Adicionar Domínio**.

    ![Domínio Zoho One](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Na página **Add Domain,** execute os seguintes passos:

    ![Zoho One adicionar domínio](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Na caixa de texto **Nome de Domínio,** digite domínio como contoso.com.

    b. Clique em **Adicionar**.

    >[!Note]
    >Depois de adicionar o domínio siga [estes](https://www.zoho.com/one/help/admin-guide/domain-verification.html) passos para verificar o seu domínio. Uma vez verificado o domínio, utilize o seu nome de domínio no **URL de início de** sessão na secção basic **SAML configuração** no portal Azure.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Zoho One.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Zoho One**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zoho One**.

    ![O link Zoho One na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-zoho-one-test-user"></a>Criar zoho Um utilizador de teste

Para permitir que os utilizadores da AD Azure inscrevam-se no Zoho One, devem ser aprovisionados no Zoho One. No Zoho One, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Zoho One como administrador de segurança.

2. No separador **Utilizadores,** clique no logótipo do **utilizador**.

    ![Zoho Um utilizador](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Na página **Adicionar Utilizador,** execute os seguintes passos:

    ![Zoho One adicionar utilizador](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Em **Nome** caixa de texto, introduza o nome de utilizador como **Britta simon**.
    
    b. Na caixa de texto **'Endereço de e-mail',** introduza o e-mail do utilizador como brittasimon@contoso.com.

    >[!Note]
    >Selecione o seu domínio verificado na lista de domínios.

    c. Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Zoho One no Painel de Acesso, deve ser automaticamente inscrito no Zoho One para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

