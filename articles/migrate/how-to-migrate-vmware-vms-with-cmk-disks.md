---
title: Migrar máquinas virtuais VMware para Azure com encriptação do lado do servidor (SSE) e chaves geridas pelo cliente (CMK) utilizando a Migração do Servidor Migratório Migratório De Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migrações Azure
description: Saiba como migrar VMware VMs para Azure com encriptação do lado do servidor (SSE) e chaves geridas pelo cliente (CMK) usando a Migração do Servidor Migratório Migratório De Migração de Migração de Migração de Migração de Migração de Migrações Azure
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269487"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>VMs migratórios para VMs Azure habilitados com encriptação do lado do servidor e chaves geridas pelo cliente

Este artigo descreve como migrar VMware VMs para máquinas virtuais Azure com discos encriptados usando encriptação do lado do servidor (SSE) com chaves geridas pelo cliente (CMK), utilizando a Migração do Servidor De Migração do Servidor Migratório De Migração de Migração de Servidores Migratórios Azure (replicação sem agente).

A experiência do portal migração do servidor de migração Azure Migrate permite [migrar VMware VMware VMware para Azure com replicação sem agente.](tutorial-migrate-vmware.md) A experiência do portal atualmente não oferece a capacidade de ligar a SSE com CMK para os seus discos replicados em Azure. A capacidade de ligar a SSE com CMK para os discos replicados está atualmente disponível apenas através da API REST. Neste artigo, você verá como criar e implementar um modelo de Gestor de [Recursos Azure](../azure-resource-manager/templates/overview.md) para replicar um VMware VM e configurar os discos replicados em Azure para usar SSE com CMK.

Os exemplos deste artigo utilizam o [Azure PowerShell](/powershell/azure/new-azureps-module-az) para executar as tarefas necessárias para criar e implementar o modelo de Gestor de Recursos.

[Saiba mais](../virtual-machines/windows/disk-encryption.md) sobre encriptação do lado do servidor (SSE) com chaves geridas pelo cliente (CMK) para discos geridos.

## <a name="prerequisites"></a>Pré-requisitos

- [Reveja o tutorial](tutorial-migrate-vmware.md) sobre migração de VMware VMs para Azure com replicação sem agente para entender os requisitos da ferramenta.
- [Siga estas instruções](how-to-add-tool-first-time.md) para criar um projeto Azure Migrate e adicione a ferramenta **De migração Azure: Server Migração** ao projeto.
- [Siga estas instruções](how-to-set-up-appliance-vmware.md) para configurar o aparelho Azure Migrate para VMware no seu ambiente no local e uma descoberta completa.

## <a name="prepare-for-replication"></a>Preparar para a replicação

Uma vez concluída a descoberta vM, a linha Discovered Servers no azulejo da Migração do Servidor mostrará uma contagem de VMware VMs descobertos pelo aparelho.

Antes de começar a replicar VMs, a infraestrutura de replicação precisa de ser preparada.

1. Crie uma instância de ônibus de serviço na região alvo. O Ônibus de Serviço é utilizado pelo aparelho Azure Migrate no local para comunicar com o serviço de Migração do Servidor para coordenar a replicação e a migração.
2. Crie uma conta de armazenamento para transferência de registos de operação a partir da replicação.
3. Crie uma conta de armazenamento para a a quais o aparelho Azure Migrate carrega dados de replicação.
4. Crie um Cofre chave e configure o Cofre chave para gerir fichas de assinatura de acesso partilhado para acesso à bolha nas contas de armazenamento criadas nos passos 3 e 4.
5. Gere uma assinatura de acesso partilhado para o autocarro de serviço criado no passo 1 e crie um segredo para o símbolo no Key Vault criado no passo anterior.
6. Crie uma política de acesso ao Cofre chave para dar ao aparelho Azure Migrate no local (utilizando a aplicação AAD do aparelho) e ao serviço de migração do servidor acesso ao Cofre chave.
7. Crie uma política de replicação e configure o serviço de Migração do Servidor com detalhes da infraestrutura de replicação criada no passo anterior.

A infraestrutura de replicação deve ser criada na região-alvo de Azure para a migração e na subscrição-alvo do Azure para a que os VMe estão a ser migrados.

A experiência do portal Server Migration simplifica a preparação da infraestrutura de replicação fazendo-a automaticamente quando replica um VM pela primeira vez num projeto. Neste artigo, assumimos que já replicou um ou mais VMs usando a experiência do portal e que a infraestrutura de replicação já está criada. Vamos ver como descobrir detalhes da infraestrutura de replicação existente e como usar estes detalhes como inputs para o modelo de Gestor de Recursos que será usado para configurar replicação com CMK.

### <a name="identifying-replication-infrastructure-components"></a>Identificação de componentes de infraestrutura de replicação

1. No portal Azure, vá na página dos grupos de recursos e selecione o grupo de recursos em que foi criado o projeto Azure Migrate.
2. Selecione **Implementações** do menu esquerdo e procure um nome de implementação começando com a cadeia *"Microsoft.MigrateV2.VMwareV2EnableMigrate"*. Você verá uma lista de modelos de Gestor de Recursos criados pela experiência do portal para configurar replicação para VMs neste projeto. Vamos descarregar um desses modelos e usá-lo como base para preparar o modelo para replicação com CMK.
3. Para baixar o modelo, selecione qualquer implementação que corresponda ao padrão de cordas no passo anterior > selecione **Modelo** do menu esquerdo > Clique **no Download** a partir do menu superior. Guarde o ficheiro template.json localmente. Vai editar este ficheiro de modelo no último passo.

## <a name="create-a-disk-encryption-set"></a>Criar um conjunto de encriptação de disco

Um conjunto de encriptação de disco mapas de objetos geridos discos para um cofre chave que contém o CMK para usar para SSE. Para replicar VMs com CMK, irá criar um conjunto de encriptação de disco e passá-lo como uma entrada para a operação de replicação.

Siga o exemplo [aqui](../virtual-machines/windows/disk-encryption.md#powershell) para criar um conjunto de encriptação de disco utilizando o Azure PowerShell. Certifique-se de que o conjunto de encriptação do disco é criado na subscrição-alvo para a que os VMs estão a ser migrados, e na região-alvo do Azure para a migração.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Obtenha detalhes do VMware VM para migrar

Neste passo, você usará o Azure PowerShell para obter os detalhes do VM que precisa de ser migrado. Estes detalhes serão usados para construir o modelo de Gestor de Recursos para replicação. Especificamente, as duas propriedades de interesse são:

- A identificação de recursos da máquina para os VMs descobertos.
- A lista de discos para o VM e os seus identificadores de disco.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Copie o valor da cadeia SiteId correspondente ao aparelho Azure Migrate que o VM é descoberto. No exemplo acima indicado, o SiteId é *"/subscrições/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Copie os valores uuides resourceId, nome e disco para a máquina a migrar.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Criar modelo de Gestor de Recursos para replicação

- Abra o ficheiro de modelo do Gestor de Recursos que descarregou nos componentes de **infraestrutura de replicação de identificação,** entra num editor à sua escolha.
- Remova todas as definições de recursos do modelo, com exceção dos recursos do tipo *"Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"*
- Se houver várias definições de recursos do tipo acima, remova todos, menos um. Remova **quaisquer** definições de propriedade dependentes da definição de recursos.
- No final deste passo, deve ter um ficheiro que se pareça com o exemplo abaixo e que tenha o mesmo conjunto de propriedades.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Editar a propriedade do **nome** na definição de recursos. Substitua a cadeia que segue o último "/" na propriedade do nome pelo valor de *$machine. Nome*(do passo anterior).
- Altere o valor da propriedade **properties.providerSpecificDetails.vmwareMachineId** com valor de *$machine. ResourceId*(do passo anterior).
- Detete os valores para **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** para o ID do grupo de recursos alvo, ID de recurso de rede virtual alvo e nome de sub-rede alvo, respectivamente.
- Detete o valor do **licençaType** para "WindowsServer" para aplicar o Benefício Híbrido Azure para este VM. Se este VM não for elegível para O Benefício Híbrido Azure, detete o valor do **licençaType** para NoLicenseType.
- Altere o valor da propriedade **targetVmName** para o nome de máquina virtual Azure desejado para o VM migrado.
- Adicione opcionalmente uma propriedade chamada **targetVmSize** abaixo da propriedade **targetVmName.** Detete o valor da propriedade **targetVmSize** para o tamanho da máquina virtual Azure desejada para o VM migrado.
- A propriedade **do discoToInclude** é uma lista de inputs de disco para replicação com cada item da lista que representa um disco no local. Crie tantos itens de lista como o número de discos no VM no local. Substitua a propriedade **diskId** no item da lista para o uuid dos discos identificados no passo anterior. Detete o valor **isOSDisk** como "verdadeiro" para o disco OS do VM e "falso" para todos os outros discos. Deixe inalterado o **logStorageAccountId** e as propriedades **do logStorageAccountSasSecretName.** Detete o valor **do discoType** para o tipo disco gerido azure *(Standard_LRS, Premium_LRS, StandardSSD_LRS*) para utilizar para o disco. Para os discos que precisam de ser encriptados com CMK, adicione uma propriedade chamada **diskEncryptionSetId** e detetete o valor para o ID de recurso do conjunto de encriptação do disco**criado($des. Id**) no passo Criar um Conjunto de *Encriptação de Disco*
- Guarde o ficheiro do modelo editado. Para o exemplo acima, o ficheiro de modelo editado é o seguinte:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Configurar a replicação

Agora pode implementar o modelo de Gestor de Recursos editado para o grupo de recursos do projeto para configurar a replicação para o VM. Saiba como implementar recursos com modelos de Gestor de [Recursos Azure e PowerShell Azure](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Passos seguintes

[Monitorize](tutorial-migrate-vmware.md#track-and-monitor) o estado de replicação através da experiência do portal e realize migrações de teste e migração.
