---
title: Notificações por e-mail no PIM - Diretório Ativo Azure / Microsoft Docs
description: Descreve notificações de e-mail em Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee5f2edbae28276f8485ae774a5b1c52e1af2fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72756391"
---
# <a name="email-notifications-in-pim"></a>Notificações por e-mail em PIM

A Privileged Identity Management (PIM) informa-o quando ocorrerem eventos importantes na sua organização Azure Ative Directory (Azure AD), como quando uma função é atribuída ou ativada. A Gestão de Identidade Privilegiada mantém-no informado enviando notificações de e-mail a si e a outros participantes. Estes e-mails também podem incluir links para tarefas relevantes, tais como ativar ou renovar um papel. Este artigo descreve como são estes e-mails, quando são enviados, e quem os recebe.

## <a name="sender-email-address-and-subject-line"></a>Endereço de e-mail do remetente e linha de assunto

Os e-mails enviados da Privileged Identity Management para as funções de recurso Azure AD e Azure têm o seguinte endereço de e-mail remetente:

- Endereço de e-mail: **\@azure-noreply microsoft.com**
- Nome do ecrã: Microsoft Azure

Estes e-mails incluem um prefixo **PIM** na linha de assunto. Segue-se um exemplo:

- PIM: Alain Charon foi permanentemente designado o papel de Leitor de Backup

## <a name="notifications-for-azure-ad-roles"></a>Notificações para funções da AD Azure

A Privileged Identity Management envia e-mails quando ocorrem os seguintes eventos para funções da AD Azure:

- Quando uma ativação de papel privilegiado está pendente de aprovação
- Quando um pedido de ativação de funções privilegiada é concluído
- Quando a Azure AD Privileged Identity Management está ativada

Quem recebe estes e-mails para funções de AD Azure depende do seu papel, do evento e da definição de notificações:

| Utilizador | A ativação de funções está pendente de aprovação | O pedido de ativação de funções está concluído | PiM está ativado |
| --- | --- | --- | --- |
| Administrador privilegiado</br>(Ativado/Elegível) | Sim</br>(apenas se não forem especificados os aprovadores explícitos) | Sim* | Sim |
| Administrador de Segurança</br>(Ativado/Elegível) | Não | Sim* | Sim |
| Administrador Global</br>(Ativado/Elegível) | Não | Sim* | Sim |

\*Se a definição de [ **Notificações** ](pim-how-to-change-default-settings.md#notifications) estiver definida para **ativar**.

O seguinte mostra um e-mail de exemplo que é enviado quando um utilizador ativa uma função Azure AD para a organização fictícia Contoso.

![Novo e-mail privilegiado de Gestão de Identidade para funções da Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Gestão de Identidade Privilegiada Semanal digesta e-mail para funções de AD Azure

Um e-mail semanal de Gestão de Identidade Privilegiada para funções de AD Azure é enviado para Administradores de Papel Privilegiado, Administradores de Segurança e Administradores Globais que permitiram a Gestão de Identidade Privilegiada. Este e-mail semanal fornece uma imagem instantânea de atividades privilegiadas de Gestão de Identidade para a semana, bem como atribuições de papéis privilegiados. Só está disponível para inquilinos na nuvem pública. Aqui está um e-mail de exemplo:

![Gestão de Identidade Privilegiada Semanal digesta e-mail para funções de AD Azure](./media/pim-email-notifications/email-directory-weekly.png)

O e-mail inclui quatro azulejos:

| Mosaico | Descrição |
| --- | --- |
| **Utilizadores ativados** | Número de vezes que os utilizadores ativaram o seu papel elegível dentro do inquilino. |
| **Utilizadores tornados permanentes** | O número de vezes que os utilizadores com uma atribuição elegível são permanentes. |
| **Atribuições de funções na Gestão de Identidade Privilegiada** | Número de vezes que os utilizadores recebem um papel elegível dentro da Gestão de Identidade Privilegiada. |
| **Atribuições de funções fora da PIM** | Número de vezes que os utilizadores recebem um papel permanente fora da Gestão de Identidade Privilegiada (dentro da AD Azure). |

A visão geral da sua secção de **funções superiores** lista as cinco melhores funções do seu inquilino com base no número total de administradores permanentes e elegíveis para cada função. O link **take action** abre o [assistente PIM](pim-security-wizard.md) onde pode converter administradores permanentes em administradores elegíveis em lotes.

## <a name="pim-emails-for-azure-resource-roles"></a>E-mails da PIM para funções de recursos Do Azure

A Gestão de Identidade Privilegiada envia e-mails aos Proprietários e Administradores de Acesso ao Utilizador quando ocorrem os seguintes eventos para funções de recursos Do Azure:

- Quando uma atribuição de funções está pendente de aprovação
- Quando um papel é atribuído
- Quando um papel está prestes a expirar
- Quando um papel é elegível para estender
- Quando um papel está a ser renovado por um utilizador final
- Quando um pedido de ativação de funções é concluído

A Privileged Identity Management envia e-mails aos utilizadores finais quando ocorrem os seguintes eventos para funções de recursos Do Azure:

- Quando uma função é atribuída ao utilizador
- Quando o papel de um utilizador é expirado
- Quando o papel de um utilizador é alargado
- Quando o pedido de ativação de funções de um utilizador é concluído

O seguinte mostra um e-mail de exemplo que é enviado quando um utilizador é atribuído a uma função de recurso Azure para a organização fictícia Contoso.

![Novo e-mail privilegiado de Gestão de Identidade para funções de recurso Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Passos seguintes

- [Configure definições de funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-change-default-settings.md)
- [Aprovar ou negar pedidos de funções da Azure AD na Gestão de Identidade Privilegiada](azure-ad-pim-approval-workflow.md)
