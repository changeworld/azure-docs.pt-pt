---
title: Dashboards de recursos para avaliações de acesso em PIM - Azure AD Microsoft Docs
description: Descreve como usar um dashboard de recursos para realizar uma revisão de acesso na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847033"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Utilize um painel de recursos para realizar uma revisão de acesso na Gestão de Identidade Privilegiada

Pode utilizar um painel de recursos para realizar uma revisão de acesso em Gestão de Identidade Privilegiada (PIM). O painel de instrumentos Da Visão De Administrador em Azure Ative Directory (Azure AD) tem três componentes primários:

- Uma representação gráfica das ativações de funções de recursos
- Gráficos que exibem a distribuição de atribuições de papéis por tipo de atribuição
- Uma área de dados contendo informações para novas atribuições de funções

![Screenshot do dashboard Da Visão de Administrador, mostrando gráficos e gráficos](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Screenshot do dashboard 'Visão De Administrador', mostrando listas de dados](media/pim-resource-roles-overview-dashboards/role-settings.png)

A representação gráfica das ativações de funções de recursos cobre os últimos sete dias. Estes dados são remetidos para o recurso selecionado e exibem ativações para as funções mais comuns (proprietário, colaborador, administrador de acesso ao utilizador) e para todas as funções combinadas.

De um lado do gráfico de ativações, dois gráficos mostram a distribuição de atribuições por tipo de atribuição, tanto para utilizadores como para grupos. Pode alterar o valor para uma percentagem (ou vice-versa), selecionando uma fatia do gráfico.

Abaixo estão listados o número de utilizadores e grupos com novas atribuições de funções ao longo dos últimos 30 dias, e funções ordenadas por atribuição total por ordem descendente.

## <a name="next-steps"></a>Passos seguintes

- [Inicie uma revisão de acesso para funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-start-access-review.md)
