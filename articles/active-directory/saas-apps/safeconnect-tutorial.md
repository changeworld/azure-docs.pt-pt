---
title: 'Tutorial: Integração de Diretório Ativo Azure com a SafeConnect [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SafeConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f9aaac2e-cdba-4f01-a57f-2c5c26287085
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 701e4ef37e9508923409a7ce65991b9174678f4f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68880208"
---
# <a name="tutorial-integrate-safeconnect-with-azure-active-directory"></a>Tutorial: Integrar o SafeConnect com o Diretório Ativo Azure

Neste tutorial, você aprenderá a integrar o SafeConnect com o Azure Ative Directory (Azure AD). Quando integrar o SafeConnect com o Azure AD, pode:

* Controlo em Azure AD que tenha acesso ao SafeConnect.
* Ative que os seus utilizadores sejam automaticamente inscritos no SafeConnect com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada pelo safeConnect (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* SafeConnect suporta **SP** iniciado SSO

## <a name="adding-safeconnect-from-the-gallery"></a>Adicionar SafeConnect da galeria

Para configurar a integração do SafeConnect em Azure AD, precisa de adicionar o SafeConnect da galeria à sua lista de aplicações SaaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite SafeConnect** na caixa de pesquisa.
1. Selecione **SafeConnect** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com SafeConnect utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SafeConnect.

Para configurar e testar o Azure AD SSO com o SafeConnect, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure O SafeConnect SSO](#configure-safeconnect-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. Criar o utilizador de **[teste SafeConnect](#create-safeconnect-test-user)** - para ter uma contraparte de B.Simon no SafeConnect que esteja ligada à representação do Utilizador azure AD.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **SafeConnect,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://portal.myweblogon.com:8443/saml/login`

1. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **Metadados XML** e selecione **Download** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Configurar o SafeConnect,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-safeconnect-sso"></a>Configure SafeConnect SSO

Para configurar um único sinal no lado **SafeConnect,** precisa enviar os **Metadados XML** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte SafeConnect](mailto:support@impulse.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

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

Nesta secção, permitirá que a B.Simon utilize um único sinal de Acesso Azure, concedendo acesso ao SafeConnect.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SafeConnect**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-safeconnect-test-user"></a>Criar o utilizador de teste SafeConnect

Nesta secção, cria-se um utilizador chamado Britta Simon no SafeConnect. Trabalhe com a equipa de [suporte SafeConnect](mailto:support@impulse.com) para adicionar os utilizadores na plataforma SafeConnect. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SafeConnect no Painel de Acesso, deve ser automaticamente inscrito no SafeConnect para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)