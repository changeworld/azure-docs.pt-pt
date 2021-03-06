---
title: 'Roteiro PowerShell: List Azure Data Share convites enviados a um consumidor Microsoft Docs'
description: Este script PowerShell aceita convites de uma partilha de dados existente.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 157b844afd9d59b6c03c7ae8585bc2182370d3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307156"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Use powerShell para obter um convite de partilha de dados

Este guião da PowerShell recebe convites enviados a um consumidor.

## <a name="sample-script"></a>Script de exemplo
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Obtenha e lista envie convites de partilha de dados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script de partilha de dados azure PowerShell podem ser encontradas nas [amostras de PowerShell de partilha](../../samples-powershell.md)de dados do Azure .
