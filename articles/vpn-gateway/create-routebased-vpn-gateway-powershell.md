---
title: 'Gateway Azure VPN: Criar gateway baseado em rotas: PowerShell'
description: Crie rapidamente um VPN Gateway baseado em rotas usando powerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: 8a4bb9d2ac7b8124fa9b1e00f3ecceda4f4a4cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152963"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Criar um gateway VPN baseado em rotas usando powerShell

Este artigo ajuda-o a criar rapidamente um gateway Azure VPN baseado em rotas usando o PowerShell. Um gateway VPN é usado ao criar uma ligação VPN à sua rede no local. Também pode utilizar um gateway VPN para ligar VNets.

## <a name="before-you-begin"></a>Antes de começar

Os passos neste artigo criarão um VNet, uma subnet, uma subnet gateway e um gateway VPN baseado em rota (gateway de rede virtual). Uma vez concluída a criação do portal, pode criar ligações. Estes passos requerem uma subscrição Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos. Se estiver a executar o PowerShell localmente, abra a consola PowerShell `Connect-AzAccount` com privilégios elevados e ligue-se ao Azure utilizando o comando.

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Criar uma rede virtual

Criar uma rede virtual com [new-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual chamada **VNet1** na localização **EastUS:**

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Crie uma configuração de sub-rede utilizando o cmdlet [New-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Detete a configuração da sub-rede para a rede virtual utilizando o cmdlet [Set-AzVirtualNetwork.](/powershell/module/az.network/Set-azVirtualNetwork)


```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Adicionar uma sub-rede do gateway

A sub-rede gateway contém os endereços IP reservados que os serviços de gateway da rede virtual utilizam. Utilize os seguintes exemplos para adicionar uma sub-rede de gateway:

Detete uma variável para o seu VNet.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Crie a sub-rede de gateway utilizando o [add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-azVirtualNetworkSubnetConfig) cmdlet.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Detete a configuração da sub-rede para a rede virtual utilizando o cmdlet [Set-AzVirtualNetwork.](/powershell/module/az.network/Set-azVirtualNetwork)

```azurepowershell-interactive
$vnet | Set-AzVirtualNetwork
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Solicite um endereço IP público

Um gateway VPN deve ter um endereço IP público dinamicamente atribuído. Quando cria uma ligação a um gateway VPN, este é o endereço IP que especifica. Utilize o seguinte exemplo para solicitar um endereço IP público:

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="create-the-gateway-ip-address-configuration"></a><a name="GatewayIPConfig"></a>Criar a configuração do endereço IP gateway

A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo seguinte para criar a configuração do seu gateway:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Criar o gateway de VPN

Um gateway de VPN pode demorar 45 minutos ou mais a ser criado. Uma vez concluída a porta de entrada, pode criar uma ligação entre a sua rede virtual e outro VNet. Ou criar uma ligação entre a sua rede virtual e um local no local. Crie um gateway VPN utilizando o cmdlet [New-AzVirtualNetworkGateway.](/powershell/module/az.network/New-azVirtualNetworkGateway)

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Ver o gateway VPN

Pode visualizar o gateway VPN utilizando o cmdlet [Get-AzVirtualNetworkGateway.](/powershell/module/az.network/Get-azVirtualNetworkGateway)

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

A saída será semelhante a este exemplo:

```
Name                   : VNet1GW
ResourceGroupName      : TestRG1
Location               : eastus
Id                     : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provide
                         rs/Microsoft.Network/virtualNetworkGateways/VNet1GW
Etag                   : W/"0952d-9da8-4d7d-a8ed-28c8ca0413"
ResourceGuid           : dc6ce1de-2c4494-9d0b-20b03ac595
ProvisioningState      : Succeeded
Tags                   :
IpConfigurations       : [
                           {
                             "PrivateIpAllocationMethod": "Dynamic",
                             "Subnet": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet"
                             },
                             "PublicIpAddress": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP"
                             },
                             "Name": "default",
                             "Etag": "W/\"0952d-9da8-4d7d-a8ed-28c8ca0413\"",
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                         RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/de
                         fault"
                           }
                         ]
GatewayType            : Vpn
VpnType                : RouteBased
EnableBgp              : False
ActiveActive           : False
GatewayDefaultSite     : null
Sku                    : {
                           "Capacity": 2,
                           "Name": "VpnGw1",
                           "Tier": "VpnGw1"
                         }
VpnClientConfiguration : null
BgpSettings            : {
     
```

## <a name="view-the-public-ip-address"></a><a name="viewgwpip"></a>Ver o endereço IP público

Para ver o endereço IP público para o seu gateway VPN, utilize o cmdlet [Get-AzPublicIpAddress.](/powershell/module/az.network/Get-azPublicIpAddress)

```azurepowershell-interactive
Get-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

Na resposta do exemplo, o valor ipAddress é o endereço IP público.

```
Name                     : VNet1GWIP
ResourceGroupName        : TestRG1
Location                 : eastus
Id                       : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provi
                           ders/Microsoft.Network/publicIPAddresses/VNet1GWIP
Etag                     : W/"5001666a-bc2a-484b-bcf5-ad488dabd8ca"
ResourceGuid             : 3c7c481e-9828-4dae-abdc-f95b383
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.90.153.3
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                           RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/
                           default"
                           }
DnsSettings              : null
Zones                    : {}
Sku                      : {
                             "Name": "Basic"
                           }
IpTags                   : {}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou, utilize o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para eliminar o grupo de recursos. Isto elimina o grupo de recursos e todos os recursos contidos no mesmo.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Passos seguintes

Uma vez que o gateway termine de criar, pode criar uma ligação entre a sua rede virtual e outro VNet. Ou criar uma ligação entre a sua rede virtual e um local no local.

> [!div class="nextstepaction"]
> [Criar uma ligação site a site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Criar uma ligação de ponto a site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Criar uma ligação a outro VNet](vpn-gateway-vnet-vnet-rm-ps.md)
