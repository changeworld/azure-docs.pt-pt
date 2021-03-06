---
title: Guia de provisionamento para VMs do Servidor SQL com Azure PowerShell [ Microsoft Docs
description: Fornece passos e comandos PowerShell para criar um Azure VM com imagens de galeria de máquinas virtuais SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1578547fbca4caaecb209021569f0fbb2f1ae24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790629"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Como fornecer máquinas virtuais SQL Server com PowerShell Azure

Este guia explica as suas opções para criar VMs do Servidor Windows SQL com O PowerShell Azure. Para um exemplo de PowerShell Azure simplificado com valores mais padrão, consulte o [quickstart SQL VM Azure PowerShell](quickstart-sql-vm-create-powershell.md).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Configurar a sua subscrição

1. Abra a PowerShell e estabeleça o acesso à sua conta Azure executando o comando **Connect-AzAccount.**

   ```powershell
   Connect-AzAccount
   ```

1. Devia ver um ecrã para introduzir as suas credenciais. Utilize o mesmo e-mail e palavra-passe que utiliza para iniciar sessão no portal do Azure.

## <a name="define-image-variables"></a>Definir variáveis de imagem
Para reutilizar valores e simplificar a criação de scripts, comece por definir uma série de variáveis. Altere os valores do parâmetro como desejar, mas esteja ciente de que as restrições de nomeação estão relacionadas com os comprimentos de nome e caracteres especiais ao modificar os valores fornecidos.

### <a name="location-and-resource-group"></a>Grupo de localização e recursos
Defina a região de dados e o grupo de recursos no qual cria os outros recursos VM.

Modifique como quiser e, em seguida, executar estes cmdlets para inicializar estas variáveis.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Propriedades de armazenamento
Defina a conta de armazenamento e o tipo de armazenamento a utilizar pela máquina virtual.

Modifique como quiser e, em seguida, executar o seguinte cmdlet para inicializar estas variáveis. Recomendamos a utilização de [SSDs premium](../disks-types.md#premium-ssd) para cargas de trabalho de produção.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Propriedades da rede
Defina as propriedades a utilizar pela rede na máquina virtual. 

- Interface de rede
- Método de atribuição de TCP/IP
- Nome da rede virtual
- Nome de sub-rede virtual
- Gama de endereços IP para a rede virtual
- Gama de endereços IP para a sub-rede
- Etiqueta de nome de domínio público

Modifique como quiser e, em seguida, execute este cmdlet para inicializar estas variáveis.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Propriedades de máquinas virtuais
Defina o nome da máquina virtual, o nome do computador, o tamanho da máquina virtual e o nome do disco do sistema operativo para a máquina virtual.

Modifique como quiser e, em seguida, execute este cmdlet para inicializar estas variáveis.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Escolha uma imagem do Servidor SQL

Utilize as seguintes variáveis para definir a imagem do Servidor SQL para utilizar para a máquina virtual. 

1. Primeiro, enumere todas as ofertas de `Get-AzVMImageOffer` imagem do SQL Server com o comando. Este comando lista as imagens atuais que estão disponíveis no Portal Do Azure e também imagens mais antigas que só podem ser instaladas com powerShell:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Para este tutorial, utilize as seguintes variáveis para especificar o SQL Server 2017 no Windows Server 2016.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Em seguida, enumere as edições disponíveis para a sua oferta.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Para este tutorial, utilize a edição de Desenvolvimento do SQL Server 2017 **(SQLDEV).** A edição developer está livremente licenciada para testes e desenvolvimento, e você só paga pelo custo de execução do VM.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Com o modelo de implementação do Gestor de Recursos, o primeiro objeto que cria é o grupo de recursos. Utilize o cmdlet [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) para criar um grupo de recursos Azure e seus recursos. Especifique as variáveis que previamente inicializou para o nome e localização do grupo de recursos.

Execute este cmdlet para criar o seu novo grupo de recursos.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Criar uma conta do Storage
A máquina virtual requer recursos de armazenamento para o disco do sistema operativo e para os dados do Servidor SQL e ficheiros de registo. Para a simplicidade, vaicriar um único disco para ambos. Pode anexar discos adicionais mais tarde utilizando o cmdlet do [Disco Add-Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk) para colocar os dados do SQL Server e registar ficheiros em discos dedicados. Utilize o cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) para criar uma conta de armazenamento padrão no seu novo grupo de recursos. Especifique as variáveis que previamente inicializou para o nome da conta de armazenamento, nome Sku de armazenamento e localização.

Execute este cmdlet para criar a sua nova conta de armazenamento.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> A criação da conta de armazenamento pode demorar alguns minutos.

## <a name="create-network-resources"></a>Criar recursos de rede
A máquina virtual requer uma série de recursos de rede para a conectividade da rede.

* Cada máquina virtual requer uma rede virtual.
* Uma rede virtual deve ter pelo menos uma sub-rede definida.
* Uma interface de rede deve ser definida com um endereço IP público ou privado.

### <a name="create-a-virtual-network-subnet-configuration"></a>Criar uma configuração de subnet de rede virtual
Comece por criar uma configuração de sub-rede para a sua rede virtual. Para este tutorial, crie uma sub-rede predefinida utilizando o cmdlet [New-AzVirtualNetworkSubnetconfig.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) Especifique as variáveis que previamente inicializou para o nome da sub-rede e prefixo de endereço.

> [!NOTE]
> Pode definir propriedades adicionais da configuração da subnet de rede virtual utilizando este cmdlet, mas que está fora do âmbito deste tutorial.

Execute este cmdlet para criar a sua configuração de sub-rede virtual.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Em seguida, crie a sua rede virtual no seu novo grupo de recursos utilizando o cmdlet [New-AzVirtualNetwork.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) Especifique as variáveis que previamente inicializou para o prefixo de nome, localização e endereço. Utilize a configuração da sub-rede que definiu no passo anterior.

Execute este cmdlet para criar a sua rede virtual.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Criar o endereço IP público
Agora que a sua rede virtual está definida, tem de configurar um endereço IP para conectividade com a máquina virtual. Para este tutorial, crie um endereço IP público utilizando endereços IP dinâmicos para apoiar a conectividade da Internet. Utilize o cmdlet [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) para criar o endereço IP público no seu novo grupo de recursos. Especifique as variáveis que previamente inicializou para o nome, localização, método de atribuição e etiqueta de nome de domínio DNS.

> [!NOTE]
> Pode definir propriedades adicionais do endereço IP público utilizando este cmdlet, mas que está fora do âmbito deste tutorial inicial. Também pode criar um endereço privado ou um endereço com um endereço estático, mas que também está fora do âmbito deste tutorial.

Execute este cmdlet para criar o seu endereço IP público.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Criar o grupo de segurança da rede
Para proteger o tráfego vm e SQL Server, crie um grupo de segurança de rede.

1. Em primeiro lugar, crie uma regra do grupo de segurança da rede para o RDP para permitir ligações remotas de ambiente de trabalho.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Configure uma regra do grupo de segurança da rede que permita o tráfego na porta TCP 1433. Ao fazê-lo, permite que as ligações ao SQL Server passem pela internet.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Crie o grupo de segurança da rede.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Criar a interface de rede
Está agora pronto para criar a interface de rede para a sua máquina virtual. Utilize o cmdlet [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) para criar a sua interface de rede no seu novo grupo de recursos. Especifique o nome, localização, sub-rede e endereço IP público previamente definido.

Execute este cmdlet para criar a sua interface de rede.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Configurar um objeto VM
Agora que os recursos de armazenamento e rede estão definidos, está pronto para definir os recursos computacionais para a máquina virtual.

- Especifique o tamanho da máquina virtual e várias propriedades do sistema operativo.
- Especifique a interface de rede que criou anteriormente.
- Defina o armazenamento de bolhas.
- Especifique o disco do sistema operativo.

### <a name="create-the-vm-object"></a>Criar o objeto VM
Comece por especificar o tamanho da máquina virtual. Para este tutorial, especifique um DS13. Utilize o cmdlet [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) para criar um objeto de máquina virtual configurável. Especifique as variáveis que previamente inicializou para o nome e tamanho.

Execute este cmdlet para criar o objeto de máquina virtual.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Crie um objeto credencial para manter o nome e a senha para as credenciais do administrador local
Antes de poder definir as propriedades do sistema operativo para a máquina virtual, deve fornecer as credenciais para a conta de administrador local como uma cadeia segura. Para isso, utilize o cmdlet [Get-Credential.](https://technet.microsoft.com/library/hh849815.aspx)

Executar o seguinte cmdlet e, na janela de pedido de credencial PowerShell, digite o nome e a palavra-passe para usar na conta do administrador local na máquina virtual.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Detete as propriedades do sistema operativo para a máquina virtual
Agora está pronto para definir as propriedades do sistema operativo da máquina virtual com o cmdlet [Set-AzVMOperatingSystem.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem)

- Detete o tipo de sistema operativo como Windows.
- Exija a instalação do agente virtual da [máquina.](../../extensions/agent-windows.md)
- Especifique que o cmdlet permite a atualização automática.
- Especifique as variáveis que previamente inicializou para o nome da máquina virtual, o nome do computador e a credencial.

Execute este cmdlet para definir as propriedades do sistema operativo para a sua máquina virtual.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Adicione a interface de rede à máquina virtual
Em seguida, utilize o cmdlet [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) para adicionar a interface de rede utilizando a variável que definiu anteriormente.

Execute este cmdlet para definir a interface de rede para a sua máquina virtual.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Delineie o local de armazenamento de bolhas para o disco a utilizar pela máquina virtual
Em seguida, delineie o local de armazenamento de bolhas para o disco do VM utilizando as variáveis que definiu anteriormente.

Execute este cmdlet para definir o local de armazenamento blob.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Detete as propriedades do disco do sistema operativo para a máquina virtual
Em seguida, detete as propriedades do disco do sistema operativo para a máquina virtual utilizando o cmdlet [Set-AzVMOSDisk.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) 

- Especifique que o sistema operativo para a máquina virtual virá de uma imagem.
- Ajuste o cache para ler apenas (porque o Servidor SQL está a ser instalado no mesmo disco).
- Especifique as variáveis que previamente inicializou para o nome VM e o disco do sistema operativo.

Execute este cmdlet para definir as propriedades do disco do sistema operativo para a sua máquina virtual.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Especifique a imagem da plataforma para a máquina virtual
O último passo de configuração é especificar a imagem da plataforma para a sua máquina virtual. Para este tutorial, utilize a mais recente imagem CTP do SQL Server 2016. Utilize o cmdlet [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) para utilizar esta imagem com as variáveis que definiu anteriormente.

Execute este cmdlet para especificar a imagem da plataforma para a sua máquina virtual.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Criar a VM do SQL
Agora que terminou os passos de configuração, está pronto para criar a máquina virtual. Utilize o cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) para criar a máquina virtual utilizando as variáveis que definiu.

> [!TIP]
> Criar o VM pode levar alguns minutos.

Execute este cmdlet para criar a sua máquina virtual.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

A máquina virtual é criada.

> [!NOTE]
> Se tiver um erro sobre diagnósticos de arranque, pode ignorá-lo. É criada uma conta de armazenamento padrão para diagnósticos de arranque porque a conta de armazenamento especificada para o disco da máquina virtual é uma conta de armazenamento premium.

## <a name="install-the-sql-iaas-agent"></a>Instalar o Agente Iaas do SQL
As máquinas virtuais do SQL Server suportam funcionalidades de gestão automatizadas com a extensão do [agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Para instalar o agente no novo VM e registá-lo com o fornecedor de recursos, execute o comando [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) após a criação da máquina virtual. Especifique o tipo de licença para o seu VM de Servidor SQL, escolhendo entre pagar-as-você-go ou trazer a sua própria licença através do [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Para mais informações sobre licenciamento, consulte [o modelo de licenciamento.](virtual-machines-windows-sql-ahb.md) 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```


## <a name="stop-or-remove-a-vm"></a>Parar ou remover um VM

Se não precisar que o VM seja executado continuamente, pode evitar encargos desnecessários, impedindo-o quando não estiver a ser utilizado. O comando seguinte para a VM, mas deixa-a disponível para utilização futura.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Também pode eliminar permanentemente todos os recursos associados à máquina virtual com o comando **Remove-AzResourceGroup.** Ao fazê-lo, também elimina permanentemente a máquina virtual, por isso use este comando com cuidado.

## <a name="example-script"></a>Script de exemplo
O seguinte script contém o script completo PowerShell para este tutorial. Assume que já configura a subscrição Azure para utilizar com os comandos **Connect-AzAccount** e **Select-AzSubscription.**

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Passos seguintes
Após a criação da máquina virtual, pode:

- Ligue-se à máquina virtual utilizando RDP
- Configure as definições do Servidor SQL no portal para o seu VM, incluindo:
   - [Configurações de armazenamento](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Tarefas de gestão automatizadas](virtual-machines-windows-sql-server-agent-extension.md)
- [Configurar a conectividade](virtual-machines-windows-sql-connect.md)
- Ligar clientes e aplicações à nova instância do SQL Server

