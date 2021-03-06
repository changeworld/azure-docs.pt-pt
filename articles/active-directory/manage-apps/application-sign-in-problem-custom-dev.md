---
title: Problemas de contratação de uma aplicação desenvolvida sob medida Microsoft Docs
description: Erros comuns que podem estar a fazer com que não possa assinar uma aplicação que desenvolveu com a Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ad2499aea8bf4e41ca00d6c78d76e112f0493e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825242"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problemas de sessão numa aplicação desenvolvida sob medida

Existem vários erros que podem estar a fazer com que não consiga assinar uma aplicação. A maior razão pela qual as pessoas se deparam com este problema são as aplicações mal configuradas.

## <a name="errors-related-to--misconfigured-apps"></a>Erros relacionados com apps mal configuradas

* Verifique ambas as configurações do portal correspondem às que tem na sua aplicação. Especificamente, compare id cliente/aplicação, URLs de resposta, segredos/chaves do cliente e ID de aplicação URI.

* Compare o recurso a que está a pedir acesso em código com as permissões configuradas no separador **Recursos Necessários** para se certificar de que apenas solicita recursos configurados.

* Consulte [o Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) para obter erros ou problemas semelhantes.

## <a name="next-steps"></a>Passos seguintes

[Guia de desenvolvimento da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Consentimento e Integração de Apps para AD Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Consentimento e Permissão para Aplicações Convergentes Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Empilhador Azure ADOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
