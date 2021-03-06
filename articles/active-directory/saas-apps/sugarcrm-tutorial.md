---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Sugar CRM [ Integração de assinaturas únicas do Diretório Ativo azure com o Sugar CRM ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fae7b80fd4d2fcec32bbef5e4cdf18e576412a86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74231967"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com O CRM de Açúcar

Neste tutorial, você vai aprender a integrar o Sugar CRM com o Azure Ative Directory (Azure AD). Quando integrar o Sugar CRM com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a CRM de açúcar.
* Ative que os seus utilizadores sejam automaticamente inscritos no Sugar CRM com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura única de inscrição ativada por CRM de açúcar (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Sugar CRM suporta **SP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-sugar-crm-from-the-gallery"></a>Adicionar CRM de açúcar da galeria

Para configurar a integração do Sugar CRM em Azure AD, você precisa adicionar Sugar CRM da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Sugar CRM** na caixa de pesquisa.
1. Selecione **Sugar CRM** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Configure e teste Azure AD single sign-on para Sugar CRM

Configure e teste Azure AD SSO com CRM de açúcar utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em SUGAR CRM.

Para configurar e testar o Azure AD SSO com o Sugar CRM, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Sugar CRM SSO](#configure-sugar-crm-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador](#create-sugar-crm-test-user)** de teste de CRM sugar - para ter uma contrapartida de B.Simon em SUGAR CRM que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **sugar CRM,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.eu/<companyname>`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Inscrição e Resposta real. Contacte a equipa de apoio ao [Cliente Sugar CRM](https://support.sugarcrm.com/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **De Configuração** de CRM de Açúcar, copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Sugar CRM.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Sugar CRM**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-sugar-crm-sso"></a>Configure Sugar CRM SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Sugar CRM como administrador.

1. Vai ao **Administrador.**

    ![Administração](./media/sugarcrm-tutorial/ic795888.png "Administrador")

1. Na secção **Administração,** clique em **Gestão de Passwords**.

    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administração")

1. **Selecione ativar a autenticação SAML**.

    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administração")

1. Na secção **de Autenticação SAML,** execute os seguintes passos:

    ![Autenticação SAML](./media/sugarcrm-tutorial/ic795891.png "Autenticação SAML")  

    a. Na caixa de texto **login URL,** cola o valor do URL de **Login,** que copiou do portal Azure.
  
    b. Na caixa de texto **slo URL,** cola o valor do URL de **Logout,** que copiou do portal Azure.
  
    c. Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de recção e, em seguida, cole todo o Certificado na caixa de texto **X.509 Certificate.**
  
    d. Clique em **Guardar**.

### <a name="create-sugar-crm-test-user"></a>Criar o utilizador de teste DE CRM de açúcar

Para permitir que os utilizadores da AD Azure assinem o Sugar CRM, devem ser provisionados ao Sugar CRM. No caso do Sugar CRM, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa **Sugar CRM** como administrador.

1. Vai ao **Administrador.**

    ![Administração](./media/sugarcrm-tutorial/ic795888.png "Administrador")

1. Na secção **Administração,** clique em **Gestão de Utilizadores**.

    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administração")

1. Vá para **os Utilizadores \> Criar novo Utilizador**.

    ![Criar novo utilizador](./media/sugarcrm-tutorial/ic795894.png "Criar novo utilizador")

1. No separador Perfil do **Utilizador,** execute os seguintes passos:

    ![Novo Utilizador](./media/sugarcrm-tutorial/ic795895.png "Novo Utilizador")

    * Digite o nome de **utilizador,** **apelido,** e endereço de **e-mail** de um utilizador de Diretório Ativo Azure válido nas caixas de texto relacionadas.
  
1. As **Status,** selecione **Ative**.

1. No separador Password, execute os seguintes passos:

    ![Novo Utilizador](./media/sugarcrm-tutorial/ic795896.png "Novo Utilizador")

    a. Digite a palavra-passe na caixa de texto relacionada.

    b. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador de Sugar CRM ou APIs fornecidas pela Sugar CRM para fornecer contas de utilizador azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SUGAR CRM no Painel de Acesso, deve ser automaticamente inscrito no CRM de açúcar para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Sugar CRM com AD Azure](https://aad.portal.azure.com/)

