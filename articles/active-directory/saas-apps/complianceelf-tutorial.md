---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Compliance ELF [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e o COMPLIANCE ELF.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 69c6efc3-54c7-49ec-b827-33177c09aa13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f3e23a4c3c3b668939c9f0ac1331214ddbb7cdf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159363"
---
# <a name="tutorial-azure-active-directory-integration-with-compliance-elf"></a>Tutorial: Integração do Diretório Ativo Azure com o Compliance ELF

Neste tutorial, aprende-se a integrar o Compliance ELF com o Azure Ative Directory (Azure AD).
Integrar o Compliance ELF com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Compliance ELF.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Compliance ELF (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Compliance ELF, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por assinatura ativada por assinatura seletiva DO ELF

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Compliance ELF suporta **SP e IDP** iniciado SSO

## <a name="adding-compliance-elf-from-the-gallery"></a>Adicionando compliance ELF da galeria

Para configurar a integração do Compliance ELF em Azure AD, você precisa adicionar Compliance ELF da galeria à sua lista de aplicações saaS geridas.

**Para adicionar compliance ELF da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **o COMPLIANCE ELF,** selecione **Compliance ELF** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Compliance ELF na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure ad com compliance ELF com base num utilizador de teste chamado **Britta Simon**.
Para que o início único funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Compliance ELF.

Para configurar e testar o único sinal de Azure AD com o Compliance ELF, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o 'Sign-On Único' DO INÍCIO](#configure-compliance-elf-single-sign-on)** de Conformidade - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Compliance ELF test user](#create-compliance-elf-test-user)** - para ter uma contrapartida de Britta Simon no Compliance ELF que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o Compliance ELF, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **COMPLIANCE ELF,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute o seguinte passo:

    ![Conformidade Domínio ELF e URLs informações únicas de inscrição](common/idp-identifier.png)

    Na caixa de texto **identificador,** digite um URL:`https://sso.cordium.com`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<subdomain>.complianceelf.com`

    > [!NOTE]
    > O valor do Signon on URL não é real. Atualize o valor com o sinal real no URL. Contacte a equipa de suporte do [Cliente COMPLIANCE ELF](mailto:support@complianceelf.com) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-compliance-elf-single-sign-on"></a>Configure Compliance ELF Single Sign-On

Para configurar um único login no lado ELF de **Conformidade,** você precisa enviar o Url de **Metadados da Federação de Aplicações** para [compliance ELF equipe](mailto:support@complianceelf.com)de suporte . Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Compliance ELF.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Compliance ELF**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Compliance ELF**.

    ![O link ELF de conformidade na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-compliance-elf-test-user"></a>Criar o utilizador de teste ELF de Conformidade

Nesta secção, cria-se uma utilizadora chamada Britta Simon no Compliance ELF. Trabalhe com a equipa de [suporte do Compliance ELF](mailto:support@complianceelf.com) para adicionar os utilizadores na plataforma Compliance ELF. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo COMPLIANCE ELF no Painel de Acesso, deverá ser automaticamente inscrito no ELF de Conformidade para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

