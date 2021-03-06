---
title: Criação de gestão de grupos de self-service - Azure Ative Directory [ Diretório Ativo Azure ] Microsoft Docs
description: Criar e gerir grupos de segurança ou grupos do Office 365 no Azure Active Directory e pedir adesões ao grupo de segurança ou ao grupo do Office 365
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e52c37e293941a767621cf56ef75f8cc83b1925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298008"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Criação de gestão de grupos de self-service no Azure Ative Directory 

Pode permitir que os utilizadores criem e gerem os seus próprios grupos de segurança ou grupos office 365 no Azure Ative Directory (Azure AD). O proprietário do grupo pode aprovar ou negar pedidos de adesão, podendo delegar o controlo da adesão ao grupo. As funcionalidades de gestão de grupos self-service não estão disponíveis para grupos de segurança ou listas de distribuição ativadas por correio.

## <a name="self-service-group-membership-defaults"></a>Incumprimentos de membros do grupo self-service

Quando os grupos de segurança são criados no portal Azure ou usam o Azure AD PowerShell, apenas os proprietários do grupo podem atualizar a adesão. Grupos de segurança criados por self-service no [painel access](https://account.activedirectory.windowsazure.com/r#/joinGroups) e todos os grupos do Office 365 estão disponíveis para aderir a todos os utilizadores, aprovados pelo proprietário ou aprovados automaticamente. No painel Access, pode alterar as opções de adesão quando criar o grupo.

Grupos criados em | Comportamento padrão do grupo de segurança | Comportamento padrão do grupo 365 do Office 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Só os proprietários podem adicionar membros<br>Visível mas não disponível para participar no painel Access | Aberto para se juntar a todos os utilizadores
[Portal Azure](https://portal.azure.com) | Só os proprietários podem adicionar membros<br>Visível mas não disponível para participar no painel Access<br>Proprietário não é atribuído automaticamente na criação de grupo | Aberto para se juntar a todos os utilizadores
[Painel de acesso](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Aberto para se juntar a todos os utilizadores<br>As opções de adesão podem ser alteradas quando o grupo é criado | Aberto para se juntar a todos os utilizadores<br>As opções de adesão podem ser alteradas quando o grupo é criado

## <a name="self-service-group-management-scenarios"></a>Cenários de gestão de grupos de self-service

* **Gestão de grupos delegados** Um exemplo é um administrador que está a gerir o acesso a uma aplicação SaaS que a empresa está a usar. A gestão destes direitos de acesso está a tornar-se complexa, por isso este administrador pede ao proprietário da empresa que crie um novo grupo. O administrador atribui acesso à candidatura ao novo grupo, e acrescenta ao grupo todas as pessoas que já acedem à aplicação. O proprietário da empresa pode então adicionar mais utilizadores e estes são aprovisionados automaticamente para a aplicação. O proprietário da empresa não tem de aguardar que o administrador faça a gestão do acesso dos utilizadores. Se o administrador conceder a mesma permissão a um gestor num grupo empresarial diferente, essa pessoa também pode gerir o acesso aos seus próprios membros do grupo. Nem o empresário nem o gestor podem ver ou gerir os membros do grupo uns dos outros. O administrador ainda pode ver todos os utilizadores que têm acesso à aplicação e bloquear os direitos de acesso, se for necessário.
* **Gestão de grupode self-service** Um exemplo deste cenário são dois utilizadores que ambos têm sites SharePoint Online que configuram de forma independente. Querem dar às equipas um do outro acesso aos seus sites. Para o conseguirem, podem criar um grupo no Azure AD e no SharePoint Online cada um deles seleciona esse grupo para fornecer acesso aos respetivos sites. Quando alguém quiser ter acesso, pode solicitá-lo no Painel de Acesso e, após a aprovação, obtém automaticamente acesso a ambos os sites do SharePoint Online. Posteriormente, um deles decide que todas as pessoas que acedem ao site devem também ter acesso a uma determinada aplicação SaaS. O administrador da aplicação SaaS pode adicionar direitos de acesso da aplicação ao site do SharePoint Online. A partir daí, quaisquer pedidos que ele aprove concedem acesso aos dois sites SharePoint Online e também a esta aplicação SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Disponibilizar um grupo para personalização pelo utilizador

1. Inicie sessão no [Centro de administradores do Azure AD](https://aad.portal.azure.com) com uma conta que seja administrador global do diretório.
1. Selecione **Grupos**e, em seguida, selecione as definições **gerais.**
1. Definir Os Proprietários podem gerir os pedidos de **membros do grupo no Painel de Acesso** a **Sim**.
1. Definir **restringir o acesso aos grupos no Painel** de Acesso ao **Nº**.
1. Se definir **os Utilizadores podem criar grupos de segurança em portais Azure** ou utilizadores podem criar **grupos office 365 em portais Azure** para

    - **Sim:** Todos os utilizadores da sua organização Azure AD estão autorizados a criar novos grupos de segurança e adicionar membros a estes grupos. Estes novos grupos apareceriam também no Painel de Acesso para todos os outros utilizadores. Se a definição de política no grupo o permitir, outros utilizadores podem criar pedidos para se juntarem a estes grupos
    - **Não**: Os utilizadores não podem criar grupos e não podem alterar os grupos existentes para os quais são proprietários. No entanto, podem continuar gerir os membros desses grupos e aprovar pedidos de outros utilizadores para pertencer aos respetivos grupos.

Também pode utilizar proprietários que possam atribuir membros como proprietários de **grupos em portais** e proprietários do Azure que possam atribuir membros como proprietários de **grupos em portais Azure** para obter um controlo de acesso mais granular sobre a gestão de grupos de self-service para os seus utilizadores.

Quando os utilizadores podem criar grupos, todos os utilizadores da sua organização podem criar novos grupos e, em seguida, podem, como proprietário padrão, adicionar membros a estes grupos. Não se pode especificar indivíduos que podem criar os seus próprios grupos. Só é possível especificar indivíduos para fazer de outro membro do grupo um proprietário de grupo.

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso a recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Cmdlets do Azure Active Directory para configurar definições de grupo](groups-settings-cmdlets.md)
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar as identidades no local no Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
