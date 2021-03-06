---
title: 'Azure VPN Gateway: Anuncie rotas personalizadas para clientes VpN P2S'
description: Passos para anunciar rotas personalizadas para os seus clientes ponto-a-site
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 3588755e2aab1c84d443e917eca8c7fca280b49a
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756893"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Anuncie rotas personalizadas para clientes VpN P2S

Pode querer anunciar rotas personalizadas para todos os seus clientes VPN ponto-a-site. Por exemplo, quando ativou pontos finais de armazenamento no seu VNet e quer que os utilizadores remotos possam aceder a estas contas de armazenamento através da ligação VPN. Pode anunciar o endereço IP do ponto final de armazenamento a todos os seus utilizadores remotos para que o tráfego para a conta de armazenamento atravesse o túnel VPN, e não a Internet pública.

![Exemplo de ligação Multilocal de Gateway de VPN do Azure](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Para anunciar rotas personalizadas

Para anunciar rotas personalizadas, use o `Set-AzVirtualNetworkGateway cmdlet`. O exemplo que se segue mostra como anunciar o IP para as tabelas de contas de [armazenamento Contoso.](https://contoso.table.core.windows.net)

1. Ping *contoso.table.core.windows.net* e note o endereço IP. Por exemplo:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Execute os seguintes comandos do PowerShell:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Para adicionar várias rotas personalizadas, utilize uma vírem e espaços para separar os endereços. Por exemplo:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Para ver rotas personalizadas

Utilize o seguinte exemplo para ver rotas personalizadas:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Para eliminar rotas personalizadas

Utilize o seguinte exemplo para eliminar rotas personalizadas:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais sobre o encaminhamento p2S, consulte [sobre o encaminhamento ponto-a-local](vpn-gateway-about-point-to-site-routing.md).
