---
title: Executar livros de corridas no Azure Automation Hybrid Runbook Worker
description: Este artigo fornece informações sobre a execução de livros de execução em máquinas no seu datacenter local ou fornecedor de nuvem com o papel de Trabalhador de Runbook Híbrido.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 902734ddc7195d643c3aedb4054f57723d1a51c2
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632125"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Running runbooks em um trabalhador de runbook híbrido

Os livros de execução que visam um Trabalhador de Runbook Híbrido normalmente gerem recursos no computador local ou contra recursos no ambiente local onde o trabalhador é implantado. Os livros de corridas em Automação Azure normalmente gerem recursos na nuvem Azure. Apesar de serem usados de forma diferente, os livros que funcionam na Azure Automation e os livros que funcionam num Trabalhador de Runbook Híbrido são idênticos na estrutura.

Quando você é autor de um livro de corridas para executar em um Trabalhador de Runbook Híbrido, você deve editar e testar o livro de execução na máquina que acolhe o trabalhador. A máquina hospedeira tem todos os módulos PowerShell e acesso à rede necessários para gerir e aceder aos recursos locais. Uma vez testado o livro de execução na máquina Hybrid Runbook Worker, pode então carregá-lo para o ambiente De automação Azure, onde pode ser executado sobre o trabalhador. 

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Permissões de livro de corridas para um trabalhador híbrido do livro de corridas

Como estão a aceder a recursos não-Azure, os livros de execução que funcionam num Trabalhador do Livro Híbrido não podem utilizar o mecanismo de autenticação normalmente utilizado por livros de execução autenticando recursos Azure. Um livro de execução ou fornece a sua própria autenticação aos recursos locais, ou confunde a autenticação usando [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Também pode especificar uma conta Run As para fornecer um contexto de utilizador para todos os livros de execução.

### <a name="runbook-authentication"></a>Autenticação do livro de corridas

Por padrão, os livros de execução são executados no computador local. Para o Windows, funcionam no contexto da conta do Sistema local. Para o Linux, funcionam no contexto da **nxautomação**especial da conta de utilizador. Em qualquer dos cenários, os cadernos de contas devem fornecer a sua própria autenticação aos recursos a que acedem.

Pode utilizar ativos [credenciais](automation-credentials.md) e [certificados](automation-certificates.md) no seu livro de execução com cmdlets que lhe permitem especificar credenciais para que o livro de execução possa autenticar diferentes recursos. O exemplo que se segue mostra uma parte de um livro que reinicia um computador. Recupera credenciais de um ativo credencial e o nome do computador a partir `Restart-Computer` de um ativo variável e depois utiliza estes valores com o cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Também pode utilizar uma atividade [InlineScript.](automation-powershell-workflow.md#inlinescript) `InlineScript`permite-lhe executar blocos de código noutro computador com credenciais especificadas pelo [parâmetro comum PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>Conta Run As

Em vez de ter o seu livro de execução fornecer a sua própria autenticação aos recursos locais, pode especificar uma conta Run As para um grupo híbrido runbook worker. Para isso, deve definir um [ativo credencial](automation-credentials.md) que tenha acesso aos recursos locais. Estes recursos incluem lojas de certificados e todos os livros de execução executados sob estas credenciais em um Trabalhador de Runbook Híbrido no grupo.

O nome do utilizador da credencial deve estar num dos seguintes formatos:

* nome de utilizador de domínio\
* username@domain
* nome de utilizador (para contas locais para o computador no local)

Utilize o seguinte procedimento para especificar uma Conta Gema para um grupo híbrido de trabalhadores de runbook.

1. Criar um [ativo credencial](automation-credentials.md) com acesso aos recursos locais.
2. Abra a conta Automation no portal Azure.
3. Selecione o azulejo **dos Grupos De Trabalho Híbrido** e, em seguida, selecione o grupo.
4. Selecione **Todas as definições**, seguidas por configurações do **grupo de trabalhadores híbridos**.
5. Alterar o valor da **execução de** **predefinição** para **personalizado**.
6. Selecione a credencial e clique **em Guardar**.

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Identidades Geridas para recursos azure

Os trabalhadores híbridos do livro de corridas em máquinas virtuais Azure podem usar identidades geridas para os recursos azure para autenticar os recursos azure. Utilizando identidades geridas para recursos Azure em vez de Run As contas proporcionam benefícios porque não precisa:

* Exportar o certificado Run As e, em seguida, importá-lo para o Trabalhador do Livro De Execução Híbrido.
* Renovar o certificado utilizado pela conta Run As.
* Manuseie o executar Como objeto de ligação no seu código de rumir.

Siga os próximos passos para utilizar uma identidade gerida para os recursos Azure num Trabalhador de Livro Híbrido.

1. Crie um VM Azure.
2. Configure identidades geridas para os recursos Azure no VM. Consulte [as identidades geridas pela Configure para os recursos Azure num VM utilizando o portal Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Dê ao VM acesso a um grupo de recursos em Gestor de Recursos. Consulte [a Utilização](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)de uma identidade gerida atribuída ao sistema Windows VM para aceder ao Gestor de Recursos .
4. Instale o trabalhador do Livro híbrido no VM. Consulte [a implementação de um trabalhador](automation-windows-hrw-install.md)do livro híbrido do Windows .
5. Atualize o livro de execução para utilizar o `Identity` cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) com o parâmetro para autenticar os recursos do Azure. Esta configuração reduz a necessidade de utilizar uma conta Run As e realizar a gestão da conta associada.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`Trabalha para um Trabalhador de RaquinaDo Híbrido utilizando uma identidade atribuída ao sistema e uma única identidade atribuída ao utilizador. Se utilizar várias identidades atribuídas ao utilizador no Trabalhador do Livro de Execução Híbrido, o seu livro de execução deve especificar o parâmetro *AccountId* para `Connect-AzAccount` selecionar uma identidade específica atribuída ao utilizador.

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Execução de automação Como conta

Como parte do seu processo automatizado de construção para a implantação de recursos no Azure, poderá necessitar de acesso a sistemas no local para suportar uma tarefa ou conjunto de passos na sua sequência de implantação. Para fornecer a autenticação contra o Azure utilizando a conta Run As, tem de instalar o certificado de conta Run As.

O seguinte livro de execução powerShell, chamado **Export-RunAsCertificateToHybridWorker,** exporta o Certificado De Execução Como certificado da sua conta Azure Automation. O livro de execução descarrega e importa o certificado para a loja de certificados de máquina local num Trabalhador híbrido do Runbook que está ligado à mesma conta. Uma vez concluído esse passo, o livro de execução verifica que o trabalhador pode autenticar com sucesso o Azure usando a conta Run As.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Para os livros `Add-AzAccount` de `Add-AzureRMAccount` execução da `Connect-AzAccount`PowerShell, e são pseudónimos para . Ao pesquisar os itens da sua biblioteca, se não vir `Connect-AzAccount`, pode usar, `Add-AzAccount`ou pode atualizar os seus módulos na sua conta Deautomação.

Para terminar de preparar a Conta Run As:

1. Guarde o livro de execução **Export-RunAsCertificateToHybridWorker** para o seu computador com uma extensão **.ps1.**
2. Importe-o para a sua conta de Automação.
3. Editar o livro de execução, alterando o valor da `Password` variável a sua própria palavra-passe. 
4. Publique o livro de corridas.
5. Executar o livro de corridas, visando o grupo Hybrid Runbook Worker que executa e autentica os livros de execução usando a conta Run As. 
6. Examine o fluxo de trabalho para ver se relata a tentativa de importar o certificado para a loja de máquinas local, e segue com várias linhas. Este comportamento depende de quantas contas de Automação define na sua subscrição e do grau de sucesso da autenticação.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Comportamento de trabalho em trabalhadores híbridos de runbook

A Azure Automation trata de trabalhos em Trabalhadores de Livro Híbrido de forma um pouco diferente dos empregos executados em caixas de areia Azure. Uma diferença fundamental é que não há limite para a duração do trabalho nos trabalhadores do livro. Os livros de corridas executados em caixas de areia Azure estão limitados a três horas por causa de [uma quota justa.](automation-runbook-execution.md#fair-share)

Para um livro de corridas de longa duração, você deve ter certeza de que é resistente a um possível reinício, por exemplo, se a máquina que acolhe o trabalhador reiniciar. Se a máquina de anfitriões Hybrid Runbook Worker reiniciar, qualquer trabalho de resta em execução reinicia desde o início, ou a partir do último ponto de verificação para os livros de execução powerShell Workflow. Depois de um trabalho de rés-do-livro ser reiniciado mais de três vezes, está suspenso.

Lembre-se que os empregos para os Trabalhadores híbridos do Livro de Corridas são executados sob a conta do Sistema local no Windows ou na conta **de nxautomation** no Linux. Para o Linux, deve garantir que a conta **de nxautomation** tem acesso ao local onde os módulos de caderneta estão armazenados. Quando utilizar o cmdlet de [Módulo de Instalação, certifique-se](/powershell/module/powershellget/install-module) de especificar todos os Utilizadores para o `Scope` parâmetro para garantir que a conta de **automação** tem acesso. Para obter mais informações sobre o PowerShell no Linux, consulte [Questões Conhecidas para PowerShell em plataformas não Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Iniciar um livro de corridas em um trabalhador híbrido do livro de corridas

O início de um livro de [execução na Azure Automation](automation-starting-a-runbook.md) descreve diferentes métodos para iniciar um livro de corridas. O arranque de um livro de corridas num Trabalhador de Runbook Híbrido usa uma **opção Run** que lhe permite especificar o nome de um grupo híbrido runbook worker. Quando um grupo é especificado, um dos trabalhadores desse grupo recupera e dirige o livro de corridas. Se o seu livro de execução não especificar esta opção, a Azure Automation executa o livro de execução como de costume.

Quando inicia um livro de corridas no portal Azure, é-lhe apresentado o **Run em** opção para o qual pode selecionar O Trabalhador **Azure** ou **Híbrido**. Se selecionar **O Trabalhador Híbrido,** pode escolher o grupo Hybrid Runbook Worker a partir de uma queda.

Utilize `RunOn` o parâmetro `Start-AzureAutomationRunbook` com o cmdlet. O exemplo seguinte utiliza o Windows PowerShell para iniciar um livro de corridas chamado **Test-Runbook** num grupo híbrido de runbook worker chamado MyHybridGroup.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> O `RunOn` parâmetro foi `Start-AzureAutomationRunbook` adicionado na versão 0.9.1 do Microsoft Azure PowerShell. Deverá [descarregar a versão mais recente](https://azure.microsoft.com/downloads/) se tiver uma anterior instalada. Instale apenas esta versão na estação de trabalho onde está a iniciar o livro de execução da PowerShell. Não precisa de o instalar no computador Hybrid Runbook Worker a menos que pretenda iniciar os livros a partir deste computador.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Trabalhar com livros de execução assinados num Trabalhador de Runbook Híbrido windows

Pode configurar um Trabalhador do Livro Híbrido do Windows para executar apenas livros de execução assinados.

> [!IMPORTANT]
> Uma vez configurado um Trabalhador do Livro Híbrido para executar apenas livros de execução assinados, os livros de execução que não tenham sido assinados não serão executados no trabalhador.

### <a name="create-signing-certificate"></a>Criar certificado de assinatura

O exemplo seguinte cria um certificado auto-assinado que pode ser usado para a assinatura de livros de execução. Este código cria o certificado e exporta-o para que o Trabalhador do Livro Híbrido possa importá-lo mais tarde. A impressão digital também é devolvida para posterior utilização na referência ao certificado.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Certificado de importação e trabalhadores configurados para validação de assinaturas

Copie o certificado que criou a cada Trabalhador Híbrido de Runbook em grupo. Executar o seguinte guião para importar o certificado e configurar os trabalhadores para usar a validação de assinatura seletivas em livros de execução.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Assine os seus livros de execução usando o certificado

Com os Trabalhadores híbridos do Runbook configurados para utilizar apenas livros de execução assinados, deve assinar livros de execução que devem ser utilizados no Trabalhador do Livro Híbrido. Utilize o seguinte código PowerShell para assinar estes livros de execução.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando um livro de execução tiver sido assinado, deve importá-lo para a sua conta Deautomação e publicá-lo com o bloco de assinaturas. Para aprender a importar livros de execução, consulte [importar um livro de execução de um ficheiro para a Automação Azure.](manage-runbooks.md#importing-a-runbook)

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Trabalhando com livros assinados num Trabalhador de Runbook Híbrido Linux

Para poder trabalhar com livros de corridas assinados, um Trabalhador do Livro híbrido Linux deve ter o [GPG](https://gnupg.org/index.html) executável na máquina local.

> [!IMPORTANT]
> Uma vez configurado um Trabalhador do Livro Híbrido para executar apenas livros de execução assinados, os livros de execução que não tenham sido assinados não serão executados no trabalhador.

### <a name="create-a-gpg-keyring-and-keypair"></a>Criar um teclado GPG e um teclado

Para criar o teclado GPG e o teclado, utilize a conta **de nxautomação hybrid** Runbook Worker.

1. Utilize a aplicação sudo para iniciar sessão como conta **de nxautomation.**

    ```bash
    sudo su – nxautomation
    ```

2. Uma vez que esteja a utilizar **a nxautomation,** gere o par de chaves GPG. A GPG guia-o através dos degraus. Deve fornecer nome, endereço de e-mail, tempo de validade e palavra-passe. Depois espera-se até que haja entropia suficiente na máquina para que a chave seja gerada.

    ```bash
    sudo gpg --generate-key
    ```

3. Como o diretório GPG foi gerado com o sudo, você deve mudar o seu proprietário para **nxautomation** usando o seguinte comando.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Disponibilize o porta-chaves ao Trabalhador híbrido do livro de corridas

Uma vez criado o porta-chaves, disponibilize-o ao Trabalhador híbrido do livro de corridas. Modificar o ficheiro de definições **/var/opt/microsoft/omsagent/state/automaworker/diy/worker.conf** para `[worker-optional]`incluir o seguinte código de exemplo na secção de ficheiros .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Verifique se a validação da assinatura está em

Se a validação da assinatura tiver sido desativada na máquina, deve ligá-la executando o seguinte comando sudo. Substitua-a `<LogAnalyticsworkspaceId>` com o seu ID do espaço de trabalho.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Assine um livro de corridas

Depois de configurar a validação de assinaturas, utilize o seguinte comando GPG para assinar um livro de execução.

```bash
gpg –-clear-sign <runbook name>
```

O livro de `<runbook name>.asc`execução assinado chama-se .

Agora pode enviar o livro de execução assinado para a Azure Automation e executá-lo como um livro regular.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os métodos para iniciar um livro de corridas, consulte [Iniciar um Livro de Corridas em Automação Azure](automation-starting-a-runbook.md).
* Para entender como usar o editor textual para trabalhar com os livros powerShell em Automação Azure, consulte [Editar um Livro de Corridas em Automação Azure](automation-edit-textual-runbook.md).
* Se os seus livros não estiverem a completar com sucesso, reveja o guia de resolução de problemas para falhas na [execução de livros](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)de corridas .
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
