---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184139"
---
Se estiver a trabalhar com o modelo de implementação do Gestor de Recursos, pode mudar para o novo gateway SKUs. Quando muda de um portal de gateway legacy SKU para um novo SKU, elimina o gateway VPN existente e cria um novo gateway VPN.

Fluxo de trabalho:

1. Remova várias ligações a um gateway de rede virtual.
2. Elimine o gateway de VPN antigo.
3. Crie o gateway de VPN novo.
4. Atualize os seus dispositivos VPN no local com o novo endereço IP do gateway do VPN (para ligações de rede).
5. Atualize o valor do endereço IP do gateway para gateways de rede local VNet para VNet que se ligam a este gateway.
6. Transfira novos pacotes de configuração do cliente VPN para clientes de P2S que se ligam à rede virtual por meio deste gateway VPN.
7. Volte a criar as ligações a um gateway de rede virtual.

Considerações:

* Para se mudar para as novas SKUs, o seu gateway VPN deve estar no modelo de implementação do Gestor de Recursos.
* Se tiver um gateway VPN clássico, deve continuar a usar o legado antigo SKUs para esse portal, no entanto, pode redimensionar entre o legado SKUs. Não pode mudar para as novas SKUs.
* Terá tempo de inatividade de conectividade quando mudar de um Legado SKU para um novo SKU.
* Ao mudar para um novo gateway SKU, o endereço IP público para o seu gateway VPN mudará. Isto acontece mesmo que especifique o mesmo objeto de endereço IP público que utilizou anteriormente.