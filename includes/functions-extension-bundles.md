---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68639122"
---
A forma mais fácil de instalar extensões de ligação é ativar [os feixes de extensão.](../articles/azure-functions/functions-bindings-register.md#extension-bundles) Quando ativa os pacotes, um conjunto predefinido de pacotes de extensão é automaticamente instalado.

Para ativar os pacotes de extensão, abra o ficheiro host.json e atualize o seu conteúdo para corresponder ao seguinte código:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```