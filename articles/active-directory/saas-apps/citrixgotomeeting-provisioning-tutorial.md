---
title: 'Tutorial: Configure GoToMeeting para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0ac06fc3018b4230cbf32712067c48400599082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058268"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Tutorial: Configure GoToMeeting para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para realizar no GoToMeeting e azure AD para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para GoToMeeting.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

*   Um inquilino de diretório Azure Ative.
*   Uma subscrição ativada por um único sinal GoToMeeting.
*   Uma conta de utilizador no GoToMeeting com permissões de Team Admin.

## <a name="assigning-users-to-gotomeeting"></a>Atribuir utilizadores ao GoToMeeting

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o serviço de provisionamento, tem de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua aplicação GoToMeeting. Uma vez decidido, pode atribuir estes utilizadores à sua aplicação GoToMeeting seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Dicas importantes para atribuir utilizadores ao GoToMeeting

*   Recomenda-se que um único utilizador da AD Azure seja designado para o GoToMeeting para testar a configuração de provisionamento. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

*   Ao atribuir um utilizador ao GoToMeeting, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="enable-automated-user-provisioning"></a>Ativar o fornecimento automatizado de utilizadores

Esta secção orienta-o através da ligação do seu AD Azure à conta de utilizador do GoToMeeting que aprovisiona a API, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas no GoToMeeting com base na atribuição de utilizador e grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar o Single Sign-On baseado em SAML para o GoToMeeting, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o fornecimento automático de conta de utilizador:

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > todas as aplicações.**

1. Se já configurou o GoToMeeting para um único sinal, procure a sua instância de GoToMeeting utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **goToMeeting** na galeria de aplicações. Selecione GoToMeeting a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de GoToMeeting e, em seguida, selecione o separador **Provisioning.**

1. Detete o modo **de provisionamento** para **automático**. 

    ![provisionamento](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. No âmbito da secção de Credenciais de Administrador, execute os seguintes passos:
   
    a. Na caixa de **texto GoToMeeting Admin User Name,** digite o nome de utilizador de um administrador.

    b. Na caixa de **texto GoToMeeting Admin Password,** a palavra-passe do administrador.

1. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação GoToMeeting. Se a ligação falhar, certifique-se de que a sua conta GoToMeeting tem permissões de Team Admin e tente novamente o passo **"Credenciais de Administrador".**

1. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo de email de **notificação** e verifique a caixa de verificação.

1. Clique em **Guardar.**

1. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Users to GoToMeeting.**

1. Na secção **DeMapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para GoToMeeting. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no GoToMeeting para operações de atualização. Selecione o botão Guardar para elegiro qualquer alteração.

1. Para ativar o serviço de provisionamento de AD Azure para GoToMeeting, altere o Estado de **Provisionamento** para **On** na secção Definições

1. Clique em **Guardar.**

Inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos ao GoToMeeting na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a realizar do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento na sua aplicação GoToMeeting.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar um único signo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


