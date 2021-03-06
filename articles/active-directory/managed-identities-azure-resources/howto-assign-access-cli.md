---
title: Atribuir um acesso de identidade gerido a um recurso utilizando o Azure CLI - Azure AD
description: Instruções passo a passo para atribuir uma identidade gerida num recurso, acesso a outro recurso, utilizando o Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b241ac223fd1eb9df2b0a914726d8f37df5f4d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547363"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Atribuir um acesso de identidade gerido a um recurso utilizando o Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Uma vez configurado um recurso Azure com uma identidade gerida, pode dar à identidade gerida acesso a outro recurso, como qualquer diretor de segurança. Este exemplo mostra-lhe como dar a uma máquina virtual Azure ou ao conjunto de máquinas virtuais acesso de identidade a uma conta de armazenamento Azure utilizando o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos do script CLI, tem três opções:
    - Utilize a Casca de [Nuvem Azure](../../cloud-shell/overview.md) a partir do portal Azure (ver secção seguinte).
    - Utilize a casca de nuvem azure incorporada através do botão "Try It", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) se preferir utilizar uma consola CLI local. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Utilize o RBAC para atribuir um acesso de identidade gerido a outro recurso

Depois de ter ativado a identidade gerida num recurso Azure, como uma [máquina virtual Azure](qs-configure-cli-windows-vm.md) ou um conjunto de [máquinas virtuais Azure:](qs-configure-cli-windows-vmss.md) 

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta associada à subscrição Azure sob a qual deseja implementar o conjunto de escala de máquinas VM ou virtual:

   ```azurecli-interactive
   az login
   ```

2. Neste exemplo, estamos a dar a uma máquina virtual Azure acesso a uma conta de armazenamento. Primeiro usamos a [lista de recursos az](/cli/azure/resource/#az-resource-list) para obter o principal de serviço para a máquina virtual chamada myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Para um conjunto de escala de máquina virtual Azure, o comando é o mesmo, exceto aqui, obtém-se o principal de serviço para o conjunto de escala de máquina virtual denominado "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Assim que tiver o ID principal do serviço, utilize a criação de [função az](/cli/azure/role/assignment#az-role-assignment-create) para dar à máquina virtual ou à escala de máquina virtual o acesso "Reader" a uma conta de armazenamento chamada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para a visão geral dos recursos do Azure](overview.md)
- Para permitir a identidade gerida numa máquina virtual Azure, consulte [as identidades geridas pela Configure para os recursos Azure num Azure VM utilizando o Azure CLI](qs-configure-cli-windows-vm.md).
- Para permitir a identidade gerida num conjunto de máquinas virtuais Azure, consulte [as identidades geridas pela Configure para os recursos Azure num conjunto de máquinas virtuais utilizando o Azure CLI](qs-configure-cli-windows-vmss.md).
