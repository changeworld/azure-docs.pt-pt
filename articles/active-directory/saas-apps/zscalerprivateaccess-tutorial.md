---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Acesso Privado zscaler (ZPA) [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zscaler Private Access (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a1538f640bb4722eca1d4f3a80125837593bab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085745"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Tutorial: Integrar o Acesso Privado zscaler (ZPA) com o Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar o Zscaler Private Access (ZPA) com o Azure Ative Directory (Azure AD). Quando integrar o Zscaler Private Access (ZPA) com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Zscaler Private Access (ZPA).
* Ative que os seus utilizadores sejam automaticamente inscritos no Zscaler Private Access (ZPA) com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pelo Acesso Privado (ZPA) zscaler Private Access (ZPA) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. Zscaler Private Access (ZPA) suporta **SP** iniciado SSO.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Adicionando acesso privado zscaler (ZPA) da galeria

Para configurar a integração do Zscaler Private Access (ZPA) no Azure AD, é necessário adicionar o Zscaler Private Access (ZPA) da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **digite Zscaler Private Access (ZPA)** na caixa de pesquisa.
1. Selecione **Zscaler Private Access (ZPA)** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Zscaler Private Access (ZPA) utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Zscaler Private Access (ZPA).

Para configurar e testar o Azure AD SSO com o Zscaler Private Access (ZPA), complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Acesso Privado zscaler (ZPA)](#configure-zscaler-private-access-zpa)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sign-on da Azure AD com britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de **[teste Zscaler Private Access (ZPA)](#create-zscaler-private-access-zpa-test-user)** para ter uma contrapartida da Britta Simon no Zscaler Private Access (ZPA) que está ligada à representação do utilizador da AD Azure.
6. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Zscaler Private Access (ZPA),** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    1. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Na caixa de texto **identificador (Id da entidade),** digite um URL:`https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > O **valor do Signon on URL** não é real. Atualize o valor com o sinal real no URL. Contacte a equipa de apoio ao [cliente zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Configurar o Acesso Privado (ZPA) do Zscaler,** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Configure Zscaler Private Access (ZPA)

1. Para automatizar a configuração dentro do Acesso Privado de Zscaler (ZPA), precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Zscaler Private Access (ZPA)** irá direcioná-lo para a aplicação Zscaler Private Access (ZPA). A partir daí, forneça as credenciais de administração para assinar no Zscaler Private Access (ZPA). A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se pretender configurar manualmente o Zscaler Private Access (ZPA), abra uma nova janela do navegador web e inscreva-se no site da empresa Zscaler Private Access (ZPA) como administrador e execute os seguintes passos:

4. Do lado esquerdo do menu, clique em **Administração** e navegue até à secção **DE AUTENTICAÇÃO** clique na **configuração idp**.

    ![Administração de Administrador de Acesso Privado Zscaler](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. No canto superior direito, clique em **Adicionar Configuração IdP**. 

    ![Zscaler Private Access Administrator IDP](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Na página **de Configuração Add IDP** efetuar os seguintes passos:
 
    ![Selecionador de Acesso Privado Zscaler](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Clique em **Selecionar Ficheiro** para fazer o upload do ficheiro Metadados descarregado a partir do Azure AD no campo de upload de **ficheiros idp metadados.**

    b. Lê os **metadados idp** da AD Azure e povoa todas as informações dos campos, como mostrado abaixo.

    ![Administrador de Acesso Privado Zscaler config](./media/zscalerprivateaccess-tutorial/config.png)

    c. Selecione o seu domínio a partir do campo **Domínios.**
    
    d. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Zscaler Private Access (ZPA).

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Zscaler Private Access (ZPA)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-zscaler-private-access-zpa-test-user"></a>Criar o utilizador de teste de acesso privado zscaler (ZPA)

Nesta secção, cria-se uma utilizadora chamada Britta Simon no Zscaler Private Access (ZPA). Por favor, trabalhe com a equipa de [suporte zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para adicionar os utilizadores na plataforma Zscaler Private Access (ZPA).

### <a name="test-sso"></a>Teste SSO

Ao selecionar o azulejo Zscaler Private Access (ZPA) no Painel de Acesso, deverá ser automaticamente inscrito no Zscaler Private Access (ZPA) para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)