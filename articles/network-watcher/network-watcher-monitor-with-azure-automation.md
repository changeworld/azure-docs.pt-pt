---
title: Resolução de problemas e monitor de portais VPN - Automação Azure
titleSuffix: Azure Network Watcher
description: Este artigo descreve como diagnostica conectividade no local com a Automação Azure e o Observador de Rede
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
ms.openlocfilehash: 74c9f44ff5fbbbb50bba1594d371633fd49857eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845035"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Monitor de portais VPN com resolução de problemas do Observador de Rede

Obter informações profundas sobre o desempenho da sua rede é fundamental para fornecer serviços fiáveis aos clientes. Por conseguinte, é fundamental detetar rapidamente as condições de paragem da rede e tomar medidas corretivas para mitigar a condição de paragem. A Azure Automation permite-lhe implementar e executar uma tarefa de forma programática através de livros de execução. A utilização da Automatização Azure cria uma receita perfeita para realizar monitorização e alerta contínuos e proativos de rede.

## <a name="scenario"></a>Cenário

O cenário na imagem seguinte é uma aplicação multi-camadas, com conectividade no local estabelecida usando um Gateway VPN e túnel. Garantir que o VPN Gateway está a funcionar é fundamental para o desempenho das aplicações.

Um livro de execução é criado com um script para verificar o estado de ligação do túnel VPN, utilizando a API de resolução de problemas de recursos para verificar se há estado de túnel de ligação. Se o estado não for saudável, um gatilho de e-mail é enviado aos administradores.

![Exemplo de cenário][scenario]

Este cenário irá:

- Crie um livro `Start-AzureRmNetworkWatcherResourceTroubleshooting` de corridas chamando o cmdlet para o estado de ligação de resolução de problemas
- Ligue um horário ao livro de corridas

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar este cenário, deve ter os seguintes pré-requisitos:

- Uma conta de automação Azure em Azure. Certifique-se de que a conta de automação tem os módulos mais recentes e conta também com o módulo AzureRM.Network. O módulo AzureRM.Network está disponível na galeria do módulo se precisar adicioná-lo à sua conta de automação.
- Deve ter um conjunto de credenciais configuradas na Automação Azure. Saiba mais na [segurança da Automação Azure](../automation/automation-security-overview.md)
- Um servidor SMTP válido (Office 365, seu e-mail no local ou outro) e credenciais definidas na Automação Azure
- Um Gateway de rede virtual configurado em Azure.
- Uma conta de armazenamento existente com um recipiente existente para armazenar os registos.

> [!NOTE]
> A infraestrutura retratada na imagem anterior é para fins de ilustração e não são criadas com os passos contidos neste artigo.

### <a name="create-the-runbook"></a>Criar o livro de corridas

O primeiro passo para configurar o exemplo é criar o livro de execução. Este exemplo usa uma conta executada. Para saber sobre contas executadas, visite [AAuthenticate Runbooks com Azure Run Como conta](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>Passo 1

Navegue para a Automação Azure no [portal Azure](https://portal.azure.com) e clique em **Runbooks**

![visão geral da conta de automação][1]

### <a name="step-2"></a>Passo 2

Clique **em Adicionar um livro de execução** para iniciar o processo de criação do livro de execução.

![lâmina de livros de corridas][2]

### <a name="step-3"></a>Passo 3

Em **Quick Create,** clique em **Criar um novo livro de execução** para criar o livro de corridas.

![adicionar uma lâmina de livro de corridas][3]

### <a name="step-4"></a>Passo 4

Neste passo, damos um nome ao livro de execução, no exemplo em que se chama **Get-VPNGatewayStatus**. É importante dar ao livro de execução um nome descritivo, e recomendado dar-lhe um nome que siga os padrões padrão de nomeação powerShell. O tipo de livro de execução para este exemplo é **PowerShell**, as outras opções são Graphical, PowerShell workflow e Graphical PowerShell workflow.

![lâmina de livro de corridas][4]

### <a name="step-5"></a>Passo 5

Neste passo é criado o livro de execução, o seguinte exemplo de código fornece todo o código necessário para o exemplo. Os itens do código que \<\> contêm valor precisam de ser substituídos com os valores da sua subscrição.

Use o seguinte código como clique em **Guardar**

```powershell
# Set these variables to the proper values for your environment
$o365AutomationCredential = "<Office 365 account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for Office 365 account
$cred = Get-AutomationPSCredential -Name $o365AutomationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>Passo 6

Uma vez que o livro de execução é guardado, um horário deve ser ligado a ele para automatizar o início do livro de execução. Para iniciar o processo, clique em **Agendar**.

![Passo 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Ligue um horário ao livro de corridas

Deve ser criado um novo horário. Clique em Link um horário para o seu livro de **execução**.

![Passo 7][7]

### <a name="step-1"></a>Passo 1

Na lâmina **de Agenda,** clique em **Criar um novo horário**

![Passo 8][8]

### <a name="step-2"></a>Passo 2

Na **lâmina new schedule** preencha a informação do horário. Os valores que podem ser definidos estão na seguinte lista:

- **Nome** - O nome amigável da agenda.
- **Descrição** - Uma descrição da agenda.
- **Início -** Este valor é uma combinação de data, hora e fuso horário que compõem o horário que o horário dispara.
- **Recorrência** - Este valor determina a repetição dos horários.  Valores válidos são **Uma vez** ou **Recorrentes.**
- **Recur cada -** O intervalo de recorrência do horário em horas, dias, semanas ou meses.
- **Definir Expiração** - O valor determina se o horário deve expirar ou não. Pode ser definido para **Sim** ou **Não.** Uma data e hora válidas devem ser fornecidas se sim for escolhido.

> [!NOTE]
> Se precisa de ter um livro de corridas mais frequentemente do que a cada hora, vários horários devem ser criados em intervalos diferentes (ou seja, 15, 30, 45 minutos após a hora)

![Passo 9][9]

### <a name="step-3"></a>Passo 3

Clique em Guardar para guardar o horário para o livro de execução.

![Passo 10][10]

## <a name="next-steps"></a>Passos seguintes

Agora que tem um entendimento sobre como integrar a resolução de problemas do Network Watcher com a Azure Automation, aprenda a desencadear capturas de pacotes em alertas VM visitando [Criar um alerta desencadeado pela captura de pacotes com](network-watcher-alert-triggered-packet-capture.md)o Azure Network Watcher .

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png
