---
title: 'Tutorial: Configure Concur para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441aa9805f2a453e22f207238315125d2a281838
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60280438"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Tutorial: Configure Concur para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para realizar em Concur e Azure AD para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para Concur.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

*   Um inquilino de diretório Azure Ative.
*   Uma assinatura ativada por um único sinal do Concur.
*   Uma conta de utilizador em Concur com permissões de Team Admin.

## <a name="assigning-users-to-concur"></a>Atribuir utilizadores ao Concur

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o serviço de provisionamento, tem de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua aplicação Concur. Uma vez decidido, pode atribuir estes utilizadores à sua app Concur seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Dicas importantes para atribuir utilizadores ao Concur

*   Recomenda-se que um único utilizador da AD Azure seja designado para o Concur para testar a configuração de provisionamento. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

*   Ao atribuir um utilizador ao Concur, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="enable-user-provisioning"></a>Ativar o fornecimento de utilizadores

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da Concur que aprovisiona a API, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas em Concur com base na atribuição de utilizador e grupo em Azure AD.

> [!Tip] 
> Também pode optar por ativar o Single Sign-On baseado em SAML para Concur, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o fornecimento da conta do utilizador:

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizadores do Diretório Ativo à Concur.

Para ativar aplicações no Serviço de Despesas, tem de haver configuração e utilização adequadas de um perfil de Administração do Serviço Web. Não adicione o papel de Administrador wS ao seu perfil de administrador existente que utiliza para funções administrativas T&E.

Os Consultores Concur ou o administrador do cliente devem criar um perfil distinto do Administrador de Serviço Web e o administrador do Cliente deve utilizar este perfil para as funções do Administrador de Serviços Web (por exemplo, ativar aplicações). Estes perfis devem ser mantidos separados do perfil diário de administração T&E do administrador do administrador do administrador do cliente (o perfil de administração T&E não deve ter a função WSAdmin atribuída).

Quando criar o perfil a utilizar para ativar a aplicação, introduza o nome do administrador do cliente nos campos de perfil do utilizador. Isto atribui a propriedade ao perfil. Uma vez criados um ou mais perfis, o cliente deve iniciar sessão com este perfil para clicar no botão "*Ativar*" para uma App de Parceiros dentro do menu de Serviços Web.

Pelas seguintes razões, esta ação não deve ser feita com o perfil que utilizam para a administração Normal T&E.

* O cliente tem de ser aquele que clica "*Sim*" na janela de diálogo que é exibida após a ativação de uma aplicação. Este clique reconhece que o cliente está disposto a aceder à aplicação do Parceiro para aceder aos seus dados, pelo que você ou o Parceiro não podem clicar nesse botão Sim.

* Se um administrador cliente que tenha ativado uma aplicação utilizando o perfil de administrador T&E sai da empresa (resultando na inativação do perfil), quaisquer aplicações habilitadas a utilizar esse perfil não funcionam até que a app esteja ativada com outro perfil ativo da WS Admin. É por isso que é suposto criares perfis distintos da Administração WS.

* Se um administrador deixar a empresa, o nome associado ao perfil WS Admin pode ser alterado para o administrador de substituição, se desejar, sem afetar a aplicação ativada, uma vez que esse perfil não necessita de ser inativado.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Aceda ao seu inquilino **do Concur.**

2. No menu **'Administração',** selecione **Serviços Web**.
   
    ![Inquilino concur](./media/concur-provisioning-tutorial/IC721729.png "Inquilino concur")

3. Do lado esquerdo, a partir do painel de **Serviços Web,** selecione **Enable Partner Application**.
   
    ![Ativar a aplicação do parceiro](./media/concur-provisioning-tutorial/ic721730.png "Ativar a aplicação do parceiro")

4. A partir da lista **enable Application,** selecione **Diretório Ativo Azure**, e, em seguida, clique em **Ativar**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Clique **em Sim** para fechar o diálogo confirmar **ação.**
   
    ![Confirmar ação](./media/concur-provisioning-tutorial/ic721732.png "Confirmar ação")

6. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > todas as aplicações.**

7. Se já configurou o Concur para uma única inscrição, procure a sua instância de Concur utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **O Concur** na galeria de aplicações. Selecione Concur a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

8. Selecione a sua instância de Concur e, em seguida, selecione o separador **Provisioning.**

9. Detete o **modo de provisionamento** para **automático**. 
 
    ![provisionamento](./media/concur-provisioning-tutorial/provisioning.png)

10. Na secção **credenciais de administrador,** introduza o nome de **utilizador** e a **palavra-passe** do seu administrador Concur.

11. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Concur. Se a ligação falhar, certifique-se de que a sua conta Concur tem permissões de Team Admin.

12. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo de email de **notificação** e verifique a caixa de verificação.

13. Clique em **Guardar.**

14. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Users to Concur.**

15. Na secção **DeMapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD a Concur. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Concur para operações de atualização. Selecione o botão Guardar para elegiro qualquer alteração.

16. Para ativar o serviço de provisionamento de AD Azure para O Cur, altere o Estado de **Provisionamento** para **On** na secção **Definições**

17. Clique em **Guardar.**

Agora pode criar uma conta de teste. Aguarde até 20 minutos para verificar se a conta foi sincronizada com o Concur.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar um único signo](concur-tutorial.md)

