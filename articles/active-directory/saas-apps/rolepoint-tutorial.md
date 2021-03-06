---
title: 'Tutorial: Integração de Diretório Ativo Azure com RolePoint / Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre o Diretório Ativo Azure e o RolePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68d37f40-15da-45f5-a9e1-d53f78e786d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 0b6fd17d2f8577532778733866260f43e9ac7685
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092724"
---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>Tutorial: Integração de Diretório Ativo Azure com rolePoint

Neste tutorial, aprenderá a integrar o RolePoint com o Azure Ative Directory (Azure AD).
Esta integração proporciona estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao RolePoint.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no RolePoint (único sinal) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o RolePoint, é necessário ter:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição rolePoint com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar o único sign-on Azure AD em um ambiente de teste.

* RolePoint suporta SSO iniciado por SP.

## <a name="add-rolepoint-from-the-gallery"></a>Adicione RolePoint da galeria

Para configurar a integração do RolePoint em Azure AD, precisa de adicionar rolePoint da galeria à sua lista de aplicações saaS geridas.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory:**

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir a **aplicações** > da Enterprise**Todas as aplicações:**

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione Nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **RolePoint**. Selecione **RolePoint** nos resultados da pesquisa e, em seguida, **selecione Adicionar**.

     ![Resultados de pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, irá configurar e testar um único sign-on azure ad com o RolePoint utilizando um utilizador de teste chamado Britta Simon.
Para permitir uma única inscrição, é necessário estabelecer uma relação entre um utilizador da AD Azure e o utilizador correspondente no RolePoint.

Para configurar e testar o único signo do Azure AD com o RolePoint, é necessário completar estes passos:

1. Configure o único sinal de entrada **[da AD Azure](#configure-azure-ad-single-sign-on)** para ativar a funcionalidade para os seus utilizadores.
2. **[Configure](#configure-rolepoint-single-sign-on)** o único sinal de rolePoint no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de AD Azure.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar o único sinal de entrada da Azure AD para o utilizador.
5. **[Crie um utilizador](#create-a-rolepoint-test-user)** de teste RolePoint ligado à representação do AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, irá ativar o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o RolePoint, tome estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações RolePoint, selecione **Um único sign-on:**

    ![Selecione um único sinal](common/select-sso.png)

2. Na selectuma única caixa de diálogo do **método de sinalização,** selecione o modo **SAML/WS-Fed** para ativar um único sinal:

    ![Selecione um único método de sinalização](common/select-saml-option.png)

3. Na configuração de um único sign-on com a página **SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **Basic SAML Configuration,** dê os seguintes passos.

    ![Caixa de diálogo de configuração SAML básica](common/sp-identifier.png)

    1. No **signo na** caixa URL, introduza um URL neste padrão:

       `https://<subdomain>.rolepoint.com/login`

    1. Na caixa **Identifier (Id da Entidade),** introduza um URL neste padrão:

       `https://app.rolepoint.com/<instancename>`

    > [!NOTE]
    > Estes valores são espaços reservados. Tens de usar o URL e identificador de inscrição real. Sugerimos que use um valor de cadeia único no identificador. Contacte a equipa de suporte do [RolePoint](mailto:info@rolepoint.com) para obter estes valores. Também pode consultar os padrões mostrados na caixa de diálogo **Basic SAML Configuration** no portal Azure.

5. Na configuração de um único sinal com a página **SAML,** na secção Certificado de **Assinatura SAML,** selecione o link **de descarregamento** ao lado do **Federation Metadata XML,** de acordo com os seus requisitos, e guarde o ficheiro no seu computador.

    ![Link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **set up RolePoint,** copie os URLs apropriados, com base nos seus requisitos:

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure AD**.

    1. **URL de logout**.


### <a name="configure-rolepoint-single-sign-on"></a>Configure o sign-on single rolePoint

Para configurar um único sign-on no lado do RolePoint, precisa de trabalhar com a equipa de [suporte rolePoint](mailto:info@rolepoint.com). Envie a esta equipa o ficheiro Da Federação metadados XML e os URLs que obteve do portal Azure. Configurarão o RolePoint para garantir que a ligação SAML SSO está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar uma utilizadora de teste chamada Britta Simon no portal Azure.

1. No portal Azure, selecione **Azure Ative Directory** no painel esquerdo, selecione **Utilizadores,** e, em seguida, selecione **Todos os utilizadores:**

    ![Selecionar Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior da janela:

    ![Selecione Novo utilizador](common/new-user.png)

3. Na caixa de diálogo **do Utilizador,** tome os seguintes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    1. Na caixa **de nomes,** entre **brittaSimon.**
  
    1. Na caixa de **nomes do Utilizador,** introduza **\<BrittaSimon@> de domínio da\< sua empresa.>de extensão. ** (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **Mostrar palavra-passe**e, em seguida, anote o valor que está na caixa **password.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que Britta Simon use o único sign-on azure, concedendo-lhe acesso ao RolePoint.

1. No portal Azure, selecione **aplicações Enterprise,** selecione **Todas as aplicações,** e, em seguida, selecione **RolePoint**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **RolePoint**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **De utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior da janela.

6. Se esperar um valor de papel na afirmação Do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecionar** na parte inferior da janela.

7. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-a-rolepoint-test-user"></a>Criar um utilizador de teste RolePoint

Em seguida, você precisa criar um utilizador chamado Britta Simon no RolePoint. Trabalhe com a equipa de [suporte rolePoint](mailto:info@rolepoint.com) para adicionar utilizadores ao RolePoint. Os utilizadores precisam de ser criados e ativados antes de poder utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora precisa de testar a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo RolePoint no Painel de Acesso, deve ser automaticamente inscrito na instância RolePoint para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [O Acesso e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
