---
title: Problemas Desfilmam problemas de instalação do Azure AD Connect Microsoft Docs'
description: Este tópico fornece passos para como resolver problemas com a instalação do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75ad29c5b4a76de5317991995f132c6cb53bbe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70211839"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Troubleshoot: Azure AD Connect instala problemas

## <a name="recommended-steps"></a>**Passos Recomendados**
Verifique qual o tipo de [instalação Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) adequado para si. Se cumprir os critérios de instalação expressa, recomendamos vivamente que vá com a instalação expressa. A instalação expressa dá-lhe as opções mínimas necessárias para terminar a instalação, pelo que há menos probabilidade de qualquer problema. 

No entanto, se não cumprir os critérios de instalação expresso e tiver de fazer a instalação personalizada, então aqui estão algumas boas práticas que pode seguir para evitar problemas comuns. Por uma questão de simplicidade, apenas são mencionadas opções seletivas aqui:

* Certifique-se de que é administrador na máquina na qual está a instalar o AAD Connect. Inicie sessão na máquina com as mesmas credenciais de administrador.

* Deixe todas as opções serem predefinidas na página seguinte, com exceção de "Use um Servidor SQL existente", se pretender utilizar o Servidor SQL existente. Aqui estão [mais detalhes](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) sobre como usar opções de instalação personalizadas. 

    ![Utilizar o servidor SQL existente](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na página seguinte, escolha a opção "Criar uma nova conta AD", para evitar quaisquer problemas de permissão com a conta existente.

    ![Conta Florestal AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Problemas Comuns**

* Problemas de conectividade com o [Diretório Ativo no local.](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools)

* [Problemas de conectividade com o Diretório Ativo Azure online.](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity)

* [Problemas de permissão com o Diretório Ativo no local.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account)

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Pré-requisitos do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Selecionar o tipo de instalação a utilizar para o Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Introdução ao Azure AD Connect com as definições rápidas](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Instalação personalizada do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: atualizar de uma versão anterior para a mais recente](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: O que é o servidor de encenação?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [O que é o Módulo ADConnectivityTool do PowerShell?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Passos seguintes
- [Sincronização Azure AD Connect](how-to-connect-sync-whatis.md).
- [O que é a identidade híbrida?](whatis-hybrid-identity.md)





