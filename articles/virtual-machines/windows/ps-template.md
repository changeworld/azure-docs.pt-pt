---
title: Crie um VM windows a partir de um modelo em Azure
description: Utilize um modelo de Gestor de Recursos e PowerShell para criar facilmente um novo VM do Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99e292930414ae027c9cbbf3a901d550041899d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74065553"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Criar uma máquina virtual do Windows a partir de um modelo de Gestor de Recursos

Aprenda a criar uma máquina virtual do Windows utilizando um modelo de Gestor de Recursos Azure e Azure PowerShell da concha Azure Cloud. O modelo utilizado neste artigo implementa uma única máquina virtual que executa o Windows Server numa nova rede virtual com uma única subnet. Para criar uma máquina virtual Linux, consulte [como criar uma máquina virtual Linux com modelos de Gestor de Recursos Azure](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

A criação de uma máquina virtual Azure geralmente inclui dois passos:

- Crie um grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual.
- Cria uma máquina virtual.

O exemplo seguinte cria um VM a partir de um [modelo Azure Quickstart](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Aqui está uma cópia do modelo:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Para executar o script PowerShell, selecione **Experimente-o** para abrir a concha Azure Cloud. Para colar o script, clique na concha e, em seguida, selecione **Pasta:**

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Se optar por instalar e utilizar o PowerShell localmente em vez da concha Azure Cloud, este tutorial requer o módulo PowerShell Azure. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar a PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

No exemplo anterior, especificou um modelo armazenado no GitHub. Também pode descarregar ou criar um modelo e `--template-file` especificar o caminho local com o parâmetro.

Seguem-se alguns recursos adicionais:

- Para aprender a desenvolver modelos de Gestor de Recursos, consulte a [documentação do Gestor de Recursos do Azure.](/azure/azure-resource-manager/)
- Para ver os esquemas de máquinas virtuais Azure, consulte a referência do [modelo Azure](/azure/templates/microsoft.compute/allversions).
- Para ver mais amostras de modelos de máquinas virtuais, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

O último comando PowerShell do script anterior mostra o nome da máquina virtual. Para ligar à máquina virtual, consulte [como ligar e iniciar sessão numa máquina virtual Azure que executa](./connect-logon.md)o Windows .

## <a name="next-steps"></a>Passos Seguintes

- Se houver problemas com a implementação, poderá ver os erros comuns de implantação do [Azure com o Azure Resource Manager.](../../resource-manager-common-deployment-errors.md)
- Aprenda a criar e gerir uma máquina virtual em [Criar e gerir VMs do Windows com o módulo PowerShell Azure](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para saber mais sobre a criação de modelos, consulte a sintaxe jSON e propriedades para os tipos de recursos que implementou:

- [Microsoft.Network/publicIPAddresss](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
