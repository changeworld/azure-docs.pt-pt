---
title: Proteção de Zonas e Registos Privados DNS - DNS Azure
description: Neste caminho de aprendizagem, começar a proteger zonas privadas de DNS e recordes em DNS Microsoft Azure.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: da94c9aa97483ab5792e917d6a8f60f846b0722e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473067"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Como proteger zonas e registos privados de DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zonas e registos privados de DNS são recursos críticos. A abater uma zona DNS ou um único registo DNS pode resultar numa interrupção de serviço. É importante que as zonas e registos dNS estejam protegidos contra alterações não autorizadas ou acidentais.

Este artigo explica como o Azure DNS permite proteger as suas zonas privadas de DNS e registos contra tais alterações.  Aplicamos duas poderosas funcionalidades de títulos fornecidas pelo Azure Resource Manager: [controlo de acesso baseado em funções](../role-based-access-control/overview.md) e bloqueios de [recursos.](../azure-resource-manager/management/lock-resources.md)

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O Azure Role-Based Access Control (RBAC) permite uma gestão de acesso de grãos finos para utilizadores, grupos e recursos Azure. Com o RBAC, pode conceder o nível de acesso de que os utilizadores precisam. Para obter mais informações sobre como o RBAC o ajuda a gerir o acesso, consulte [o que é o Controlo de Acesso baseado em Papéis.](../role-based-access-control/overview.md)

### <a name="the-private-dns-zone-contributor-role"></a>O papel de Colaborador da Zona DNS Privada

O papel de Colaborador privado da Zona DNS é um papel integrado na gestão dos recursos privados do DNS. Esta função aplicada a um utilizador ou grupo permite-lhes gerir recursos Privados DNS.

O grupo de recursos *myPrivateDNS* contém cinco zonas para a Corporação Contoso. A concessão do administrador do DNS Private DNS Zone Contributor permissões a esse grupo de recursos, permite o controlo total sobre essas zonas de DNS. Evita conceder permissões desnecessárias. O administrador do DNS não pode criar ou parar máquinas virtuais.

A forma mais simples de atribuir permissões RBAC é [através do portal Azure.](../role-based-access-control/role-assignments-portal.md)  

Controlo de Acesso Aberto **(IAM)** para o grupo de recursos, selecione **Adicionar,** em seguida, selecione a função de Colaborador da **Zona DNS Privada.** Selecione os utilizadores ou grupos necessários para conceder permissões.

![RBAC de nível de grupo de recursos através do portal Azure](./media/dns-protect-private-zones-recordsets/rbac1.png)

As permissões também podem ser [concedidas através do Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

O comando equivalente também está [disponível através do Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-rbac"></a>RBAC nível de zona privada

As regras Do RBAC do Azure podem ser aplicadas a uma subscrição, a um grupo de recursos ou a um recurso individual. Esse recurso pode ser uma zona DNS individual, ou um conjunto de registos individual.

Por exemplo, o grupo de recursos *myPrivateDNS* contém a zona *private.contoso.com* e uma *customers.private.contoso.com*de subzona . Os registos CNAME são criados para cada conta de cliente. A conta de administrador utilizada para gerir os registos CNAME é atribuída permissões para criar registos na zona *customers.private.contoso.com.* A conta só pode gerir *customers.private.contoso.com.*

As permissões RBAC de nível de zona podem ser concedidas através do portal Azure.  Controlo de acesso aberto **(IAM)** para a zona, selecione **Adicionar**e, em seguida, selecione a função **de Colaborador da Zona DNS Privada.** Selecione os utilizadores ou grupos necessários para conceder permissões.

![DNS Zone nível RBAC através do portal Azure](./media/dns-protect-private-zones-recordsets/rbac2.png)

As permissões também podem ser [concedidas através do Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

O comando equivalente também está [disponível através do Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>Nível de recorde RBAC

As permissões são aplicadas ao nível do recorde.  O utilizador tem o controlo das entradas de que necessita e não pode fazer quaisquer outras alterações.

As permissões RBAC de nível recorde podem ser configuradas através do portal Azure, utilizando o botão Controle de **Acesso (IAM)** na página de conjunto de registos:

![Nível recorde rBAC através do portal Azure](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Nível recorde rBAC através do portal Azure](./media/dns-protect-private-zones-recordsets/rbac4.png)

As permissões RBAC de nível recorde também podem ser [concedidas utilizando o Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

O comando equivalente também está [disponível através do Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Funções personalizadas

A função de Colaborador da Zona DNS Privada incorporada permite o controlo total sobre um recurso DNS. É possível construir os seus próprios papéis personalizados de Azure para fornecer um controlo mais fino.

A conta que é usada para gerir cnames é concedida permissão para gerir apenas os registos CNAME. A conta não pode modificar registos de outros tipos. A conta não pode fazer operações ao nível da zona, tais como a eliminação de zonas.

O exemplo que se segue mostra uma definição de função personalizada para a gestão apenas dos registos CNAME:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

A propriedade Actions define as seguintes permissões específicas do DNS:

* `Microsoft.Network/privateDnsZones/CNAME/*`concede controlo total sobre os registos CNAME
* `Microsoft.Network/privateDNSZones/read`concede permissão para ler zonas privadas dNS, mas não para modificá-las, permitindo-lhe ver a zona em que o CNAME está sendo criado.

> [!NOTE]
> Utilizar uma função RBAC personalizada para evitar a apagar os conjuntos de registos, permitindo ainda que sejam atualizados, não é um controlo eficaz. Impede que os conjuntos de registos sejam eliminados, mas não impede que sejam modificados.  As modificações permitidas incluem a adição e remoção de registos do conjunto de registos, incluindo a remoção de todos os registos para deixar um conjunto de registos vazio. Isto tem o mesmo efeito que apagar o recorde estabelecido de um ponto de vista de resolução do DNS.

Definições de papéis personalizadas não podem ser definidas atualmente através do portal Azure. Um papel personalizado baseado nesta definição de papel pode ser criado usando Azure PowerShell:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Também pode ser criado através do Azure CLI:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

O papel pode então ser atribuído da mesma forma que as funções incorporadas, como descrito anteriormente neste artigo.

Para obter mais informações sobre como criar, gerir e atribuir papéis personalizados, consulte [Papéis Personalizados em Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Bloqueios de recurso

O Azure Resource Manager suporta outro tipo de controlo de segurança, a capacidade de bloquear recursos. Os bloqueios de recursos são aplicados ao recurso e são eficazes em todos os utilizadores e funções. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Existem dois tipos de bloqueio de recursos: **CanNotDelete** e **ReadOnly**. Estes tipos de bloqueio podem ser aplicados quer a uma zona Privada de DNS, quer a um conjunto de registos individuais.  As seguintes secções descrevem vários cenários comuns e como apoiá-los usando fechaduras de recursos.

### <a name="protecting-against-all-changes"></a>Proteger contra todas as mudanças

Para evitar alterações, aplique um bloqueio De Leitura Apenas na zona. Este bloqueio impede a criação de novos conjuntos de registos e os conjuntos de registos existentes sejam modificados ou eliminados.

Os bloqueios de recursos de nível de zona podem ser criados através do portal Azure.  A partir da página de zona DNS, selecione **Locks**, em seguida, selecione **+Adicionar**:

![Bloqueios de recursos de nível de zona através do portal Azure](./media/dns-protect-private-zones-recordsets/locks1.png)

Os bloqueios de recursos ao nível da zona também podem ser criados através do [Azure PowerShell:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

O comando equivalente também está [disponível através do Azure CLI:](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create)

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Proteger registos individuais

Para evitar um registo DNS existente estabelecido contra modificação, aplique um bloqueio ReadOnly no conjunto de registos.

> [!NOTE]
> Aplicar um bloqueio CanNotDelete a um conjunto de registos não é um controlo eficaz. Impede que o registo estabelecido seja eliminado, mas não impede que seja modificado.  As modificações permitidas incluem a adição e remoção de registos do conjunto de registos, incluindo a remoção de todos os registos para deixar um conjunto de registos vazio. Isto tem o mesmo efeito que apagar o recorde estabelecido de um ponto de vista de resolução do DNS.

Atualmente, os bloqueios de recursos de nível de conjunto de registos só podem ser configurados utilizando o Azure PowerShell.  Não são suportados no portal Azure ou no Azure CLI.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Proteger contra a eliminação de zonas

Quando uma zona é eliminada em DNS Azure, todos os conjuntos de registos na zona são eliminados.  Esta operação não pode ser desfeita. A desobstrução acidental de uma zona crítica tem o potencial de ter um impacto significativo no negócio.  É importante proteger contra a eliminação acidental da zona.

A aplicação de um bloqueio CanNotDelete numa zona impede que a zona seja eliminada. As fechaduras são herdadas por recursos infantis. Um bloqueio impede que qualquer conjunto de registos na zona seja apagado. Como descrito na nota acima, é ineficaz uma vez que os registos ainda podem ser removidos dos conjuntos de recordes existentes.

Como alternativa, aplique um bloqueio CanNotDelete a um disco estabelecido na zona, como o conjunto de registos SOA. A zona não é apagada sem também apagar os recordes. Este bloqueio protege contra a eliminação da zona, permitindo que os recordes dentro da zona sejam modificados livremente. Se for feita uma tentativa de eliminar a zona, o Gestor de Recursos Azure deteta esta remoção. A remoção também eliminaria o conjunto de registos SOA, o Gestor de Recursos Azure bloqueia a chamada porque o SOA está bloqueado.  Nenhum conjunto de registos é apagado.

O seguinte comando PowerShell cria uma fechadura CanNotDelete contra o registo SOA da determinada zona:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Outra opção para evitar a eliminação acidental da zona é utilizando uma função personalizada. Esta função garante que as contas utilizadas para gerir as suas zonas não têm permissões de exclusão de zonas. 

Quando necessitar de eliminar uma zona, pode impor uma eliminação em duas etapas:

 - Primeiro, zona de concessão eliminar permissões
 - Segundo, conceda permissões para apagar a zona.

O papel personalizado funciona para todas as zonas acedidas por essas contas. As contas com zonas de exclusão de permissões, como o proprietário da subscrição, ainda podem apagar acidentalmente uma zona.

É possível usar ambas as abordagens - bloqueios de recursos e funções personalizadas - ao mesmo tempo, como uma abordagem de defesa-em profundidade para a proteção da zona DNS.

## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre o trabalho com o RBAC, consulte [Iniciar com a gestão de acesso no portal Azure.](../role-based-access-control/overview.md)
* Para obter mais informações sobre o trabalho com fechaduras de recursos, consulte os recursos de bloqueio com o Gestor de [Recursos Azure](../azure-resource-manager/management/lock-resources.md).
