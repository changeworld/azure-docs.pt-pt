---
title: Criar um disco gerido a partir de um instantâneo - Amostra CLI
description: Exemplo do Script da CLI do Azure – Criar um disco gerido a partir de um instantâneo
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 2d415a12ceaf2cda0172d806d5a621b1297a74be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75375858"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Criar um disco gerido a partir de um instantâneo com a CLI

Este script cria um disco gerido a partir de um instantâneo. Utiliza-o para restaurar uma máquina virtual a partir de instantâneos do SO e discos de dados. Crie discos do SO e discos geridos de dados a partir dos respetivos instantâneos e, em seguida, crie uma nova máquina virtual ao anexar discos geridos. Também pode restaurar discos de dados de uma VM existente ao anexar discos de dados criados a partir de instantâneos.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um instantâneo. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Obtém todas as propriedades de um instantâneo através do nome e das propriedades do grupo de recursos do instantâneo. A propriedade do ID é utilizada para criar o disco gerido.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Cria um disco gerido com o ID de um instantâneo gerido |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Máquina virtual adicional e amostras de script seletivas cli podem ser encontradas na [documentação Azure Windows VM](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
