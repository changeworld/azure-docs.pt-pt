---
title: incluir ficheiro
description: incluir ficheiro
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 68a208af1a9aa9e73f2af99021d195f264fb21f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184875"
---
## <a name="enable-event-grid-resource-provider"></a>Ativar o fornecedor de recursos do Event Grid

Se ainda não tiver utilizado anteriormente o Event Grid na sua subscrição do Azure, precisa de registar o fornecedor de recursos do Event Grid. Execute o seguinte comando:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Pode demorar algum tempo para o registo terminar. Para ver o estado, execute:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Quando `RegistrationStatus` está `Registered`, está pronto para continuar.
