---
title: 'Tutorial: Integração do Diretório Ativo azure com predictix Price Reporting Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre o Azure Ative Directory e o Predictix Price Reporting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 691d0c43-3aa1-4220-9e46-e7a88db234ad
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 808b2d964bb39af6b410a84563717102ebece454
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094111"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Tutorial: Integração do Diretório Ativo Azure com Relatório de Preços da Predictix

Neste tutorial, você aprenderá a integrar predictix Price Reporting com Azure Ative Directory (Azure AD).

Esta integração proporciona estes benefícios:

* Pode utilizar a AD Azure para controlar quem tem acesso ao Predictix Price Reporting.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Predictix Price Reporting (entrada única) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Predictix Price Reporting, precisa de:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode [inscrever-se](https://azure.microsoft.com/pricing/free-trial/) para uma subscrição experimental de um mês.
* Uma subscrição de Relatório de Preços da Predictix que tem um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar o único sign-on Azure AD em um ambiente de teste.

* Predictix Price Reporting suporta SSO iniciado por SP.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Adicionando relatórios de preços de Predictix da galeria

Para configurar a integração do Predictix Price Reporting no Azure AD, você precisa adicionar Predictix Price Reporting da galeria à sua lista de aplicações geridas saaS.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory:**

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir a **aplicações** > da Enterprise**Todas as aplicações:**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione Nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **predictix Price Reporting**. Selecione **Predictix Price Reporting** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

     ![Resultados de pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, irá configurar e testar um único sign-on da Azure AD com a Predictix Price Reporting utilizando um utilizador de teste chamado Britta Simon.
Para permitir uma única inscrição, é necessário estabelecer uma relação entre um utilizador da AD Azure e o utilizador correspondente no Predictix Price Reporting.

Para configurar e testar o único sinal de Azure AD com o Predictix Price Reporting, é necessário completar estes passos:

1. Configure o único sinal de entrada **[da AD Azure](#configure-azure-ad-single-sign-on)** para ativar a funcionalidade para os seus utilizadores.
2. **[Configure Predictix Price Reporting única inscrição](#configure-predictix-price-reporting-single-sign-on)** no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de AD Azure.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar o único sinal de entrada da Azure AD para o utilizador.
5. **[Crie um utilizador](#create-a-predictix-price-reporting-test-user)** de teste de reporte de preços da Predictix que esteja ligado à representação da AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, irá ativar o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com a Predictix Price Reporting, tome estas medidas:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Predictix Price Reporting,** selecione **Single sign-on:**

    ![Selecione um único sinal on](common/select-sso.png)

2. Na selectuma única caixa de diálogo do **método de sinalização,** selecione o modo **SAML/WS-Fed** para ativar um único sinal:

    ![Selecione um único método de sinalização](common/select-saml-option.png)

3. Na configuração de um único sign-on com a página **SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **Basic SAML Configuration,** complete os seguintes passos.

    ![Caixa de diálogo de configuração SAML básica](common/sp-identifier.png)

    1. No **signo na** caixa URL, introduza um URL neste padrão:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. Na caixa **Identifier (Id da Entidade),** introduza um URL neste padrão:

        | |
        |--|
        | `https://<companyname-pricing>.predictix.com` |
        | `https://<companyname-pricing>.dev.predictix.com` |
        | |

    > [!NOTE]
    > Estes valores são espaços reservados. Tens de usar o URL e identificador de inscrição real. Contacte a equipa de suporte do [Predictix Price Reporting](https://www.infor.com/company/customer-center/) para obter os valores. Também pode consultar os padrões mostrados na caixa de diálogo **Basic SAML Configuration** no portal Azure.

5. Na configuração de um único sinal com a página **SAML,** na secção Certificado de **Assinatura SAML,** selecione o link **de descarregamento** ao lado do **Certificado (Base64)**, de acordo com os seus requisitos, e guarde o certificado no seu computador:

    ![Link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção De Relatório de **Preços da Configuração,** copie os URLs apropriados, com base nos seus requisitos.

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure AD**.

    1. **URL de logout**.

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Configure Predictix Reportando um único sinal

Para configurar um único sinal no lado do Predictix Price Reporting, precisa enviar o certificado que descarregou e os URLs que copiou do portal Azure para a equipa de suporte de Relatório de Preços de [Previsão](https://www.infor.com/company/customer-center/). Esta equipa garante que a ligação SAML SSO está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar uma utilizadora de teste chamada Britta Simon no portal Azure.

1. No portal Azure, selecione **Azure Ative Directory** no painel esquerdo, selecione **Utilizadores,** e, em seguida, selecione **Todos os utilizadores:**

    ![Selecionar Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã:

    ![Selecione Novo utilizador](common/new-user.png)

3. Na caixa de diálogo **do Utilizador,** tome os seguintes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    1. Na caixa **de nomes,** entre **brittaSimon.**
  
    1. Na caixa de **nomes do Utilizador,** introduza **\<BrittaSimon@> de domínio da\< sua empresa.>de extensão. ** (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **Mostrar palavra-passe**e, em seguida, anote o valor que está na caixa **password.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que Britta Simon use um único sign-on da Azure AD, concedendo-lhe acesso à Predictix Price Reporting.

1. No portal Azure, selecione **aplicações Enterprise**, selecione **Todas as aplicações,** e, em seguida, selecione **Predictix Price Reporting**.

    ![Aplicações Empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Predictix Price Reporting**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **De utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se esperar um valor de papel na afirmação Do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Select** na parte inferior do ecrã.

7. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-a-predictix-price-reporting-test-user"></a>Criar um utilizador de teste de relatório de preços da Predictix

Em seguida, você precisa criar um utilizador chamado Britta Simon em Predictix Price Reporting. Trabalhe com a equipa de suporte do [Predictix Price Reporting](https://www.infor.com/company/customer-center/) para adicionar utilizadores. Os utilizadores precisam de ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora precisa de testar a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo predictix Price Reporting no Painel de Acesso, deve ser automaticamente inscrito na instância de Reporte de Preços de Predictix para a qual configura o SSO. Para mais informações, consulte [O Acesso e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)