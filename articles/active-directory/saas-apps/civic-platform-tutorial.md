---
title: 'Tutorial: Integração do Diretório Ativo Azure com plataforma cívica [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Plataforma Cívica.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d790454-143e-40ac-b3cb-5a256977b4db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ccf124c5a4160715df4e685e405dcd591c49ae7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68496827"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Tutorial: Integrar plataforma cívica com diretório ativo Azure

Neste tutorial, você vai aprender a integrar a Plataforma Cívica com o Azure Ative Directory (Azure AD). Quando integrar a Plataforma Cívica com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Plataforma Cívica.
* Permita que os seus utilizadores sejam automaticamente inscritos na Plataforma Cívica com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* A Plataforma Cívica de inscrição única (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Plataforma Cívica apoia **SP** iniciado SSO





## <a name="adding-civic-platform-from-the-gallery"></a>Adicionar plataforma cívica da galeria

Para configurar a integração da Plataforma Cívica em Azure AD, você precisa adicionar plataforma cívica da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **plataforma cívica** na caixa de pesquisa.
1. Selecione **plataforma cívica** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Plataforma Cívica utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Plataforma Cívica.

Para configurar e testar o Azure AD SSO com plataforma cívica, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a Plataforma Cívica SSO](#configure-civic-platform-sso)** - para configurar as definições de entrada única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador](#create-civic-platform-test-user)** de teste da Plataforma Cívica - para ter uma contrapartida de B.Simon na Plataforma Cívica que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da **Plataforma Cívica,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.accela.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL:`civicplatform.accela.com`

    > [!NOTE]
    > O valor do Signon on URL não é real. Atualize este valor com o sinal real no URL. Contacte a equipa de apoio ao Cliente da [Plataforma Cívica](mailto:skale@accela.com) para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

1. Navegue para > **as inscrições** da App de **Diretório Ativo Azure**em Azure AD, selecione a sua aplicação.

1. Copie o ID do **Diretório (inquilino)** e guarde-o no Bloco de Notas.

    ![Copie o diretório (ID do inquilino) e guarde-o no seu código de aplicações](media/civic-platform-tutorial/directoryid.png)

1. Copie o ID da **aplicação** e guarde-o no Bloco de Notas.

   ![Copiar o ID do pedido (cliente)](media/civic-platform-tutorial/applicationid.png)

1. Navegue para > **as inscrições** da App de **Diretório Ativo Azure**em Azure AD, selecione a sua aplicação. Selecione **Certificados & segredos**.

1. Selecione **segredos de cliente - > Novo segredo do cliente.**

1. Forneça uma descrição do segredo, e uma duração. Quando terminar, selecione **Adicionar**.

   > [!NOTE]
   > Depois de salvar o segredo do cliente, o valor do segredo do cliente é mostrado. Copie este valor porque não é capaz de recuperar a chave mais tarde.

   ![Copie o valor secreto porque não pode recuperar isto mais tarde.](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Configure plataforma cívica SSO

1. Abra uma nova janela do navegador web e inscreva-se no site da empresa Atlassian Cloud como administrador.

1. Clique em **Escolhas Padrão**.

    ![O link de descarregamento do Certificado](media/civic-platform-tutorial/standard-choices.png)

1. Crie uma escolha padrão **ssoconfig**.

1. Procure **ssoconfig** e submeta-se.

    ![O link de descarregamento do Certificado](media/civic-platform-tutorial/sso-config.png)

1. Expanda o SSOCONFIG clicando em ponto vermelho.

    ![O link de descarregamento do Certificado](media/civic-platform-tutorial/sso-config01.png)

1. Fornecer informações de configuração relacionadas com o SSO no seguinte passo:

    ![O link de descarregamento do Certificado](media/civic-platform-tutorial/sso-config02.png)

    1. No campo **de aplicação,** introduza o valor de ID da **aplicação,** que copiou do portal Azure.

    1. No campo **clientSecret,** insira o valor **Secreto,** que copiou do portal Azure.

    1. No campo **directórioId,** insira o valor de ID do **Diretório (inquilino),** que copiou do portal Azure.

    1. Introduza o idpName. Ex:- `Azure`.

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso à Plataforma Cívica.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Plataforma Cívica**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-civic-platform-test-user"></a>Criar o utilizador de teste da Plataforma Cívica

Nesta secção, cria-se um utilizador chamado B.Simon na Plataforma Cívica. Trabalhe com a equipa de apoio da Plataforma Cívica para adicionar os utilizadores na equipa de apoio ao Cliente da [Plataforma Cívica.](mailto:skale@accela.com) Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Plataforma Cívica no Painel de Acesso, deverá ser automaticamente inscrito na Plataforma Cívica para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

