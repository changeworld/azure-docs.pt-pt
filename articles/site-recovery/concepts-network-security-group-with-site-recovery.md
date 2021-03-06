---
title: Grupos de Segurança da Rede com Recuperação do Site Azure Microsoft Docs
description: Descreve como usar grupos de segurança de rede com recuperação de site azure para recuperação e migração de desastres
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: eb5ba99133f5726c44164b0ba45b7ab5d94e292f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292370"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Grupos de segurança de rede com recuperação do site Azure

Os Grupos de Segurança da Rede são utilizados para limitar o tráfego de rede aos recursos numa rede virtual. Um Grupo de Segurança de [Rede (NSG)](../virtual-network/security-overview.md#network-security-groups) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede de entrada ou saída com base no endereço IP, porto e protocolo de origem ou destino.

No âmbito do modelo de implementação do Gestor de Recursos, os NSGs podem ser associados a subredes ou interfaces de rede individuais. Quando um NSG é associado a uma sub-rede, as regras são aplicadas a todos os recursos ligados à mesma. O tráfego pode ainda ser restringido associando um NSG a interfaces de rede individuais dentro de uma subnet que já tem um NSG associado.

Este artigo descreve como pode utilizar grupos de segurança de rede com a recuperação do site Azure.

## <a name="using-network-security-groups"></a>Utilização de grupos de segurança de rede

Uma sub-rede individual pode ter zero, ou uma, NSG associada. Uma interface de rede individual também pode ter zero, ou um, NSG associado. Assim, você pode efetivamente ter dupla restrição de tráfego para uma máquina virtual associando um NSG primeiro a uma subnet, e depois outro NSG à interface de rede do VM. A aplicação das regras do NSG neste caso depende da direção do tráfego e da prioridade das regras de segurança aplicadas.

Considere um exemplo simples com uma máquina virtual da seguinte forma:
-    A máquina virtual é colocada dentro da **Subnet Contoso.**
-    **Contoso Subnet** está associado à **Subnet NSG**.
-    A interface de rede VM está adicionalmente associada ao **VM NSG**.

![NSG com recuperação do site](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

Neste exemplo, para o tráfego de entrada, o SUBnet NSG é avaliado primeiro. Qualquer tráfego permitido através da Subnet NSG é então avaliado pela VM NSG. O inverso é aplicável ao tráfego de saída, com a VM NSG a ser avaliada primeiro. Qualquer tráfego permitido através do VM NSG é então avaliado pela Subnet NSG.

Isto permite a aplicação de regra de segurança granular. Por exemplo, é possível permitir o acesso à Internet a alguns VMs de aplicação (como VMs frontais) sob uma subnet, mas restringir o acesso à Internet a outros VMs (como base de dados e outros VMs de backend). Neste caso, pode ter uma regra mais branda sobre o NSG subnet, permitindo o tráfego de internet e restringir o acesso a VMs específicos, negando o acesso em VM NSG. O mesmo pode ser aplicado ao tráfego de saída.

Ao configurar tais configurações de NSG, certifique-se de que as prioridades corretas são aplicadas às regras de [segurança](../virtual-network/security-overview.md#security-rules). As regras são processadas por ordem de prioridade, sendo os números mais baixos processados antes dos mais elevados, uma vez que têm uma prioridade superior. Quando o tráfego corresponder a uma regra, o processamento para. Como resultado, qualquer regra que exista com prioridades inferiores (números mais elevados) e que tenham os mesmos atributos das regras com prioridades superiores não é processada.

É possível que não esteja sempre ciente de que os grupos de segurança de rede estão aplicados, quer à interface de rede, quer à sub-rede. Pode verificar as regras agregadas aplicadas a uma interface de rede, visualizando as [regras de segurança efetivas](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) para uma interface de rede. Também pode utilizar a capacidade de [verificação](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) de fluxo IP no [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) para determinar se a comunicação é permitida ou a partir de uma interface de rede. A ferramenta indica se é permitida a comunicação e que regra de segurança de rede permite ou nega o tráfego.

## <a name="on-premises-to-azure-replication-with-nsg"></a>No local para a replicação de Azure com NSG

A Recuperação do Site Azure permite a recuperação de desastres e migração para Azure para [máquinas virtuais Hyper-V](hyper-v-azure-architecture.md)no local, [máquinas virtuais VMware](vmware-azure-architecture.md)e [servidores físicos.](physical-azure-architecture.md) Para todos os cenários do Azure, os dados de replicação são enviados e armazenados numa conta de Armazenamento Azure. Durante a replicação, não se paga nenhuma carga virtual de máquina. Quando executa uma falha no Azure, a Recuperação do Site cria automaticamente máquinas virtuais Azure IaaS.

Uma vez criados VMs após falha para Azure, nsGs podem ser usados para limitar o tráfego de rede para a rede virtual e VMs. A Recuperação do Site não cria NSGs como parte da operação de failover. Recomendamos a criação dos NSGs Azure necessários antes de dar início à falha. Pode então associar nsGs a falhar em VMs automaticamente durante a failover, usando scripts de automação com os poderosos planos de recuperação da [Recuperação](site-recovery-create-recovery-plans.md)do Site .

Por exemplo, se a configuração vm pós-falha for semelhante ao cenário de [exemplo](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) acima descrito:
-    Pode criar **Contoso VNet** e **Contoso Subnet** como parte do planeamento da DR na região-alvo do Azure.
-    Também pode criar e configurar tanto a **Subnet NSG** como a **VM NSG** como parte do mesmo planeamento de DR.
-    **A Subnet NSG** pode então ser imediatamente associada à **Subnet Contoso,** uma vez que tanto o NSG como a subnet já estão disponíveis.
-    **A VM NSG** pode ser associada com VMs durante a failover usando planos de recuperação.

Uma vez que os NSGs são criados e configurados, recomendamos executar um [teste failover](site-recovery-test-failover-to-azure.md) para verificar associações nsg scripted e conectividade VM pós-failover.

## <a name="azure-to-azure-replication-with-nsg"></a>Réplica azure para Azure com NSG

A Recuperação do Sítio Azure permite a recuperação de desastres de [máquinas virtuais Azure.](azure-to-azure-architecture.md) Ao permitir a replicação para VMs Azure, a Recuperação do Site pode criar as redes virtuais de réplica (incluindo subredes e subredes de gateway) na região alvo e criar os mapeamentos necessários entre a fonte e as redes virtuais alvo. Também pode pré-criar as redes laterais-alvo e as subredes, e usar o mesmo enquanto permite a replicação. A Recuperação do Site não cria Quaisquer VMs na região-alvo do Azure antes do [fracasso](azure-to-azure-tutorial-failover-failback.md).

Para a replicação do Azure VM, certifique-se de que as regras do NSG sobre a região de origem Azure permitem a [conectividade de saída](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) para o tráfego de replicação. Também pode testar e verificar estas regras necessárias através desta [configuração de NSG](azure-to-azure-about-networking.md#example-nsg-configuration)exemplo .

A Recuperação do Site não cria nem replica NSGs como parte da operação de failover. Recomendamos a criação dos NSGs necessários na região-alvo de Azure antes de dar início à falha. Pode então associar nsGs a falhar em VMs automaticamente durante a failover, usando scripts de automação com os poderosos planos de recuperação da [Recuperação](site-recovery-create-recovery-plans.md)do Site .

Tendo em conta o cenário de [exemplo](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) descrito anteriormente:
-    A Recuperação do Site pode criar réplicas de **Contoso VNet** e **Contoso Subnet** na região-alvo azure quando a replicação está ativada para o VM.
-    Pode criar as réplicas desejadas da **Subnet NSG** e **vM NSG** (nomeada, por exemplo, **Target Subnet NSG** e **Target VM NSG**, respectivamente) na região-alvo do Azure, permitindo quaisquer regras adicionais exigidas na região alvo.
-    **A sub-rede-alvo NSG** pode então ser imediatamente associada à sub-rede da região alvo, uma vez que tanto o NSG como a subnet já estão disponíveis.
-    **O Target VM NSG** pode ser associado com VMs durante a falha utilizando planos de recuperação.

Uma vez que os NSGs são criados e configurados, recomendamos executar um [teste failover](azure-to-azure-tutorial-dr-drill.md) para verificar associações nsg scripted e conectividade VM pós-failover.

## <a name="next-steps"></a>Passos seguintes
-    Saiba mais sobre grupos de [segurança de rede.](../virtual-network/security-overview.md#network-security-groups)
-    Saiba mais sobre [as regras](../virtual-network/security-overview.md#security-rules)de segurança do NSG .
-    Saiba mais sobre regras de [segurança eficazes](../virtual-network/diagnose-network-traffic-filter-problem.md) para um NSG.
-    Saiba mais sobre [os planos](site-recovery-create-recovery-plans.md) de recuperação para automatizar falha na aplicação.
