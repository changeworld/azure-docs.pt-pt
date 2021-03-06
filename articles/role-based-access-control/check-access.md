---
title: Quickstart - Veja o acesso que um utilizador tem aos recursos do Azure
description: Neste QuickStart, aprenda a visualizar o acesso que um utilizador ou outro diretor de segurança tem aos recursos do Azure utilizando o controlo de acesso baseado em funções (RBAC) e o portal Azure.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b23c10fc2a551b8044b208911dbc048968b06564
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74419613"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Quickstart: Veja o acesso que um utilizador tem aos recursos do Azure

Pode utilizar a lâmina de controlo de **acesso (IAM)** no controlo de acesso baseado em [funções (RBAC)](overview.md) para visualizar o acesso que um utilizador ou outro diretor de segurança tem aos recursos Do Azure. No entanto, por vezes basta ver rapidamente o acesso a um único utilizador ou a outro diretor de segurança. A forma mais fácil de o fazer é utilizar a funcionalidade **de acesso check** no portal Azure.

## <a name="view-role-assignments"></a>Ver atribuições de funções

 A forma como vê o acesso a um utilizador é listar as atribuições das suas funções. Siga estes passos para visualizar as atribuições de funções para um único utilizador, grupo, diretor de serviço ou identidade gerida no âmbito da subscrição.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, **subscrições.**

1. Clique na sua subscrição.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador **de acesso Verificar.**

    ![Controlo de acesso - Verifique o separador de acesso](./media/check-access/access-control-check-access.png)

1. Na lista **Find,** selecione o tipo de diretor de segurança que pretende verificar o acesso.

1. Na caixa de pesquisa, introduza uma cadeia para pesquisar o diretório para identificar nomes de exibição, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de selecionados de acesso](./media/check-access/check-access-select.png)

1. Clique no diretor de segurança para abrir o painel de **atribuições.**

    ![painel de atribuições](./media/check-access/check-access-assignments.png)

    Neste painel, pode ver as funções atribuídas ao diretor de segurança selecionado e o âmbito. Se houver alguma missão de negação neste âmbito ou herdada para este âmbito, serão listadas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Conceder ao utilizador acesso aos recursos Azure utilizando o RBAC e o portal Azure](quickstart-assign-role-user-portal.md)
