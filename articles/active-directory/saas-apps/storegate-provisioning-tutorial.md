---
title: 'Tutorial: Configure Storegate para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador ao Storegate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 72903a36f88f9092ce1d203b557003083407320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064262"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Tutorial: Configure Storegate para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Storegate e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer e desfornecer automaticamente utilizadores e/ou grupos para o Storegate.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino do Storegate](https://www.storegate.com)
* Uma conta de utilizador num Storegate com permissões do Administrador.

## <a name="assign-users-to-storegate"></a>Atribuir utilizadores ao Storegate

O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Storegate. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Storegate seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Dicas importantes para atribuir utilizadores ao Storegate

* Recomenda-se que um único utilizador da AD Azure seja designado para o Storegate para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Storegate, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-storegate-for-provisioning"></a>Configurar o Storegate para o fornecimento

Antes de configurar o Storegate para o fornecimento automático de utilizadores com o Azure AD, terá de recuperar algumas informações de fornecimento da Storegate.

1. Inscreva-se na [consola de administrador storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e navegue para as definições clicando no ícone do utilizador no canto superior direito e selecione **Definições**de Conta .

    ![Storegate Adicionar SCIM](media/storegate-provisioning-tutorial/admin.png)

2. Dentro das definições, navegue para **as Definições do Team >** e verifique se o interruptor de alternância está ligado na secção de **sinalização single.**

    ![Definições do Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Botão de alternância storegate](media/storegate-provisioning-tutorial/sso.png)

3. Copie o URL do **Inquilino** e **token**. Estes valores serão inscritos nos campos URL do **Tenant** e **Secret Token,** respectivamente, no separador de fornecimento da sua aplicação Storegate no portal Azure. 

    ![Storegate Criar Token](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Adicione Storegate da galeria

Para configurar o Storegate para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o Storegate da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **o Storegate,** selecione **Storegate** no painel de resultados. 

    ![Storegate na lista de resultados](common/search-new-app.png)

5. Selecione o **'sign-up' para** o botão Storegate, que o redireciona para a página de login do Storegate. 

    ![Storegate OIDC Add](media/storegate-provisioning-tutorial/signup.png)

6.  Inscreva-se na [consola de administrador storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e navegue para as definições clicando no ícone do utilizador no canto superior direito e selecione **Definições**de Conta .

    ![Login do Storegate](media/storegate-provisioning-tutorial/admin.png)

7. Dentro das definições, navegar á **Definições de > de Equipa** e clicar no interruptor de alternância na secção de início de sinal único, isto iniciará o fluxo de consentimento. Clique em **Ativar**.

    ![Equipa storegate](media/storegate-provisioning-tutorial/team.png)

    ![Storegate sso](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate ativar](media/storegate-provisioning-tutorial/activate.png)

8. Como o Storegate é uma aplicação OpenIDConnect, opte por iniciar sessão no Storegate utilizando a sua conta de trabalho da Microsoft.

    ![Login Storegate OIDC](media/storegate-provisioning-tutorial/login.png)

9. Após uma autenticação bem sucedida, aceite o pedido de consentimento para a página de consentimento. O pedido será adicionado automaticamente ao seu inquilino e será redirecionado para a sua conta Storegate.

    ![Storegate OIDc Consent](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Configure o fornecimento automático de utilizadores ao Storegate 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Storegate com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto final do SCIM da Storegate, consulte [isto](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Storegate em Azure AD

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Storegate**.

    ![O link Storegate na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://dialpad.com/scim` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Insera o valor que recuperou e salvou mais cedo do Storegate em **Secret Token**. Clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se ao Storegate. Se a ligação falhar, certifique-se de que a sua conta Storegate tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Storegate**.

    ![Mapeamento de utilizador storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Storegate na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Storegate para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador do Storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para o Storegate, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja fornecer ao Storegate, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no Storegate.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
