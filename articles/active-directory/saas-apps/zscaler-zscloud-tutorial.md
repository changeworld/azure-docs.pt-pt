---
title: 'Tutorial: Integração do Diretório Ativo Azure com zscaler ZSCloud [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zscaler ZSCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 43d7e58f0c267afe8a22c217d9800abb041df8cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68723065"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Tutorial: Integração do Diretório Ativo Azure com zscaler ZSCloud

Neste tutorial, aprende-se a integrar o Zscaler ZSCloud com o Azure Ative Directory (Azure AD).
Integrar zscaler ZSCloud com Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Zscaler ZSCloud.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Zscaler ZSCloud (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Zscaler ZSCloud, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de sinal único Zscaler ZSCloud

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Zscaler ZSCloud suporta **SP** iniciado SSO

* Zscaler ZSCloud suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Adicionando Zscaler ZSCloud da galeria

Para configurar a integração do Zscaler ZSCloud em Azure AD, precisa adicionar Zscaler ZSCloud da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Zscaler ZSCloud da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Zscaler ZSCloud,** selecione **Zscaler ZSCloud** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Zscaler ZSCloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com zscaler ZSCloud com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Zscaler ZSCloud.

Para configurar e testar o único sinal de Azure AD com zscaler ZSCloud, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SumO De Entrada Única Zscaler ZSCloud](#configure-zscaler-zscloud-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de **[teste Zscaler ZSCloud](#create-zscaler-zscloud-test-user)** - para ter uma contrapartida da Britta Simon no Zscaler ZSCloud que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com zscaler ZSCloud, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Zscaler ZSCloud,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Zscaler ZSCloud Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **smS Do URL Sign-on,** escreva o URL utilizado pelos seus utilizadores para iniciar sessão na sua aplicação ZScaler ZSCloud.

    > [!NOTE]
    > Tem de atualizar o valor com o URL de Inscrição real. Contacte a equipa de suporte ao [Cliente Zscaler ZSCloud](https://help.zscaler.com/) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. A sua aplicação Zscaler ZSCloud espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

6. Além de acima, a aplicação Zscaler ZSCloud espera que poucos atributos sejam retransmitidos na resposta SAML. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, execute os seguintes passos para adicionar atributo token SAML, conforme indicado no quadro abaixo:
    
    | Nome | Atributo fonte |
    | ---------| ------------ |
    | membroOf     | user.atribuídos |

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Deixe o espaço de **nome em** branco.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.
    
    f. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) para saber como configurar o papel em Azure AD

7. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

8. Na secção **'Configurar ZScaler ZSCloud',** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-zscaler-zscloud-single-sign-on"></a>Configure Zscaler ZSCloud Single Sign-On

1. Para automatizar a configuração dentro do Zscaler ZSCloud, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Zscaler ZSCloud** irá direcioná-lo para a aplicação Zscaler ZSCloud. A partir daí, forneça as credenciais de administração para assinar no Zscaler ZSCloud. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração soo](common/setup-sso.png)

3. Se pretender configurar o Zscaler ZSCloud manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Zscaler ZSCloud como administrador e execute os seguintes passos:

4. Vá à **Administração > Autenticação > Definições** de Autenticação e execute os seguintes passos:
   
    ![Administration](./media/zscaler-zscloud-tutorial/ic800206.png "Administração")

    a. No tipo de autenticação, escolha **SAML**.

    b. Clique **em Configurar SAML**.

5. Na janela **Edit SAML,** execute os seguintes passos: e clique em Guardar.  
            
    ![Gerir utilizadores & autenticação](./media/zscaler-zscloud-tutorial/ic800208.png "Gerir utilizadores & autenticação")
    
    a. Na caixa de texto URL do **Portal SAML,** colá o URL de **login** que copiou do portal Azure.

    b. Na caixa de texto 'Atribuição de **Nome de login',** introduza **o NameID**.

    c. Clique no **Upload**, para fazer upload do certificado de assinatura Azure SAML que descarregou do portal Azure no **Certificado SSL Público**.

    d. Alternar o **fornecimento automático Enable SAML**.

    e. Na caixa de texto do nome do **utilizador,** introduza o nome do **ecrã** se pretender ativar o fornecimento automático de SAML para atributos displayName.

    f. Na caixa de texto de atribuição de nome de **grupo,** introduza **o membroSe** se pretender ativar o fornecimento automático sAML para membros De atributos.

    g. No **departamento** de atribuição de nome de **departamento,** se pretender ativar o fornecimento automático de SAML para atributos do departamento.

    h. Clique em **Guardar**.

6. Na página de diálogo de autenticação do **utilizador Configurar,** execute os seguintes passos:

    ![Administração](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Paire sobre o menu **de ativação** perto do canto inferior esquerdo.

    b. Clique em **Ativar**.

## <a name="configuring-proxy-settings"></a>Definir configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para configurar as definições de procuração no Internet Explorer

1. Inicie **o Internet Explorer**.

2. Selecione **opções** de Internet a partir do menu **Ferramentas** para abrir o diálogo internet **Options.**   
    
     ![Opções de Internet](./media/zscaler-zscloud-tutorial/ic769492.png "Opções de Internet")

3. Clique no separador **Ligações.**   
  
     ![Ligações](./media/zscaler-zscloud-tutorial/ic769493.png "Ligações")

4. Clique nas **definições lan** para abrir o diálogo lan **Definições.**

5. Na secção proxy do servidor, execute os seguintes passos:   
   
    ![Servidor proxy](./media/zscaler-zscloud-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **Utilize um servidor proxy para o seu LAN**.

    b. Na caixa de texto 'Endereço', digite **o gateway. Zscaler ZSCloud.net.**

    c. Na caixa de texto portuário, tipo **80**.

    d. Selecione **servidor de procuração bypass para endereços locais**.

    e. Clique **em OK** para fechar o diálogo de **definições da Rede local (LAN).**

6. Clique **em OK** para fechar o diálogo das Opções de **Internet.**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo brittasimon@yourcompanydomain.extensionde campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a Britta Simon utilize um único sign-on Azure, concedendo acesso ao Zscaler ZSCloud.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Zscaler ZSCloud**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler ZSCloud**.

    ![O link Zscaler ZSCloud na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. No diálogo **Utilizadores e grupos,** selecione o utilizador como **Britta Simon** da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. A partir do diálogo **Select Role,** escolha a função de utilizador apropriada na lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. No diálogo **adicionar atribuição,** selecione o botão **Atribuir.**

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

    >[!NOTE]
    >A função de acesso predefinido não é suportada, uma vez que esta irá quebrar o fornecimento, pelo que a função predefinida não pode ser selecionada durante a atribuição do utilizador.

### <a name="create-zscaler-zscloud-test-user"></a>Criar o utilizador de teste Zscaler ZSCloud

Nesta secção, um utilizador chamado Britta Simon é criado no Zscaler ZSCloud. O Zscaler ZSCloud suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador ainda não existir no Zscaler ZSCloud, um novo é criado após a autenticação.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte Zscaler ZSCloud](https://help.zscaler.com/).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Zscaler ZSCloud no Painel de Acesso, deve ser automaticamente inscrito no Zscaler ZSCloud para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

