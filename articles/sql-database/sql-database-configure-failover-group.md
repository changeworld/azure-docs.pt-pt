---
title: Configure um grupo de failover
description: Aprenda a configurar um grupo de auto-failover para uma base de dados única Azure SQL, piscina elástica e instância gerida usando o portal Azure, o Az CLI e PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 3b423a25b6b13ad543ef4a74bc0335ce19f5766d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461816"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Configure um grupo de failover para a Base de Dados Azure SQL

Este tópico ensina-lhe como configurar um [grupo de auto-failover](sql-database-auto-failover-group.md) para uma base de dados única Azure SQL, piscina elástica e instância gerida usando o portal Azure, ou PowerShell. 

## <a name="single-database"></a>Base de dados individual
Crie o grupo failover e adicione-lhe uma única base de dados utilizando o portal Azure, ou PowerShell.

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- As definições de login do servidor e firewall do servidor secundário devem corresponder às do servidor principal. 

### <a name="create-failover-group"></a>Criar grupo failover

# <a name="portal"></a>[Portal](#tab/azure-portal)
Crie o seu grupo failover e adicione-lhe a sua única base de dados utilizando o portal Azure.


1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
1. Selecione a única base de dados que pretende adicionar ao grupo failover. 
1. Selecione o nome do servidor sob **o nome do Servidor** para abrir as definições para o servidor.

   ![Servidor aberto para db simples](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecione **grupos Failover** sob o painel **Definições** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover. 

    ![Adicione novo grupo de failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na página do **Grupo Failover,** introduza ou selecione os valores necessários e, em seguida, selecione **Criar**.

   - **Bases de dados dentro do grupo**: Escolha a base de dados que pretende adicionar ao seu grupo de falhas. A adição da base de dados ao grupo failover iniciará automaticamente o processo de georeplicação. 
        
    ![Adicione o SQL DB ao grupo failover](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Crie o seu grupo de failover e adicione a sua única base de dados utilizando o PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

---

### <a name="test-failover"></a>Ativação pós-falha de teste 

Teste falha do seu grupo failover usando o portal Azure, ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste falha do seu grupo de failover usando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
1. Selecione a única base de dados que pretende adicionar ao grupo failover. 

   ![Servidor aberto para db simples](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecione **grupos Failover** sob o painel **Definições** e, em seguida, escolha o grupo failover que acabou de criar. 
  
   ![Selecione o grupo failover a partir do portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Reveja qual o servidor primário e qual o servidor secundário. 
1. Selecione **Failover** do painel de tarefas para falhar sobre o seu grupo failover contendo a sua única base de dados. 
1. Selecione **Sim** no aviso que o avisa de que as sessões de TDS serão desligadas. 

   ![Fail over your failover group contendo a sua base de dados SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Reveja qual servidor é agora primário e qual servidor é secundário. Se o failover for bem sucedido, os dois servidores devem ter trocado de papéis. 
1. Selecione **Failover** novamente para falhar os servidores de volta às suas funções originais. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste falha do seu grupo de failover usando PowerShell.  

Verifique o papel da réplica secundária: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
Falhar no servidor secundário: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Reverter o grupo failover de volta ao servidor primário:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Se necessitar de eliminar a base de dados secundária, retire-a do grupo failover antes de a eliminar. Eliminar uma base de dados secundária antes de ser removido do grupo failover pode causar comportamentos imprevisíveis. 

## <a name="elastic-pool"></a>Conjunto elástico
Crie o grupo failover e adicione-lhe uma piscina elástica utilizando o portal Azure, ou PowerShell.  

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- As definições de login do servidor e firewall do servidor secundário devem corresponder às do servidor principal. 

### <a name="create-the-failover-group"></a>Criar o grupo failover 

Crie o grupo failover para a sua piscina elástica utilizando o portal Azure, ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Crie o seu grupo de failover e adicione-lhe a sua piscina elástica utilizando o portal Azure.

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
1. Selecione a piscina elástica que pretende adicionar ao grupo failover. 
1. No painel **'Visão Geral',** selecione o nome do servidor sob **o nome do Servidor** para abrir as definições para o servidor.
  
    ![Servidor aberto para piscina elástica](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecione **grupos Failover** sob o painel **Definições** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover. 

    ![Adicione novo grupo de failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na página do **Grupo Failover,** introduza ou selecione os valores necessários e, em seguida, selecione **Criar**. Ou cria um novo servidor secundário ou seleciona um servidor secundário existente. 

1. Selecione Bases de **Dados dentro do grupo** e, em seguida, escolha a piscina elástica que pretende adicionar ao grupo failover. Se uma piscina elástica ainda não existir no servidor secundário, aparece um aviso que o leva a criar uma piscina elástica no servidor secundário. Selecione o aviso e, em seguida, selecione **OK** para criar a piscina elástica no servidor secundário. 
        
    ![Adicione piscina elástica ao grupo failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selecione **Selecione para** aplicar as definições elásticas da piscina no grupo failover e, em seguida, selecione **Criar** para criar o seu grupo de failover. A adição da piscina elástica ao grupo failover iniciará automaticamente o processo de georeplicação. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o seu grupo de failover e adicione-lhe a sua piscina elástica usando o PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>Ativação pós-falha de teste

Teste falha da sua piscina elástica utilizando o portal Azure, ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Falhe no seu grupo de failover até ao servidor secundário e, em seguida, falhe usando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
1. Selecione a piscina elástica que pretende adicionar ao grupo failover. 
1. No painel **'Visão Geral',** selecione o nome do servidor sob **o nome do Servidor** para abrir as definições para o servidor.
  
    ![Servidor aberto para piscina elástica](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Selecione **os grupos Failover** sob o painel **Definições** e, em seguida, escolha o grupo failover que criou na secção 2. 
  
   ![Selecione o grupo failover a partir do portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Reveja qual o servidor primário e qual o servidor secundário. 
1. Selecione **Failover** do painel de tarefas para falhar sobre o seu grupo de failover contendo a sua piscina elástica. 
1. Selecione **Sim** no aviso que o avisa de que as sessões de TDS serão desligadas. 

   ![Fail over your failover group contendo a sua base de dados SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Reveja qual o servidor primário, qual servidor é secundário. Se o failover for bem sucedido, os dois servidores devem ter trocado de papéis. 
1. Selecione **Failover** novamente para falhar o grupo failover de volta às definições originais. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste falha do seu grupo de failover usando PowerShell.

Verifique o papel da réplica secundária: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Falhar no servidor secundário: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

> [!IMPORTANT]
> Se necessitar de eliminar a base de dados secundária, retire-a do grupo failover antes de a eliminar. Eliminar uma base de dados secundária antes de ser removido do grupo failover pode causar comportamentos imprevisíveis. 

## <a name="managed-instance"></a>Instância gerida

Crie um grupo de failover entre duas instâncias geridas usando o portal Azure, ou PowerShell. 

Você terá de configurar o [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) ou criar uma porta de entrada para a rede virtual de cada instância gerida, ligar os dois gateways e, em seguida, criar o grupo failover. 

### <a name="prerequisites"></a>Pré-requisitos
Considere os seguintes pré-requisitos:

- O caso gerido secundário deve estar vazio.
- A gama de sub-redes para a rede virtual secundária não deve sobrepor-se à gama de sub-redes da rede virtual primária. 
- A colagem e o fuso horário da instância secundária devem coincidir com os da instância primária. 
- Ao ligar os dois gateways, a **Chave Partilhada** deve ser a mesma para ambas as ligações. 

### <a name="create-primary-virtual-network-gateway"></a>Criar gateway de rede virtual primária 

Se não configurar o [ExpressRoute,](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)pode criar o portal de rede virtual primário com o portal Azure ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o portal de rede virtual primária utilizando o portal Azure. 

1. No [portal Azure,](https://portal.azure.com)dirija-se ao seu grupo de recursos e selecione o recurso de **rede Virtual** para a sua instância gerida principalmente. 
1. Selecione **Subnets** em **Definições** e, em seguida, selecione para adicionar uma nova **sub-rede Gateway**. Deixe os valores padrão. 

   ![Adicionar porta de entrada para instância gerida primária](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Assim que o gateway da sub-rede for criado, selecione **Criar um recurso** a partir do painel de navegação esquerdo e, em seguida, digitar `Virtual network gateway` na caixa de pesquisa. Selecione o recurso de gateway da **rede Virtual** publicado pela **Microsoft**. 

   ![Criar um novo portal de rede virtual](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Preencha os campos necessários para configurar o portal da sua instância principal gerida. 

   O quadro seguinte mostra os valores necessários para a porta de entrada para a instância gerida primária:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Assinatura** |  A subscrição onde é a sua principal instância gerida. |
    | **Nome** | O nome para o seu portal de rede virtual. | 
    | **Região** | A região onde o seu caso secundário gerido é. |
    | **Tipo de gateway** | Selecione **VPN**. |
    | **Tipo VPN** | Selecione **baseado em rota** |
    | **SKU**| Deixe o `VpnGw1`padrão de . |
    | **Localização**| O local onde está a sua instância secundária gerida e a rede virtual secundária.   |
    | **Rede virtual**| Selecione a rede virtual para a sua instância secundária gerida. |
    | **Endereço IP público**| Selecione **Criar novo**. |
    | **Nome do endereço IP público**| Insira um nome para o seu endereço IP. |
    | &nbsp; | &nbsp; |

1. Deixe os outros valores como padrão e, em seguida, selecione **Review + crie** para rever as definições para o seu gateway de rede virtual.

   ![Definições primárias de gateway](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selecione **Criar** para criar o seu novo portal de rede virtual. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o gateway principal da rede virtual utilizando o PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Criar gateway de rede virtual secundária

Crie o gateway de rede virtual secundário utilizando o portal Azure, ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Repita os passos na secção anterior para criar a sub-rede virtual e a porta de entrada para a instância gerida secundária. Preencha os campos necessários para configurar o portal para a sua instância gerida secundária. 

   O quadro seguinte mostra os valores necessários para a porta de entrada para a instância gerida secundária:

   | **Campo** | Valor |
   | --- | --- |
   | **Assinatura** |  A subscrição onde é a sua instância secundária gerida. |
   | **Nome** | O nome para o seu `secondary-mi-gateway`portal de rede virtual, como . | 
   | **Região** | A região onde o seu caso secundário gerido é. |
   | **Tipo de gateway** | Selecione **VPN**. |
   | **Tipo VPN** | Selecione **baseado em rota** |
   | **SKU**| Deixe o `VpnGw1`padrão de . |
   | **Localização**| O local onde está a sua instância secundária gerida e a rede virtual secundária.   |
   | **Rede virtual**| Selecione a rede virtual que foi `vnet-sql-mi-secondary`criada na secção 2, como . |
   | **Endereço IP público**| Selecione **Criar novo**. |
   | **Nome do endereço IP público**| Introduza um nome para o `secondary-gateway-IP`seu endereço IP, como . |
   | &nbsp; | &nbsp; |

   ![Definições secundárias de gateway](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o gateway de rede virtual secundário utilizando o PowerShell. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>Ligar os gateways 
Crie ligações entre os dois gateways utilizando o portal Azure, ou PowerShell. 

Há que criar duas ligações - a ligação desde a porta principal até à porta de entrada secundária, e depois a ligação desde a porta de entrada secundária até à porta principal. 

A chave partilhada utilizada para ambas as ligações deve ser a mesma para cada ligação. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Crie ligações entre os dois portais utilizando o portal Azure. 

1. Selecione **Criar um recurso** a partir do portal [Azure](https://portal.azure.com).
1. Digite `connection` na caixa de pesquisa e, em seguida, prima introduza para pesquisar, o que o leva ao recurso **Connection,** publicado pela Microsoft.
1. Selecione **Criar** para criar a sua ligação. 
1. No separador Basics, selecione os **seguintes valores** e, em seguida, selecione **OK**. 
    1. Selecione `VNet-to-VNet` para o **tipo de Ligação**. 
    1. Selecione a subscrição na lista pendente. 
    1. Selecione o grupo de recursos para a sua instância gerida na queda. 
    1. Selecione a localização da sua instância gerida principal a partir do drop-down 
1. No separador **Definições,** selecione ou introduza os seguintes valores e, em seguida, selecione **OK**:
    1. Escolha o portal principal de rede para o `Primary-Gateway`primeiro portal de **rede virtual,** como .  
    1. Escolha o gateway de rede secundário para o `Secondary-Gateway`segundo portal de **rede virtual,** como . 
    1. Selecione a caixa de verificação ao lado de **Estabelecer conectividade bidirecional**. 
    1. Ou deixe o nome de ligação primária padrão, ou mude o nome para um valor à sua escolha. 
    1. Forneça uma **chave partilhada (PSK)** para `mi1m2psk`a ligação, tais como . 

   ![Criar ligação gateway](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. No separador **Resumo,** reveja as definições para a sua ligação bidirecional e, em seguida, selecione **OK** para criar a sua ligação. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie ligações entre os dois gateways utilizando o PowerShell. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Criar o grupo failover 
Crie o grupo failover para os seus casos geridos utilizando o portal Azure, ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o grupo failover para os seus casos geridos usando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
1. Selecione a instância gerida primária que pretende adicionar ao grupo failover.  
1. Em **Definições,** navegue para **Grupos de Failover de Instância** e, em seguida, opte por adicionar **grupo** para abrir a página do **Grupo Failover de Instância.** 

   ![Adicione um grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na página do **Grupo Failover instance,** digite o nome do seu grupo failover e, em seguida, escolha a instância gerida secundária a partir da queda. Selecione **Criar** para criar o seu grupo failover. 

   ![Criar grupo failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Uma vez concluída a implementação do grupo failover, será saqueado de volta para a página do **grupo Failover.** 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o grupo failover para os seus casos geridos usando powerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>Ativação pós-falha de teste

Teste falha do seu grupo failover usando o portal Azure, ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste falha do seu grupo de failover usando o portal Azure. 

1. Navegue para a sua instância gerida _secundária_ dentro do [portal Azure](https://portal.azure.com) e selecione **Instance Failover Groups** em definições. 
1. A revisão da instância gerida é a principal, e que geriu a instância é a secundária. 
1. Selecione **Failover** e, em seguida, selecione **Sim** no aviso sobre as sessões de TDS estarem desligadas. 

   ![Falhar sobre o grupo failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Reveja qual o caso manhoso que é o principal e qual o caso secundário. Se o failover for bem sucedido, as duas instâncias deveriam ter mudado de funções. 

   ![Casos geridos mudaram de funções após falha](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Vá para a _nova_ instância secundária gerida e selecione **Failover** mais uma vez para falhar a instância primária de volta ao papel principal. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste falha do seu grupo de failover usando PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>Localizar o ponto final do ouvinte

Assim que o grupo failover estiver configurado, atualize a cadeia de ligação para a sua aplicação para o ponto final do ouvinte. Isto manterá a sua aplicação ligada ao ouvinte do grupo failover, em vez da base de dados primária, piscina elástica ou instância gerida. Desta forma, não é necessário atualizar manualmente a cadeia de ligação sempre que a sua entidade de base de dados Azure SQL falha, e o tráfego é encaminhado para qualquer entidade que esteja atualmente primária. 

O ponto final do ouvinte `fog-name.database.windows.net`é na forma de , e é visível no portal Azure, ao visualizar o grupo failover:

![Cadeia de ligação de grupo failover](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Observações

- A remoção de um grupo de failover para uma única base de dados ou agrupara-se não para a replicação e não elimina a base de dados replicada. Terá de parar manualmente a geo-replicação e eliminar a base de dados do servidor secundário se pretender adicionar uma única base de dados ou agrupar uma base de dados a um grupo de failover depois de ter sido removida. Não fazer nenhuma das coisas pode `The operation cannot be performed due to multiple errors` resultar num erro semelhante ao da tentativa de adicionar a base de dados ao grupo failover. 


## <a name="next-steps"></a>Passos seguintes

Para etapas detalhadas que configuram um grupo de failover, consulte os seguintes tutoriais:
- [Adicione uma única base de dados a um grupo de failover](sql-database-single-database-failover-group-tutorial.md)
- [Adicionar um conjunto elástico a um grupo de ativação pós-falha](sql-database-elastic-pool-failover-group-tutorial.md)
- [Adicione instâncias geridas a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md)
 
Para uma visão geral das opções de alta disponibilidade da Base de Dados Azure SQL, consulte grupos [de geo-replicação](sql-database-active-geo-replication.md) e [falha automática](sql-database-auto-failover-group.md). 
