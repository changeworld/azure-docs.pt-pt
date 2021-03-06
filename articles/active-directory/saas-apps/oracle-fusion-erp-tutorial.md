---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com o Oracle Fusion ERP [ Azure Ative Diretório) integração com o Oracle Fusion ERP [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Oracle Fusion ERP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c7586369-c1d6-4e83-a33e-5e87a68a7722
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cd7ea1a20eb82d030e9bdabe76ff7d14a619d1e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373081"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-fusion-erp"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com o ERP da Oracle Fusion

Neste tutorial, você vai aprender a integrar o Oracle Fusion ERP com o Azure Ative Directory (Azure AD). Quando integrar o ErP oracle fusion com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao ERP oracle Fusion.
* Ative que os seus utilizadores sejam automaticamente inscritos no ErP Oracle Fusion com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Oracle Fusion ERP (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* O ERP da Oracle Fusion apoia **o SP** iniciado

## <a name="adding-oracle-fusion-erp-from-the-gallery"></a>Adicionando o ERP oracle fusion da galeria

Para configurar a integração do ERP oracle fusion em Azure AD, você precisa adicionar o Oracle Fusion ERP da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **o Oracle Fusion ERP** na caixa de pesquisa.
1. Selecione **O ERP** oracle fusion do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-oracle-fusion-erp"></a>Configure e teste Azure AD SSO para Oráculo Fusão ERP

Configure e teste Azure AD SSO com OR De Fusão Oracle utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no ERP da Oracle Fusion.

Para configurar e testar o Azure AD SSO com o ERP de fusão oracle, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Oracle Fusion ERP SSO](#configure-oracle-fusion-erp-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
    1. **[Crie o utilizador](#create-oracle-fusion-erp-test-user)** de teste do Oracle Fusion ERP - para ter uma contrapartida de B.Simon no ERP de Fusão Oracle que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações erp da **Oracle Fusion,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.fa.em2.oraclecloud.com/fscmUI/faces/AtkHomePageWelcome`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.login.em2.oraclecloud.com:443/oam/fed`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de apoio ao [Cliente Da Oracle Fusion ERP](https://www.oracle.com/applications/erp/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **DE RÉP** de Fusão Oracle, copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao ERP Oracle Fusion.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Oráculo Fusion ERP**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-oracle-fusion-erp-sso"></a>Configure Oracle Fusion ERP SSO

Para configurar um único sinal no lado ERP da **Oracle Fusion,** você precisa enviar os metadados da **Federação XML** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte ERP](https://www.oracle.com/applications/erp/)Oracle Fusion . Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-oracle-fusion-erp-test-user"></a>Criar o utilizador de teste ERP da Oracle Fusion

Nesta secção, cria-se uma utilizadora chamada Britta Simon no ERP da Oracle Fusion. Trabalhe com a equipa de suporte do [ERP oracle Fusion](https://www.oracle.com/applications/erp/) para adicionar os utilizadores na plataforma ERP oracle Fusion. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ERP oracle Fusion no Painel de Acesso, deve ser automaticamente inscrito no ERP de Fusão Oracle para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o ErP de Fusão Oracle com a AD Azure](https://aad.portal.azure.com/)
