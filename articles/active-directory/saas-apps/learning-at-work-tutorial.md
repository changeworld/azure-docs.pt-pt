---
title: 'Tutorial: Integração do Diretório Ativo Azure com Aprendizagem no Trabalho [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Learning at Work.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d607174-bea1-4f40-8233-54cabe02c66a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3249957fa20c208d0fd06c676200753163d4bfc8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "70376081"
---
# <a name="tutorial-integrate-learning-at-work-with-azure-active-directory"></a>Tutorial: Integrar a aprendizagem no trabalho com o Diretório Ativo Azure

Neste tutorial, aprenderá a integrar a Aprendizagem no Trabalho com o Azure Ative Directory (Azure AD). Quando integrar a Aprendizagem no Trabalho com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Aprendizagem no Trabalho.
* Permita que os seus utilizadores sejam automaticamente inscritos na Learning at Work com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Aprender no Trabalho uma única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Aprendizagem no Trabalho apoia **SP** iniciado SSO

## <a name="adding-learning-at-work-from-the-gallery"></a>Adicionar Aprendizagem no Trabalho a partir da galeria

Para configurar a integração da Aprendizagem no Trabalho em Azure AD, você precisa adicionar Learning at Work da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, escreva **Aprendizagem no Trabalho** na caixa de pesquisa.
1. Selecione **Learning at Work** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Aprendizagem no Trabalho utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Learning at Work.

Para configurar e testar o Azure AD SSO com aprendizagem no trabalho, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar a aprendizagem no Work SSO](#configure-learning-at-work-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. **[Create Learning at Work test user](#create-learning-at-work-test-user)** - para ter uma contrapartida de B.Simon em Aprendizagem no Trabalho que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Learning at Work,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<subdomain>.sabacloud.com/Saba/Web/<company code>`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<subdomain>.sabacloud.com/Saba/saml/SSO/alias/<company name>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de apoio ao [Cliente de Aprendizagem no Trabalho](https://www.learninga-z.com/site/contact/support) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. A aplicação Learning at Work espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos saml. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .**

    Pode atualizar o valor identificador de **nomes** em Azure AD com base na configuração da sua Organização e este valor precisa de corresponder ao ID do **utilizador** na nuvem SABA, pois precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

4. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **Configurar Aprendizagem no Trabalho,** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-learning-at-work-sso"></a>Configurar a aprendizagem no trabalho SSO

Para configurar um único sign-on no lado de **Aprendizagem no Trabalho,** você precisa enviar o descarregamento da **Federação Metadados XML** e URLs copiados apropriados do portal Azure para a equipa de suporte learning [at Work](https://www.learninga-z.com/site/contact/support). Eles definiram esta definição para ter a ligação SAML SSO definida corretamente em ambos os lados

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Aprendizagem no Trabalho.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Learning at Work**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-learning-at-work-test-user"></a>Criar o utilizador de teste de aprendizagem no trabalho

Nesta secção, cria-se um utilizador chamado B.Simon in Learning at Work. Trabalhe com a equipa de suporte learning [at Work](https://www.learninga-z.com/site/contact/support) para adicionar os utilizadores na plataforma Learning at Work. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Learning at Work no Painel de Acesso, deve ser automaticamente inscrito na Aprendizagem no Trabalho para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

