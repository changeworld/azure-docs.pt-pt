---
title: Crie um VM com um endereço IP público estático - PowerShell / Microsoft Docs
description: Aprenda a criar um VM com um endereço IP público estático usando powerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 0eb4f86a2484486658171ab4b099794e4ba3e4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043390"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Crie uma máquina virtual com um endereço IP público estático usando powerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite-lhe comunicar com uma máquina virtual a partir da internet. Atribuir um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca se altera. Saiba mais sobre [endereços IP públicos estáticos.](virtual-network-ip-addresses-overview-arm.md#allocation-method) Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmica para estática, ou para trabalhar com endereços IP privados, ver [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Os endereços IP públicos têm uma [taxa nominal,](https://azure.microsoft.com/pricing/details/ip-addresses)e há um [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que você pode usar por subscrição.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Pode completar os seguintes passos do seu computador local ou utilizando a Casca de Nuvem Azure. Para utilizar o computador local, certifique-se de que tem o [Azure PowerShell instalado](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Para utilizar a Casca de Nuvem Azure, selecione **Experimente no** canto superior direito de qualquer caixa de comando que se siga. A Cloud Shell assina-te em Azure.

1. Se utilizar a Casca de Nuvem, salte para o passo 2. Abra uma sessão de comando `Connect-AzAccount`e assine em Azure com .
2. Crie um grupo de recursos com o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) O exemplo seguinte cria um grupo de recursos na região de East US Azure:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Crie uma máquina virtual com o comando [New-AzVM.](/powershell/module/az.Compute/New-azVM) A `-AllocationMethod "Static"` opção atribui um endereço IP público estático à máquina virtual. O exemplo seguinte cria uma máquina virtual do Windows Server com um endereço IP público sKU estático e básico chamado *myPublicIpAddress*. Quando solicitado, forneça um nome de utilizador e uma senha para serem usados como sinal de credenciais para a máquina virtual:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Se o endereço IP público tiver de ser um SKU padrão, tem de [criar um endereço IP público,](virtual-network-public-ip-address.md#create-a-public-ip-address)criar uma interface de [rede,](virtual-network-network-interface.md#create-a-network-interface) [atribuir o endereço IP público à interface](virtual-network-network-interface-addresses.md#add-ip-addresses)da rede e, em seguida, criar uma máquina virtual com a interface [de rede,](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)em passos separados. Saiba mais sobre o [endereço IP Público SKUs](virtual-network-ip-addresses-overview-arm.md#sku). Se a máquina virtual for adicionada ao pool de back-end de um equilíbrio público de carga Azure, o SKU do endereço IP público da máquina virtual deve corresponder ao SKU do endereço IP público do equilibrador de carga. Para mais detalhes, consulte [O Equilíbrio de Carga Sinuoso Azure](../load-balancer/concepts-limitations.md#skus).

4. Consulte o endereço IP público atribuído e confirme que foi criado como um endereço estático, com [Get-AzPublicIpAddress:](/powershell/module/az.network/get-azpublicipaddress)

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   O Azure atribuiu um endereço IP público a partir de endereços utilizados na região onde criou a máquina virtual. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

> [!WARNING]
> Não modifique as definições de endereço IP dentro do sistema operativo da máquina virtual. O sistema operativo desconhece os endereços IP públicos do Azure. Embora possa adicionar definições privadas de endereço IP ao sistema operativo, recomendamos que não o faça a menos que seja necessário, e não até depois de ler [Adicione um endereço IP privado a um sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure
- Saiba mais sobre todas as [definições de endereçoip público](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e atribuindo um [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) a uma máquina virtual Azure
- Saiba mais sobre a criação de máquinas virtuais [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
