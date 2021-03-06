---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343053"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

Pode criar um instantâneo incremental com o Azure CLI, vai precisar da versão mais recente do Azure CLI. 

No Windows, o seguinte comando irá instalar ou atualizar a sua instalação existente para a versão mais recente:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
No Linux, a instalação CLI variará consoante a versão do sistema operativo.  Consulte [a instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para a sua versão Linux em particular.

Para criar um instantâneo incremental, utilize `--incremental` [az snapshot criar](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) com o parâmetro.

O exemplo seguinte cria um `<yourDesiredSnapShotNameHere>` `<yourResourceGroupNameHere>`instantâneo`<exampleDiskName>`incremental, substitua, e `<exampleLocation>` com os seus próprios valores, em seguida, execute o exemplo:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Pode identificar instantâneos incrementais do `SourceResourceId` mesmo `SourceUniqueId` disco com as propriedades e as propriedades dos instantâneos. `SourceResourceId`é o Id de recurso do gestor de recursos Azure do disco-mãe. `SourceUniqueId`é o valor herdado da `UniqueId` propriedade do disco. Se apagar um disco e, em seguida, criar um novo `UniqueId` disco com o mesmo nome, o valor da propriedade muda.

Pode utilizar `SourceResourceId` `SourceUniqueId` e criar uma lista de todos os instantâneos associados a um determinado disco. O exemplo seguinte listará todos os instantâneos incrementais associados a um determinado disco, mas requer alguma configuração.

Este exemplo utiliza jq para consulta dos dados. Para executar o exemplo, tem de [instalar jq](https://stedolan.github.io/jq/download/).

`<yourResourceGroupNameHere>` Substitua `<exampleDiskName>` e com os seus valores, então pode usar o seguinte exemplo para listar os seus instantâneos incrementais existentes, desde que também tenha instalado jq:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
```

## <a name="resource-manager-template"></a>Modelo do Resource Manager

Também pode usar modelos do Gestor de Recursos Azure para criar um instantâneo incremental. Terá de se certificar de que a apiVersion está definida para **2019-03-01** e que a propriedade incremental também está definida como verdadeira. O seguinte corte é um exemplo de como criar um instantâneo incremental com modelos de Gestor de Recursos:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

Se quiser ver o código de amostra que demonstra a capacidade diferencial de instantâneos incrementais, utilizando .NET, consulte cópia [seleções de discos geridos por Copy Azure para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
