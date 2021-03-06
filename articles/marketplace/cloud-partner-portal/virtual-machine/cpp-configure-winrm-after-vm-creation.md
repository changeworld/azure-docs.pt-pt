---
title: Configure WinRM após criação de máquina virtual Azure [ Mercado Azure
description: Explica como configurar a Gestão Remota do Windows (WinRM) após a criação de uma máquina virtual hospedada no Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: dsindona
ms.openlocfilehash: b80325594eedb87293c31de3236bb4690eb89e05
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273023"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Configure winRM após criação de máquina virtual

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Create a Azure Virtual Machine para](https://aka.ms/CreateAzureVMoffer) gerir as suas ofertas migratórias.

Este artigo explica como configurar uma máquina virtual (VM) existente hospedada no Azure para ativar o WinRM em HTTPS.  Esta configuração aplica-se apenas aos VMs baseados no Windows e requer o seguinte processo em duas etapas:

1. Ativar o tráfego portuário para o protocolo WinRM sobre HTTPS.  Irá configurar esta definição para o seu VM no portal Azure.
2. Configure o VM para ativar o WinRM executando os scripts PowerShell fornecidos.


## <a name="enabling-port-traffic"></a>Habilitar o tráfego portuário

O protocolo WinRM over HTTPS utiliza a porta 5986, que não está ativada por padrão nos VMs windows pré-configurados oferecidos no Mercado Azure. Para ativar este protocolo, utilize os seguintes passos para adicionar uma nova regra ao grupo de segurança da rede (NSG) com o [portal Azure](https://portal.azure.com).  Para mais informações sobre nsgs, consulte [Grupos de Segurança](https://docs.microsoft.com/azure/virtual-network/security-overview).

1. Navegue para a lâmina **Máquinas virtuais >**   < *nome* >   vm **> Definições/Networking**.
2. Clique no nome NSG (neste exemplo, **testvm11002**) para mostrar as suas propriedades:

    ![Propriedades do grupo de segurança da rede](./media/nsg-properties.png)
 
3. Em **Definições,** selecione **as regras de segurança de entrada** para visualizar esta lâmina.
4. Clique **+Adicionar** para criar `WinRM_HTTPS` uma nova regra chamada para a porta TCP 5986.

    ![Adicionar regra de segurança da rede de entrada](./media/nsg-new-rule.png)

5. Clique **ok** quando terminar de fornecer valores.  A lista das regras de segurança de entrada deve conter as seguintes novas entradas.

    ![Listagem das regras de segurança da rede de entrada](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Configure vM para ativar winRM 

Utilize os seguintes passos para ativar e configurar a funcionalidade de Gestão Remota do Windows no seu Windows VM.   

1. Estabeleça uma ligação de ambiente de trabalho remoto ao seu VM hospedado em Azure.  Para mais informações, consulte [Como ligar e iniciar sessão numa máquina virtual Azure que executa o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Os passos restantes serão executados no seu VM.
2. Descarregue os seguintes ficheiros e guarde-os para uma pasta no seu VM:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Abra a **Consola PowerShell** com privilégios elevados **(Executar como Administrador).** 
4. Executar o seguinte comando, fornecendo o parâmetro necessário: o nome de domínio totalmente qualificado (FQDN) para o seu VM: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Script de configuração powershell 1](./media/powershell-file1.png)

    Este script depende dos outros dois ficheiros estarem na mesma pasta.


## <a name="next-steps"></a>Passos seguintes

Depois de configurar o WinRM, está pronto para [implantar o seu VM a partir dos seus VHDs constituintes](./cpp-deploy-vm-vhd.md).
