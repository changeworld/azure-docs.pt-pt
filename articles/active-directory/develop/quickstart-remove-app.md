---
title: Remova a aplicação registada na plataforma de identidade da Microsoft Azure
description: Aprenda a remover uma aplicação registada com a plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 3cc9e4458f14a63bad7f484bc16683248895ede9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240850"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Quickstart: Remova uma aplicação registada na plataforma de identidade da Microsoft

Os programadores empresariais e os fornecedores de software como um serviço (SaaS) que tenham registado aplicações com a plataforma de identidade da Microsoft poderão ter de remover o registo dessas aplicações.

Neste início rápido, vai aprender a:

* Remover uma aplicação criada por si ou pela sua organização
* Remover uma aplicação criada por outra organização

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter um inquilino que tem pedidos registrados nele. Para saber como adicionar e registar aplicações, veja [Registar uma aplicação com a plataforma de identidade da Microsoft](quickstart-register-app.md).

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Remover uma aplicação criada por si ou pela sua organização

As aplicações que o utilizador ou a sua organização registaram são representados por um objeto de aplicação e o objeto principal de serviço no seu inquilino. Para obter mais informações, veja [Objetos de Aplicação e Objetos de Principal de Serviço](active-directory-application-objects.md).

### <a name="to-remove-an-application"></a>Para remover uma aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
2. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
3. No painel de navegação à esquerda, selecione o serviço **De Diretório Ativo Azure** e, em seguida, selecione **os registos da App**. Encontre e selecione a aplicação que pretende configurar. Depois de selecionar a aplicação, verá a página **Descrição geral** da aplicação.
4. Na página **Descrição geral**, selecione **Eliminar**.
5. Selecione **Sim** para confirmar que pretende eliminar a aplicação.

   > [!NOTE]
   > Para eliminar uma aplicação, tem de estar listado como proprietário da aplicação ou ter privilégios de administrador.

## <a name="remove-an-application-authored-by-another-organization"></a>Remover uma aplicação criada por outra organização

Se estiver a ver **Registos de aplicações** no contexto de um inquilino, um subconjunto das aplicações que aparecem no separador **Todas as aplicações** são de outro inquilino e foram registadas no seu inquilino durante o processo de consentimento. Mais especificamente, são representadas apenas por um objeto principal de serviço no seu inquilino, sem um objeto de aplicação correspondente. Para obter mais informações sobre as diferenças entre objetos de aplicação e principais de serviço, veja [Objetos de aplicação e principais de serviço no Azure AD](active-directory-application-objects.md).

Para remover o acesso de uma aplicação ao seu diretório (depois de ter dado autorização), o administrador da empresa tem de remover o principal de serviço. O administrador tem de ter acesso de administrador global e pode remover a aplicação através do portal do Azure ou utilizar os [Cmdlets do PowerShell do Azure AD](https://go.microsoft.com/fwlink/?LinkId=294151) para remover o acesso.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre estes outros inícios rápidos de gestão de aplicações relacionadas:

* [Registar uma aplicação na plataforma de identidade da Microsoft](quickstart-register-app.md)
* [Configurar uma aplicação cliente para aceder a APIs Web](quickstart-configure-app-access-web-apis.md)
* [Configurar uma aplicação para expor APIs Web](quickstart-configure-app-expose-web-apis.md)
* [Modificar as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md)
