---
title: 'Tutorial: Conceder acesso em grupo aos recursos azure com RBAC e Azure PowerShell'
description: Aprenda a conceder a um grupo acesso aos recursos Azure utilizando o controlo de acesso baseado em papéis (RBAC) e azure PowerShell neste tutorial.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: 2f001e781640704cfcee161b3de75b3f4cae1423
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77138245"
---
# <a name="tutorial-grant-a-group-access-to-azure-resources-using-rbac-and-azure-powershell"></a>Tutorial: Conceder um acesso em grupo aos recursos Azure utilizando o RBAC e o Azure PowerShell

[O controlo de acesso baseado em funções (RBAC)](overview.md) é a forma como gere o acesso aos recursos do Azure. Neste tutorial irá conceder acesso a um grupo para ver tudo numa subscrição e gerir tudo num grupo de recursos com o Azure PowerShell.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceder acesso a um grupo de âmbitos diferentes
> * Listar o acesso
> * Remover o acesso

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, irá precisar de:

- Permissões para criar grupos no Azure Active Directory (ou ter um grupo existente)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>Atribuições de funções

No RBAC, para conceder acesso, crie uma atribuição de função. Uma atribuição de função é composta por três elementos: principal de segurança, definição de função e âmbito. Seguem-se as duas atribuições de funções que vai realizar neste tutorial:

| Principal de segurança | Definição de função | Âmbito |
| --- | --- | --- |
| Grupo<br>(Grupo do Tutorial do RBAC) | [Leitor](built-in-roles.md#reader) | Subscrição |
| Grupo<br>(Grupo do Tutorial do RBAC)| [Contribuinte](built-in-roles.md#contributor) | Grupo de recursos<br>(rbac-tutorial-resource-group) |

   ![Atribuições de funções para um grupo](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>Criar um grupo

Para atribuir uma função, precisa de um utilizador, um grupo ou um principal de serviço. Se ainda não tiver um grupo, pode criar um.

- No Azure Cloud Shell, crie um novo grupo com o comando [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

Se não tiver permissões para criar grupos, pode experimentar o [Tutorial: Conceda um acesso ao utilizador aos recursos Azure utilizando o RBAC e o Azure PowerShell.](tutorial-role-assignments-user-powershell.md)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Utilize um grupo de recursos para mostrar como atribuir uma função num âmbito do grupo de recursos.

1. Obtenha uma lista de localizações da região usando o comando [Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Selecione uma localização perto de si e atribua-a a uma variável.

   ```azurepowershell
   $location = "westus"
   ```

1. Crie um novo grupo de recursos utilizando o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Conceder acesso

Para conceder acesso ao grupo, utiliza o comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para atribuir uma função. Tem de especificar o principal de segurança, a definição de função e o âmbito.

1. Obtenha o ID do objeto do grupo com o comando [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. Guarde o ID do objeto de grupo numa variável.

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. Obtenha a identificação da sua subscrição utilizando o comando [Get-AzSubscription.](/powershell/module/Az.Accounts/Get-AzSubscription)

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Guarde o âmbito da subscrição numa variável.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Atribua a função [Leitor](built-in-roles.md#reader) ao grupo no âmbito da subscrição.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. Atribua a função [Contribuidor](built-in-roles.md#contributor) ao grupo no âmbito do grupo de recursos.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>Listar o acesso

1. Para verificar o acesso à subscrição, utilize o comando [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) para listar as atribuições de funções.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    No resultado, pode ver que a função de Leitor foi atribuída ao grupo do Tutorial do RBAC no âmbito da subscrição.

1. Para verificar o acesso ao grupo de recursos, utilize o comando [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) para listar as atribuições de funções.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    No resultado, pode ver que as funções de Contribuidor e de Leitor foram atribuídas ao Grupo do Tutorial do RBAC. A função de Contribuidor está no âmbito do rbac-tutorial-resource-group e a função de Leitor é herdada no âmbito da subscrição.

## <a name="optional-list-access-using-the-azure-portal"></a>(Opcional) Listar acesso com o Portal do Azure

1. Para ver as atribuições de funções, aceda ao portal do Azure, veja o painel **Controlo de acesso (IAM)** para a subscrição.

    ![Atribuições de funções para um grupo no âmbito da subscrição](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. Veja o painel **Controlo de acesso (IAM)** para o grupo de recursos.

    ![Atribuições de funções para um grupo no âmbito do grupo de recursos](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>Remover o acesso

Para remover o acesso para utilizadores, grupos e aplicações, utilize a [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) para remover uma atribuição de funções.

1. Utilize o seguinte comando para remover a atribuição da função de Contribuidor para o grupo no âmbito do grupo de recursos.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Utilize o seguinte comando para remover a atribuição da função de Leitor para a subscrição do grupo de recursos.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, elimine o grupo de recursos e o grupo.

1. Elimine o grupo de recursos utilizando o comando [Remove-AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup)

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Quando lhe pediram para confirmar, escreva **Y**. Levará alguns segundos para apagar.

1. Elimine o grupo com o comando [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup).

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    Se receber um erro ao tentar eliminar o grupo, também pode eliminar o grupo no portal.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o acesso aos recursos azure utilizando o RBAC e o Azure PowerShell](role-assignments-powershell.md)
