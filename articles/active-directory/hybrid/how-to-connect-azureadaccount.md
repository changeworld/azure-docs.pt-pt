---
title: Alterar a senha da conta do Conector Azure AD / Microsoft Docs
description: Este tópico documenta como restaurar a conta azure AD Connector.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204538"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Alterar a palavra-passe da conta do Conector do Azure AD
A conta Azure AD Connector deve ser gratuita. Se precisas de redefinir as suas credenciais, então este tópico é para ti. Por exemplo, se um Administrador Global tiver, por engano, redefinir a palavra-passe na conta utilizando o PowerShell.

## <a name="reset-the-credentials"></a>Redefinir as credenciais
Se a conta de Conector AD Azure não puder contactar a AD Azure devido a problemas de autenticação, a palavra-passe pode ser redefinida.

1. Inicie sessão no servidor de sincronização Azure AD Connect e inicie a PowerShell.
2. Execute `Add-ADSyncAADServiceAccount`.  
   ![Conta de addadsyncadservice de cmdlet PowerShell](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Forneça credenciais de administração da Azure AD Global.

Este cmdlet repõe a palavra-passe para a conta de serviço e atualiza-a tanto no Azure AD como no motor de sincronização.

## <a name="known-issues-these-steps-can-solve"></a>Questões conhecidas que estes passos podem resolver
Esta secção é uma lista de erros relatados pelos clientes que foram corrigidos por uma redefinição de credenciais na conta do Conector Azure AD.

---
Evento 6900  
O servidor encontrou um erro inesperado ao processar uma notificação de alteração de palavra-passe:  
AADSTS70002: Erro validando credenciais. AADSTS50054: A palavra-passe antiga é usada para autenticação.

---
Evento 659  
Erro ao recuperar a configuração da sincronização da política de passwords. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Erro validando credenciais. AADSTS50054: A palavra-passe antiga é usada para autenticação.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

