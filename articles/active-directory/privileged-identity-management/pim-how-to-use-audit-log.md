---
title: Ver relatório de registo de auditoria para funções da Azure AD no Azure AD PIM [ Microsoft Docs
description: Saiba como ver o histórico de registos de auditoria para funções de Azure AD na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8aef68e0f61e6ca995fc2bb362d59aba73ead2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329523"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Ver histórico de auditoria para funções da Azure AD na Gestão de Identidade Privilegiada

Pode utilizar o histórico de auditoria de Gestão de Identidade Privilegiada (PIM) para ver todas as atribuições e ativações de funções nos últimos 30 dias para todas as funções privilegiadas. Se quiser ver o histórico completo de auditoria da sua organização Azure Ative Directory (Azure AD), incluindo administrador, utilizador final e atividade de sincronização, pode utilizar os relatórios de segurança e atividade do [Diretório Ativo Azure.](../reports-monitoring/overview-reports.md)

## <a name="determine-your-version-of-pim"></a>Determine a sua versão do PIM

A partir de novembro de 2019, a parte da AD Azure da Privileged Identity Management está a ser atualizada para uma nova versão que corresponde às experiências para funções de recursos Do Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está a ser lançada, quais os procedimentos que segue neste artigo dependem da versão da Gestão de Identidade Privilegiada que tem atualmente. Siga os passos nesta secção para determinar qual a versão da Gestão de Identidade Privilegiada que tem. Depois de conhecer a sua versão de Gestão de Identidade Privilegiada, pode selecionar os procedimentos neste artigo que correspondam a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que esteja no papel de [administrador privilegiado.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Open **Azure AD Privileged Identity Management.** Se tiver um banner no topo da página de visão geral, siga as instruções no separador **versão Nova** deste artigo. Caso contrário, siga as instruções no separador versão **anterior.**

    [![Azure AD papé nova versão](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Selecione o separador para a sua versão")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Nova versão](#tab/new)

Siga estes passos para ver o histórico de auditoria para funções de AD Azure.

## <a name="view-resource-audit-history"></a>Ver histórico de auditoria de recursos

A auditoria de recursos dá-lhe uma visão de todas as atividades associadas às suas funções de AD Azure.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione **a auditoria de recursos**.

1. Filtre o histórico utilizando uma data ou gama personalizada predefinida.

    ![Lista de auditoria de recursos com filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Ver a minha auditoria

A minha auditoria permite-lhe visualizar a sua atividade pessoal.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione o recurso para o que pretende ver o histórico de auditoria.

1. Selecione **A minha auditoria**.

1. Filtre o histórico utilizando uma data ou gama personalizada predefinida.

    ![Lista de auditoria para o utilizador atual](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="view-audit-history"></a>Ver histórico de auditoria

Siga estes passos para ver o histórico de auditoria para funções de AD Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que seja membro do papel de Administrador de [Funções Privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione o histórico de auditoria de **funções de Diretório.**

    Dependendo do histórico de auditoria, é apresentado um gráfico de colunas juntamente com as ativações totais, ativações máximas por dia e ativações médias por dia.

    [![Azure AD papé nova versão](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Ver histórico de auditoria de papéis de diretório")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Na parte inferior da página, é apresentada uma tabela com informações sobre cada ação no histórico de auditoria disponível. As colunas têm os seguintes significados:

    | Coluna | Descrição |
    | --- | --- |
    | Hora | Quando a ação ocorreu. |
    | Requerente | Utilizador que solicitou a ativação ou alteração da função. Se o valor for **o Sistema Azure,** consulte o histórico de auditoria do Azure para obter mais informações. |
    | Ação | Ações tomadas pelo solicitador. As ações podem incluir Atribuir, Não atribuir, Ativar, Desativar ou Adicionar OutsidePIM. |
    | Membro | Utilizador que esteja ativando ou atribuído a um papel. |
    | Função | Função atribuída ou ativada pelo utilizador. |
    | Raciocínio | Texto que foi introduzido no campo da razão durante a ativação. |
    | Expiração | Quando uma função ativada expirar. Aplica-se apenas a atribuições de funções elegíveis. |

1. Para classificar o histórico de auditoria, clique nos botões **Time**, **Action**e **Role.**

## <a name="filter-audit-history"></a>Histórico de auditoria de filtro

1. No topo da página de histórico de auditoria, clique no botão **Filter.**

    Aparece a painel de parâmetros do **gráfico de atualização.**

1. Na **gama de tempo,** selecione um intervalo de tempo.

1. Em **Funções,** selecione as caixas de verificação para indicar as funções que pretende ver.

    ![Painel de parâmetros de gráfico de atualização](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Selecione **Feito** para ver o histórico de auditoria filtrado.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Obtenha razão, aprovador e número de bilhete para eventos de aprovação

1. Inscreva-se no [portal Azure](https://aad.portal.azure.com) com permissões de funções de administrador privilegiado e abra a Azure AD.
1. Selecione **Registos de auditoria**.
1. Utilize o filtro **De serviço** para exibir apenas eventos de auditoria para o serviço de Gestão de Identidade Privilegiada. Na página de registos da **Auditoria,** pode:

    - Consulte a razão de um evento de auditoria na coluna **'Razão do Estado'.**
    - Consulte o aprovador na coluna **Iniciado por (ator)** para o evento "adicionar membro ao pedido de função aprovado".

    [![Azure AD papé nova versão](media/pim-how-to-use-audit-log/filter-audit-logs.png "Filtrar o registo de auditoria do serviço PIM")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Selecione um evento de registo de auditoria para ver o número do bilhete no separador **Atividade** do painel **Detalhes.**
  
    [![Azure AD papé nova versão](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Verifique o número do bilhete para o evento de auditoria")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. Pode visualizar o requester (pessoa ativando o papel) no separador **Targets** do painel **De Detalhes** para um evento de auditoria. Existem dois tipos-alvo para funções de AD Azure:

    - O papel **(Tipo** = Função)
    - O requester **(Tipo** = Utilizador)

Tipicamente, o evento de registo de auditoria imediatamente acima do evento de aprovação é um evento para "Adicionar membro ao papel concluído" onde o **Iniciado por (ator)** é o solicitador. Na maioria dos casos, não precisará de encontrar o solicitador no pedido de aprovação do ponto de vista da auditoria.

---

## <a name="next-steps"></a>Passos seguintes

- [Ver histórico de atividades e auditorias para funções de recursos Azure na Gestão de Identidade Privilegiada](azure-pim-resource-rbac.md)
