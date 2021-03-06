---
title: 'Tutorial: Configure as minhas Políticas para o fornecimento automático de utilizadores com diretório ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo azure para fornecer automaticamente e desfornecer contas de utilizadores para as minhas Políticas.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f000896d-a78c-4d20-a79c-74c1f9b4961a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 353da826b6e339d40a5d85bbf63caac5bf7094f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061374"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Tutorial: Configure as minhas Políticas para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados nas minhas Políticas e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos para as minhas Políticas.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino myPolicies.](https://mypolicies.com/index.html#section10)
* Uma conta de utilizador nas minhas Políticas com permissões admin.

## <a name="assigning-users-to-mypolicies"></a>Atribuir utilizadores às minhas Políticas

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso às minhas Políticas. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos às minhas Políticas seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Dicas importantes para atribuir utilizadores às minhas Políticas

* Recomenda-se que um único utilizador da AD Azure seja atribuído às minhas Políticas para testar a configuração automática de fornecimento de utilizadores. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador às minhas Políticas, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-mypolicies-for-provisioning"></a>Configurar as minhas Políticas para o provisionamento

Antes de configurar as minhas Políticas para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM nas minhas Políticas.

1. Contacte o seu representante **support@mypolicies.com** de myPolicies para obter o símbolo secreto necessário para configurar o fornecimento de SCIM.

2.  Guarde o valor simbólico fornecido pelo representante das myPolicies. Este valor será inserido no campo **Secret Token** no separador de fornecimento da sua aplicação myPolicies no portal Azure.

## <a name="add-mypolicies-from-the-gallery"></a>Adicione as minhas Políticas da galeria

Para configurar as minhas Políticas de fornecimento automático de utilizadores com a AD Azure, é necessário adicionar as minhas Políticas da galeria de aplicações Azure AD à sua lista de aplicações saaS geridas.

**Para adicionar as minhas Políticas à galeria de aplicações da AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **as minhas Políticas,** selecione **myPolicies** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![myPolicies na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Configurar o fornecimento automático de utilizadores às minhas Políticas 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos nas myPolicies com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sign-on baseado em SAML para as minhas Políticas, seguindo as instruções fornecidas no [tutorial de inscrição single](mypolicies-tutorial.md)políticas myPolicies . O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para as minhas Políticas em Anúncio salé-o:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **myPolicies**.

    ![O link myPolicies na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. De acordo com a secção `https://<myPoliciesCustomDomain>.mypolicies.com/scim` **de Credenciais de Administrador,** insere-se no URL do **Inquilino,** onde `<myPoliciesCustomDomain>` está o seu domínio personalizado myPolicies. Pode recuperar o domínio do cliente myPolicies, a partir do seu URL.
Exemplo: `<demo0-qa>`.mypolicies.com.

6. Em **Secret Token,** insira o valor simbólico que foi recuperado anteriormente. Clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se às minhas Políticas. Se a ligação falhar, certifique-se de que a sua conta myPolicies tem permissões de Administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to myPolicies**.

    ![myPolicies User Mappings](media/mypolicies-provisioning-tutorial/usermapping.png)

10. Reveja os atributos do utilizador que são sincronizados desde o Azure AD até às minhas Políticas na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder às contas do utilizador nas minhas Políticas para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![myPolicies User Mappings](media/mypolicies-provisioning-tutorial/userattribute.png)

11. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de provisionamento de AD Azure para as minhas Políticas, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

13. Defina os utilizadores e/ou grupos que deseja fornecer às minhas Políticas, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure nas minhas Políticas.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* myPolicies requer sempre **o nome do utilizador,** **o e-mail** e **o id externo**.
* myPolicies não suporta eliminações duras para atributos do utilizador.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
