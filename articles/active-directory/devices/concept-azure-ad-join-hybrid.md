---
title: O que é um dispositivo híbrido Azure AD?
description: Saiba como a gestão da identidade do dispositivo pode ajudá-lo a gerir dispositivos que estão a aceder a recursos no seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512254"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos associados ao Azure AD

Muitas organizações utilizam há mais de uma década a associação a um domínio no Active Directory no local para permitir:

- Aos departamentos de TI gerirem dispositivos detidos pela organização num local central.
- Aos utilizadores iniciarem sessão nos dispositivos deles com as contas escolares ou profissionais do Active Directory.

Normalmente, as organizações com uma pegada no local dependem de métodos de imagem para fornecer dispositivos, e muitas vezes usam O Gestor de **Configuração** ou a política de **grupo (GP)** para geri-los.

Se o seu ambiente tiver uma infraestrutura do AD no local e também quiser tirar partido das capacidades que o Azure Active Directory oferece, pode implementar dispositivos híbridos associados ao Azure AD. Estes dispositivos são dispositivos que se juntam ao seu Diretório Ativo no local e registados no seu Diretório Ativo Azure.

|   | AD Hybrid Azure Junta-se |
| --- | --- |
| **Definição** | Juntou-se às instalações a D.A. e a Azure AD exigindo que a conta organizacional assinasse o dispositivo |
| **Audiência primária** | Adequado para organizações híbridas com infraestrutura ad existente no local |
|   | Aplicável a todos os utilizadores de uma organização |
| **Propriedade dos dispositivos** | Organização |
| **Sistemas Operativos** | Windows 10, 8.1 e 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 e 2019 |
| **Aprovisionamento** | Windows 10, Windows Server 2016/2019 |
|   | Domain join by IT and autojoin via Azure AD Connect ou ADFS config |
|   | Domain join by Windows Autopilot e autojoin via Azure AD Connect ou Config ADFS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2 - Requerer MSI |
| **Sinal de dispositivo em opções** | Contas organizacionais utilizando: |
|   | Palavra-passe |
|   | Windows Olá para negócios para Win10 |
| **Gestão de dispositivos** | Política de Grupo |
|   | Gestor de Configuração autónomo ou cogestão com a Microsoft Intune |
| **Principais capacidades** | SSO tanto para os recursos de nuvem como para o local |
|   | Acesso Condicional através do Domínio aderir ou através de Intune se cogerido |
|   | Reset de palavra-passe de autosserviço e reset PIN do Windows Hello PIN no ecrã de bloqueio |
|   | Roaming do Estado Da Empresa através de dispositivos |

![Dispositivos híbridos associados ao Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Cenários

Utilize dispositivos híbridos Azure AD se:

- Tiver aplicações Win32 implementadas nesses dispositivos que se baseiam na autenticação de máquinas do Active Directory.
- Pretende continuar a utilizar a Política de Grupo para gerir a configuração do dispositivo.
- Pretende continuar a utilizar soluções de imagem existentes para implementar e configurar dispositivos.
- Tem de suportar dispositivos Windows 7 e 8.1 de nível inferior, além do Windows 10

## <a name="next-steps"></a>Passos seguintes

- [Planear a sua implementação de associação ao Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Gerir identidades do dispositivo utilizando o portal Azure](device-management-azure-portal.md)
- [Gerir dispositivos velhos em Azure AD](manage-stale-devices.md)
