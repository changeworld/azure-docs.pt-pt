---
title: Use powerShell para atribuir uma função RBAC para acesso a dados
titleSuffix: Azure Storage
description: Aprenda a usar o PowerShell para atribuir permissões a um diretor de segurança do Azure Ative Directory com controlo de acesso baseado em funções (RBAC). O Azure Storage suporta funções RBAC incorporadas e personalizadas para autenticação via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1413035c879198cf333aeeb5d8fe993162939172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460577"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Use powerShell para atribuir uma função RBAC para acesso a dados de blob e fila

O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos garantidos através do [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md). O Azure Storage define um conjunto de funções RBAC incorporadas que englobam conjuntos comuns de permissões usadas para aceder a recipientes ou filas.

Quando uma função RBAC é atribuída a um diretor de segurança da AD Azure, o Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser consultado ao nível da subscrição, do grupo de recursos, da conta de armazenamento ou de um recipiente ou fila individual. Um diretor de segurança da AD Azure pode ser um utilizador, um grupo, um diretor de serviço de aplicação ou uma [identidade gerida para os recursos Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Este artigo descreve como usar o Azure PowerShell para listar as funções RBAC incorporadas e atribuí-las aos utilizadores. Para mais informações sobre a utilização do Azure PowerShell, consulte a [visão geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções RBAC para bolhas e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o âmbito dos recursos

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Lista de funções RBAC disponíveis

Para listar as funções RBAC incorporadas disponíveis com a Azure PowerShell, utilize o comando [Get-AzRoleDefinition:](/powershell/module/az.resources/get-azroledefinition)

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Você verá as funções de dados de Armazenamento Azure incorporados listados, juntamente com outras funções incorporadas para O Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Atribuir uma função RBAC a um diretor de segurança

Para atribuir uma função RBAC a um diretor de segurança, utilize o comando [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment) O formato do comando pode diferir com base no âmbito da atribuição. Para executar o comando, é necessário atribuir o papel de Proprietário ou Colaborador no âmbito correspondente. Os exemplos seguintes mostram como atribuir uma função a um utilizador em vários âmbitos, mas pode usar o mesmo comando para atribuir uma função a qualquer diretor de segurança.

### <a name="container-scope"></a>Âmbito do contentor

Para atribuir uma função a um recipiente, especifique uma `--scope` cadeia que contenha o alcance do recipiente para o parâmetro. A margem de manobra para um recipiente está na forma:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

O exemplo seguinte atribui a função de Contribuinte de **Dados blob** de armazenamento a um utilizador, remeto para um recipiente chamado *recipiente de amostras*. Certifique-se de substituir os valores da amostra e os valores do espaço reservado nos parênteses por valores próprios: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Âmbito da fila

Para atribuir uma função de função a uma fila, especifique uma cadeia que contenha o âmbito da fila para o `--scope` parâmetro. A margem para uma fila está na forma:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

O exemplo seguinte atribui a função de Colaborador de Dados da Fila de **Armazenamento** a um utilizador, com o objetivo de uma fila chamada *sample-queue*. Certifique-se de substituir os valores da amostra e os valores do espaço reservado nos parênteses por valores próprios: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Âmbito da conta de armazenamento

Para atribuir uma função à conta de armazenamento, especifique `--scope` o âmbito do recurso da conta de armazenamento para o parâmetro. A margem para uma conta de armazenamento está na forma:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

O exemplo seguinte mostra como analisar a função de Leitor de **Dados de Armazenamento Blob** para um utilizador ao nível da conta de armazenamento. Certifique-se de substituir os valores da amostra por valores próprios: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Âmbito do grupo de recursos

Para atribuir uma função ao grupo de recursos, especifique o nome do grupo de recursos ou o ID para o `--resource-group` parâmetro. O exemplo seguinte atribui a função de Leitor de Dados de Fila de **Armazenamento** a um utilizador ao nível do grupo de recursos. Certifique-se de substituir os valores da amostra e os valores dos suportes por parênteses por valores próprios: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Âmbito de subscrição

Para atribuir uma função à subscrição, especifique `--scope` o âmbito para a subscrição do parâmetro. A margem para uma subscrição está no formulário:

```
/subscriptions/<subscription>
```

O exemplo seguinte mostra como atribuir a função de Leitor de **Dados blob** de armazenamento a um utilizador ao nível da conta de armazenamento. Certifique-se de substituir os valores da amostra por valores próprios: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Passos seguintes

- [Gerir o acesso aos recursos azure utilizando o RBAC e o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Grant access to Azure blob and queue data with RBAC using Azure CLI](storage-auth-aad-rbac-cli.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através da CLI do Azure)
- [Grant access to Azure blob and queue data with RBAC in the Azure portal](storage-auth-aad-rbac-portal.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC no portal do Azure)
