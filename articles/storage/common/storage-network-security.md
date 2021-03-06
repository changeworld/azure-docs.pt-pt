---
title: Configure Firewalls de armazenamento Azure e redes virtuais / Microsoft Docs
description: Configure a segurança da rede em camadas para a sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 7120ba2cf71c9af5373b830d04d0b67952922887
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113511"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configure firewalls de armazenamento Azure e redes virtuais

O Armazenamento do Azure fornece um modelo de segurança em camadas. Este modelo permite-lhe assegurar e controlar o nível de acesso às suas contas de armazenamento que as suas aplicações e ambientes empresariais exigem, com base no tipo e subconjunto de redes utilizadas. Quando as regras de rede são configuradas, apenas as aplicações que solicitam dados sobre o conjunto especificado de redes podem aceder a uma conta de armazenamento. Pode limitar o acesso à sua conta de armazenamento a pedidos originários de endereços IP especificados, intervalos IP ou de uma lista de subredes numa Rede Virtual Azure (VNet).

As contas de armazenamento têm um ponto final público que é acessível através da internet. Também pode criar [Pontos Finais Privados para a sua conta](storage-private-endpoints.md)de armazenamento, que atribui um endereço IP privado do seu VNet à conta de armazenamento, e assegura todo o tráfego entre o seu VNet e a conta de armazenamento sobre um link privado. A firewall de armazenamento Azure fornece acesso ao controlo de acesso para o ponto final público da sua conta de armazenamento. Também pode utilizar a firewall para bloquear todos os acessos através do ponto final público quando utilizar pontos finais privados. A configuração da firewall de armazenamento também permite selecionar serviços de plataforma Azure fidedignos para aceder à conta de armazenamento de forma segura.

Uma aplicação que acede a uma conta de armazenamento quando as regras da rede estão em vigor ainda requer autorização adequada para o pedido. A autorização é suportada com credenciais azure Ative Directory (Azure AD) para bolhas e filas, com uma chave de acesso à conta válida, ou com um token SAS.

> [!IMPORTANT]
> A aplicação das regras de firewall para a sua conta de armazenamento bloqueia os pedidos de dados por defeito, a menos que os pedidos tenham origem num serviço que opera dentro de uma Rede Virtual Azure (VNet) ou de endereços IP públicos permitidos. Os pedidos que estão bloqueados incluem os de outros serviços Azure, do portal Azure, dos serviços de exploração madeireira e métrica, e assim por diante.
>
> Pode conceder acesso aos serviços Azure que operam a partir de um VNet, permitindo o tráfego a partir da subnet que acolhe a instância de serviço. Também pode ativar um número limitado de cenários através do mecanismo [de exceções](#exceptions) descrito abaixo. Para aceder aos dados da conta de armazenamento através do portal Azure, teria de estar numa máquina dentro do limite de confiança (ip ou VNet) que configura.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para garantir a sua conta de armazenamento, deve primeiro configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de internet) no ponto final do público, por padrão. Em seguida, deve configurar regras que concedam acesso ao tráfego a partir de VNets específicos. Também pode configurar regras para conceder acesso ao tráfego a partir de gamas de endereços IP de internet pública selecionadas, permitindo ligações de clientes específicos da Internet ou no local. Esta configuração permite-lhe construir um limite de rede seguro para as suas aplicações.

Pode combinar regras de firewall que permitem o acesso a partir de redes virtuais específicas e a partir de intervalos de endereços IP públicos na mesma conta de armazenamento. As regras de firewall de armazenamento podem ser aplicadas às contas de armazenamento existentes, ou ao criar novas contas de armazenamento.

As regras de firewall de armazenamento aplicam-se ao ponto final público de uma conta de armazenamento. Não é necessário regras de acesso a firewall para permitir o tráfego para pontos finais privados de uma conta de armazenamento. O processo de aprovação da criação de um ponto final privado concede acesso implícito ao tráfego a partir da subnet que acolhe o ponto final privado.

As regras de rede são aplicadas em todos os protocolos de rede ao armazenamento Azure, incluindo REST e SMB. Para aceder a dados utilizando ferramentas como o portal Azure, o Storage Explorer e o AZCopy, devem ser configuradas regras explícitas de rede.

Uma vez aplicadas as regras da rede, são aplicadas para todos os pedidos. Os tokens SAS que concedem acesso a um endereço IP específico servem para limitar o acesso do titular do token, mas não concedem novo acesso para além das regras de rede configuradas.

O tráfego de discos de máquinas virtuais (incluindo operações de montagem e desmontagem, e IO do disco) não é afetado pelas regras da rede. O acesso ao REST às bolhas de página está protegido pelas regras da rede.

As contas de armazenamento clássicas não suportam firewalls e redes virtuais.

Pode utilizar discos não geridos em contas de armazenamento com regras de rede aplicadas para backup e restauro de VMs criando uma exceção. Este processo está documentado na secção [Exceções](#exceptions) deste artigo. As exceções à firewall não são aplicáveis com discos geridos, uma vez que já são geridos pelo Azure.

## <a name="change-the-default-network-access-rule"></a>Change the default network access rule (Alterar a regra de acesso de rede predefinida)

Por predefinição, as contas de armazenamento aceitam ligações de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, primeiro tem de alterar a ação predefinida.

> [!WARNING]
> Fazer alterações às regras de rede pode afetar a capacidade de ligação das suas aplicações ao Armazenamento do Azure. Definir a regra da rede predefinida para **negar** os bloqueios a todos os acessos aos dados, a menos que sejam também aplicadas regras específicas de rede que **concedam** acesso. Certifique-se de que concede acesso a quaisquer redes permitidas através das regras de rede antes de alterar a regra predefinida para negar o acesso.

### <a name="managing-default-network-access-rules"></a>Gerir regras de acesso de rede predefinidas

Pode gerir as regras de acesso à rede padrão para contas de armazenamento através do portal Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Aceda à conta de armazenamento que pretende proteger.

1. Clique no menu de definições chamado **Firewalls e redes virtuais**.

1. Para negar o acesso por padrão, opte por permitir o acesso a partir de **redes Selecionadas**. Para permitir tráfego de todas as redes, opte por permitir o acesso a partir de **Todas as redes**.

1. Clique em **Guardar** para aplicar as suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [inscreva-se](/powershell/azure/authenticate-azureps).

1. Mostrar o estado da regra predefinida para a conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Desdefinir a regra predefinida para negar o acesso à rede por defeito.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Desdefinir a regra predefinida para permitir o acesso à rede por defeito.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se](/cli/azure/authenticate-azure-cli).

1. Mostrar o estado da regra predefinida para a conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Desdefinir a regra predefinida para negar o acesso à rede por defeito.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Desdefinir a regra predefinida para permitir o acesso à rede por defeito.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso a partir de uma rede virtual

Pode configurar contas de armazenamento para permitir o acesso apenas a partir de subredes específicas. As subredes permitidas podem pertencer a um VNet na mesma subscrição, ou as de uma subscrição diferente, incluindo subscrições pertencentes a um inquilino do Diretório Ativo Azure diferente.

Ative um [ponto final](/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço para armazenamento Azure dentro do VNet. O ponto final de serviço faz o tráfego do VNet através de um caminho ideal para o serviço de Armazenamento Azure. As identidades da subnet e da rede virtual também são transmitidas a cada pedido. Os administradores podem então configurar as regras de rede para a conta de armazenamento que permitem que os pedidos sejam recebidos de subredes específicas num VNet. Os clientes que tenham acesso através destas regras de rede devem continuar a cumprir os requisitos de autorização da conta de armazenamento para aceder aos dados.

Cada conta de armazenamento suporta até 100 regras de rede virtual, que podem ser combinadas com as regras da [rede IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiões de rede virtual disponíveis

Em geral, os pontos finais de serviço funcionam entre redes virtuais e instâncias de serviço na mesma região do Azure. Ao utilizar pontos finais de serviço com armazenamento Azure, este âmbito cresce para incluir a [região emparelhada.](/azure/best-practices-availability-paired-regions) Os pontos finais do serviço permitem a continuidade durante uma falha regional e o acesso a casos de armazenamento geo-redundante (RA-GRS) de leitura. As regras de rede que concedem acesso de uma rede virtual a uma conta de armazenamento também concedem acesso a qualquer instância RA-GRS.

Ao planear a recuperação de desastres durante uma paragem regional, deve criar antecipadamente os VNets na região emparelhada. Ativar pontos finais de serviço para o Armazenamento Azure, com regras de rede que concedem acesso a partir destas redes virtuais alternativas. Em seguida, aplique estas regras nas suas contas de armazenamento geo-redundantes.

> [!NOTE]
> Os pontos finais de serviço não se aplicam ao tráfego fora da região da rede virtual e do par de regiões designadas. Só é possível aplicar regras de rede que concedam acesso de redes virtuais a contas de armazenamento na região primária de uma conta de armazenamento ou na região emparelhada designada.

### <a name="required-permissions"></a>Permissões obrigatórias

Para aplicar uma regra de rede virtual a uma conta de armazenamento, o utilizador deve ter as permissões adequadas para que as subredes sejam adicionadas. A permissão necessária é *aderir ao Serviço a uma Subnet* e está incluída na função incorporada do Contribuinte de Conta de *Armazenamento.* Também pode ser adicionado às definições de papéis personalizados.

A conta de armazenamento e as redes virtuais de acesso podem estar em diferentes subscrições, incluindo subscrições que fazem parte de um inquilino azure diferente.

> [!NOTE]
> A configuração de regras que concedem acesso a subredes em redes virtuais que fazem parte de um diferente inquilino do Diretório Ativo Azure são atualmente apenas suportadas através de APIs Powershell, CLI e REST. Tais regras não podem ser configuradas através do portal Azure, embora possam ser vistas no portal.

### <a name="managing-virtual-network-rules"></a>Gestão de regras de rede virtual

Pode gerir as regras de rede virtual para contas de armazenamento através do portal Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Aceda à conta de armazenamento que pretende proteger.

1. Clique no menu de definições chamado **Firewalls e redes virtuais**.

1. Verifique se selecionou para permitir o acesso a partir de **redes Selecionadas**.

1. Para garantir o acesso a uma rede virtual com uma nova regra de rede, em **redes Virtuais,** clique em **Adicionar rede virtual existente,** selecione redes **Virtuais** e **subnets** e, em seguida, clique em **Adicionar**. Para criar uma nova rede virtual e conceder-lhe acesso, clique em **Adicionar nova rede virtual**. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, clique em **Criar**.

    > [!NOTE]
    > Se um ponto final de serviço para o Armazenamento Azure não estivesse previamente configurado para a rede virtual selecionada e subnets, pode configurá-lo como parte desta operação.
    >
    > Atualmente, apenas redes virtuais pertencentes ao mesmo inquilino azure Ative Directory são mostradas para seleção durante a criação de regras. Para conceder acesso a uma subneta numa rede virtual pertencente a outro inquilino, utilize apis Powershell, CLI ou REST.

1. Para remover uma regra de rede virtual ou subnet, **clique...** para abrir o menu de contexto para a rede virtual ou subnet, e clique em **Remover**.

1. Clique em **Guardar** para aplicar as suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [inscreva-se](/powershell/azure/authenticate-azureps).

1. Listar as regras da rede virtual.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Ative o ponto final de serviço para o Armazenamento Azure numa rede virtual existente e subnet.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Adicione uma regra de rede para uma rede virtual e subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Para adicionar uma regra de rede para uma subnet a uma VNet pertencente a outro inquilino DaD Azure, utilize um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formulário "/subscrições/subscrições/subscrição-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

1. Remova uma regra de rede para uma rede virtual e sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Certifique-se de [que estabelece a regra padrão](#change-the-default-network-access-rule) para **negar**, ou as regras da rede não têm qualquer efeito.

#### <a name="cliv2"></a>CLIv2

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se](/cli/azure/authenticate-azure-cli).

1. Listar as regras da rede virtual.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Ative o ponto final de serviço para o Armazenamento Azure numa rede virtual existente e subnet.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Adicione uma regra de rede para uma rede virtual e subnet.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Para adicionar uma regra para uma sub-rede num VNet pertencente a outro inquilino DaD Azure, utilize\<um\>ID de\<sub-rede\>totalmente qualificado no formulário "/subscrições/ subscrição-ID\</recursosGroups/\>resourceGroup/resourceGroup/providers/Microsoft.Network/virtualNetworks/\<vNet-name\>/subnets/ subnet-name ".
    >
    > Você pode usar o parâmetro de **subscrição** para recuperar o ID da subnet para um VNet pertencente a outro inquilino Azure AD.

1. Remova uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Certifique-se de [que estabelece a regra padrão](#change-the-default-network-access-rule) para **negar**, ou as regras da rede não têm qualquer efeito.

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso a partir de uma gama IP da Internet

Pode configurar contas de armazenamento para permitir o acesso a partir de gamas específicas de endereços IP da Internet pública. Esta configuração permite o acesso a serviços específicos baseados na Internet e redes no local e bloqueia o tráfego geral da Internet.

Fornecer gamas de endereços de internet permitidas utilizando [notação CIDR](https://tools.ietf.org/html/rfc4632) no formulário *16.17.18.0/24* ou como endereços IP individuais como *16.17.18.19*.

   > [!NOTE]
   > Não são suportadas gamas de endereços pequenas utilizando tamanhos de prefixo "/31" ou "/32". Estas gamas devem ser configuradas utilizando regras individuais de endereço IP.

As regras da rede IP só são permitidas para endereços IP da **internet pública.** As gamas de endereços IP reservadas para redes privadas (tal como definidas no [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) não são permitidas nas regras ip. As redes privadas incluem endereços que começam com _10.*_, _172.16.*_ - _172.31.*_ e _192.168.*_.

   > [!NOTE]
   > As regras da rede IP não têm qualquer efeito sobre os pedidos originários da mesma região do Azure que a conta de armazenamento. Utilize regras de [rede virtuais](#grant-access-from-a-virtual-network) para permitir pedidos na mesma região.

  > [!NOTE]
  > Os serviços implantados na mesma região que a conta de armazenamento utilizam endereços IP azure privados para comunicação. Assim, não é possível restringir o acesso a serviços específicos do Azure com base na sua gama pública de endereços IP de saída.

Apenas os endereços IPV4 são suportados para configurar as regras de firewall de armazenamento.

Cada conta de armazenamento suporta até 100 regras de rede IP.

### <a name="configuring-access-from-on-premises-networks"></a>Configurar o acesso a partir de redes no local

Para garantir o acesso das suas redes no local à sua conta de armazenamento com uma regra de rede IP, deve identificar os endereços IP virados para a Internet utilizados pela sua rede. Contacte o seu administrador de rede para obter ajuda.

Se estiver a utilizar o [ExpressRoute](/azure/expressroute/expressroute-introduction) a partir das suas instalações, para espreitar público ou espreitar a Microsoft, terá de identificar os endereços IP NAT que são utilizados. Para peering público, cada circuito ExpressRoute, por predefinição, utiliza dois endereços IP NAT que são aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede do Microsoft Azure. Para o peering da Microsoft, os endereços IP NAT utilizados são fornecidos pelo cliente ou são fornecidos pelo prestador de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gestão das regras da rede IP

Pode gerir as regras da rede IP para contas de armazenamento através do portal Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Aceda à conta de armazenamento que pretende proteger.

1. Clique no menu de definições chamado **Firewalls e redes virtuais**.

1. Verifique se selecionou para permitir o acesso a partir de **redes Selecionadas**.

1. Para garantir o acesso a uma gama IP da Internet, introduza o endereço IP ou a gama de endereços (em formato CIDR) no âmbito do Alcance do**Endereço** **firewall** > .

1. Para remover uma regra de rede IP, clique no ícone do caixote do lixo ao lado do intervalo de endereços.

1. Clique em **Guardar** para aplicar as suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [inscreva-se](/powershell/azure/authenticate-azureps).

1. Lista rita as regras da rede IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereçoIP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereçoIP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Certifique-se de [que estabelece a regra padrão](#change-the-default-network-access-rule) para **negar**, ou as regras da rede não têm qualquer efeito.

#### <a name="cliv2"></a>CLIv2

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se](/cli/azure/authenticate-azure-cli).

1. Lista rita as regras da rede IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereçoIP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereçoIP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Certifique-se de [que estabelece a regra padrão](#change-the-default-network-access-rule) para **negar**, ou as regras da rede não têm qualquer efeito.

## <a name="exceptions"></a>Exceções

As regras da rede ajudam a criar um ambiente seguro para as ligações entre as suas aplicações e os seus dados para a maioria dos cenários. No entanto, algumas aplicações dependem de serviços Azure que não podem ser isolados exclusivamente através de regras de rede virtual ou endereço IP. Mas esses serviços devem ser concedidos ao armazenamento para permitir a funcionalidade completa da aplicação. Nestas situações, pode utilizar os ***serviços da Microsoft fidedignos,*** para permitir que esses serviços acedam aos seus dados, registos ou análises.

### <a name="trusted-microsoft-services"></a>Serviços fidedignos da Microsoft

Alguns serviços da Microsoft operam a partir de redes que não podem ser incluídas nas regras da sua rede. Pode conceder um subconjunto de serviços da Microsoft de confiança à conta de armazenamento, mantendo as regras de rede para outras aplicações. Estes serviços fidedignos utilizarão então uma autenticação forte para se ligarem à sua conta de armazenamento de forma segura. Permitimos dois modos de acesso fidedigno aos serviços da Microsoft.

- Os recursos de alguns serviços, **quando registados na sua subscrição,** podem aceder à sua conta de armazenamento **na mesma subscrição** para operações selecionadas, tais como registos de escrita ou cópia de segurança.
- Os recursos de alguns serviços podem ter acesso explícito à sua conta de armazenamento **atribuindo uma função RBAC** à sua identidade gerida atribuída pelo sistema.


Quando ativa os **serviços fidedignos** da Microsoft... definição, os recursos dos seguintes serviços que estão registados na mesma subscrição que a sua conta de armazenamento têm acesso a um conjunto limitado de operações, conforme descrito:

| Serviço                  | Nome do fornecedor de recursos     | Operações permitidas                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Executar backups e restauros de discos não geridos em máquinas virtuais IAAS. (não necessário para discos geridos). [Saiba mais](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | Permite a importação de dados para o Azure utilizando a Data Box. [Saiba mais](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Criação de imagem personalizada e instalação de artefactos. [Saiba mais](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Ative a publicação de eventos blob Storage e permita que a Rede de Eventos publique nas filas de armazenamento. Saiba mais sobre eventos de [armazenamento de blob](/azure/event-grid/event-sources) e [publicação em filas.](/azure/event-grid/event-handlers) |
| Azure Event Hubs         | Microsoft.EventHub         | Arquivar dados com captura de Hubs de Eventos. [Saiba mais.](/azure/event-hubs/event-hubs-capture-overview) |
| Azure File Sync          | Microsoft.StorageSync      | Permite-lhe transformar o seu servidor de ficheiros on-prem numa cache para ações do Ficheiro Azure. Permitindo sincronização multi-site, rápida recuperação de desastres e backup do lado da nuvem. [Mais informações](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Fornecer o conteúdo inicial do sistema de ficheiros predefinido para um novo cluster HDInsight. [Saiba mais](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Exportação de Importações De Azure      | Microsoft.ImportExport     | Permite a importação de dados para o Azure e a exportação de dados do Azure através do serviço de importação/exportação. [Saiba mais](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | Permite a escrita de dados de monitorização para uma conta de armazenamento segura, incluindo registos de diagnóstico de recursos, registos de login e auditoria do Azure Ative Directory e registos de auditoria da Microsoft Intune. [Saiba mais](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Rede Azure         | Microsoft.Network          | Armazenar e analisar registos de tráfego de rede. [Saiba mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Ativar a replicação para a recuperação de desastres das máquinas virtuais Azure IaaS ao utilizar contas de armazenamento de cache, fonte ou armazenamento ativadas por firewall.  [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

A definição de **serviços fidedignos** da Microsoft... permite também que uma determinada instância dos serviços abaixo aceda à conta de armazenamento, se atribuir explicitamente [uma função RBAC](storage-auth-aad.md#assign-rbac-roles-for-access-rights) à [identidade gerida atribuída](../../active-directory/managed-identities-azure-resources/overview.md) pelo sistema para essa instância de recursos. Neste caso, o âmbito de acesso, por exemplo, corresponde ao papel RBAC atribuído à identidade gerida.

| Serviço                        | Nome do fornecedor de recursos                 | Objetivo            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Permite que os serviços de Pesquisa Cognitiva acedam a contas de armazenamento para indexação, processamento e consulta. |
| Tarefas do Azure Container Registry | Microsoft.ContainerRegistry/registos | As Tarefas ACR podem aceder a contas de armazenamento ao construir imagens de contentores. |
| Azure Data Factory             | Microsoft.DataFactory/fábricas        | Permite o acesso às contas de armazenamento através do tempo de execução da ADF. |
| Azure Data Share               | Microsoft.DataShare/contas           | Permite o acesso a contas de armazenamento através da Data Share. |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Permite que aplicações lógicas acedam a contas de armazenamento. [Saiba mais](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Serviço Azure Machine Learning | Microsoft.MachineLearningServices      | Os espaços de trabalho autorizados do Azure Machine Learning escrevem saída seleção, modelos e registos para o armazenamento blob e lêem os dados. [Saiba mais](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Permite a importação e exportação de dados de instâncias específicas da Base de Dados SQL utilizando a PolyBase. [Saiba mais](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Permite que os dados de um trabalho de streaming sejam escritos ao armazenamento blob. Esta funcionalidade encontra-se em pré-visualização. [Saiba mais](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft.Synapse/espaços de trabalho          | Permite o acesso aos dados no Armazenamento Azure da Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Acesso a dados de análise de armazenamento

Em alguns casos, o acesso à leitura de registos e métricas de diagnóstico é necessário fora do limite da rede. Ao configurar o acesso de serviços fidedignos à conta de armazenamento, pode permitir o acesso de leitura para os ficheiros de registo, tabelas de métricas ou ambos. [Saiba mais sobre trabalhar com análise de armazenamento.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gestão de exceções

Pode gerir exceções à regra da rede através do portal Azure, PowerShell ou Azure CLI v2.

#### <a name="azure-portal"></a>Portal do Azure

1. Aceda à conta de armazenamento que pretende proteger.

1. Clique no menu de definições chamado **Firewalls e redes virtuais**.

1. Verifique se selecionou para permitir o acesso a partir de **redes Selecionadas**.

1. Em **exceções,** selecione as exceções que pretende conceder.

1. Clique em **Guardar** para aplicar as suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [inscreva-se](/powershell/azure/authenticate-azureps).

1. Exiba as exceções para as regras da rede de conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Configure as exceções às regras da rede de conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Remova as exceções às regras da rede de conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Certifique-se de [que definir a regra de incumprimento](#change-the-default-network-access-rule) para **negar,** ou remover exceções não tem qualquer efeito.

#### <a name="cliv2"></a>CLIv2

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se](/cli/azure/authenticate-azure-cli).

1. Exiba as exceções para as regras da rede de conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Configure as exceções às regras da rede de conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Remova as exceções às regras da rede de conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Certifique-se de [que definir a regra de incumprimento](#change-the-default-network-access-rule) para **negar,** ou remover exceções não tem qualquer efeito.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os pontos finais do serviço da Rede Azure nos [pontos finais do Serviço](/azure/virtual-network/virtual-network-service-endpoints-overview).

Investigue mais profundamente a segurança do Armazenamento Azure no guia de [segurança Azure Storage](../blobs/security-recommendations.md).
