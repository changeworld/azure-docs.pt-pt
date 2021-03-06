---
title: 'Tutorial: Integração do Diretório Ativo Azure com proMaster (por Inlogik) [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ProMaster (por Inlogik).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 348dbd37-dc4f-49df-bb90-53d249d456b3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: b48d11e15d452b72426c5b83d387f9e004e95dc7
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80293933"
---
# <a name="tutorial-azure-active-directory-integration-with-promaster-by-inlogik"></a>Tutorial: Integração de Diretório Ativo Azure com proMaster (por Inlogik)

Neste tutorial, aprende-se a integrar o ProMaster (por Inlogik) com o Azure Ative Directory (Azure AD).
Integrar o ProMaster (por Inlogik) com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao ProMaster (por Inlogik).
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na ProMaster (by Inlogik) (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o ProMaster (por Inlogik), precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* ProMaster (por Inlogik) assinatura única ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* ProMaster (por Inlogik) suporta **SP** e **IDP** iniciadoS SSO
* Assim que configurar o ProMaster (por Inlogik) pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-promaster-by-inlogik-from-the-gallery"></a>Adicionar ProMaster (por Inlogik) da galeria

Para configurar a integração do ProMaster (por Inlogik) no Azure AD, precisa de adicionar proMaster (por Inlogik) da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **ProMaster (por Inlogik)** na caixa de pesquisa.
1. Selecione **ProMaster (por Inlogik)** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com proMaster (por Inlogik) com base num utilizador de teste chamado **B.Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no ProMaster (da Inlogik).

Para configurar e testar o único sign-on azure ad com proMaster (por Inlogik), você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o ProMaster (por Inlogik) SSO](#configure-promaster-by-inlogik-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create ProMaster (by Inlogik) test user](#create-promaster-by-inlogik-test-user)** - para ter uma contraparte de B.Simon em ProMaster (por Inlogik) que está ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com proMaster (por Inlogik), execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **ProMaster (by Inlogik),** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | |
    |-|-|
    | `https://secure.inlogik.com/<COMPANYNAME>`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE`|
    | |

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | |
    |-|-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:

    | |
    |-|-|
    | `https://secure.inlogik.com/<COMPANYNAME>`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE`|
    | |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de suporte ao [cliente ProMaster (by Inlogik)](https://www.inlogik.com/contact) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao ProMaster (por Inlogik).

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **ProMaster (por Inlogik)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-promaster-by-inlogik-sso"></a>Configure ProMaster (por Inlogik) SSO

Para configurar um único sign-on no lado **ProMaster (por Inlogik),** precisa enviar o Url de **Metadados da Federação de Aplicações** para a equipa de [suporte ProMaster (by Inlogik).](https://www.inlogik.com/contact) Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-promaster-by-inlogik-test-user"></a>Criar o utilizador de teste ProMaster (por Inlogik)

Nesta secção, cria-se um utilizador chamado B.Simon no ProMaster (por Inlogik). Trabalhe com a equipa de [suporte ProMaster (da Inlogik)](https://www.inlogik.com/contact) para adicionar os utilizadores na plataforma ProMaster (by Inlogik). Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ProMaster (por Inlogik) no Painel de Acesso, deve ser automaticamente inscrito no ProMaster (por Inlogik) para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente proMaster (por Inlogik) com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o ProMaster (por Inlogik) com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
