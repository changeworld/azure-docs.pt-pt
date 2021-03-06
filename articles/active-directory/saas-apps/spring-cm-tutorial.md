---
title: 'Tutorial: Integração do Diretório Ativo Azure com a SpringCM [ SpringCM ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bd9ea5565ea64415c00a458236456bed04b6c38
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233389"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Tutorial: Integração do Diretório Ativo Azure com a SpringCM

Neste tutorial, aprende-se a integrar o SpringCM com o Azure Ative Directory (Azure AD).
Integrar o SpringCM com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a SpringCM.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na SpringCM (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a SpringCM, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada por sinal único SpringCM

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* SpringCM suporta **SP** iniciado SSO

## <a name="adding-springcm-from-the-gallery"></a>Adicionando SpringCM da galeria

Para configurar a integração do SpringCM em Azure AD, você precisa adicionar SpringCM da galeria à sua lista de aplicações saaS geridas.

**Para adicionar SpringCM da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone do **Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique no novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SpringCM,** selecione **SpringCM** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![SpringCM na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure com springCM com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em SpringCM.

Para configurar e testar o único sinal de Azure AD com springcm, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Single SpringCM](#configure-springcm-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-springcm-test-user)** de teste SpringCM - para ter uma contraparte de Britta Simon em SpringCM que esteja ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com springcm, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **SpringCM,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![SpringCM Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de apoio ao [cliente da SpringCM](https://knowledge.springcm.com/support) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

6. Na secção **SpringCM configurar,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-springcm-single-sign-on"></a>Configure SpringCM Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa **SpringCM** como administrador.

1. No menu em cima, clique em **GO TO,** clique em **Preferências,** e depois, na secção Preferências de **Conta,** clique em **SAML SSO**.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. Na secção configuração do fornecedor de identidade, execute os seguintes passos:
   
    ![Configuração do Fornecedor de Identidade](./media/spring-cm-tutorial/ic797052.png "Configuração do Fornecedor de Identidade")
    
    a. Para fazer o upload do certificado de diretório Ativo Azure descarregado, clique em **Select Emitor ou** **Noemitado**de Alteração .
    
    b. Na caixa de texto **emitentes,** pasta **Azure AD Identifier** valor, que copiou do portal Azure.
    
    c. Na caixa de texto iniciada do Fornecedor de **Serviços (SP) Endpoint,** colar o valor url de **login,** que copiou do portal Azure.
            
    d. Selecione **SAML Ativado** como **Ativar**.

    e. Clique em **Guardar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à SpringCM.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **SpringCM**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SpringCM**.

    ![O link SpringCM na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-springcm-test-user"></a>Criar o utilizador de teste SpringCM

Para permitir que os utilizadores do Diretório Ativo azure assinem o SpringCM, devem ser aprovisionados na SpringCM. No caso da SpringCM, o provisionamento é uma tarefa manual.

> [!NOTE]
> Para mais informações, consulte [Criar e Editar um Utilizador SpringCM](https://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Para fornecer uma conta de utilizador à SpringCM, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa **SpringCM** como administrador.

1. Clique em **GOTO**e, em seguida, clique em **LIVRO DE ENDEREÇOS**.
   
    ![Criar o Utilizador](./media/spring-cm-tutorial/ic797054.png "Criar Utilizador")

1. Clique em **Criar utilizador**.

1. Selecione uma **Função de Utilizador**.

1. Selecione **Enviar e-mail**de ativação .

1. Digite o primeiro nome, apelido e endereço de e-mail de uma conta de utilizador de Diretório Ativo Azure válida que pretende fornecer nas caixas de texto relacionadas.

1. Adicione o utilizador a um grupo de **Segurança**.

1. Clique em **Guardar**.

   > [!NOTE]
   > Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador springcm ou APIs fornecidas pela SpringCM para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SpringCM no Painel de Acesso, deve ser automaticamente inscrito no SpringCM para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

