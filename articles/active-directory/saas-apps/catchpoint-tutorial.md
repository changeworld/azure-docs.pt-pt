---
title: 'Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com catchpoint'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Catchpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b19e286d299811a950df05f93d221bd710676ea
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743492"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Tutorial: Azure Ative Directory integração individual de assinatura com Catchpoint

Neste tutorial, aprende-se a integrar o Catchpoint com o Azure Ative Directory (Azure AD). Quando integrar o Catchpoint com o Azure AD, pode:

* Controle o acesso do utilizador ao Catchpoint a partir de Azure AD.
* Ative o início automático de entrada no Catchpoint para utilizadores com contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de apps saaS com a Azure AD, veja [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição catchpoint com um único sinal (SSO) ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* O Catchpoint suporta o SSO iniciado por SP e o SSO iniciado pelo IDP.
* O Catchpoint suporta o fornecimento de utilizadores just-in-time (JIT).
* Depois de configurar o Catchpoint, pode impor o controlo da sessão. Esta precaução protege contra a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão é uma extensão do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="add-catchpoint-from-the-gallery"></a>Adicione Catchpoint da galeria

Para configurar a integração do Catchpoint em Azure AD, adicione o Catchpoint à sua lista de aplicações SaaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de trabalho, escola ou Microsoft pessoal.
1. No painel esquerdo, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Catchpoint** na caixa de pesquisa.
1. Selecione **Catchpoint** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Configure e teste Azure AD único sign-on para Catchpoint

Para que o SSO funcione, é necessário ligar um utilizador da AD Azure a um utilizador em Catchpoint. Para este tutorial, vamos configurar um utilizador de teste chamado **B.Simon**. 

Complete as seguintes secções:

1. [Configure O SSO AD Azure,](#configure-azure-ad-sso)para ativar esta funcionalidade para os seus utilizadores.
    * [Crie um utilizador de teste Azure AD,](#create-an-azure-ad-test-user)para testar o único signo da Azure AD com b.Simon.
    * Atribuir o utilizador de [teste Azure AD,](#assign-the-azure-ad-test-user)para permitir que a B.Simon utilize um único sinal de AD Azure.
1. [Configure o Catchpoint SSO,](#configure-catchpoint-sso)para configurar as definições de início de sessão únicas no lado da aplicação.
    * [Crie o utilizador de teste Catchpoint,](#create-a-catchpoint-test-user)para permitir a ligação da conta de teste DaD B.Simon Azure a uma conta de utilizador semelhante em Catchpoint.
1. [Teste SSO,](#test-sso)para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos no portal Azure para permitir o Azure AD SSO:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Na página de integração da aplicação **Catchpoint,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set Up Single Sign-On com** a página SAML, selecione o ícone da caneta para editar as definições básicas de **configuração do SAML.**

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Configure o modo iniciado para catchpoint:
   - Para o modo **idp-iniciado,** introduza os valores para os seguintes campos:
     - Para **identificador:**`https://portal.catchpoint.com/SAML2`
     - Para **url de resposta:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - Para o modo **sp-iniciado,** selecione **Definir URLs adicionais** e introduzir o seguinte valor:
     - Para **URL de iniciar sessão:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. A aplicação Catchpoint espera as afirmações do SAML num formato específico. Adicione mapeamentos de atributos personalizados à sua configuração de atributos token SAML. A tabela seguinte contém a lista de atributos predefinidos:

    | Nome | Atributo de origem|
    | ------------ | --------- |
    | Nome dado | user.givenneame |
    | Apelido | utilizador.sobrenome |
    | Endereço de e-mail | utilizador.mail |
    | Nome | user.userprincipalname |
    | Identificador único de utilizador | user.userprincipalname |

    ![Imagem de lista de atributos do utilizador &](common/default-attributes.png)

1. Além disso, o pedido Catchpoint espera que outro atributo seja aprovado numa resposta SAML. Consulte a tabela seguinte. Este atributo também é pré-povoado, mas pode revê-lo e atualizá-lo de acordo com os seus requisitos.

    | Nome | Atributo de origem|
    | ------------ | --------- |
    | espaço de nomes | user.atribuídorole |

    > [!NOTE]
    > A `namespace` reclamação tem de ser mapeada com o nome da conta. Este nome de conta deve ser criado com um papel na AD Azure a ser retransmitido na resposta saml. Para obter mais informações sobre papéis em Azure AD, consulte [Configure a alegação de função emitida no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Vá ao set up single sign-on com a página **SAML.** Na secção certificado de **assinatura SAML,** encontre **certificado (Base64)**. Selecione **Download** para guardar o certificado para o seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **'Catchpoint' de configuração,** copie os URLs de que necessita num passo posterior.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, utiliza-se o portal Azure para criar um utilizador de teste Azure AD chamado B.Simon.

1. A partir do painel esquerdo no portal Azure,**Users** > selecione **Utilizadores de Diretório** > Ativo Azure**Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, introduza `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe Mostrar.** Note o valor da palavra-passe visualizada.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a B.Simon utilize um único sign-on Azure, concedendo acesso ao Catchpoint.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações**.
1. Na lista de aplicações, selecione **Catchpoint**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![O link "Adicionar utilizador"](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Clique em **Selecionar** na parte inferior do ecrã.
1. Se espera um valor de papel na afirmação do SAML, procure na caixa de diálogo **Select Role** e escolha o papel do utilizador na lista. Clique no botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

## <a name="configure-catchpoint-sso"></a>Configure Catchpoint SSO

1. Numa janela de navegador web diferente, inscreva-se na aplicação Catchpoint como administrador.

1. Selecione o ícone **Definições** e, em seguida, **SSO Identity Provider**.

    ![Screenshot de definições de ponto de captura com Fornecedor de Identidade SSO selecionado](./media/catchpoint-tutorial/configuration1.png)

1. Na página **Single Sign On,** introduza os seguintes campos:

   ![Sinal único do ponto de captura na imagem da página](./media/catchpoint-tutorial/configuration2.png)

   Campo | Valor
   ----- | ----- 
   **Espaço de nome** | Um valor de espaço de nome válido.
   **Emitente de Fornecedor de Identidade** | O `Azure AD Identifier` valor do portal Azure.
   **Sinal único na url** | O `Login URL` valor do portal Azure.
   **Certificado** | O conteúdo do `Certificate (Base64)` ficheiro descarregado do portal Azure. Utilize o Bloco de Notas para visualizar e copiar.

   Também pode carregar os **Metadados da Federação XML** selecionando a opção **Deenvio de Metadados.**

1. Selecione **Guardar**.

### <a name="create-a-catchpoint-test-user"></a>Criar um utilizador de teste catchpoint

O Catchpoint suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não tem objetos de ação nesta secção. Se a B.Simon já não existir como utilizador em Catchpoint, é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de Acesso AD Azure utilizando o portal My Apps.

Ao selecionar o azulejo Catchpoint no portal My Apps, deverá ser automaticamente inscrito na aplicação Catchpoint com o SSO configurado. Para mais informações sobre o portal My Apps, consulte [o Sign in e inicie aplicações a partir do portal My Apps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

> [!NOTE]
> Quando estiver inscrito na aplicação Catchpoint através da página de login, depois de fornecer **credenciais**de ponto de captura, introduza o valor **de espaço** de nome válido no campo **credenciais da empresa (SSO)** e selecione **Login**.
> 
> ![Configuração do ponto de captura](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações do SaaS com diretório ativo azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Catchpoint com a AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
