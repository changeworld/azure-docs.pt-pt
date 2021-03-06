---
title: Criar um cluster de tecido de serviço em Powershell
description: Amostra de script Azure PowerShell - Crie um cluster de tecido de serviço protegido com um certificado X.509.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: f8e1a0ca86f9346cf07c87a738d48cb56f6d7d57
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614779"
---
# <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric

Este script de exemplo cria um cluster do Service Fabric com cinco nós protegido por um certificado X.509.  O comando cria um certificado autoassinado e carrega-o para um novo cofre de chaves. O certificado é também copiado para um diretório local.  Defina o parâmetro *-OS* para escolher a versão do Windows ou do Linux que funciona nos nós do cluster.  Personalize os parâmetros conforme necessário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessário, instale o Azure PowerShell utilizando as instruções `Connect-AzAccount` encontradas no [guia Azure PowerShell](/powershell/azure/overview) e, em seguida, corra para criar uma ligação com o Azure. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos, o cluster e todos os recursos relacionados.

```powershell
$groupname="mysfclustergroup"
Remove-AzResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Novo AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) | Cria um novo cluster do Service Fabric. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver os exemplos do Azure Powershell adicionais para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).
