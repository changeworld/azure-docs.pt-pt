---
title: Copiar um disco gerido para uma conta de armazenamento - amostra CLI
description: Amostra Azure CLI - Exportar ou copiar um disco gerido para uma conta de srage.
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
ms.date: 09/17/2018
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 92c752c56c37b854bc793d897c664337cf8bfccd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75463629"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Exportar/Copiar um disco gerido para uma conta de armazenamento utilizando o Azure CLI

Este script exporta o VHD subjacente de um disco gerido para uma conta de armazenamento na mesma região ou numa região diferente. Gera primeiro o URI de SAS do disco gerido e, em seguida, utiliza-o para copiar o VHD para uma conta de armazenamento. Utilize este script para copiar discos geridos para expansão a nível regional.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para gerar o URI de SAS de um disco gerido e copia o VHD subjacente para uma conta de armazenamento com o URI de SAS. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az disk grant-access](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Gera o SAS só de leitura utilizado para copiar o ficheiro VHD subjacente para uma conta de armazenamento ou transferi-lo para o local  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Copia um blob de forma assíncrona de uma conta de armazenamento para outra |

## <a name="next-steps"></a>Passos seguintes

[Criar um disco gerido a partir de um VHD](virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Máquina virtual adicional e amostras de script seletivas cli podem ser encontradas na [documentação Azure Windows VM](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
