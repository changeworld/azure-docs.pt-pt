---
title: 'Tutorial: Configure o fornecimento automático de utilizadores figma com diretório ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador à Figma.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: a50f1c81f5eda78ee6834aba3085f685c197b4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057962"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Tutorial: Configure Figma para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Figma e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer e desfornecer automaticamente utilizadores e/ou grupos à Figma.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino da Figma.](https://www.figma.com/pricing/)
* Uma conta de utilizador em Figma com permissões de administrador.

## <a name="assign-users-to-figma"></a>Atribuir utilizadores à Figma.
O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Figma. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à Figma seguindo as instruções aqui:
 
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Dicas importantes para atribuir utilizadores à Figma

 * Recomenda-se que um único utilizador da AD Azure seja atribuído à Figma para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador à Figma, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento.

## <a name="set-up-figma-for-provisioning"></a>Configurar a Figma para o provisionamento

Antes de configurar a Figma para o fornecimento automático de utilizadores com a AD Azure, terá de obter algumas informações de provisionamento da Figma.

1. Inscreva-se na consola [Figma Admin](https://www.Figma.com/). Clique no ícone de engrenagem ao lado do seu inquilino.

    ![FigmaFigma-empregado-prestação](media/Figma-provisioning-tutorial/image0.png)

2. Navegue para o Registo de **Atualização de > Geral nas Definições**.

    ![FigmaFigma-empregado-prestação](media/Figma-provisioning-tutorial/figma03.png)

3. Copie a identificação do **inquilino.** Este valor será utilizado para a construção do URL de ponto final do SCIM a ser introduzido no campo URL do **Arrendatário** no separador provisionamento da sua aplicação Figma no portal Azure.

    ![Figma Criar Símbolo](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Desloque-se para baixo e clique em **Generate API Token**.

    ![Figma Criar Símbolo](media/Figma-provisioning-tutorial/token.png)

5. Copie o valor **do Token DaPi.** Este valor será inserido no campo **Secret Token** no separador de provisionamento da sua aplicação Figma no portal Azure. 

    ![Figma Criar Símbolo](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Adicione Figma da galeria

Para configurar a Figma para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar figma da galeria de aplicações Azure AD à sua lista de aplicações geridas do SaaS.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **figma,** selecione **Figma** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Figma na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Configurar o fornecimento automático de utilizadores à Figma 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos na Figma com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para figma, seguindo as instruções fornecidas no tutorial de [sinalização Figma Single](figma-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para figma em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Figma**.

    ![O link Figma na lista de Candidaturas](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. De acordo com a secção `https://www.figma.com/scim/v2/<TenantID>` de **Credenciais de Administrador,** input in Tenant **URL** onde **TenantID** é o valor que você recuperou de Figma mais cedo. Insera o valor **da API Token** em **Ficha Secreta.** Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se à Figma. Se a ligação falhar, certifique-se de que a sua conta Figma tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Figma**.

    ![Mapeamento de utilizador figma](media/Figma-provisioning-tutorial/figma05.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Figma na secção de Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Figma para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Utilizador figma](media/Figma-provisioning-tutorial/figma06.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para figma, altere o Estado de **Provisionamento** para **Ligado** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer à Figma, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure na Figma.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)