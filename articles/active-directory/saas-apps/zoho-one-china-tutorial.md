---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com zoho One China [ Azure Ative Diretório) integração com zoho One China [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zoho One China.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77eff5fd-01c1-4834-b6b6-75dcad0e55fe
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c484b2c8b23cd6001cdbe53f5969875b0f79be8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385516"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoho-one-china"></a>Tutorial: Azure Ative Directory integração individual (SSO) com zoho One China

Neste tutorial, você vai aprender a integrar zoho One China com Azure Ative Directory (Azure AD). Quando integrar o Zoho One China com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Zoho One China.
* Permita que os seus utilizadores sejam automaticamente inscritos no Zoho One China com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Zoho One China único sign-on (SSO) permitiu a subscrição.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Zoho One China apoia **SP e IDP** iniciado SSO
* Assim que configurar o Zoho One China, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-zoho-one-china-from-the-gallery"></a>Adicionando Zoho One China da galeria

Para configurar a integração do Zoho One China em Azure AD, você precisa adicionar Zoho One China da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** **escreva Zoho One China** na caixa de pesquisa.
1. Selecione **Zoho One China** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoho-one-china"></a>Configure e teste Azure AD único sign-on para Zoho One China

Configure e teste Azure AD SSO com Zoho One China usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Zoho One China.

Para configurar e testar o Azure AD SSO com o Zoho One China, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure zoho One China SSO](#configure-zoho-one-china-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie](#create-zoho-one-china-test-user)** o utilizador de teste Zoho One China - para ter uma contrapartida de B.Simon no Zoho One China que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **zoho One China,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://accounts.zoho.com.cn/signin/samlsp/<zoid>`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://accounts.zoho.com.cn/samlauthrequest/<zoid>?serviceurl=https://one.zoho.com.cn`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Resposta real e URL de Sinal. Contacte a equipa de apoio ao [cliente Zoho One China](mailto:support@zohocorp.com.cn) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **"Configurar zoho One China",** copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso ao Zoho One China.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Zoho One China**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-zoho-one-china-sso"></a>Configure Zoho One China SSO

Para configurar um único sign-on no lado do **Zoho One China,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para [zoho One China equipe](mailto:support@zohocorp.com.cn)de apoio . Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-zoho-one-china-test-user"></a>Criar zoho One China test user

Nesta secção, cria-se uma utilizadora chamada Britta Simon no Zoho One China. Trabalhe com a equipa de [apoio da Zoho One China](mailto:support@zohocorp.com.cn) para adicionar os utilizadores na plataforma Zoho One China. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Zoho One China no Painel de Acesso, deve ser automaticamente inscrito no Zoho One China para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente zoho One China com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger zoho One China com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

