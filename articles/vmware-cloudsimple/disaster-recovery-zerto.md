---
title: Solução Azure VMware by CloudSimple - Use a Nuvem Privada como local de desastre para cargas de trabalho no local
description: Descreve como configurar a sua CloudSimple Private Cloud como um local de recuperação de desastres para cargas de trabalho vMware no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083145"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Configurar cloudSimple Private Cloud como um local de recuperação de desastres para cargas de trabalho vMware no local

A cloudSimple Private Cloud pode ser configurada como um local de recuperação para aplicações no local para fornecer continuidade ao negócio em caso de desastre. A solução de recuperação baseia-se na Replicação Virtual zerto como plataforma de replicação e orquestração. As máquinas virtuais de infraestrutura crítica e aplicação podem ser replicadas continuamente desde o seu vCenter no local até à sua Nuvem Privada. Pode utilizar a sua Cloud Privada para testes de failover e para garantir a disponibilidade da sua aplicação durante um desastre. Uma abordagem semelhante pode ser seguida para configurar a Nuvem Privada como um local primário que é protegido por um local de recuperação em um local diferente.

> [!NOTE]
> Consulte o documento Zerto [dimensionando considerações para a replicação virtual zerto](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) para obter orientações sobre dimensionamento do seu ambiente de recuperação de desastres.

A solução CloudSimple:

* Elimina a necessidade de criar um datacenter especificamente para a recuperação de desastres (DR).
* Permite-lhe aproveitar as localizações do Azure onde a CloudSimple está implantada para resiliência geográfica mundial.
* Dá-lhe a opção de reduzir os custos de implantação e o custo total de propriedade para a DR.

A solução requer:

* Instale, configure e gereo Zerto na sua Nuvem Privada.
* Forneça as suas próprias licenças para zerto quando a Nuvem Privada for o site protegido. Você pode emparelhar Zerto correndo no site CloudSimple com o seu site no local para licenciamento.

A figura que se segue mostra a arquitetura para a solução Zerto.

![Arquitetura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Como implementar a solução

As seguintes secções descrevem como implementar uma solução DR utilizando a Replicação Virtual Zerto na sua Nuvem Privada.

1. [Pré-requisitos](#prerequisites)
2. [Configuração opcional na CloudSimple Private Cloud](#optional-configuration-on-your-private-cloud)
3. [Configurar ZVM e VRA na CloudSimple Private Cloud](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Criar o Grupo de Proteção Virtual Zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Pré-requisitos

Para ativar a Replicação Virtual Zerto do seu ambiente no local até à sua Nuvem Privada, complete os seguintes pré-requisitos.

1. [Instale uma ligação VPN site-to-site entre a sua rede no local e a sua CloudSimple Private Cloud](set-up-vpn.md).
2. [Configurar a procura dNS para que os seus componentes de gestão private Cloud sejam encaminhados para servidores DNS de Nuvem Privada](on-premises-dns-setup.md).  Para permitir o reencaminhamento da procura de DNS, crie uma entrada `*.cloudsimple.io` de zona de reencaminhamento no seu servidor DNS no local para servidores DNS CloudSimple.
3. Configurar a procura dNS de modo a que os componentes vCenter no local sejam encaminhados para servidores DNS no local.  Os servidores DNS devem ser acessíveis a partir da sua CloudSimple Private Cloud através do Site-to-Site VPN. Para assistência, apresente um pedido de [apoio,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)fornecendo as seguintes informações.  

    * No local, nome de domínio DNS
    * No local, os endereços IP do servidor DNS

4. Instale um servidor Windows na sua Cloud Privada. O servidor é utilizado para instalar o Zerto Virtual Manager.
5. [Aumente os privilégios CloudSimple.](escalate-private-cloud-privileges.md)
6. Crie um novo utilizador no seu vCenter Private Cloud com a função administrativa a utilizar como conta de serviço para O Gestor Virtual Zerto.

### <a name="optional-configuration-on-your-private-cloud"></a>Configuração opcional na sua Nuvem Privada

1. Crie um ou mais recursos no seu vCenter de Nuvem Privada para usar como conjuntos de recursos-alvo para VMs a partir do seu ambiente no local.
2. Crie uma ou mais pastas no seu vCenter De Nuvem Privada para usar como pastas-alvo para VMs a partir do seu ambiente no local.
3. Crie VLANs para a rede failover e crie regras de firewall. Abra um pedido de [apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para assistência.
4. Crie grupos portuários distribuídos para rede de failover e rede de teste para testar falhas de VMs.
5. Instale [servidores DHCP e DNS](dns-dhcp-setup.md) ou utilize um controlador de domínio Ative Directory no seu ambiente Cloud Privado.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Instale ZVM e VRA na sua nuvem privada

1. Instale o Zerto Virtual Manager (ZVM) no servidor Windows na sua Cloud Privada.
2. Inscreva-se na ZVM utilizando a conta de serviço criada em etapas anteriores.
3. Configurar o licenciamento para zerto Virtual Manager.
4. Instale o Aparelho de Replicação Virtual Zerto (VRA) nos anfitriões ESXi da sua Nuvem Privada.
5. Emparelhe o seu ZVM de Nuvem Privada com o seu ZVM no local.

### <a name="set-up-zerto-virtual-protection-group"></a>Criar o Grupo de Proteção Virtual Zerto

1. Crie um novo Grupo de Proteção Virtual (VPG) e especifique a prioridade para o VPG.
2. Selecione as máquinas virtuais que requerem proteção para a continuidade do negócio e personalize a encomenda de arranque se necessário.
3. Selecione o site de recuperação como o seu Cloud Privado e o servidor de recuperação padrão como o cluster Private Cloud ou o grupo de recursos que criou. Selecione **vsanDatastore** para a loja de dados de recuperação na sua Cloud Privada.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Pode personalizar a opção de anfitrião para VMs individuais sob a opção Definições VM.

4. Personalize as opções de armazenamento conforme necessário.
5. Especifique as redes de recuperação a utilizar para a rede failover e rede de teste failover como os grupos portuários distribuídos criados anteriormente e personalize os scripts de recuperação conforme necessário.
6. Personalize as definições de rede para VMs individuais, se necessário, e crie o VPG.
7. Teste falha quando a replicação terminar.

## <a name="reference"></a>Referência

[Documentação Zerto](https://www.zerto.com/myzerto/technical-documentation/)
