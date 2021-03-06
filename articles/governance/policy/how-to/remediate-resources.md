---
title: Identificar recursos em não conformidade
description: Este guia acompanha-o através da remediação de recursos que não estão em conformidade com as políticas da Política Azure.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 71af5c81e0dce4d5c0a0461534f634db36bd66a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471392"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Remediar recursos não conformes com a Política Azure

Os recursos que não estão em conformidade com uma **implementação IfNotExists** ou **modificam** a política podem ser colocados num estado conforme através **da Reparação**. A reparação é realizada instruindo a Política Azure a executar o efeito **deployIfNotExists** ou as **operações** de etiqueta da política atribuída nos seus recursos existentes, seja para um grupo de gestão, uma subscrição, um grupo de recursos ou um recurso individual. Este artigo mostra os passos necessários para compreender e realizar a reparação com a Política Azure.

## <a name="how-remediation-security-works"></a>Como funciona a segurança da reparação

Quando a Política Azure executa o modelo na definição de política **implementaDaIfNotExists,** fá-lo usando uma [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md).
A Política Azure cria uma identidade gerida para cada atribuição, mas deve ter detalhes sobre quais as funções para conceder a identidade gerida. Se a identidade gerida faltar a papéis, este erro é demonstrado durante a atribuição da apólice ou de uma iniciativa. Ao utilizar o portal, a Política Azure concede automaticamente à identidade gerida as funções listadas assim que a atribuição começar. A _localização_ da identidade gerida não afeta a sua operação com a Política Azure.

![Identidade gerida - papel em falta](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Se um recurso modificado por **deployIfNotExists** ou **modificar** estiver fora do âmbito da atribuição de políticas ou o modelo aceder a propriedades em recursos fora do âmbito da atribuição de políticas, a identidade gerida da atribuição deve ter [acesso manualmente](#manually-configure-the-managed-identity) ou a implementação da reparação falhará.

## <a name="configure-policy-definition"></a>Configurar definição de política

O primeiro passo é definir as funções que **implementamIfNotExists** e **modificar** necessidades na definição de política para implementar com sucesso o conteúdo do seu modelo incluído. Sob a propriedade **de detalhes,** adicione uma propriedade **roleDefinitionIds.** Esta propriedade é uma variedade de cordas que combinam com papéis no seu ambiente. Para um exemplo completo, consulte o [exemplo do desdobreIfNotExists](../concepts/effects.md#deployifnotexists-example) ou os [exemplos de modificação](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

A propriedade **roleDefinitionIds** utiliza o identificador de recursos completo e não assume o nome de **função** curto. Para obter o ID para o papel de 'Contribuinte' no seu ambiente, utilize o seguinte código:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Configure manualmente a identidade gerida

Ao criar uma atribuição utilizando o portal, a Política Azure gera a identidade gerida e concede-lhe as funções definidas nas **roleDefinitionIds**. Nas seguintes condições, devem ser feitas medidas para criar a identidade gerida e atribuir-lhe permissões manualmente:

- Durante a utilização do SDK (como o Azure PowerShell)
- Quando um recurso fora do âmbito de atribuição é modificado pelo modelo
- Quando um recurso fora do âmbito de atribuição é lido pelo modelo

> [!NOTE]
> A Azure PowerShell e a .NET são os únicos SDKs que suportam atualmente esta capacidade.

### <a name="create-managed-identity-with-powershell"></a>Criar identidade gerida com a PowerShell

Para criar uma identidade gerida durante a atribuição da apólice, a **Localização** deve ser definida e atribuir **Identidade** utilizada. O exemplo seguinte obtém a definição da política incorporada Implementar encriptação transparente de **dados SQL DB,** define o grupo de recursos alvo e, em seguida, cria a atribuição.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

A `$assignment` variável agora contém a identificação principal da identidade gerida juntamente com os valores padrão devolvidos na criação de uma atribuição de política. Pode ser acedido `$assignment.Identity.PrincipalId`através de .

### <a name="grant-defined-roles-with-powershell"></a>Grant papéis definidos com powerShell

A nova identidade gerida deve completar a replicação através do Azure Ative Directory antes de poder ser-lhe concedidas as funções necessárias. Uma vez concluída a replicação, o exemplo `$policyDef` seguinte iteita a definição de política para os **roleDefinitionIds** e utiliza a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para conceder à nova identidade gerida as funções.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Conceder papéis definidos através do portal

Existem duas formas de conceder à identidade gerida de uma atribuição as funções definidas utilizando o portal, utilizando o controlo de **acesso (IAM)** ou editando a atribuição de políticas ou iniciativas e clicando em **Guardar**.

Para adicionar um papel à identidade gerida da atribuição, siga estes passos:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy.

1. Localize a atribuição que tem uma identidade gerida e clique no nome.

1. Encontre a propriedade **de Id** de Atribuição na página de edição. O ID de atribuição será algo como:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   O nome da identidade gerida é a última parte do `2802056bfc094dfb95d4d7a5` ID do recurso de atribuição, que está neste exemplo. Copie esta parte do ID do recurso de atribuição.

1. Navegue para o recurso ou para o contentor-mãe de recursos (grupo de recursos, subscrição, grupo de gestão) que precisa da definição de função adicionada manualmente.

1. Clique no link de controlo de **acesso (IAM)** na página de recursos e clique + Adicione a atribuição de **funções** no topo da página de controlo de acesso.

1. Selecione a função adequada que corresponde a uma **funçãoDefiniIds** da definição de política.
   Deixe **o acesso atribuído ao** padrão de 'Utilizador, grupo ou aplicação Azure AD'. Na caixa **Select,** cola ou escreva a parte do ID do recurso de atribuição localizado anteriormente. Assim que a pesquisa estiver concluída, clique no objeto com o mesmo nome para selecionar ID e clique em **Guardar**.

## <a name="create-a-remediation-task"></a>Criar uma tarefa de reparação

### <a name="create-a-remediation-task-through-portal"></a>Criar uma tarefa de reparação através do portal

Durante a avaliação, a atribuição de políticas com **implementaçãoIfNotExists** ou **modificaos** efeitos determina se existem recursos não conformes. Quando os recursos não conformes são encontrados, os detalhes são fornecidos na página de **Reparação.** Juntamente com a lista de políticas que têm recursos não conformes é a opção de desencadear uma tarefa de **reparação.** Esta opção é o que cria uma implementação a partir do modelo **de implementaçãoIfNotExists** ou das operações **modificadas.**

Para criar uma tarefa de **reparação,** siga estes passos:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

   ![Pesquisa de Política em Todos os Serviços](../media/remediate-resources/search-policy.png)

1. Selecione **Remediação** no lado esquerdo da página Política Azure.

   ![Selecione Remediação na página Política](../media/remediate-resources/select-remediation.png)

1. Todos os **implementaçõesIfNotExists** e **modificam** as atribuições políticas com recursos não conformes estão incluídos nas **Políticas para remediar** o separador e a tabela de dados. Clique numa política com recursos que não são compatíveis. A nova página de tarefas de **reparação** abre.

   > [!NOTE]
   > Uma forma alternativa de abrir a página de tarefas de **reparação** é encontrar e clicar na política a partir da página **compliance** e, em seguida, clicar no botão Criar Tarefa de **Reparação.**

1. Na nova página de tarefas de **reparação,** filtre os recursos para remediar utilizando as elipses do **Âmbito** para recolher recursos infantis de onde a política é atribuída (incluindo até aos objetos de recursos individuais). Além disso, utilize o drop-down dos **Locais** para filtrar ainda mais os recursos. Apenas os recursos enumerados na tabela serão remediados.

   ![Remediar - selecione quais recursos para remediar](../media/remediate-resources/select-resources.png)

1. Inicie a tarefa de reparação uma vez filtrados os recursos clicando em **Remediar**. A página de conformidade de políticas abre-se ao separador de tarefas de **reparação** para mostrar o estado das tarefas progredir. As implementações criadas pela tarefa de reparação começam imediatamente.

   ![Remediar - progresso das tarefas de reparação](../media/remediate-resources/task-progress.png)

1. Clique na tarefa de **reparação** a partir da página de conformidade de política para obter detalhes sobre o progresso. A filtragem utilizada para a tarefa é mostrada juntamente com uma lista dos recursos que estão a ser remediados.

1. A partir da página de **tarefade reparação,** clique à direita num recurso para visualizar a implementação da tarefa de reparação ou o recurso. No final da linha, clique em **eventos relacionados** para ver detalhes como uma mensagem de erro.

   ![Remediar - menu de contexto de tarefa de recursos](../media/remediate-resources/resource-task-context-menu.png)

Os recursos utilizados através de uma tarefa de **reparação** são adicionados ao separador **Recursos Implantados** na página de conformidade de políticas.

### <a name="create-a-remediation-task-through-azure-cli"></a>Criar uma tarefa de reparação através do Azure CLI

Para criar uma tarefa de **reparação** `az policy remediation` com o Azure CLI, utilize os comandos. `{subscriptionId}` Substitua-a pelo `{myAssignmentId}` id de subscrição e pelo seu **implementadoIfNotExists** ou **modifique** o ID de atribuição de políticas.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para outros comandos e exemplos de reparação, consulte os comandos de [reparação política da AZ.](/cli/azure/policy/remediation)

### <a name="create-a-remediation-task-through-azure-powershell"></a>Criar uma tarefa de reparação através do Azure PowerShell

Para criar uma tarefa de **reparação** `Start-AzPolicyRemediation` com a Azure PowerShell, utilize os comandos. `{subscriptionId}` Substitua-a pelo `{myAssignmentId}` id de subscrição e pelo seu **implementadoIfNotExists** ou **modifique** o ID de atribuição de políticas.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para outras cmdlets de reparação e exemplos, consulte o módulo [Az.PolicyInsights.](/powershell/module/az.policyinsights/#policy_insights)

## <a name="next-steps"></a>Passos seguintes

- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Compreender como [criar políticas programáticas.](programmatically-create.md)
- Saiba como obter dados de [conformidade.](get-compliance-data.md)
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)
