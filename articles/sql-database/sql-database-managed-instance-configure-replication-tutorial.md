---
title: Configure a replicação transacional entre duas instâncias geridas e o SQL Server
description: Um tutorial que confunde a replicação entre uma instância gerida pela Editora, uma instância gerida pelo Distribuidor, e um assinante do SQL Server num VM Azure, juntamente com componentes de rede necessários, tais como a zona privada de DNS e o peering VPN.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: fa6e393500e9deeb91ee84aa5255320003817f08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719896"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Tutorial: Configure a replicação transacional entre duas instâncias geridas e o Servidor SQL


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Configure um Exemplo Gerido como um Editor de replicação. 
> - Configure uma instância gerida como distribuidor de replicação. 
> - Configure um Servidor SQL como assinante. 

![Replicação entre um pub SQL MI, SQL MI Dist, e um sub SQL Server](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Este tutorial destina-se a um público experiente e assume que o utilizador está familiarizado com a implementação e ligação a ambos os casos geridos, e VMs sQL Server dentro do Azure. Como tal, certos passos neste tutorial são ensombrados. 

Para saber mais, consulte a Base de [Dados Azure SQL gerida](sql-database-managed-instance-index.yml)na visão geral da instância , [capacidades](sql-database-managed-instance.md)e artigos de [replicação transacional SQL.](sql-database-managed-instance-transactional-replication.md)

Para configurar a replicação entre um editor de instância gerido e um assinante de instância gerido, consulte a [replicação transacional Configure entre duas instâncias geridas](replication-with-sql-database-managed-instance.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para completar o tutorial, certifique-se de que tem os seguintes pré-requisitos:

- Uma [subscrição Azure.](https://azure.microsoft.com/free/) 
- Experiência com a implementação de duas instâncias geridas dentro da mesma rede virtual. 
- Um assinante Do SQL Server, quer no local, quer num VM Azure. Este tutorial usa um VM Azure.  
- [Estúdio de Gestão de Servidores SQL (SSMS) 18.0 ou superior](/sql/ssms/download-sql-server-management-studio-ssms).
- A versão mais recente do [Azure Powershell.](/powershell/azure/install-az-ps?view=azps-1.7.0)
- A porta 445 e o 1433 permitem o tráfego SQL tanto na Firewall Azure como na Firewall do Windows. 

## <a name="1---create-the-resource-group"></a>1 - Criar o grupo de recursos
Utilize o seguinte código PowerShell para criar um novo grupo de recursos:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 - Criar dois casos geridos
Crie duas instâncias geridas dentro deste novo grupo de recursos utilizando o [portal Azure.](https://portal.azure.com) 

- O nome da instância gerida pela `sql-mi-publisher` editora deve ser: (juntamente com alguns caracteres `vnet-sql-mi-publisher`para aleatoriedade) e o nome da rede virtual deve ser .
- O nome da instância gerida pelo `sql-mi-distributor` distribuidor deve ser: (juntamente com alguns caracteres para aleatoriedade) e deve estar na mesma rede virtual que _a instância gerida pelo editor._

   ![Utilize a vnet do editor para o distribuidor](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

Para mais informações sobre a criação de um caso gerido, consulte [Criar uma instância gerida no portal](sql-database-managed-instance-get-started.md)

  > [!NOTE]
  > Por uma questão de simplicidade, e por ser a configuração mais comum, este tutorial sugere colocar o distribuidor gerido na mesma rede virtual que a editora. No entanto, é possível criar o distribuidor numa rede virtual separada. Para isso, terá de configurar o peering VPN entre as redes virtuais do editor e distribuidor e, em seguida, configurar o peering VPN entre as redes virtuais do distribuidor e assinante. 

## <a name="3---create-a-sql-server-vm"></a>3 - Criar um VM de servidor SQL
Crie uma máquina virtual SQL Server utilizando o [portal Azure](https://portal.azure.com). A máquina virtual Do Servidor SQL deve ter as seguintes características:

- Nome:`sql-vm-sub`
- Imagem: SQL Server 2016 ou superior
- Grupo de recursos: o mesmo que a instância gerida
- Rede virtual:`sql-vm-sub-vnet` 

Para obter mais informações sobre a implementação de um VM de servidor SQL para o Azure, consulte [Quickstart: Create SQL Server VM](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md).

## <a name="4---configure-vpn-peering"></a>4 - Configure VPN peering
Configure o peering VPN para permitir a comunicação entre a rede virtual das duas instâncias geridas, e a rede virtual do Servidor SQL. Para tal, utilize este código PowerShell:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet 

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Assim que o peering VPN estiver estabelecido, teste a conectividade lançando o SQL Server Management Studio (SSMS) no seu Servidor SQL e conectando-se a ambas as instâncias geridas. Para obter mais informações sobre a ligação a uma instância gerida utilizando SSMS, consulte [Use SSMS para se ligar ao MI](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance). 

![Testar a conectividade com os casos geridos](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5 - Criar zona privada de DNS

Uma zona privada de DNS permite o encaminhamento de DNS entre as instâncias geridas e o Servidor SQL. 

### <a name="create-private-dns-zone"></a>Criar zona privada de DNS
1. Assine no [portal Azure.](https://portal.azure.com)
1. Selecione **Criar um recurso** para criar um novo recurso Azure. 
1. Procure `private dns zone` no Azure Marketplace. 
1. Escolha o recurso **privado da zona DNS** publicado pela Microsoft e, em seguida, selecione **Criar** para criar a zona DNS. 
1. Escolha o grupo de subscrição e recursos a partir da queda. 
1. Forneça um nome arbitrário para `repldns.com`a sua zona DNS, tais como . 

   ![Criar zona privada de DNS](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Selecione **Rever + criar**. Reveja os parâmetros da sua zona Privada DNS e, em seguida, selecione **Criar** para criar o seu recurso. 

### <a name="create-a-record"></a>Criar um disco

1. Vá à sua nova **zona Privada dNS** e selecione **visão geral**. 
1. Selecione **+ Conjunto de registos** para criar um novo A-Record. 
1. Forneça o nome do seu VM do Servidor SQL, bem como o endereço IP interno privado. 

   ![Configurar um disco](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Selecione **OK** para criar o disco A. 

### <a name="link-the-virtual-network"></a>Ligar a rede virtual

1. Vá para a sua nova **zona Privada DNS** e selecione links de **rede Virtual**. 
1. Selecione **+ Adicionar**. 
1. Forneça um nome para o `Pub-link`link, como . 
1. Selecione a sua subscrição a partir do drop-down e, em seguida, selecione a rede virtual para a sua instância gerida pelo editor. 
1. Verifique a caixa ao lado do **registo automático enable**. 

   ![Criar ligação vnet](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. Selecione **OK** para ligar a sua rede virtual. 
1. Repita estes passos para adicionar um link para a `Sub-link`rede virtual do assinante, com um nome como . 


## <a name="6---create-azure-storage-account"></a>6 - Criar conta de armazenamento azure

[Crie uma Conta](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) de Armazenamento Azure para o diretório de trabalho e, em seguida, crie uma parte de [ficheiro](../storage/files/storage-how-to-create-file-share.md) dentro da conta de armazenamento. 

Copiar o caminho da partilha de ficheiros no formato de:`\\storage-account-name.file.core.windows.net\file-share-name`   

Exemplo: `\\replstorage.file.core.windows.net\replshare`

Copie a cadeia de ligação de ligação de acesso ao armazenamento no formato de:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Exemplo: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


Para mais informações, consulte Gerir as chaves de [acesso à conta](../storage/common/storage-account-keys-manage.md)de armazenamento . 


## <a name="7---create-a-database"></a>7 - Criar uma base de dados
Crie uma nova base de dados sobre o MI da editora. Para o fazer, siga estes passos:

1. Lance o Estúdio de Gestão de Servidores SQL (SSMS) no seu Servidor SQL. 
1. Ligue-se `sql-mi-publisher` à instância gerida. 
1. Abra uma nova janela **de consulta** e execute a seguinte consulta T-SQL para criar a base de dados:

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="8---configure-distribution"></a>8 - Distribuição de configuração 
Uma vez estabelecida a conectividade e tiver uma base de `sql-mi-distributor` dados de amostras, pode configurar a distribuição na sua instância gerida. Para o fazer, siga estes passos:

1. Lance o Estúdio de Gestão de Servidores SQL (SSMS) no seu Servidor SQL. 
1. Ligue-se `sql-mi-distributor` à instância gerida. 
1. Abra uma nova janela **de consulta** e execute o seguinte código Transact-SQL para configurar a distribuição na instância gerida pelo distribuidor: 

   ```sql
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Certifique-se de que utiliza`\`apenas @working_directory as pestanas traseiras para o parâmetro. A utilização`/`de um corte dianteiro () pode causar um erro ao ligar-se à parte do ficheiro. 

1. Ligue-se `sql-mi-publisher` à instância gerida. 
1. Abra uma nova janela **de consulta** e execute o seguinte código Transact-SQL para registar o distribuidor na editora: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9 - Criar a publicação
Uma vez configurada a distribuição, pode agora criar a publicação. Para o fazer, siga estes passos: 

1. Lance o Estúdio de Gestão de Servidores SQL (SSMS) no seu Servidor SQL. 
1. Ligue-se `sql-mi-publisher` à instância gerida. 
1. No **Object Explorer,** expanda o nó de **replicação** e clique à direita na pasta **De publicação local.** Selecione **Nova Publicação...**. 
1. Selecione **Next** para passar pela página de boas-vindas. 
1. Na página Base de `ReplTutorial` Dados de **Publicação,** selecione a base de dados que criou anteriormente. Selecione **Next**. 
1. Na página do **tipo Publicação,** selecione **publicação transacional**. Selecione **Next**. 
1. Na página de **Artigos,** verifique a caixa ao lado das **Tabelas**. Selecione **Next**. 
1. Na página **Filter Table Rows,** selecione **Seguinte** sem adicionar filtros. 
1. Na página do **Snapshot Agent,** verifique imediatamente a caixa ao lado da **Create snapshot e mantenha o instantâneo disponível para inicializar as subscrições**. Selecione **Next**. 
1. Na página de Segurança do **Agente,** selecione Definições de **Segurança..**. Forneça credenciais de login do SQL Server para utilizar para o agente Snapshot e para se ligar ao Editor. Selecione **OK** para fechar a página de Segurança do **Agente Instantâneo.** Selecione **Next**. 

   ![Configurar a segurança do Agente Snapshot](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. Na página **Wizard Actions,** opte por **Criar a publicação** e (opcionalmente) optar por Gerar um ficheiro de script com **passos para criar a publicação** se quiser guardar este script para mais tarde. 
1. Na página **Complete the Wizard,** nomeie a sua publicação `ReplTest` e selecione **Next** para criar a sua publicação. 
1. Uma vez criada a sua publicação, refresque o nó **de Replicação** no **Object Explorer** e expanda **publicações locais** para ver a sua nova publicação. 


## <a name="10---create-the-subscription"></a>10 - Criar a subscrição 

Uma vez criada a publicação, pode criar a subscrição. Para o fazer, siga estes passos: 

1. Lance o Estúdio de Gestão de Servidores SQL (SSMS) no seu Servidor SQL. 
1. Ligue-se `sql-mi-publisher` à instância gerida. 
1. Abra uma nova janela **de consulta** e execute o seguinte código Transact-SQL para adicionar o agente de subscrição e distribuição. Utilize o DNS como parte do nome do assinante. 

```sql
use [ReplTutorial]
exec sp_addsubscription 
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub', 
@subscription_type = N'Push', 
@sync_type = N'automatic', 
@article = N'all', 
@update_mode = N'read only', 
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub', 
@job_login = N'azureuser', 
@job_password = '<Complex Password>', 
@subscriber_security_mode = 0, 
@subscriber_login = N'azureuser', 
@subscriber_password = '<Complex Password>', 
@dts_package_location = N'Distributor'
GO
```

## <a name="11---test-replication"></a>11 - Replicação de teste 

Uma vez configurada a replicação, pode testá-la inserindo novos itens na editora e assistindo às alterações propagadas ao assinante. 

Executar o seguinte corte T-SQL para ver as linhas no assinante:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Faça o seguinte corte T-SQL para inserir linhas adicionais na editora e, em seguida, verifique novamente as linhas no assinante. 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar recursos

1. Navegue para o seu grupo de recursos no [portal Azure.](https://portal.azure.com) 
1. Selecione as instâncias geridas e, em seguida, **selecione Eliminar**. Digite `yes` a caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**. Este processo pode demorar algum tempo a ser concluído em segundo plano, e até que esteja feito, não poderá eliminar o *cluster Virtual* ou quaisquer outros recursos dependentes. Monitorize a eliminação no separador Atividade para confirmar que a sua instância gerida foi eliminada. 
1. Uma vez eliminada a instância gerida, elimine o *cluster Virtual* selecionando-o no seu grupo de recursos e, em seguida, escolhendo **Eliminar**. Digite `yes` a caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**. 
1. Eliminar os recursos restantes. Digite `yes` a caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**. 
1. Elimine o grupo de recursos selecionando o grupo de `myResourceGroup`recursos **Delete**, digitando em nome do grupo de recursos e, em seguida, selecionando **Delete**. 

## <a name="known-errors"></a>Erros conhecidos

### <a name="windows-logins-are-not-supported"></a>Os logins do Windows não são suportados

`Exception Message: Windows logins are not supported in this version of SQL Server.`

O agente foi configurado com um login do Windows e precisa de utilizar um login do SQL Server. Utilize a página de Segurança do **Agente** das propriedades da **Publicação** para alterar as credenciais de login para um login do Servidor SQL. 


### <a name="failed-to-connect-to-azure-storage"></a>Falha na ligação ao Armazenamento Azure


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Fio de conexão de armazenamento azure obtido para replstorage 2019-11-19 02:21:05.07 Ligação a Azure Arquivos Armazenamento '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Não conseguiu ligar-se ao Armazenamento Azure '' com erro de OS: 53.


Isto provavelmente porque a porta 445 está fechada na firewall Azure, na firewall do Windows ou em ambos. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

A utilização de um corte para a frente em vez de barrar no caminho do ficheiro para a parte do ficheiro pode causar este erro. Isto está `\\replstorage.file.core.windows.net\replshare` bem: isto pode causar um erro de OS 55:`'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Não podia ligar-se ao Assinante

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Possíveis soluções:
- Certifique-se de que a porta 1433 está aberta. 
- Certifique-se de que o TCP/IP está ativado no assinante. 
- Confirme que o nome DNS foi usado na criação do assinante. 
- Verifique se as suas redes virtuais estão corretamente ligadas na zona privada de DNS. 
- Verifique se o seu registo A está configurado corretamente. 
- Verifique se o seu peering VPN está configurado corretamente. 

### <a name="no-publications-to-which-you-can-subscribe"></a>Nenhuma publicação a que possa subscrever

Ao adicionar uma nova subscrição utilizando o novo assistente **de subscrição,** na página **De publicação,** poderá descobrir que não existem bases de dados e publicações listadas como opções disponíveis, podendo ver a seguinte mensagem de erro:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
Embora seja possível que esta mensagem de erro seja precisa, e não existam publicações disponíveis na editora a que ligou, ou que não tenha permissões suficientes, este erro também pode ser causado por uma versão mais antiga do SQL Server Management Studio. Tente atualizar para o SQL Server Management Studio 18.0 ou superior para descartar isto como uma causa principal. 


## <a name="next-steps"></a>Passos seguintes

### <a name="enable-security-features"></a>Ativar funcionalidades de segurança

Consulte o seguinte artigo de funcionalidades de segurança de funcionalidades de segurança de recursos de instância [geridas](sql-database-managed-instance.md#azure-sql-database-security-features) para uma lista completa de formas de proteger a sua base de dados. São discutidos os seguintes recursos de segurança:

- [Auditoria de instância gerida](sql-database-managed-instance-auditing.md) 
- [Sempre encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Deteção de ameaças](sql-database-managed-instance-threat-detection.md) 
- [Máscara de dados dinâmica](/sql/relational-databases/security/dynamic-data-masking)
- [Segurança ao Nível da Linha](/sql/relational-databases/security/row-level-security) 
- [Encriptação transparente de dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Funcionalidades de instância gerida

Para uma visão geral completa das capacidades de instância geridas, consulte:

> [!div class="nextstepaction"]
> [Funcionalidades de instância gerida](sql-database-managed-instance.md)
