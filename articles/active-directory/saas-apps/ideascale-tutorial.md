---
title: 'Tutorial: Integração de Diretório Sonárgico Azure com o IdeaScale [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o IdeaScale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81594e6a21372f2b4dacedbda638cc87bad966db
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227567"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Tutorial: Integração de Diretório Ativo Azure com ideaScale

Neste tutorial, aprende-se a integrar o IdeaScale com o Azure Ative Directory (Azure AD).
Integrar o IdeaScale com a AD Azur e proporcionar-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao IdeaScale.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no IdeaScale (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o IdeaScale, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de inscrição única ideaScale ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* IdeaScale suporta **SP** iniciado SSO

## <a name="adding-ideascale-from-the-gallery"></a>Adicionar IdeaScale da galeria

Para configurar a integração do IdeaScale em Azure AD, precisa de adicionar o IdeaScale da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar ideaScale a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite ideaScale**, **Add** **selecione IdeaScale** do painel de resultados e, em seguida, clique em Adicionar o botão para adicionar a aplicação.

     ![IdeaScale na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com o IdeaScale com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no IdeaScale.

Para configurar e testar o único signo azure ad com o IdeaScale, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Single IdeaScale](#configure-ideascale-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create IdeaScale test user](#create-ideascale-test-user)** - para ter uma contrapartida de Britta Simon no IdeaScale que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o IdeaScale, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **IdeaScale,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![IdeaScale Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<companyname>.ideascale.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:
    
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte ao [Cliente IdeaScale](https://support.ideascale.com/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **Configurar ideaScale,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-ideascale-single-sign-on"></a>Configure IdeaScale Single Sign-On

1. Numa janela de navegador web diferente, inicie sessão no site da empresa IdeaScale como administrador.

2. Ir para **As Definições Comunitárias**.

    ![Definições comunitárias](./media/ideascale-tutorial/ic790847.png "Definições comunitárias")

3. Vá para as definições de **sinalização single \> **de segurança .

    ![Definições de signon único](./media/ideascale-tutorial/ic790848.png "Definições de signon único")

4. Como **Tipo de Sinalização Única,** selecione **SAML 2.0**.

    ![Tipo de signon único](./media/ideascale-tutorial/ic790849.png "Tipo de signon único")

5. No diálogo de **definições de sinalização única,** execute os seguintes passos:

    ![Definições de signon único](./media/ideascale-tutorial/ic790850.png "Definições de signon único")

    a. Na caixa de texto **ID da Entidade IdP SAML,** colá o valor do **Identificador Azure Ad** que copiou do portal Azure.

    b. Abra o ficheiro de metadados descarregado do portal Azure para o Notepad, copie o conteúdo do mesmo e cole na caixa de texto **sAML IdP Metadata.**

    c. Na caixa de texto **URL logout Success,** colhe o valor do URL de **Logout** que copiou do portal Azure.

    d. Clique em **Guardar Alterações**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao IdeaScale.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **IdeaScale**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **IdeaScale**.

    ![O link IdeaScale na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-ideascale-test-user"></a>Criar o utilizador de teste IdeaScale

Para permitir que os utilizadores de Anúncios Azure entrem no IdeaScale, devem ser aprovisionados no IdeaScale. No caso do IdeaScale, o provisionamento é uma tarefa manual.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inicie sessão no site da empresa **IdeaScale** como administrador.

2. Ir para **As Definições Comunitárias**.

    ![Definições comunitárias](./media/ideascale-tutorial/ic790847.png "Definições comunitárias")

3. Ir para a **Gestão de Membros de Definições \> Básicas.**

4. Clique em **Adicionar Membro**.

    ![Gestão de Membros](./media/ideascale-tutorial/ic790852.png "Gestão de Membros")

5. Na secção Adicionar Novo Membro, execute os seguintes passos:

    ![Adicionar novo membro](./media/ideascale-tutorial/ic790853.png "Adicionar novo membro")

    a. Na caixa de texto endereços de **e-mail,** escreva o endereço de e-mail de uma conta Azure AD válida que pretende fornecer.

    b. Clique em **Guardar Alterações**.

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail com um link para confirmar a conta antes de se tornar ativa.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador ideaScale ou APIs fornecidas pelo IdeaScale para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo IdeaScale no Painel de Acesso, deverá ser automaticamente inscrito no IdeaScale para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

