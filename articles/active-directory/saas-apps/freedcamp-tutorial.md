---
title: 'Tutorial: Integração do Diretório Ativo Azure com freedcamp Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Freedcamp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de1ae135df4cd070fe9c6ee322e304caa1e17c9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67101918"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Tutorial: Integrar o Freedcamp com o Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Freedcamp com o Azure Ative Directory (Azure AD). Quando integrar o Freedcamp com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Freedcamp.
* Permita que os seus utilizadores sejam automaticamente inscritos no Freedcamp com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal (SSO) do Freedcamp.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. Freedcamp apoia **SP e IDP** iniciado SSO.

## <a name="adding-freedcamp-from-the-gallery"></a>Adicionando Freedcamp da galeria

Para configurar a integração do Freedcamp em Azure AD, você precisa adicionar Freedcamp da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Freedcamp** na caixa de pesquisa.
1. Selecione **Freedcamp** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Freedcamp utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Freedcamp.

Para configurar e testar o Azure AD SSO com o Freedcamp, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure freedcamp](#configure-freedcamp)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sign-on da Azure AD com britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador](#create-freedcamp-test-user)** de teste Freedcamp para ter uma contrapartida de Britta Simon em Freedcamp que está ligada à representação do utilizador da AD Azure.
6. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Freedcamp,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    1. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Os utilizadores também podem introduzir os valores do url em relação `freedcamp.com`ao seu próprio domínio de cliente e podem não ser necessariamente do padrão, podem introduzir qualquer valor específico do domínio do cliente, específico da sua instância de aplicação. Também pode contactar a equipa de suporte do [Cliente Freedcamp](mailto:devops@freedcamp.com) para obter mais informações sobre os padrões de url.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **"Configurar Freedcamp",** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Configure Freedcamp

1. Para automatizar a configuração dentro do Freedcamp, é necessário instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configuração Freedcamp** irá direcioná-lo para a aplicação Freedcamp. A partir daí, forneça as credenciais de administração para assinar em Freedcamp. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Freedcamp manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Freedcamp como administrador e execute os seguintes passos:

4. No canto superior direito da página, clique no **perfil** e depois navegue para **a Minha Conta**.

    ![Configuração freedcamp](./media/freedcamp-tutorial/config01.png)

5. Do lado esquerdo da barra de menus, clique no **SSO** e na página de **ligações SSO** execute os seguintes passos:

    ![Configuração freedcamp](./media/freedcamp-tutorial/config02.png)

    a. Na caixa de texto **Title,** escreva o título.

    b. Na caixa de texto **Id da Entidade,** pasta o valor do **identificador AD Azure,** que copiou do portal Azure.

    c. Na caixa de texto **URL login,** colá o valor URL de **Login,** que copiou do portal Azure.

    d. Abra o certificado codificado Base64 no bloco de notas, copie o seu conteúdo e cole-o na caixa de texto **do Certificado.**

    e. Clique em **Submeter**.

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

Nesta secção, você permitirá que Britta Simon use o único sign-on Azure, concedendo acesso ao Freedcamp.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Freedcamp**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-freedcamp-test-user"></a>Criar o utilizador de teste Freedcamp

Para permitir que os utilizadores da AD Azure, inscrevam-se no Freedcamp, devem ser aprovisionados no Freedcamp. Em Freedcamp, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Numa janela de navegador web diferente, inscreva-se no Freedcamp como administrador de segurança.

2. No canto superior direito da página, clique no **perfil** e, em seguida, navegue para Gerir o **Sistema**.

    ![Configuração freedcamp](./media/freedcamp-tutorial/config03.png)

3. No lado direito da página Manage System, execute os seguintes passos:

    ![Configuração freedcamp](./media/freedcamp-tutorial/config04.png)

    a. Clique em **Adicionar ou convidar utilizadores.**

    b. Na caixa de texto **e-mail,** introduza o e-mail do utilizador como `Brittasimon@contoso.com`.

    c. Clique em **Adicionar Utilizador**.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Freedcamp no Painel de Acesso, deve ser automaticamente inscrito no Freedcamp para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)