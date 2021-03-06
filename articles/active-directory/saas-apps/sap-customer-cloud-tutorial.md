---
title: 'Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a SAP Cloud para cliente [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAP Cloud para o Cliente.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837787d375a7570b7daf0a149960ca0020bcdced
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72264040"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a SAP Cloud para cliente

Neste tutorial, você vai aprender a integrar a Nuvem SAP para Cliente com O Diretório Ativo Azure (Azure AD). Quando integrar a Nuvem SAP para Cliente com Anúncio Azure, pode:

* Controlo em Azure AD que tem acesso a SAP Cloud para Cliente.
* Ative que os seus utilizadores sejam automaticamente inscritos na SAP Cloud para cliente com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SAP Cloud para assinatura única (SSO) ativada pelo Cliente.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* SAP Cloud para suportes ao cliente **SP** iniciadoS SSO

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Adicionar nuvem SAP para cliente da galeria

Para configurar a integração da Nuvem SAP para Cliente em Azure AD, você precisa adicionar SAP Cloud para Cliente da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **SAP Cloud para Cliente** na caixa de pesquisa.
1. Selecione **SAP Cloud para Cliente** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Configure e teste Azure AD single sign-on para SAP Cloud para Cliente

Configure e teste Azure AD SSO com SAP Cloud para Cliente utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Nuvem SAP para o Cliente.

Para configurar e testar o Azure AD SSO com a Nuvem SAP para cliente, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure a Nuvem SAP para O SSO](#configure-sap-cloud-for-customer-sso)** do Cliente - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. **[Crie](#create-sap-cloud-for-customer-test-user)** a Nuvem SAP para o utilizador do teste do Cliente - para ter uma contrapartida de B.Simon na Nuvem SAP para cliente que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da SAP Cloud para o Cliente,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<server name>.crm.ondemand.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a Equipa de Apoio ao [Cliente da SAP Cloud para](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação SAP Cloud for Customer espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo de Atributos do Utilizador.

    ![image](common/edit-attribute.png)

1. Na secção **Atributos** do Utilizador no diálogo **de Atributos do Utilizador & Reclamações,** execute os seguintes passos:

    a. Clique no **ícone Editar** para abrir o diálogo de reivindicações do **utilizador Gerir.**

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Selecione **a Transformação** como **fonte**.

    c. Na lista **de Transformação,** selecione **ExtractMailPrefix()**.

    d. A partir da lista **parametómetro 1,** selecione o atributo do utilizador que pretende utilizar para a sua implementação.
    Por exemplo, se pretender utilizar o EmployeeID como identificador único do utilizador e tiver armazenado o valor do atributo no ExtensionAttribute2, então selecione user.extensionattribute2.

    e. Clique em **Guardar**.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na **secção SAP Cloud para cliente,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso à Nuvem SAP para cliente.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SAP Cloud para Cliente**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-sap-cloud-for-customer-sso"></a>Configure a Nuvem SAP para O SSO do Cliente

1. Abra uma nova janela do navegador web e inscreva-se no seu site da empresa SAP Cloud para o cliente como administrador.

2. Do lado esquerdo do menu, clique em **Fornecedores** > de**Identidade Corporativa Fornecedores** > de Identidade**Adicionar** e no pop-up adicione o nome do fornecedor de identidade como **Azure AD,** clique em **Guardar** e clique em **Configuração SAML 2.0**.

    ![Configuração SAP](./media/sap-customer-cloud-tutorial/configure01.png)

3. Na secção **de configuração SAML 2.0,** execute os seguintes passos:

    ![Configuração SAP](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Clique em **Navegar** para carregar o ficheiro Federation Metadata XML, que descarregou do portal Azure.

    b. Assim que o ficheiro XML for carregado com sucesso, os valores abaixo serão automaticamente povoados automaticamente e clicarem em **Guardar**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Criar nuvem SAP para o utilizador de teste do cliente

Para permitir que os utilizadores de Anúncios Azure insinuem na SAP Cloud para cliente, devem ser aprovisionados na Cloud SAP para o Cliente. Na Nuvem SAP para Cliente, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na SAP Cloud para cliente como administrador de segurança.

2. From the left side of the menu, click on **Users & Authorizations** > **User Management** > **Add User**.

    ![Configuração SAP](./media/sap-customer-cloud-tutorial/configure03.png)

3. Na secção **Adicionar Novo Utilizador,** execute os seguintes passos:

    ![Configuração SAP](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Na caixa de texto **First Name,** introduza o nome do utilizador como **B**.

    b. Na caixa de texto **De Apelido,** introduza o nome de utilizador como **Simon**.

    c. Na caixa de texto **e-mail,** `B.Simon@contoso.com`introduza o e-mail do utilizador como .

    d. Na caixa de texto **Sin Name,** introduza o nome do utilizador como **B.Simon**.

    e. Selecione **User Type** de acordo com o seu requisito.

    f. Selecione a opção **de ativação da conta** de acordo com o seu requisito.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar na nuvem SAP para azulejos do Cliente no Painel de Acesso, deverá ser automaticamente inscrito na Cloud SAP para cliente para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Nuvem SAP para cliente com a AD Azure](https://aad.portal.azure.com/)

