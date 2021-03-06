---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com software Maxient Conduct Manager [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Maxient Conduct Manager Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85e71b76-cac3-4ce6-a35f-796d2cb7bdb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1a657a7d57b3e725b0ae92b5110935c0aecf73f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75533854"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com software maxient Conduct Manager

Neste tutorial, você vai aprender a integrar o Software Maxient Conduct Manager com o Azure Ative Directory (Azure AD). Quando integrar o Software Maxient Conduct Manager com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Software Maxient Conduct Manager.
* Ative que os seus utilizadores sejam automaticamente inscritos no Software Maxient Conduct Manager com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Maxient Conduct Manager Software uma subscrição única ativada por si.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.



* Maxient Conduct Manager Software suporta **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Adicionar software maxient conduct manager da galeria

Para configurar a integração do Software Maxient Conduct Manager em Azure AD, precisa adicionar software maxient Conduct Manager da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** digite o **Software Maxient Conduct Manager** na caixa de pesquisa.
1. **Selecione Software Maxient Conduct Manager** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Configure e teste Azure AD single sign-on for Maxient Conduct Manager Software

Configure e teste Azure AD SSO com Software Maxient Conduct Manager utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Software Maxient Conduct Manager.

Para configurar e testar o Azure AD SSO com o Software Maxient Conduct Manager, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Software SSO](#configure-maxient-conduct-manager-software-sso)** do Gestor de Conduta Maxient - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. Crie o utilizador de teste de **[software Maxient Conduct Manager](#create-maxient-conduct-manager-software-test-user)** - para ter uma contrapartida de B.Simon no Software Maxient Conduct Manager que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do **Maxient Conduct Manager Software,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção de  **Configuração Básica saml,** a aplicação está pré-configurada no modo iniciado **idp** e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.** 

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de sign-on real. Contacte a equipa de suporte do Cliente do Cliente do Gestor de [Conduta Maxient](mailto:support@maxient.com) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on do Azure, concedendo acesso ao Software Maxient Conduct Manager.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Maxient Conduct Manager Software**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-maxient-conduct-manager-software-sso"></a>Configure Maxient Conduct Manager Software SSO

Para configurar um único login no lado do **Software Maxient Conduct Manager,** você precisa enviar o Url de **Metadados da Federação de Aplicações** para a equipa de suporte de [software Maxient Conduct Manager](mailto:support@maxient.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-maxient-conduct-manager-software-test-user"></a>Criar o utilizador de teste de software Maxient Conduct Manager

Nesta secção, cria-se um utilizador chamado Britta Simon no Software Maxient Conduct Manager. Trabalhe com a equipa de suporte do [Software Maxient Conduct Manager](mailto:support@maxient.com) para adicionar os utilizadores na plataforma Maxient Conduct Manager Software. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo maxient Conduct Manager Software no Painel de Acesso, deve ser automaticamente inscrito no Software Maxient Conduct Manager para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente software maxient Conduct Manager com Azure AD](https://aad.portal.azure.com/)

