---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Lucidchart Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Lucidchart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3eace60445dc9d52f9690da74360282efbb4cbe5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76291216"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Tutorial: Azure Ative Directory integração individual (SSO) com lucidchart

Neste tutorial, você vai aprender a integrar lucidchart com o Azure Ative Directory (Azure AD). Quando integrar a Lucidchart com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Lucidchart.
* Permita que os seus utilizadores sejam automaticamente inscritos na Lucidchart com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada pela Lucidchart single (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Lucidchart apoia **SP** iniciado SSO
* Lucidchart suporta o fornecimento de utilizadores **Just In Time**
* Assim que configurar o Lucidchart, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-lucidchart-from-the-gallery"></a>Adicionando Lucidchart da galeria

Para configurar a integração da Lucidchart em Azure AD, você precisa adicionar Lucidchart da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** **digite Lucidchart** na caixa de pesquisa.
1. Selecione **Lucidchart** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-lucidchart"></a>Configure e teste Azure AD single sign-on para Lucidchart

Configure e teste Azure AD SSO com Lucidchart utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Lucidchart.

Para configurar e testar o Azure AD SSO com a Lucidchart, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure lucidchart SSO](#configure-lucidchart-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie um utilizador](#create-lucidchart-test-user)** de teste Lucidchart - para ter uma contrapartida de B.Simon em Lucidchart que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Lucidchart,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

   Na caixa de texto **de URL sign-on,** escreva um URL como:`https://chart2.office.lucidchart.com/saml/sso/azure`

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **Configurar lucidchart,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso à Lucidchart.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Lucidchart**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-lucidchart-sso"></a>Configure Lucidchart SSO

1. Numa janela diferente do navegador web, inicie sessão no site da sua empresa Lucidchart como administrador.

2. No menu em cima, clique em **Team**.

    ![Equipa](./media/lucidchart-tutorial/ic791190.png "Equipa")

3. Clique em **Aplicações \> Gerir O SAML**.

    ![Gerir o SAML](./media/lucidchart-tutorial/ic791191.png "Gerir o SAML")

4. Na página de diálogo **SAML Authentication Settings,** execute os seguintes passos:

    a. **Selecione ativar a autenticação SAML**e, em seguida, clique **opcional**.

    ![Definições de autenticação SAML](./media/lucidchart-tutorial/ic791192.png "Definições de autenticação SAML")

    b. Na caixa de texto **domínio,** escreva o seu domínio e clique no **Certificado de Alteração**.

    ![Certificado de alteração](./media/lucidchart-tutorial/ic791193.png "Certificado de alteração")

    c. Abra o ficheiro de metadados descarregados, copie o conteúdo e colá-lo na caixa de texto **de Metadados de Upload.**

    ![Enviar Metadados](./media/lucidchart-tutorial/ic791194.png "Enviar Metadados")

    d. Selecione **Automaticamente Adicione novos utilizadores à equipa**e, em seguida, clique em Guardar **alterações**.

    ![Salvar alterações](./media/lucidchart-tutorial/ic791195.png "Salvar alterações")

### <a name="create-lucidchart-test-user"></a>Criar utilizador de teste Lucidchart

Não existe nenhum item de ação para configurar o fornecimento de utilizadores à Lucidchart.  Quando um utilizador designado tenta entrar na Lucidchart utilizando o painel de acesso, a Lucidchart verifica se o utilizador existe.  

Se ainda não existe uma conta de utilizador disponível, é automaticamente criada pela Lucidchart.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Lucidchart no Painel de Acesso, deve ser automaticamente inscrito no Lucidchart para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Lucidchart com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger lucidchart com visibilidade avançada e controlos](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
