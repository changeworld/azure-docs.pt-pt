---
title: Erros de resolução de problemas com os Runbooks de Automação Azure
description: Aprenda a resolver problemas e a resolver problemas que poderá encontrar com os livros de execução da Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 26c5c5b31d5f3f9e1a642c0bafb947190e479055
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632617"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Resolver erros com runbooks

Quando tiver erros na execução de livros de execução na Automatização Azure, pode utilizar os seguintes passos para ajudar a diagnosticar os problemas.

1. **Certifique-se de que o seu script de rés-do-livro executa com sucesso na sua máquina local.** 

    Consulte os [Docs PowerShell](/powershell/scripting/overview) ou [Python](https://docs.python.org/3/) para obter módulos de referência e aprendizagem linguísticas. Executar o seu guião localmente pode descobrir e resolver erros comuns, tais como:

      * Módulos em falta
      * Erros de sintaxe
      * Erros lógicos

2. **Investigue os fluxos de erros do livro de [corridas.](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)**

    Veja estes fluxos para mensagens específicas e compare-as com os erros documentados neste artigo.

3. **Certifique-se de que os seus nódosos e espaço de trabalho automationol dispõem dos módulos necessários.** 

    Se o seu livro de recortes importar quaisquer módulos, verifique se estão disponíveis na sua conta Automation utilizando os passos listados nos [módulos de Importação](../shared-resources/modules.md#importing-modules). Atualize os seus módulos para a versão mais recente seguindo as instruções dos [módulos Update Azure em Automação Azure](..//automation-update-azure-modules.md). Para obter mais informações sobre resolução de [problemas, consulte os módulos Troubleshoot](shared-resources.md#modules).

4. **Faça se o seu livro de execução estiver suspenso ou falhar inesperadamente.**

    * [Verificar o estado do trabalho](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) define os estatutos do livro de corridas e algumas possíveis causas.
    * [Adicione uma saída adicional](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) ao livro de execução para identificar o que acontece antes da suspensão do livro de execução.
    * [Lide com quaisquer exceções](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) que sejam lançadas pelo seu trabalho.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Cenário: Executar Login-AzureRMAccount para iniciar sessão

### <a name="issue"></a>Problema

Recebe o seguinte erro ao executar um livro de execução:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

Este erro pode ocorrer quando não estiver a utilizar uma conta 'Executar Como' ou a Conta Executar Como a conta tiver expirado. Ver Gerir a Execução de [Automação Azure Como contas](https://docs.microsoft.com/azure/automation/manage-runas-account).

Este erro tem duas causas primárias:

* Existem diferentes versões do módulo AzureRM ou Az.
* Estás a tentar aceder a recursos numa subscrição separada.

### <a name="resolution"></a>Resolução

Se receber este erro depois de atualizar um módulo AzureRM ou Az, deverá atualizar todos os seus módulos para a mesma versão.

Se estiver a tentar aceder a recursos noutra subscrição, pode seguir os passos abaixo para configurar permissões.

1. Vá à Execução de Automação Como conta e copie o ID da aplicação e a impressão digital.
  ![Id de aplicação de cópia e impressão digital](../media/troubleshoot-runbooks/collect-app-id.png)
1. Vá ao Controlo de Acesso da subscrição onde a conta de Automação NÃO é hospedada, e adicione uma nova atribuição de funções.
  ![Controlo de acesso](../media/troubleshoot-runbooks/access-control.png)
1. Adicione o ID da aplicação recolhido anteriormente. Selecione permissões do Contribuinte.
   ![Adicionar atribuição de função](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Copie o nome da subscrição.
1. Agora pode utilizar o seguinte código de livro para testar as permissões da sua conta Automation para a outra subscrição. Substitua `"\<CertificateThumbprint\>"` pelo valor copiado no passo 1. Substitua `"\<SubscriptionName\>"` pelo valor copiado no passo 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Cenário: Incapaz de encontrar a subscrição do Azure

### <a name="issue"></a>Problema

Recebe o seguinte erro ao `Select-AzureSubscription` `Select-AzureRmSubscription` trabalhar com o cmdlet ou cmdlet:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Erro

Este erro pode ocorrer se:

* O nome da subscrição não é válido.
* O utilizador do Diretório Ativo Azure que está a tentar obter os detalhes da subscrição não está configurado como administrador da subscrição.

### <a name="resolution"></a>Resolução

Siga os passos abaixo para determinar se autenticou ao Azure e tenha acesso à subscrição que está a tentar selecionar.

1. Para garantir que o seu guião funciona sozinho, teste-o fora da Automação Azure.
2. Certifique-se de que `Add-AzureAccount` o seu script `Select-AzureSubscription` executa o cmdlet antes de executar o cmdlet.
3. Adicione `Disable-AzureRmContextAutosave –Scope Process` ao início do seu livro de corridas. Esta chamada cmdlet garante que quaisquer credenciais se aplicam apenas à execução do atual livro de execução.
4. Se ainda vir esta mensagem de erro, `AzureRmContext` modifique `Add-AzureAccount` o seu código adicionando o parâmetro para o cmdlet e, em seguida, execute o código.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Cenário: A autenticação para o Azure falhou porque a autenticação de vários fatores está ativada

### <a name="issue"></a>Problema

Recebe o seguinte erro ao autenticar o Azure com o seu nome de utilizador EI e senha:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Causa

Se tiver a autenticação multifactor na sua conta Azure, não pode utilizar um utilizador do Azure Ative Directory para autenticar o Azure. Em vez disso, precisa de usar um certificado ou um diretor de serviço para autenticar.

### <a name="resolution"></a>Resolução

Para utilizar um certificado com cmdlets modelo de implantação clássico azure, consulte criar [e adicionar um certificado para gerir os serviços Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Para utilizar um diretor de serviço com o Azure Resource Manager cmdlets, consulte [Criar o principal de serviço utilizando o portal Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e autenticar um diretor de serviço com o Gestor de Recursos [Azure.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Cenário: Vê um erro nos fluxos de trabalho sobre o método get_SerializationSettings

### <a name="issue"></a>Problema

Você vê o seguinte erro nos seus fluxos de trabalho para um livro de corridas:

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>Causa

Este erro é causado pela utilização de cmdlets de módulo AzureRM e Az num livro de execução. Ocorre quando importa o módulo Az antes de importar o módulo AzureRM.

### <a name="resolution"></a>Resolução

Os cmdlets Az e AzureRM não podem ser importados e usados no mesmo livro de corridas. Para saber mais sobre os cmdlets Az na Automação Azure, consulte o [suporte do módulo Az na Automação Azure.](../az-modules.md)

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Cenário: O livro de corridas falha com o erro: Uma tarefa foi cancelada

### <a name="issue"></a>Problema

O seu livro de execução falha com um erro semelhante ao seguinte exemplo:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Causa

Este erro pode ser causado pela utilização de módulos Azure desatualizados.

### <a name="resolution"></a>Resolução

Pode resolver este erro atualizando os seus módulos Azure para a versão mais recente. 

1. Na sua conta De automação, clique em **Módulos**, em **seguida, atualize os módulos Azure**. 
2. A atualização demora cerca de 15 minutos. Uma vez concluído, recorra o livro de execução que falhou.

Para saber mais sobre a atualização dos seus módulos, consulte [módulos Update Azure em Automação Azure](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Cenário: Os livros de corridas falham ao lidar com várias subscrições

### <a name="issue"></a>Problema

Ao executar livros de execução, o livro de execução não consegue gerir os recursos do Azure.

### <a name="cause"></a>Causa

O livro de execução não está a usar o contexto correto quando corre.

### <a name="resolution"></a>Resolução

O contexto de subscrição pode perder-se quando um livro de execução invoca vários livros de execução. Para garantir que o contexto de subscrição é passado para os `Start-AzureRmAutomationRunbook` livros de execução, o livro de execução do cliente passe o contexto para o cmdlet no `AzureRmContext` parâmetro. Utilize `Disable-AzureRmContextAutosave` o cmdlet `Scope` com o `Process` parâmetro definido para garantir que as credenciais especificadas só são utilizadas para o livro de execução atual. Para mais informações, consulte [Trabalhar com várias subscrições.](../automation-runbook-execution.md#working-with-multiple-subscriptions)

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Cenário: Termo não reconhecido como o nome de um cmdlet, função, script

### <a name="issue"></a>Problema

O seu livro de execução falha com um erro semelhante ao seguinte exemplo:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Causa

Este erro pode acontecer pelas seguintes razões:

* O módulo que contém o cmdlet não é importado para a conta de Automação.
* O módulo que contém o cmdlet é importado, mas está desatualizado.

### <a name="resolution"></a>Resolução

Faça uma das seguintes tarefas para resolver este erro. 

* Para um módulo Azure, consulte [como atualizar os módulos Azure PowerShell na Azure Automation](../automation-update-azure-modules.md) para aprender a atualizar os seus módulos na sua conta Automation.

* Para um módulo não-Azure, certifique-se de que o módulo importado para a sua conta Deautomação.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Cenário: O início do trabalho do livro de corridas foi tentado três vezes, mas não começou de cada vez

### <a name="issue"></a>Problema

O seu livro de execução falha com o seguinte erro:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Este erro ocorre devido a uma das seguintes questões:

* Limite de memória. Um trabalho pode falhar se estiver a usar mais de 400 MB de memória. Os limites documentados da memória atribuída a uma caixa de areia encontram-se nos [limites](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)do serviço Automation . 

* Tomadas de rede. As caixas de areia azure estão limitadas a 1000 tomadas de rede simultâneas. Consulte [os limites do serviço automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Módulo Incompatível. As dependências dos módulos podem não estar corretas. Neste caso, o seu livro `Command not found` de `Cannot bind parameter` execução normalmente devolve uma ou mensagem.

* Sem Autenticação com Diretório Ativo para caixa de areia. O seu livro de corridas tentou chamar um executável ou subprocessque que funciona numa caixa de areia Azure. Não é suportado configurar livros de execução para autenticar com a AD Azure utilizando a Biblioteca de Autenticação de Diretórios Ativos Azure (ADAL).

* Demasiados dados de exceção. O seu livro de execução tentou escrever demasiados dados de exceção ao fluxo de saída.

### <a name="resolution"></a>Resolução

* Limite de memória, tomadas de rede. As formas sugeridas de trabalhar dentro dos limites de memória são dividir a carga de trabalho entre vários livros de execução, processar menos dados na memória, evitar escrever saídas desnecessárias dos seus livros de execução, e considerar quantos postos de controlo estão escritos nos seus livros de fluxo de trabalho PowerShell. Utilize o método claro, como, por exemplo, `$myVar.clear`para limpar variáveis e usar `[GC]::Collect` para executar a recolha de lixo imediatamente. Estas ações reduzem a pegada de memória do seu livro de execução durante o tempo de execução.

* Módulo Incompatível. Atualize os seus módulos Azure seguindo os passos em Como atualizar os [módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

* Sem Autenticação com Diretório Ativo para Sandbox. Ao autenticar a Azure AD com um livro de execução, certifique-se de que o módulo Azure AD está disponível na sua conta Automation. Certifique-se de conceder a Executar Como conta as permissões necessárias para executar as tarefas que o livro de execução automatiza.

  Se o seu livro de execução não puder chamar um executável ou subprocesso a funcionar numa caixa de areia Azure, utilize o livro de execução num Trabalhador híbrido do livro de [corridas](../automation-hrw-run-runbooks.md). Os trabalhadores híbridos não se limitam pelos limites de memória e rede que as caixas de areia Azure têm.

* Demasiados dados de exceção. Há um limite de 1MB no fluxo de saída de emprego. Certifique-se de que o seu livro de execução encerra chamadas para um executável ou subprocesse utilizando `try` e `catch` bloqueia. Se as operações lançarem uma exceção, faça com que o código escreva a mensagem da exceção para uma variável de Automação. Esta técnica impede que a mensagem seja escrita no fluxo de saída de trabalho.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Cenário: Inscrição na conta Azure falhou

### <a name="issue"></a>Problema

Recebe um dos seguintes erros `Add-AzureAccount` ao `Connect-AzureRmAccount` trabalhar com o ou cmdlet:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Estes erros ocorrem se o nome do ativo credencial não for válido. Podem também ocorrer se o nome de utilizador e a palavra-passe que usou para configurar o ativo credencial automation não forem válidos.

### <a name="resolution"></a>Resolução

Para determinar o que está errado, tome os seguintes passos:

1. Certifique-se de que não tem personagens especiais. Estes caracteres `\@` incluem o personagem no nome de ativo credencial automation que está a usar para ligar ao Azure.
2. Verifique se pode utilizar o nome de utilizador e a palavra-passe que estão armazenados na credencial Azure Automation no seu editor local powerShell ISE. Executar os seguintes cmdlets no PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Se a sua autenticação falhar localmente, não configura as suas credenciais de Diretório Ativo Azure corretamente. Consulte a [Autenticação para o Azure utilizando](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) o post de blog do Azure Ative Directory para configurar corretamente a conta Azure Ative Directory.

4. Se o erro parecer transitório, tente adicionar lógica de novo tentativa à sua rotina de autenticação para tornar a autenticação mais robusta.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Cenário: Referência do objeto não definida para uma instância de um objeto

### <a name="issue"></a>Problema

Recebe o seguinte erro ao invocar um `Wait` livro de execução infantil com o parâmetro e o fluxo de saída contém um objeto:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

`Start-AzureRmAutomationRunbook`não manuseia corretamente o fluxo de saída se o fluxo contiver objetos.

### <a name="resolution"></a>Resolução

Recomenda-se implementar uma lógica de sondagem e utilizar o cmdlet [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) para recuperar a saída. Uma amostra desta lógica é definida abaixo.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Cenário: Runbook falha por causa de objeto desserializado

### <a name="issue"></a>Problema

O seu livro de execução falha com o erro:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Causa

Se o seu livro de execução for um PowerShell Workflow, armazena objetos complexos num formato desserializado para persistir o seu estado de releiro se o fluxo de trabalho for suspenso.

### <a name="resolution"></a>Resolução

Utilize qualquer uma das seguintes soluções para corrigir este problema.

* Se estiver a canalizar objetos complexos de um cmdlet para `InlineScript` outro, enrole estes cmdlets numa atividade.
* Passe o nome ou valor que precisa do objeto complexo em vez de passar todo o objeto.
* Utilize um livro de execução PowerShell em vez de um livro de execução powerShell Workflow.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Cenário: Trabalho de releiro falha porque quota atribuída excedeu

### <a name="issue"></a>Problema

O seu trabalho no livro de recortes falha com o erro:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Este erro ocorre quando a execução do trabalho excede a quota gratuita de 500 minutos para a sua conta. Esta quota aplica-se a todos os tipos de tarefas de execução de postos de trabalho. Algumas destas tarefas estão a testar um trabalho, a iniciar um trabalho a partir do portal, a executar um trabalho utilizando webhooks, ou a agendar um trabalho para executar usando o portal Azure ou o seu datacenter. Para saber mais sobre os preços da Automação, consulte [os preços da Automação.](https://azure.microsoft.com/pricing/details/automation/)

### <a name="resolution"></a>Resolução

Se pretender utilizar mais de 500 minutos de processamento por mês, altere a sua subscrição do nível Livre para o nível Básico.

1. Inscreva-se na sua assinatura Azure.
2. Selecione a conta Automation para atualizar.
3. Clique em **Definições,** em **seguida, preços.**
4. Clique em **Ativar** na página inferior para atualizar a sua conta para o nível Básico.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Cenário: Cmdlet não reconhecido na execução de um livro de corridas

### <a name="issue"></a>Problema

O seu trabalho no livro de recortes falha com o erro:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Este erro é causado quando o motor PowerShell não encontra o cmdlet que está a usar no seu livro de execução. É possível que o módulo que contém o cmdlet esteja ausente da conta, há um conflito de nomes com um nome de livro de corridas, ou o cmdlet também existe em outro módulo e a Automação não consegue resolver o nome.

### <a name="resolution"></a>Resolução

Utilize qualquer uma das seguintes soluções para corrigir o problema.

* Certifique-se de que introduziu corretamente o nome cmdlet.
* Certifique-se de que o cmdlet existe na sua conta De automação e que não existem conflitos. Para verificar se o cmdlet está presente, abra um livro de execução no modo de `Get-Command <CommandName>`edição e procure o cmdlet que pretende encontrar na biblioteca, ou corra . Depois de ter validado que o cmdlet está disponível para a conta, e que não existem conflitos de nomecom outros cmdlets ou livros de execução, adicione o cmdlet à tela e certifique-se de que está a utilizar um parâmetro válido definido no seu livro de execução.
* Se tiver um conflito de nomes e o cmdlet estiver disponível em dois módulos diferentes, resolva o problema utilizando o nome totalmente qualificado para o cmdlet. Por exemplo, pode utilizar `ModuleName\CmdletName`.
* Se estiver a executar o livro de corridas no local num grupo de trabalhadores híbridos, certifique-se de que o módulo e o cmdlet estão instalados na máquina que acolhe o trabalhador híbrido.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Cenário: Um livro de corridas de longa duração não está concluído

### <a name="issue"></a>Problema

O teu livro mostra-se em estado de parada depois de correr 3 horas. Também pode receber este erro:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Este comportamento é por design em caixas de areia Azure devido à monitorização [justa](../automation-runbook-execution.md#fair-share) dos processos no âmbito da Automação Azure. Se um processo executar mais de três horas, a parte justa para automaticamente um livro de execução. O estatuto de um livro de execução que ultrapassa o prazo de partilha justa difere por tipo de livro de execução. Os livros powerShell e Python estão definidos para um estado de paragem. Os livros de fluxo de trabalho PowerShell estão definidos para falhar.

### <a name="cause"></a>Causa

O livro de corridas ultrapassou o limite de 3 horas permitido por uma participação justa numa caixa de areia Azure.

### <a name="resolution"></a>Resolução

Uma solução recomendada é executar o livro de corridas num Trabalhador híbrido do livro de [corridas.](../automation-hrw-run-runbooks.md) Os Trabalhadores Híbridos não estão limitados pelo limite de 3 horas de fair book que as caixas de areia Azure têm. Os livros de corridas executados em Trabalhadores híbridos de runbook devem ser desenvolvidos para apoiar comportamentos de reinício se houver problemas inesperados de infraestrutura local.

Outra solução é otimizar o livro de corridas criando [livros infantis.](../automation-child-runbooks.md) Se o seu livro de recortes passar pela mesma função em vários recursos, por exemplo, numa operação de base de dados em várias bases de dados, pode mover a função para um livro de rés-do-ar para crianças. Cada livro de corridas de crianças executa em paralelo num processo separado. Este comportamento diminui a quantidade total de tempo para o livro de execução dos pais completar.

Os cmdlets PowerShell que permitem o cenário do livro de crianças são:

* [Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook). Este cmdlet permite-lhe iniciar um runbook e transmitir parâmetros para o runbook.

* [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob). Se houver operações que devam ser realizadas após o fim do livro de recorridas para crianças, este cmdlet permite-lhe verificar o estado de trabalho de cada criança.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Cenário: Estado: 400 Mau Pedido ao chamar um webhook

### <a name="issue"></a>Problema

Quando tenta invocar um webhook para um livro de execução da Automação Azure, recebe o seguinte erro:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

O gancho que está a tentar ligar está desativado ou expirado.

### <a name="resolution"></a>Resolução

Se o webhook estiver desativado, pode voltar a ativar o webhook através do portal Azure. Se o webhook tiver expirado, deve apagar e recriá-lo. Só pode [renovar um webhook](../automation-webhooks.md#renew-webhook) se ainda não tiver expirado.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Cenário: 429: A taxa de pedido é atualmente demasiado grande...

### <a name="issue"></a>Problema

Recebe a seguinte mensagem de `Get-AzureRmAutomationJobOutput` erro ao executar o cmdlet:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Este erro pode ocorrer ao recuperar a saída de trabalho de um livro de execução que tem muitos [fluxos Verbose](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Resolução

Faça um dos seguintes para resolver este erro.

* Editar o livro de execução e reduzir o número de fluxos de trabalho que emite.

* Reduza o número de correntes a recuperar ao executar o cmdlet. Para tal, pode definir o `Stream` valor do `Get-AzureRmAutomationJobOutput` parâmetro para o cmdlet recuperar apenas os fluxos de saída. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Cenário: Trabalho da PowerShell falha com erro: Não pode invocar método

### <a name="issue"></a>Problema

Recebe a seguinte mensagem de erro ao iniciar um trabalho powerShell num livro de corridas em funcionamento em Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Este erro pode indicar que os livros de execução que executam numa caixa de areia Azure não podem ser executados no [modo de idioma completo](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Resolução

Há duas maneiras de resolver este erro.

* Em vez `Start-Job`de `Start-AzureRmAutomationRunbook` utilizar, use para iniciar o livro de corridas.
* Tente executar o livro de corridas num trabalhador híbrido.

Para saber mais sobre este comportamento e outros comportamentos dos livros de execução da Automação Azure, consulte o comportamento do Livro de [Corridas.](../automation-runbook-execution.md#runbook-behavior)

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Cenário: Linux Hybrid Runbook Worker recebe um pedido de senha ao assinar um livro de corridas

### <a name="issue"></a>Problema

Executar `sudo` o comando de um Trabalhador do Livro híbrido Linux recupera um pedido inesperado para uma senha.

### <a name="cause"></a>Causa

A conta **de nxautomationuser** para o agente Log Analytics para Linux não está corretamente configurada no ficheiro **sudoers.** O Trabalhador do Livro de Corridas Híbrido sofres necessita da configuração adequada de permissões de conta e outros dados para que possa assinar livros de execução no Linux Runbook Worker.

### <a name="resolution"></a>Resolução

* Certifique-se de que o Trabalhador do Livro Híbrido tem o GnuPG (GPG) executável na máquina.

* Verifique a configuração da conta **nxautomationuser** no ficheiro **sudoers.** Ver [runbooks running em um trabalhador de runbook híbrido](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Cenário: Falha da Cmdlet no livro de execução PnP PowerShell sobre automação Azure

### <a name="issue"></a>Problema

Quando um livro de execução escreve um objeto gerado por PnP PowerShell diretamente para a saída da Automação Azure, a saída de cmdlet não pode voltar para a Automação.

### <a name="cause"></a>Causa

Esta questão ocorre mais frequentemente quando a Azure Automation processa livros de execução `add-pnplistitem`que invocam cmdlets PnP PowerShell, por exemplo, sem pegar os objetos de retorno.

### <a name="resolution"></a>Resolução

Edite os seus scripts para atribuir quaisquer valores de retorno a variáveis para que os cmdlets não tentem escrever objetos inteiros à saída padrão. Um script pode redirecionar o fluxo de saída para um cmdlet como mostrado abaixo.

```azurecli
  $null = add-pnplistitem
```

Se o seu script analisar a saída cmdlet, o script deve armazenar a saída numa variável e manipular a variável em vez de simplesmente transmitir a saída.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>O meu problema não está na lista acima.

As secções abaixo listam outros erros comuns e fornecem documentação de apoio para ajudá-lo a resolver o seu problema.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>A função de trabalho de runbook híbrida não executa trabalhos ou não está a responder

Se estiver a trabalhar num Trabalhador Híbrido em vez de na Azure Automation, poderá ter de [resolver problemas com o próprio trabalhador híbrido.](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>O livro de corridas falha sem permissão ou alguma variação

Executar Como contas podem não ter as mesmas permissões contra os recursos do Azure que a sua conta corrente. Certifique-se de que a sua conta Run As tem [permissões para aceder a quaisquer recursos utilizados](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) no seu script.

### <a name="issues-passing-parameters-into-webhooks"></a>Problemas que passam parâmetros em webhooks

Para obter ajuda com a passagem de parâmetros em webhooks, consulte [Iniciar um livro de corridas a partir de um webhook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problemas usando módulos Az

A utilização de módulos Az e módulos AzureRM na mesma conta Automation não é suportada. Consulte [os módulos Az em livros](https://docs.microsoft.com/azure/automation/az-modules) de corridas para obter mais detalhes.

### <a name="inconsistent-behavior-in-runbooks"></a>Comportamento inconsistente nos runbooks

Siga a orientação na execução do Livro de [Corridas](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) para evitar problemas com empregos simultâneos, recursos criados várias vezes, ou outra lógica sensível ao tempo em livros de execução.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook falha com o erro Sem permissão, Proibido (403), ou alguma variação

Executar Como contas podem não ter as mesmas permissões contra os recursos do Azure que a sua conta corrente. Certifique-se de que a sua conta Run As tem [permissões para aceder a quaisquer recursos utilizados](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) no seu script.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Os runbooks estavam a funcionar, mas pararam repentinamente

* Certifique-se de que a conta Run As não expirou. Ver [renovação de certificação.](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)
* Se estiver a utilizar um [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) para iniciar um livro de execução, certifique-se de que o webhook não expirou.

### <a name="passing-parameters-into-webhooks"></a>Transmitir parâmetros para os webhooks

Para obter ajuda com a passagem de parâmetros em webhooks, consulte [Iniciar um livro de corridas a partir de um webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-az-modules"></a>Utilizar os módulos do Az

A utilização de módulos Az e módulos AzureRM na mesma conta Automation não é suportada. Consulte [os módulos Az em livros de execução.](https://docs.microsoft.com/azure/automation/az-modules)

### <a name="using-self-signed-certificates"></a>Utilização de certificados auto-assinados

Para utilizar certificados auto-assinados, consulte [Criar um novo certificado](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Acesso negado ao usar caixa de areia Azure para um livro de corridas

A caixa de areia Azure impede o acesso a todos os servidores COM fora de processo. Por exemplo, uma aplicação ou um livro de execução sandbox não pode ser chamado para a Instrumentação de Gestão do Windows (WMI), ou para o serviço de Instalação do Windows (msiserver.exe). Para mais detalhes sobre a utilização da caixa de areia, consulte a execução do Livro de [Corridas na Automação Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Documentos Recomendados

* [Iniciar um livro de corridas na Automação Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Execução de livro de corridas na Automação Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
