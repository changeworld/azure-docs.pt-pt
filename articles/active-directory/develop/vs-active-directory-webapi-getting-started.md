---
title: Começar com azure AD em projetos WebApi do Estúdio Visual
description: Como começar a usar o Diretório Ativo Azure em projetos WebApi após ligar ou criar um Anúncio Azure usando serviços conectados do Estúdio Visual
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 9a9f8aaa9e3f90e78668d2294ea9959b480e3384
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886080"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Começar com o Diretório Ativo Azure (projetos WebApi)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

Este artigo fornece orientação adicional depois de ter adicionado O Diretório Ativo a um projeto webAPI ASP.NET através do **comando de Serviços Conectados do Project > Connected Services** do Visual Studio. Se ainda não adicionou o serviço ao seu projeto, pode fazê-lo a qualquer momento.

Veja [o que aconteceu ao meu projeto WebAPI?](vs-active-directory-webapi-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Exigindo autenticação para aceder aos controladores

Todos os controladores do seu `[Authorize]` projeto foram adornados com o atributo. Este atributo exige que o utilizador seja autenticado antes de aceder às APIs definidas por estes controladores. Para permitir que o controlador seja acedido de forma anónima, remova este atributo do controlador. Se pretender definir as permissões a um nível mais granular, aplique o atributo a cada método que requer autorização em vez de a aplicar à classe de controlador.

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para o Diretório Ativo do Azure](authentication-scenarios.md)
- [Adicione o sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v2-aspnet-webapp.md)
