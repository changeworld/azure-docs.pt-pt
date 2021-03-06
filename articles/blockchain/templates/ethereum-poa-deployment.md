---
title: Implementar modelo de solução de consórcio Ethereum Proof-of-Authority no Azure
description: Utilize a solução do consórcio Ethereum Proof-of-Authority para implantar e configurar uma rede multi-membro do consórcio Ethereum no Azure
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387680"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Implementar modelo de solução de consórcio ethereum de prova de autoridade no Azure

Você pode usar o modelo de solução de [pré-visualização do Consórcio Ethereum De pré-perceção Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) para implementar, configurar e governar uma rede de comprovação de multi-membros do consórcio Ethereum com o mínimo de conhecimento Azure e Ethereum.

O modelo de solução pode ser usado por cada membro do consórcio para fornecer uma pegada de rede blockchain utilizando a computação, networking e serviços de armazenamento do Microsoft Azure. A pegada de rede de cada membro do consórcio consiste num conjunto de nós validadores equilibrados em carga com os quais uma aplicação ou utilizador pode interagir para submeter transações do Ethereum.

## <a name="choose-an-azure-blockchain-solution"></a>Escolha uma solução Azure Blockchain

Antes de optar por utilizar o modelo de solução de consórcio ethereum proof-of-authority, compare o seu cenário com os casos comuns de utilização das opções disponíveis do Azure Blockchain.

Opção | Modelo de serviço | Caso de uso comum
-------|---------------|-----------------
Modelos de solução | IaaS | Os modelos de solução são modelos do Gestor de Recursos Azure que pode usar para fornecer uma topologia de rede blockchain totalmente configurada. Os modelos implantam e configuram a computação, a rede e os serviços de armazenamento do Microsoft Azure para um dado tipo de rede blockchain.
[Azure Blockchain Service](../service/overview.md) | PaaS | A Pré-visualização do Serviço Azure Blockchain simplifica a formação, gestão e governação das redes blockchain do consórcio. Utilize o Serviço Azure Blockchain para soluções que requerem paaS, gestão de consórcios ou privacidade de contratos e transações.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | A Azure Blockchain Workbench Preview é uma coleção de serviços e capacidades Azure projetados para ajudá-lo a criar e implementar aplicações blockchain para partilhar processos de negócio e dados com outras organizações. Utilize a bancada azure blockchain workbench para prototipagem de uma solução blockchain ou uma prova de conceito de aplicação blockchain.

## <a name="solution-architecture"></a>Arquitetura de soluções

Utilizando o modelo de solução Ethereum, pode implementar uma rede de consórcios multi-membros ethereum com base em várias regiões.

![arquitetura de implantação](./media/ethereum-poa-deployment/deployment-architecture.png)

Cada destacamento de membro do consórcio inclui:

* Máquinas Virtuais para executar os validadores poa
* Azure Load Balancer para distribuir pedidos de RPC, peering e governance DApp
* Cofre chave Azure para garantir as identidades do validador
* Armazenamento Azure para hospedar informações persistentes de rede e coordenação de leasing
* Monitor Azure para agregação de registos e estatísticas de desempenho
* VNet Gateway (opcional) para permitir ligações VPN através de VNets privados

Por padrão, o RPC e os pontos finais de observação são acessíveis através de IP público supor uma conectividade simplificada entre subscrições e nuvens. Para controlos de acesso ao nível de aplicação, pode utilizar os contratos de [autorização da Parity.](https://wiki.parity.io/Permissioning) As redes implantadas por trás de VPNs, que alavancam os gateways VNet para conectividade de subscrição cruzada são suportadas. Uma vez que as implementações vpn e VNet são mais complexas, pode querer começar com um modelo IP público ao prototiping uma solução.

Os recipientes de estiva são usados para fiabilidade e modularidade. O Registo de Contentores Azure é utilizado para hospedar e servir imagens versonizadas como parte de cada implantação. As imagens do recipiente consistem em:

* Orquestrador - Gera identidades e contratos de governação. Armazena identidades numa loja de identidade.
* Cliente da paridade - Identidade de arrendamento da loja de identidade. Descobre e liga aos pares.
* EthStats Agent - Recolhe registos e estatísticas locais via RPC e empurra informações para o Monitor Azure.
* Governance DApp - Interface web para interagir com contratos de Governança.

### <a name="validator-nodes"></a>Nósodes validadores

No protocolo de prova de autoridade, os nós validadores tomam o lugar dos nós tradicionais mineiros. Cada validator tem uma identidade Ethereum única que lhe permite participar no processo de criação de blocos. Cada membro do consórcio pode fornecer dois ou mais nós de validador em cinco regiões, para o geo-despedimento. Os nódosos validadores comunicam com outros nódosos validadores para chegar a um consenso sobre o estado do livro-razão distribuído subjacente. Para garantir uma participação justa na rede, cada membro do consórcio está proibido de utilizar mais validadores do que o primeiro membro da rede. Por exemplo, se o primeiro membro implementar três validadores, cada membro só pode ter até três validadores.

### <a name="identity-store"></a>Loja de identidade

Uma loja de identidade é implantada na subscrição de cada membro que detém de forma segura as identidades Ethereum geradas. Para cada validador, o recipiente de orquestração gera uma chave privada Ethereum e armazena-a no Cofre chave Azure.

## <a name="deploy-ethereum-consortium-network"></a>Implementar rede de consórcio ethereum

Nesta caminhada, vamos supor que está a criar uma rede multi-partidária de consórcioe Ethereum. O seguinte fluxo é um exemplo de uma implantação multipartidária:

1. Três membros cada um gera uma conta Ethereum usando metaMask
1. *Membro A* implementa Ethereum PoA, fornecendo o seu endereço público Ethereum
1. *Membro A* fornece o URL do consórcio ao *membro B* e *membro C*
1. *Membro B* e *Membro C,* Ethereum PoA, fornecendo o seu Endereço Público Ethereum e *membro A's*consortium URL
1. *Membro A* vota no *membro B* como administrador
1. *Membro A* e *membro B* ambos votam *membro C* como administrador

As próximas secções mostram-lhe como configurar a pegada do primeiro membro na rede.

### <a name="create-resource"></a>Criar um recurso

No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso** no canto superior esquerdo.

Selecione **Blockchain** > **Ethereum Proof-of-Authority Consortium (pré-visualização)**.

### <a name="basics"></a>Noções básicas

No **Basics,** especifique os valores para os parâmetros padrão para qualquer implantação.

![Noções básicas](./media/ethereum-poa-deployment/basic-blade.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Criar uma nova rede ou aderir à rede existente | Pode criar uma nova rede de consórcios ou aderir a uma rede de consórcios pré-existente. A adesão a uma rede existente requer parâmetros adicionais. | Criar novo
Endereço de E-mail | Recebe uma notificação por e-mail quando a sua implementação completa com informações sobre a sua implementação. | Um endereço de e-mail válido
Nome de utilizador VM | Nome de utilizador administrador de cada VM implantado | 1-64 caracteres alfanuméricos
Tipo de autenticação | O método para autenticar a máquina virtual. | Palavra-passe
Palavra-passe | A palavra-passe para a conta do administrador para cada uma das máquinas virtuais implantadas. Todos os VMs têm inicialmente a mesma senha. Pode alterar a palavra-passe após o fornecimento. | 12-72 caracteres 
Subscrição | A subscrição para a qual implantar a rede de consórcios |
Grupo de Recursos| O grupo de recursos para o qual implantar a rede do consórcio. | myResourceGroup
Localização | A região de Azure para o grupo de recursos. | E.U.A.Oeste 2

Selecione **OK**.

### <a name="deployment-regions"></a>Regiões de implantação

Nas *regiões de implantação,* especifique o número de regiões e locais para cada um. Pode implantar-se no máximo de cinco regiões. A primeira região deve coincidir com a localização do grupo de recursos da secção *Basics.* Para redes de desenvolvimento ou teste, pode utilizar uma única região por membro. Para produção, desloque-se por duas ou mais regiões para alta disponibilidade.

![regiões de implantação](./media/ethereum-poa-deployment/deployment-regions.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Número de regiões|Número de regiões para implantar a rede de consórcios| 2
Primeira região | Primeira região a implantar a rede de consórcios | E.U.A.Oeste 2
Segunda região | Segunda região a implantar a rede de consórcios. Regiões adicionais são visíveis quando o número de regiões é duas ou maior. | E.U.A. Leste 2

Selecione **OK**.

### <a name="network-size-and-performance"></a>Tamanho e desempenho da rede

No *tamanho e desempenho da Rede,* especifique as inputs para o tamanho da rede do consórcio. O tamanho do armazenamento do nó validador dita o tamanho potencial da blockchain. O tamanho pode ser alterado após a implantação.

![Tamanho e desempenho da rede](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Número de nós validadores equilibrados de carga | O número de nós validadores a provisionar como parte da rede. | 2
Desempenho do armazenamento do nó validator | O tipo de disco gerido para cada um dos nós validadores implantados. Para mais detalhes sobre os preços, consulte [os preços de armazenamento](https://azure.microsoft.com/pricing/details/managed-disks/) | SSD Standard
Tamanho da máquina virtual do nó validator | O tamanho da máquina virtual utilizado para nódosos validadores. Para mais detalhes sobre os preços, consulte [preços de máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | Padrão D2 v3

A máquina virtual e o nível de armazenamento afetam o desempenho da rede.  Utilize a tabela seguinte para ajudar a escolher a eficiência de custos:

Máquina Virtual SKU|Nível de armazenamento|Preço|Débito|Latência
---|---|---|---|---
F1|SSD Standard|baixo|baixo|alta
D2_v3|SSD Standard|médio|médio|médio
F16s|SSD Premium|alta|alta|baixo

Selecione **OK**.

### <a name="ethereum-settings"></a>Definições de Ethereum

Em *definições ethereum,* especifique as definições de configuração relacionadas com o Ethereum.

![Definições de Ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
ID membro do consórcio | A identificação associada a cada membro participante na rede do consórcio. É usado para configurar espaços de endereço IP para evitar colisão. Para uma rede privada, o ID dos membros deve ser único em diferentes organizações da mesma rede.  Um ID único membro é necessário mesmo quando a mesma organização se implanta em várias regiões. Tome nota do valor deste parâmetro uma vez que precisa partilhá-lo com outros membros que se juntam para garantir que não há colisão. O intervalo válido é de 0 a 255. | 0
ID da rede | O ID da rede para a rede ethereum do consórcio que está a ser implantado. Cada rede Ethereum tem o seu próprio ID de rede, sendo 1 o ID para a rede pública. O intervalo válido é de 5 a 999.999.999 | 10101010
Endereço Ethereum do Administrador | O endereço de conta Ethereum usado para participar na governação do PoA. Pode utilizar o MetaMask para gerar um endereço Ethereum. |
Opções Avançadas | Opções avançadas para definições de Ethereum | Ativar
Implementar usando IP público | Se a VNet privada for selecionada, a rede é implantada atrás de um VNet Gateway e remove o acesso de pares. Para a VNet privada, todos os membros devem utilizar um VNet Gateway para que a ligação seja compatível. | IP público
Limite de gás de bloco | O limite de gás do bloco inicial da rede. | 50000000
Período de resbloqueio de blocos (seg) | A frequência com que serão criados blocos vazios quando não houver transações na rede. Uma frequência mais alta terá um final mais rápido, mas aumento dos custos de armazenamento. | 15
Contrato de Autorização de Transação | Bytecode para o contrato de permissão de transação. Restringe a implementação e execução inteligente de contratos a uma lista permitida das contas Ethereum. |

Selecione **OK**.

### <a name="monitoring"></a>Monitorização

A monitorização permite configurar um recurso de registo para a sua rede. O agente de monitorização recolhe e superfícies métricas e registos úteis da sua rede, fornecendo a capacidade de verificar rapidamente os problemas de saúde da rede ou depurados.

![Monitor Azure](./media/ethereum-poa-deployment/azure-monitor.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Monitorização | Opção para permitir a monitorização | Ativar
Ligar aos registos do Monitor Azure existentes | Opção de criar uma nova instância de registos do Monitor Azure ou aderir a uma instância existente | Criar novo
Localização | A região onde a nova instância é implantada | E.U.A. Leste
ID do espaço de trabalho de análise de registo existente (Ligar aos registos do Monitor Azure existentes = Juntar-se existente)|Id do espaço de trabalho da instância de registos do Monitor Azure existente||ND
Chave primária de análise de registo existente (Ligar aos registos do Monitor Azure existentes = Juntar-se existente)|A chave principal usada para ligar à instância de registos do Monitor Azure existente||ND

Selecione **OK**.

### <a name="summary"></a>Resumo

Clique no resumo para rever as inputs especificadas e executar a validação básica de pré-implantação. Antes de ser implementado, pode descarregar o modelo e os parâmetros.

Selecione **Criar** para implementar.

Se a implementação incluir Gateways VNet, a implementação pode demorar 45 a 50 minutos.

## <a name="deployment-output"></a>Saída de implantação

Uma vez concluída a implantação, pode aceder aos parâmetros necessários utilizando o portal Azure.

### <a name="confirmation-email"></a>E-mail de confirmação

Se fornecer um endereço de e-mail[(Secção Básica),](#basics)é enviado um e-mail que inclui as informações de implementação e links para esta documentação.

![e-mail de implantação](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

Uma vez concluída a implantação com sucesso e todos os recursos tiverem sido aprovisionados, pode ver os parâmetros de saída no seu grupo de recursos.

1. Vá ao seu grupo de recursos no portal.
1. Selecione **resumo > Implementações**.

    ![Visão geral do grupo de recursos](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Selecione a implementação **microsoft-azure-blockchain.azure-blockchain-ether-....**
1. Selecione a secção **Saídas.**

    ![Saídas de implantação](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Crescendo o consórcio

Para expandir o seu consórcio, tem primeiro de ligar a rede física. Se estiver a ser implantado atrás de uma VPN, consulte a secção [De ligação vNet Gateway](#connecting-vnet-gateways) configurar a ligação de rede como parte da implementação do novo membro. Uma vez concluída a sua implementação, utilize o DApp de [Governação](#governance-dapp) para se tornar um administrador de rede.

### <a name="new-member-deployment"></a>Implantação de novos membros

Partilhe as seguintes informações com o membro que se junta. A informação encontra-se no seu e-mail pós-implantação ou na saída de implementação do portal.

* URL de dados do consórcio
* O número de nós que implantou
* Id de recurso VNet Gateway (se utilizar VPN)

O membro de implantação deve utilizar o mesmo modelo de solução de consórcio Ethereum Proof-of-Authority ao implantar a sua presença na rede utilizando as seguintes orientações:

* **Selecione Juntar-se existente**
* Escolha o mesmo número de nós validadores que os restantes membros da rede para garantir uma representação justa
* Use o mesmo endereço Admin Ethereum
* Utilize o Url de Dados do *Consórcio* Fornecido nas *Definições do Ethereum*
* Se o resto da rede estiver por trás de uma VPN, **selecione Private VNet** sob a secção avançada

### <a name="connecting-vnet-gateways"></a>Gateways VNet de ligação

Esta secção só é necessária se tiver implantado usando um VNet privado. Pode saltar esta secção se estiver a utilizar endereços IP públicos.

Para uma rede privada, os diferentes membros estão ligados através de ligações de gateway VNet. Antes que um membro possa aderir à rede e ver o tráfego de transações, um membro existente deve fazer uma configuração final no seu gateway VPN para aceitar a ligação. Os nós de Ethereum do membro que se junta não funcionarão até que seja estabelecida uma ligação. Para reduzir as hipóteses de um único ponto de falha, crie ligações redundantes de rede no consórcio.

Após a implantação do novo membro, o membro existente deve completar a ligação bidirecional, estabelecendo uma ligação de gateway VNet ao novo membro. O membro existente precisa:

* O VNet gateway ResourceID do membro de ligação. Ver [saída de implantação](#deployment-output).
* A chave de ligação partilhada.

O membro existente deve executar o seguinte script PowerShell para completar a ligação. Pode utilizar a Azure Cloud Shell localizada na barra de navegação superior direita do portal.

![casca de nuvem](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>Monitorização do serviço

Pode localizar o portal Do Monitor Azure, seguindo o link no e-mail de implementação ou localizando o parâmetro na saída de implementação [OMS_PORTAL_URL].

O portal apresentará primeiro estatísticas de rede de alto nível e visão geral do nó.

![Monitorizar categorias](./media/ethereum-poa-deployment/monitor-categories.png)

A seleção da **visão geral** do nó mostra-lhe estatísticas de infraestruturas por nó.

![Estatísticas do nó](./media/ethereum-poa-deployment/node-stats.png)

A seleção de estatísticas de **rede** mostra-lhe estatísticas da rede Ethereum.

![Estatísticas da rede](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Amostra de consultas kusto

Pode consultar os registos de monitorização para investigar falhas ou alertade limiar de configuração. As seguintes consultas são exemplos que pode executar na ferramenta *'Pesquisa* de Registos':

Os blocos de lista que foram relatados por mais de uma consulta validador podem ser úteis para ajudar a encontrar garfos em cadeia.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Obtenha uma contagem média de pares para um nó validator especificado em média mais de 5 minutos baldes.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>Acesso SSH

Por razões de segurança, o acesso à porta SSH é negado por regra de segurança do grupo de rede por defeito. Para aceder às ocorrências de máquinas virtuais na rede PoA, é necessário alterar a seguinte regra de segurança para *permitir*.

1. Vá à secção **de visão geral** do grupo de recursos implantados no portal Azure.

    ![visão geral do SSH](./media/ethereum-poa-deployment/ssh-overview.png)

1. Selecione o Grupo de Segurança da **Rede** para a região do VM a que pretende aceder.

    ![ssh nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Selecione a regra de **permitir-ssh.**

    ![ssh-permitir](./media/ethereum-poa-deployment/ssh-allow.png)

1. Alterar **ação** para **permitir**

    ![ssh permitir](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Selecione **Guardar**. As alterações podem demorar alguns minutos a aplicar.

Pode ligar-se remotamente às máquinas virtuais para os nós validadores via SSH com o nome de utilizador e a chave password/SSH fornecidos. O comando SSH para aceder ao primeiro nó validador está listado na saída de implantação do modelo. Por exemplo:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Para chegar a nós de transação adicionais, incremente o número da porta por um.

Se tiver implantado em mais de uma região, altere o comando para o nome DNS ou endereço IP do equilibrador de carga naquela região. Para encontrar o nome DNS ou endereço IP das outras regiões, encontre o recurso com a convenção ** \* \* \* \* \*\# ** de nomeação -lbpip-reg e veja o seu nome DNS e propriedades de endereçoIP.

## <a name="azure-traffic-manager-load-balancing"></a>Equilíbrio de carga do Gestor de Tráfego Azure

O Azure Traffic Manager pode ajudar a reduzir o tempo de inatividade e a melhorar a capacidade de resposta da rede PoA, encaminhando o tráfego através de várias implementações em diferentes regiões. Verificações sanitárias incorporadas e reencaminhamento automático ajudam a garantir uma elevada disponibilidade dos pontos finais do RPC e do DApp de Governação. Esta funcionalidade é útil se tiver implantado em várias regiões e estiver pronto para a produção.

Utilize o Traffic Manager para melhorar a disponibilidade da rede PoA com falhas automáticas. Também pode utilizar o Traffic Manager para aumentar a capacidade de resposta das suas redes, encaminhando os utilizadores finais para a localização Azure com menor latência de rede.

Se decidir criar um perfil de Gestor de Tráfego, pode utilizar o nome DNS do perfil para aceder à sua rede. Uma vez adicionados outros membros do consórcio à rede, o Gestor de Tráfego também pode ser usado para carregar o equilíbrio entre os seus validadores implantados.

### <a name="creating-a-traffic-manager-profile"></a>Criação de um perfil de Gestor de Tráfego

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso** no canto superior esquerdo.
1. Pesquisar o perfil do Gestor de **Tráfego.**

    ![Pesquisa por Gestor de Tráfego Azure](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Dê ao perfil um nome único e selecione o Grupo de Recursos que foi utilizado para a implementação do PoA.

1. Selecione **Criar** para implementar.

    ![Criar Gestor de Tráfego](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Uma vez implantado, selecione a instância no grupo de recursos. O nome DNS para aceder ao gestor de tráfego pode ser encontrado no separador Overview.

    ![Localizar o gestor de tráfego DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Escolha o separador **Pontos Finais** e selecione o botão **Adicionar.**
1. Dê ao ponto final um nome único.
1. Para o tipo de **recurso-alvo,** escolha **o endereço IP público**.
1. Escolha o endereço IP público do equilibrador de carga da primeira região.

    ![Gestor de tráfego de encaminhamento](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Repita para cada região da rede implantada. Uma vez que os pontos finais estão no estado **ativado,** são automaticamente carregados e região equilibrado sintetizando com o nome DNS do gestor de tráfego. Agora pode utilizar este nome DNS no lugar do parâmetro [CONSORTIUM_DATA_URL] noutras etapas do artigo.

## <a name="data-api"></a>API de dados

Cada membro do consórcio acolhe as informações necessárias para que outros se conectem à rede. Para permitir a facilidade de conectividade, cada membro acolhe um conjunto de informações de ligação no ponto final da API de dados.

O membro existente fornece o [CONSORTIUM_DATA_URL] antes da implantação do membro. Após a implantação, um membro que se junta irá recolher informações da interface JSON no seguinte ponto final:

`<CONSORTIUM_DATA_URL>/networkinfo`

A resposta contém informações úteis para juntar membros (bloco Génesis, contrato de validador ABI, bootnodes) e informações úteis ao membro existente (endereços validadores). Você pode usar esta normalização para estender o consórcio através de fornecedores de nuvem. Esta API devolve uma resposta formatada jSON com a seguinte estrutura:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>DApp de Governação

No centro da prova de autoridade está a governação descentralizada. Uma vez que a prova de autoridade depende de uma lista permitida de autoridades de rede para manter a rede saudável, é importante fornecer um mecanismo justo para fazer modificações nesta lista de permissões. Cada implantação vem com um conjunto de contratos inteligentes e portal para a governação em cadeia desta lista permitida. Uma vez que uma proposta de alteração atinge a maioria dos membros do consórcio, a mudança é promulgada. A votação permite que novos participantes de consenso sejam removidos ou comprometidos participantes sejam removidos de forma transparente que incentive uma rede honesta.

O DApp de governação é um conjunto de [contratos inteligentes](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) pré-implementados e uma aplicação web que são usadas para governar as autoridades na rede. As autoridades são divididas em identidades de administrador e nódeos validadores.
Os administradores têm o poder de delegar a participação consensual num conjunto de substantivos validadores. Os administradores também podem votar outros administradores dentro ou fora da rede.

![DApp de Governação](./media/ethereum-poa-deployment/governance-dapp.png)

* **Governação Descentralizada:** As mudanças nas autoridades de rede são administradas através da votação em cadeia por administradores selecionados.
* **Delegação validadora:** As autoridades podem gerir os seus nós validadores que são criados em cada destacamento de PoA.
* **Histórico de Alterações Auditáveis:** Cada alteração é registada na blockchain, proporcionando transparência e auditabilidade.

### <a name="getting-started-with-governance"></a>Começar com a governação

Para realizar qualquer tipo de transações através do DApp de Governação, você precisa usar uma carteira Ethereum. A abordagem mais simples é usar uma carteira no navegador, como o [MetaMask;](https://metamask.io) no entanto, como estes contratos inteligentes são implementados na rede, também pode automatizar as suas interações com o contrato de Governação.

Depois de instalar o MetaMask, navegue para o DApp de Governação no navegador.  Pode localizar o URL através do portal Azure na saída de implantação.  Se não tiver uma carteira instalada no navegador, não poderá realizar quaisquer ações; no entanto, pode ver o estado de administrador.  

### <a name="becoming-an-admin"></a>Tornando-se um administrador

Se é o primeiro membro que se implantou na rede, torna-se automaticamente um administrador e os seus nós de paridade são listados como validadores. Se está a aderir à rede, precisa de ser votado como administrador por maioria (superior a 50%) do conjunto de administração existente. Se optar por não se tornar administrador, os seus nós ainda sincronizam e validam a blockchain; no entanto, não participam no processo de criação de blocos. Para iniciar o processo de votação para se tornar administrador, selecione **Nomear** e insira o seu endereço Ethereum e pseudónimo.

![Nomeie](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Candidatos

A seleção do separador **Candidatos** mostra-lhe o conjunto atual de administradores candidatos.  Uma vez que um candidato atinge a maioria dos votos pelos atuais administradores, o candidato é promovido a um administrador.  Para votar num candidato, selecione a linha e selecione **Vote em**. Se mudar de opinião numa votação, selecione o candidato e selecione **O voto de Rescindir**.

![Candidatos](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Administradores

O separador **Admins** mostra o conjunto atual de administradores e fornece-lhe a capacidade de votar contra.  Uma vez que um administrador perde mais de 50% de suporte, eles são removidos como um administrador na rede. Qualquer validador de nomes que o administrador detém perde o estatuto de validador e se torna nódeade transação na rede. Um administrador pode ser removido por qualquer número de razões; no entanto, cabe ao consórcio chegar a acordo sobre uma política antecipadamente.

![Administradores](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validadores

A seleção do separador **Validadores** apresenta os nódosos de paridade implementados atuais, por exemplo, e o seu estado atual (tipo nó). Cada membro do consórcio tem um conjunto diferente de validadores nesta lista, uma vez que esta visão representa o membro do consórcio destacado atualmente. Se a instância for recentemente implementada e não tiver adicionado os seus validadores, obtém a opção de **Adicionar Validadores**. A adição de validadores escolhe automaticamente um conjunto de nódeos de paridade equilibrados regionalmente e atribui-os ao seu conjunto de validadores. Se tiver implantado mais nós do que a capacidade permitida, os restantes nós tornam-se nós de transação na rede.

O endereço de cada validador é automaticamente atribuído através da loja de [identidade](#identity-store) em Azure.  Se um nó cair, renuncia à sua identidade, permitindo que outro nó na sua implantação tome o seu lugar. Este processo garante que a sua participação consensual está altamente disponível.

![Validadores](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Nome do consórcio

Qualquer administrador pode atualizar o nome do consórcio.  Selecione o ícone de engrenagem na parte superior esquerda para atualizar o nome do consórcio.

### <a name="account-menu"></a>Menu de conta

No canto superior direito, está o seu pseudónimo e identicon da sua conta Ethereum.  Se é administrador, tem a capacidade de atualizar o seu pseudónimo.

![Conta](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Desenvolvimento do Ethereum<a id="tutorials"></a>

Para compilar, implementar e testar contratos inteligentes, aqui estão algumas opções que pode considerar para o desenvolvimento do Ethereum:
* [Truffle Suite](https://www.trufflesuite.com/docs/truffle/overview) - Ambiente de desenvolvimento do Ethereum baseado no cliente
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) - Ambiente de desenvolvimento ethereum baseado no navegador e local

### <a name="compile-deploy-and-execute-smart-contract"></a>Compilar, implementar e executar contrato inteligente

No exemplo seguinte, cria-se um simples contrato inteligente. Usa a Truffle para compilar e implementar o contrato inteligente para a sua rede blockchain. Uma vez implementado, você chama uma função de contrato inteligente através de uma transação.

#### <a name="prerequisites"></a>Pré-requisitos

* Instale [python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python é necessário para Trufas e Web3. Selecione a opção de instalação para incluir python no seu caminho.
* Instale trufas v5.0.5 `npm install -g truffle@v5.0.5`. A trufa requer várias ferramentas para serem instaladas, incluindo [Node.js,](https://nodejs.org) [Git](https://git-scm.com/). Para mais informações, consulte [a documentação da Truffle.](https://github.com/trufflesuite/truffle)

### <a name="create-truffle-project"></a>Criar projeto Truffle

Antes de poder compilar e implementar um contrato inteligente, precisa de criar um projeto Truffle.

1. Abra um pedido de comando ou concha.
1. Crie uma pasta com o nome `HelloWorld`.
1. Mude o diretório `HelloWorld` para a nova pasta.
1. Inicialize um novo projeto `truffle init`Truffle usando o comando.

    ![Criar um novo projeto Truffle](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Adicione um contrato inteligente

Crie os seus contratos inteligentes no subdiretório de **contratos** do seu projeto Truffle.

1. Crie um ficheiro `postBox.sol` no subdiretório de **contratos** do seu projeto Truffle.
1. Adicione o seguinte código solidez ao **postBox.sol**.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Implementar contrato inteligente usando trufas

Os projetos de trufas contêm um ficheiro de configuração para detalhes de ligação à rede blockchain. Modifique o ficheiro de configuração para incluir as informações de ligação para a sua rede.

> [!WARNING]
> Nunca envie a sua chave privada Ethereum pela rede. Certifique-se de que cada transação é assinada localmente primeiro e a transação assinada é enviada pela rede.

1. Precisa da frase mnemónica para a [conta de administração Ethereum utilizada na implementação](#ethereum-settings)da sua rede blockchain . Se usou o MetaMask para criar a conta, pode recuperar o mnemónico do MetaMask. Selecione o ícone da conta do administrador na parte superior direita da extensão MetaMask e selecione **Definições > Segurança & Privacidade > Revelar Palavras de Semente**.
1. Substitua o `truffle-config.js` conteúdo do seu projeto Truffle pelo seguinte conteúdo. Substitua o ponto final do espaço reservado e os valores mnemónicos.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Uma vez que estamos a utilizar o fornecedor Truffle HD `npm install truffle-hdwallet-provider --save`Wallet, instale o módulo no seu projeto utilizando o comando .

Truffle usa scripts de migração para implementar contratos inteligentes para uma rede blockchain. Precisas de um guião de migração para implementar o teu novo contrato inteligente.

1. Adicione uma nova migração para implementar o novo contrato. Crie `2_deploy_contracts.js` ficheiros no subdiretório de **migrações** do projeto Truffle.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Desloque-se para a rede poa usando o comando de migração truffle. No comando no diretório do projeto Truffle, executar:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Ligue para uma função de contrato inteligente

Agora que o seu contrato inteligente foi implementado, pode enviar uma transação para chamar uma função.

1. No diretório do projeto Truffle, `sendtransaction.js`crie um novo ficheiro chamado .
1. Adicione os seguintes conteúdos para **enviar transaction.js**.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Execute o guião usando o comando de execução de Trufas.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Execute script para função de chamada através de transação](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Suporte da WebAssembly (WASM)

O suporte da WebAssembly já está habilitado para si em redes poa recém-implantadas. Permite o desenvolvimento de contratos inteligentes em qualquer idioma que transpiles para Web-Assembly (Rust, C, C++). Para mais informações, consulte: [Visão geral da Paridade da WebAssembly](https://wiki.parity.io/WebAssembly-Home) e tutorial da [Parity Tech](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>FAQ

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Reparei que há muitas transações na rede que não enviei. De onde vêm estes?

É inseguro desbloquear a [API pessoal.](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html) Os bots ouvem as contas desbloqueadas do Ethereum e tentam drenar os fundos. O bot assume que estas contas contêm éter real e tentam ser os primeiros a sifonar o saldo. Não ative a API pessoal na rede. Em vez disso, pré-assine as transações manualmente utilizando uma carteira como o MetaMask ou programáticamente.

### <a name="how-to-ssh-onto-a-vm"></a>Como o SSH num VM?

A porta SSH não está exposta por razões de segurança. Siga [este guia para ativar a porta SSH](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Como posso configurar um membro da auditoria ou nós de transações?

Os nós de transação são um conjunto de clientes de paridade que são espreitados pela rede, mas não estão a participar em consensos. Estes nódosos ainda podem ser usados para submeter transações ethereum e ler o estado de contrato inteligente. Este mecanismo funciona para fornecer auditabilidade a membros de consórcios não-autoridade na rede. Para isso, siga os passos de [Crescimento do Consórcio.](#growing-the-consortium)

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Porque é que as transações do MetaMask estão a demorar muito tempo?

Para garantir que as transações são recebidas na ordem correta, cada transação ethereum vem com um nonce incrementante. Se usou uma conta no MetaMask numa rede diferente, tem de redefinir o valor nonce. Clique no ícone de definições (três barras), Definições, Reset Account. O histórico de transações será apurado e agora pode reenviar a transação.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Preciso especificar a taxa de gás no MetaMask?

Éter não serve um propósito no consórcio de comprovativo. Por conseguinte, não há necessidade de especificar a taxa de gás ao submeter transações no MetaMask.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>O que devo fazer se a minha implantação falhar devido à falta de fornecimento de OMS Azure?

A monitorização é uma característica opcional. Em alguns casos raros em que a sua implementação falha devido à incapacidade de fornecer recursos Azure Monitor com sucesso, pode reimplantar sem o Monitor Azure.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>As implementações públicas de IP são compatíveis com as implantações de redes privadas?

Não. O peering requer uma comunicação bidirecional, pelo que toda a rede deve ser pública ou privada.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Qual é o resultado esperado da transação da Prova de Autoridade?

O início da transação será altamente dependente dos tipos de transações e da topologia da rede. Usando transações simples, comparamos uma média de 400 transações por segundo com uma rede implantada em várias regiões.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Como subscrevo eventos de contratos inteligentes?

A Ethereum Proof-of-Authority suporta agora as tomadas web.  Verifique a sua saída de implantação para localizar o URL e a porta da tomada web.

## <a name="next-steps"></a>Passos seguintes

Para obter mais soluções Azure Blockchain, consulte a [documentação Azure Blockchain](https://docs.microsoft.com/azure/blockchain/).
