---
title: Windows Virtual Desktop tipo de tarefa pessoal desktop - Azure
description: Como configurar o tipo de atribuição para um pool de anfitriões pessoal do Windows Virtual Desktop.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128282"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configure o tipo pessoal de atribuição de piscina de anfitrião do ambiente de trabalho

Pode configurar o tipo de atribuição do seu conjunto pessoal de anfitriões para ajustar o ambiente de ambiente de trabalho virtual do Windows para melhor atender às suas necessidades. Neste tópico, vamos mostrar-lhe como configurar a atribuição automática ou direta para os seus utilizadores.

>[!NOTE]
> As instruções deste artigo aplicam-se apenas a piscinas pessoais de anfitriões de ambiente de trabalho, não piscinas de anfitriões reunidas, uma vez que os utilizadores em piscinas de anfitriões não são atribuídos a anfitriões específicos da sessão.

## <a name="configure-automatic-assignment"></a>Configurar a atribuição automática

A atribuição automática é o tipo de atribuição padrão para novos conjuntos pessoais de anfitriões de ambiente de trabalho criados no seu ambiente de ambiente de trabalho virtual Windows. Atribuir automaticamente os utilizadores não requer um anfitrião de sessão específico.

Para atribuir automaticamente os utilizadores, atribua-os primeiro ao conjunto de anfitriões pessoal para que possam ver o ambiente de trabalho no seu feed. Quando um utilizador designado lançar o ambiente de trabalho no seu feed, solicitará um anfitrião de sessão disponível se ainda não estiver ligado à piscina anfitriã, que completa o processo de atribuição.

Antes de começar, [faça o download e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) se ainda não o fez. 

> [!NOTE]
> Certifique-se de que instalou a versão 1.0.1534.2001 do módulo PowerShell do Windows Virtual, antes de seguir estas instruções.

Depois disso, execute o seguinte cmdlet para iniciar sessão na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Para configurar um conjunto de anfitriões para atribuir automaticamente os utilizadores a VMs, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Para atribuir um utilizador à piscina de anfitriões pessoal do ambiente de trabalho, execute o seguinte cmdlet PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Configurar a atribuição direta

Ao contrário da atribuição automática, quando utiliza a atribuição direta, deve atribuir o utilizador ao conjunto de anfitriões pessoal do ambiente de trabalho e a um anfitrião de sessão específico antes de se ligar ao seu ambiente de trabalho pessoal. Se o utilizador for atribuído apenas a uma piscina de anfitriões sem uma atribuição de anfitriões de sessão, não poderá aceder aos recursos.

Para configurar um pool de anfitriões para exigir a atribuição direta dos utilizadores aos anfitriões da sessão, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Para atribuir um utilizador à piscina de anfitriões pessoal do ambiente de trabalho, execute o seguinte cmdlet PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Para atribuir um utilizador a um anfitrião de sessão específico, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Passos seguintes

Agora que configurao o tipo de atribuição pessoal de desktop, pode iniciar sessão num cliente do Windows Virtual Desktop para o testar como parte de uma sessão de utilizador. Estes próximos dois How-tos dir-lhe-ão como se conectar a uma sessão usando o cliente da sua escolha:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](connect-windows-7-and-10.md)
- [Ligar com o cliente web](connect-web.md)
