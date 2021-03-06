---
title: Tutorial - Crie e gerencie um portal usando o Azure VPN Gateway
description: Tutorial - Criar e Gerir o gateway de VPN com o módulo do Azure PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 66efa0f2922e70908616c7c447d782efee8f6b1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79137182"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Tutorial: Criar e gerir um gateway VPN usando powerShell

Os gateways de VPN do Azure fornecem conectividade em vários locais entre as instalações do cliente e o Azure. Este tutorial abrange itens básicos da implementação do gateway de VPN do Azure, tais como criar e gerir um gateway de VPN. Saiba como:

> [!div class="checklist"]
> * Criar um gateway de VPN
> * Ver o endereço IP público
> * Redimensionar um gateway de VPN
> * Repor um Gateway de VPN

O diagrama seguinte mostra a rede virtual e o gateway de VPN criados como parte deste tutorial.

![VNet e gateway de VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="working-with-azure-cloud-shell-and-azure-powershell"></a>Trabalhando com Azure Cloud Shell e Azure PowerShell

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Valores de parâmetros de rede comuns

Abaixo estão os valores do parâmetro utilizados para este tutorial. Nos exemplos, as variáveis traduzem-se para o seguinte:

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

Altere os valores abaixo com base no seu ambiente e configuração da rede, em seguida, copie e cole para definir as variáveis para este tutorial. Se a sua sessão Cloud Shell passar do tempo, ou precisar de utilizar uma janela PowerShell diferente, copiar e colar as variáveis à sua nova sessão e continuar o tutorial.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Primeiro, tem de ser criado um grupo de recursos. No exemplo seguinte, é criado um grupo de recursos com o nome *TestRG1* na região *E.U.A. Leste*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

O gateway de VPN do Azure fornece conectividade em vários locais e a funcionalidade de servidor VPN P2S para a sua rede virtual. Adicione o gateway de VPN a uma rede virtual existente ou crie uma nova rede virtual e o gateway. Note que o exemplo especifica especificamente o nome da sub-rede gateway. Deve especificar sempre o nome da sub-rede gateway como "GatewaySubnet" para que funcione corretamente. Este exemplo cria uma nova rede virtual com três subredes: Frontend, Backend e GatewaySubnet utilizando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) e [New-AzVirtualNetwork:](/powershell/module/az.network/new-azvirtualnetwork)

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Pedir um endereço IP público para o gateway de VPN

Os gateways de VPN do Azure comunicam com os dispositivos VPN no local através da Internet para efetuar a negociação de IKE (Internet Key Exchange) e estabelecer túneis IPsec. Crie e atribua um endereço IP público ao seu gateway VPN, como mostrado no exemplo abaixo com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) e [New-AzVirtualNetworkGatewayIpConfig:](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig)

> [!IMPORTANT]
> Atualmente, só pode utilizar um endereço IP público dinâmico para o gateway. O endereço IP estático não é suportado nos gateways de VPN do Azure.

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>Criar um gateway de VPN

Um gateway de VPN pode demorar 45 minutos ou mais a ser criado. Depois de concluída a criação do gateway, pode criar uma ligação entre a rede virtual e outra VNet. Em alternativa, pode criar uma ligação entre a rede virtual e uma localização no local. Crie um gateway VPN utilizando o cmdlet [New-AzVirtualNetworkGateway.](/powershell/module/az.network/New-azVirtualNetworkGateway)

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Valores de parâmetros chave:
* GatewayType: utilize **Vpn** para ligações site a site e VNet a VNet
* VpnType: Utilize **RouteBased** para interagir com um leque mais vasto de dispositivos VPN e mais funcionalidades de encaminhamento
* GatewaySku: **VpnGw1** é o padrão; altere-o para outro VpnGw SKU se precisar de mais sonoros ou mais ligações. Para obter mais informações, veja [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Se estiver a utilizar o TryIt, a sua sessão pode sair. Tudo bem. O portal ainda vai criar.

Depois de concluída a criação do gateway, pode criar uma ligação entre a rede virtual e outra VNet ou criar uma ligação entre a rede virtual e uma localização no local. Também pode configurar uma ligação P2S para a sua VNet a partir de um computador cliente.

## <a name="view-the-gateway-public-ip-address"></a>Ver o endereço IP público gateway

Se souber o nome do endereço IP público, utilize o [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) para mostrar o endereço IP público atribuído ao portal.

Se a sua sessão tiver um prazo cronometrado, copie os parâmetros comuns da rede desde o início deste tutorial para a sua nova sessão e prossiga e, em seguida, proceda.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Redimensionar um portal

Pode alterar a SKU do gateway de VPN após a criação do gateway. Diferentes Gateway SKUs suportam diferentes especificações, tais como entradas, número de ligações, etc. O exemplo seguinte utiliza [resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) para redimensionar o seu portal de VpnGw1 para VpnGw2. Para obter mais informações, veja [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

O redimensionamento de um gateway de VPN também demora cerca de 30 a 45 minutos, embora esta operação **não** vá interromper ou remover as ligações e configurações existentes.

## <a name="reset-a-gateway"></a>Redefinir um portal

Como parte dos passos de resolução de problemas, pode repor o gateway de VPN do Azure para obrigar o gateway VPN a reiniciar as configurações de túnel IPsec/IKE. Utilize [reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) para redefinir o seu gateway.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Para obter mais informações, veja [Repor um gateway de VPN](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se está a avançar para o [próximo tutorial,](vpn-gateway-tutorial-vpnconnection-powershell.md)vai querer manter estes recursos porque são os pré-requisitos.

No entanto, se o gateway fizer parte de um protótipo, teste ou implementação de prova de conceito, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o gateway VPN e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre a criação e gestão básicas do gateway de VPN, como, por exemplo:

> [!div class="checklist"]
> * Criar um gateway de VPN
> * Ver o endereço IP público
> * Redimensionar um gateway de VPN
> * Repor um Gateway de VPN

Avance para os tutoriais seguintes para saber mais sobre ligações S2S, VNet a VNet e P2S.

> [!div class="nextstepaction"]
> * [Criar ligações S2S](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Criar ligações VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Criar ligações P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
