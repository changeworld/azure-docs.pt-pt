---
title: Exemplo do Script da CLI do Azure - Adicionar uma Aplicação no Batch
description: Este script de amostra demonstra como adicionar uma aplicação para uso com um pool De Lote Azure ou uma tarefa.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: labrenne
ms.openlocfilehash: b19f5dbe27ba0ecabdca6557e4e8e1996fbef78a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77017127"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Exemplo da CLI: Adicionar uma aplicação a uma conta do Azure Batch

Este script demonstra como adicionar uma aplicação para utilizar com um conjunto ou tarefas do Azure Batch. Para configurar uma aplicação para adicionar à sua conta do Batch, reúna o executável juntamente com quaisquer dependências num ficheiro zip. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.20 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos.
Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria uma conta de armazenamento. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Cria a conta do Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentica contra a conta do Batch especificada para obter mais interação da CLI.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Cria uma aplicação.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Adiciona um pacote de aplicação à aplicação especificada.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Atualiza as propriedades de uma aplicação.  |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
