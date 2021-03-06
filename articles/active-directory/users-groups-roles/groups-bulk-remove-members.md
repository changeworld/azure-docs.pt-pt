---
title: A granel remova os membros do grupo através do upload de um ficheiro CSV - Diretório Ativo Azure [ Diretório Ativo ] Microsoft Docs
description: Adicione utilizadores a granel no centro de administração Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d384ea4749e2d0bc7edf8df7ac0508566f2f76b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517099"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Remoção a granel dos membros do grupo (pré-visualização) no Diretório Ativo do Azure

Utilizando o portal Azure Ative Directory (Azure AD), pode remover um grande número de membros de um grupo utilizando um ficheiro de valores separados de vírem (CSV) para remover a granel os membros do grupo.

## <a name="to-bulk-remove-group-members"></a>Para remover a granel membros do grupo

1. Inscreva-se [no portal Azure](https://portal.azure.com) com uma conta de administrador de utilizador na organização. Os proprietários de grupos também podem remover em massa membros de grupos que possuem.
1. Em Azure AD, selecione **Grupos** > **Todos os grupos**.
1. Abra o grupo a partir do qual está a retirar membros e, em seguida, selecione **Membros**.
1. Na página **dos Membros,** selecione **Remover membros**.
1. Na página de remoção a **granel dos membros do grupo (Pré-visualização),** selecione **Download** para obter o modelo de ficheiro CSV com as propriedades necessárias do membro do grupo.

   ![O comando remover membros está na página de perfil do grupo](./media/groups-bulk-remove-members/remove-panel.png)

1. Abra o ficheiro CSV e adicione uma linha para cada membro do grupo que pretende remover do grupo (os valores necessários são id do objeto membro ou nome principal do utilizador). Em seguida, guarde o ficheiro.

   ![O ficheiro CSV contém nomes e IDs para os membros removerem](./media/groups-bulk-remove-members/csv-file.png)

1. Na página de remoção a **granel dos membros do grupo (Pré-visualização),** sob o **upload do ficheiro CSV,** navegue para o ficheiro. Quando selecionar o ficheiro, inicia-se a validação do ficheiro .csv.
1. Quando o conteúdo do ficheiro é validado, a página de importação a granel apresenta **file uploaded com sucesso**. Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, selecione **Submeter** para iniciar a operação a granel Azure que remove os membros do grupo do grupo.
1. Quando a operação de remoção terminar, verá uma notificação de que a operação a granel foi bem sucedida.

## <a name="check-removal-status"></a>Verifique o estado de remoção

Pode ver o estado de todos os seus pedidos a granel pendentes na página de resultados da **operação Bulk (pré-visualização).**

   ![A página de resultados das operações a granel mostra o seu estado de pedido em massa](./media/groups-bulk-remove-members/bulk-center.png)

Para mais detalhes sobre cada item de linha dentro da operação a granel, selecione os valores sob as colunas **# Sucesso**, **# Falha,** ou **Total Solicitações.** Se ocorrerem falhas, as razões da falha serão listadas.

## <a name="bulk-removal-service-limits"></a>Limites do serviço de remoção de granéis

Cada atividade a granel para remover uma lista de membros do grupo pode funcionar até uma hora. Isto permite a remoção de uma lista de pelo menos 40.000 membros.

## <a name="next-steps"></a>Passos seguintes

- [Membros do grupo de importação a granel](groups-bulk-import-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
- [Faça o download de uma lista de todos os grupos](groups-bulk-download.md)
