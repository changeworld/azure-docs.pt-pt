---
title: Analisar a segurança da rede - Security Group View - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Este artigo descreverá como usar o PowerShell para analisar uma segurança de máquinas virtuais com a Security Group View.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 4cba2c7e25b5f76b0638da1c551514f102247ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840796"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analise a sua segurança da Máquina Virtual com vista de grupo de segurança usando powerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

A visão do grupo de segurança devolução de devoluções configuradas e eficazes de segurança da rede que são aplicadas a uma máquina virtual. Esta capacidade é útil para auditar e diagnosticar Grupos de Segurança da Rede e regras que estão configuradas num VM para garantir que o tráfego está a ser corretamente permitido ou negado. Neste artigo, mostramos-lhe como recuperar as regras de segurança configuradas e eficazes a uma máquina virtual usando powerShell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, você `Get-AzNetworkWatcherSecurityGroupView` executa o cmdlet para recuperar a informação da regra de segurança.

Este cenário pressupõe que já seguiu os passos na [Create a Network Watcher](network-watcher-create.md) para criar um Observador de Rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo recupera as regras de segurança configuradas e eficazes para uma determinada máquina virtual.

## <a name="retrieve-network-watcher"></a>Observador de rede de recuperação

O primeiro passo é recuperar a instância do Observador da Rede. Esta variável é `Get-AzNetworkWatcherSecurityGroupView` passada para o cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Obter um VM

É necessária uma máquina virtual `Get-AzNetworkWatcherSecurityGroupView` para passar o cmdlet contra. O exemplo que se segue recebe um objeto VM.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Recuperar a visão do grupo de segurança

O próximo passo é recuperar o resultado da visão do grupo de segurança.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Visualização dos resultados

O exemplo seguinte é uma resposta encurtada dos resultados devolvidos. Os resultados mostram todas as regras de segurança eficazes e aplicadas na máquina virtual desagregadas em grupos de **NetworkInterfaceSecurityRules,** **DefaultSecurityRules**e **EffectiveSecurityRules**.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Passos seguintes

Visite os Grupos de Segurança da [Rede auditada (NSG) com](network-watcher-nsg-auditing-powershell.md) o Observador da Rede para saber automatizar a validação de Grupos de Segurança da Rede.


