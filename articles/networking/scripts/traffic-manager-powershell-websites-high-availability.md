---
title: Tráfego de rotas para HA de aplicações - Azure PowerShell - Traffic Manager
description: Amostra de script Azure PowerShell - Tráfego de rota para alta disponibilidade de aplicações
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: allensu
ms.openlocfilehash: 183599fccfad1806faae3cb90de225d388b77da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74049241"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-powershell"></a>Tráfego de rota para alta disponibilidade de aplicações - Azure PowerShell

Este script cria um grupo de recursos, dois planos de serviço de aplicações, duas aplicações web, um perfil de gestor de tráfego e dois pontos finais do gestor de tráfego. O Gestor de Tráfego direciona o tráfego para a aplicação numa região como a região primária, e para a região secundária quando a aplicação na região primária não está disponível. Antes de executar o script, tem de alterar os valores MyWebApp, MyWebAppL1 e MyWebAppL2 para valores únicos em todo o Azure. Depois de executar o script, pode aceder à aplicação na região primária com o URL mywebapp.trafficmanager.net.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma aplicação Web, um perfil do gestor de tráfego e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Novo AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um plano do Serviço de Aplicações. Isto é como uma quinta de servidores para a sua aplicação web Azure. |
| [Novo AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação web Azure dentro do plano de Serviço de Aplicações. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Cria uma aplicação web Azure dentro do plano de Serviço de Aplicações. |
| [Perfil new-AzTrafficManager](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Cria um perfil do Gestor de Tráfego do Azure. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Adiciona um ponto final a um Perfil do Gestor de Tráfego do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Encontrará exemplos adicionais de scripts do PowerShell de redes na [Documentação de Descrição Geral de Redes do Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
