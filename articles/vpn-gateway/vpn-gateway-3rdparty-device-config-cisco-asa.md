---
title: Configuração da amostra para ligar dispositivos Cisco ASA aos gateways VpN Azure
description: Este artigo fornece uma configuração de amostra para ligar dispositivos Cisco ASA aos gateways VpN Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/19/2018
ms.author: yushwang
ms.openlocfilehash: 96e5c26ea7b5f1baa33fd8830491ee3aa1e60221
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778087"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Configuração da amostra: Dispositivo Cisco ASA (IKEv2/no BGP)
Este artigo fornece configurações de amostras para ligar dispositivos cisco adaptive security appliance (ASA) aos gateways Azure VPN. O exemplo aplica-se aos dispositivos Cisco ASA que estão a funcionar IKEv2 sem o Protocolo de Gateway fronteiriço (BGP). 

## <a name="device-at-a-glance"></a>Dispositivo num ápice

|                        |                                   |
| ---                    | ---                               |
| Fornecedor de dispositivos          | Cisco                             |
| Modelo do dispositivo           | ASA                               |
| Versão do alvo         | 8.4 e mais tarde                     |
| Modelo testado           | ASA 5505                          |
| Versão testada         | 9.2                               |
| Versão IKE            | IKEv2                             |
| BGP                    | Não                                |
| Tipo de gateway Azure VPN | Gateway VPN baseado em rota           |
|                        |                                   |

> [!NOTE]
> A configuração da amostra liga um dispositivo Cisco ASA a um gateway VPN **baseado em rota** Azure. A ligação utiliza uma política personalizada iPsec/IKE com a opção **UsePolicyBasedTrafficSelectors,** conforme descrito [neste artigo](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> A amostra requer que os dispositivos ASA utilizem a política **IKEv2** com configurações baseadas em listas de acesso, e não baseadas em VTI. Consulte as especificações do fornecedor de dispositivos VPN para verificar se a política IKEv2 é suportada nos seus dispositivos VPN no local.


## <a name="vpn-device-requirements"></a>Requisitos do dispositivo VPN
Os gateways Azure VPN utilizam as suites padrão de protocolo IPsec/IKE para estabelecer túneis VPN local-a-local (S2S). Para os parâmetros de protocolo IPsec/IKE detalhados e algoritmos criptográficos padrão para gateways VpN Azure, consulte [sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Pode especificar opcionalmente uma combinação exata de algoritmos criptográficos e pontos fortes chave para uma ligação específica, como descrito em [sobre requisitos criptográficos](vpn-gateway-about-compliance-crypto.md). Se especificar uma combinação exata de algoritmos e pontos fortes chave, certifique-se de que utiliza as especificações correspondentes nos seus dispositivos VPN.

## <a name="single-vpn-tunnel"></a>Túnel VPN único
Esta configuração consiste num único túnel S2S VPN entre um gateway Azure VPN e um dispositivo VPN no local. Pode configurar opcionalmente o BGP através do túnel VPN.

![Túnel VPN S2S único](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Para obter instruções passo a passo para construir as configurações do Azure, consulte a configuração do [túnel VPN único](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Rede virtual e informação de gateway VPN
Esta secção lista os parâmetros para a amostra.

| **Parâmetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefixos de endereço de rede virtual        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN gateway IP         | Azure_Gateway_Public_IP      |
| Prefixos de endereço no local | 10.51.0.0/16<br>10.52.0.0/16 |
| NO local, dispositivo VPN IP    | OnPrem_Device_Public_IP     |
| * Rede virtual BGP ASN                | 65010                        |
| * Ip peer Azure BGP           | 10.12.255.30                 |
| * No local BGP ASN         | 65050                        |
| * No local BGP peer IP     | 10.52.255.254                |
|                              |                              |

\*Parâmetro opcional apenas para BGP.

### <a name="ipsecike-policy-and-parameters"></a>Política e parâmetros IPsec/IKE
A tabela seguinte lista os algoritmos IPsec/IKE e os parâmetros utilizados na amostra. Consulte as especificações do seu dispositivo VPN para verificar os algoritmos suportados pelos seus modelos de dispositivoVPN e versões de firmware.

| **IPsec/IKEv2**  | **Valor**                            |
| ---              | ---                                  |
| Encriptação IKEv2 | AES256                               |
| Integridade do IKEv2  | SHA384                               |
| Grupo DH         | DHGroup24                            |
| Encriptação IPsec | AES256                               |
| * Integridade IPsec  | SHA1                                 |
| Grupo PFS        | PFS24                                |
| Duração de SA QM   | 7.200 segundos                         |
| Seletor de tráfego | Política de UtilizaçãoSeleccionadoSDesseleccionados$True |
| Chave Pré-partilhada   | Chave Pré-Partilhada                         |
|                  |                                      |

\*Em alguns dispositivos, a Integridade do IPsec deve ser um valor nulo quando o algoritmo de encriptação IPsec é AES-GCM.

### <a name="asa-device-support"></a>Suporte ao dispositivo ASA

* O suporte para O IKEv2 requer a versão ASA 8.4 e posteriormente.

* O suporte para o Grupo DH e o Grupo PFS para além do Grupo 5 requer a versão ASA 9.x.

* O suporte para encriptação IPsec com AES-GCM e IPsec Integrity com SHA-256, SHA-384 ou SHA-512, requer a versão ASA 9.x. Este requisito de suporte aplica-se aos dispositivos ASA mais recentes. No momento da publicação, os modelos ASA 5505, 5510, 5520, 5540, 5550 e 5580 não suportam estes algoritmos. Consulte as especificações do seu dispositivo VPN para verificar os algoritmos suportados pelos seus modelos de dispositivoVPN e versões de firmware.


### <a name="sample-device-configuration"></a>Configuração do dispositivo de amostra
O script fornece uma amostra baseada na configuração e parâmetros descritos nas secções anteriores. A configuração do túnel S2S VPN consiste nas seguintes peças:

1. Interfaces e rotas
2. Listas de acesso
3. Política e parâmetros IKE (fase 1 ou modo principal)
4. Política e parâmetros iPsec (fase 2 ou modo rápido)
5. Outros parâmetros, tais como a fixação de MSS tCP

> [!IMPORTANT]
> Complete os seguintes passos antes de utilizar o script da amostra. Substitua os valores do espaço reservado no script com as definições do dispositivo para a sua configuração.

* Especifique a configuração da interface tanto para as interfaces internas como externas.
* Identifique as rotas para as suas redes interiores/privadas e externas/públicas.
* Certifique-se de que todos os nomes e números de políticas são únicos no seu dispositivo.
* Certifique-se de que os algoritmos criptográficos são suportados no seu dispositivo.
* Substitua os **seguintes valores** do espaço reservado por valores reais para a sua configuração:
  - Nome da interface exterior: **fora**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - **Pre_Shared_Key**
  - Rede virtual e nomes de gateway de rede local: **VNetName** e **LNGName**
  - **Prefixos** de endereços de rede virtual e de rede no local
  - Máscaras **de rede** adequadas

#### <a name="sample-script"></a>Script de exemplo

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Comandos simples de depuração

Utilize os seguintes comandos ASA para fins de depuração:

* Mostrar a Associação de Segurança IPsec ou IKE (SA):
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Introduza o modo de depuração:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    Os `debug` comandos podem gerar uma saída significativa na consola.

* Mostre as configurações atuais no dispositivo:
    ```
    show run
    ```
    Utilize `show` subcomandos para listar partes específicas da configuração do dispositivo, por exemplo:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Passos seguintes
Para configurar as instalações transactivas ativas ativas e as ligações VNet-to-VNet, consulte os [gateways VPN ativos da Configuração](vpn-gateway-activeactive-rm-powershell.md).
