---
title: Amostras Azure CLI - Conjunto de escala redundante de zona
description: Este script cria um conjunto de escala de máquina virtual Azure que executa Ubuntu em várias Zonas de Disponibilidade.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: 80b173daf2101180e5d78de625b914bd8396b7af
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010363"
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-azure-cli"></a>Crie um conjunto de máquinas virtuais redundante com o Azure CLI
Este script cria um conjunto de dimensionamento de máquinas virtuais com o Ubuntu em várias Zonas de Disponibilidade. Depois de executar o script, pode aceder à máquina virtual através de RDP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Execute o seguinte comando para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os seguintes comandos para criar um grupo de recursos, um conjunto de dimensionamento de máquinas virtuais e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/ad/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vmss create](/cli/azure/vmss) | Cria o conjunto de dimensionamento de máquinas virtuais e liga-o à rede virtual, à sub-rede e ao grupo de segurança de rede. É também criado um balanceador de carga para distribuir o tráfego para instâncias de VM individuais. Este comando também especifica a imagem da VM a ser utilizada e as credenciais administrativas.  |
| [az group delete](/cli/azure/ad/group) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Pode encontrar exemplos adicionais de scripts da CLI do Azure para conjuntos de dimensionamento de máquinas virtuais na [documentação do conjunto de dimensionamento de máquinas virtuais do Azure](../cli-samples.md).
