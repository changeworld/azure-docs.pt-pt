---
title: Monitorize e gerencie empregos da Azure Stream Analytics com a PowerShell
description: Este artigo descreve como usar o Azure PowerShell e cmdlets para monitorizar e gerir os trabalhos do Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 295141dfd9b84428e2ee69354ab0c249fa46d1b6
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998887"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorize e gerencie trabalhos stream analytics com cmdlets Azure PowerShell
Aprenda a monitorizar e gerir os recursos do Stream Analytics com cmdlets De PowerShell e scripts powershell que executam tarefas básicas do Stream Analytics.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Pré-requisitos para executar cmdlets Azure PowerShell para Stream Analytics
* Crie um Grupo de Recursos Azure na sua subscrição. Segue-se uma amostra do script Azure PowerShell. Para obter informações da Azure PowerShell, consulte Instalar e configurar a [PowerShell Azure;](/powershell/azure/overview)  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Os trabalhos stream Analytics criados programáticamente não têm monitorização ativada por padrão.  Pode ativar manualmente a monitorização no Portal Azure navegando para a página Monitor do trabalho e clicando no botão Enable ou pode fazê-lo programativamente seguindo os passos localizados no [Azure Stream Analytics - Monitor Stream Analytics Jobs Programmativamente](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Cmdlets Azure PowerShell para Stream Analytics
Os seguintes cmdlets Azure PowerShell podem ser usados para monitorizar e gerir trabalhos Azure Stream Analytics. Note que o Azure PowerShell tem versões diferentes. 
**Nos exemplos listados, o primeiro comando é para O Azure PowerShell 0.9.8, o segundo comando é para Azure PowerShell 1.0.** Os comandos Azure PowerShell 1.0 terão sempre "Az" no comando.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob [ Get-AzStreamAnalyticsJob
Lista todos os trabalhos do Stream Analytics definidos na subscrição do Azure ou no grupo de recursos especificado, ou obtém informações sobre um trabalho específico dentro de um grupo de recursos.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Este comando PowerShell devolve informações sobre todos os trabalhos do Stream Analytics na subscrição do Azure.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Este comando PowerShell devolve informações sobre todos os trabalhos do Stream Analytics no grupo de recursos StreamAnalytics-Default-Central-US.

**Exemplo 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Este comando PowerShell devolve informações sobre o trabalho stream analytics streamingJob no grupo de recursos StreamAnalytics-Default-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput / Get-AzStreamAnalyticsInput
Lista todas as inputs que são definidas num trabalho específico do Stream Analytics, ou obtém informações sobre uma entrada específica.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Este comando PowerShell devolve informações sobre todas as inputs definidas no trabalho StreamingJob.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Este comando PowerShell devolve informações sobre a entrada denominada EntryStream definida no trabalho StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput [ Get-AzureStreamAnalyticsOutput ] Get-AzStreamAnalyticsOutput
Lista todas as saídas que são definidas num trabalho específico do Stream Analytics, ou obtém informações sobre uma saída específica.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Este comando PowerShell devolve informações sobre as saídas definidas no trabalho StreamingJob.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Este comando PowerShell devolve informações sobre a saída denominada Output definida no trabalho StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota [ Get-AzureStreamAnalyticsQuota ] Get-AzStreamAnalyticsQuota
Obtém informações sobre a quota de unidades de streaming numa região especificada.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Este comando PowerShell devolve informações sobre a quota e utilização de unidades de streaming na região centro dos EUA.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation [ Get-AzureStreamAnalyticsTransformation ] Get-AzStreamAnalyticsTransformation
Obtém informações sobre uma transformação específica definida num trabalho de Stream Analytics.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Este comando PowerShell devolve informações sobre a transformação chamada StreamingJob no trabalho StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput / New-AzStreamAnalyticsInput
Cria uma nova entrada dentro de um trabalho de Stream Analytics, ou atualiza uma entrada especificada existente.

O nome da entrada pode ser especificado no ficheiro .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do ficheiro.

Se especificar uma entrada que já existe e não especificar o parâmetro -Força, o cmdlet perguntará se substitui ou não a entrada existente.

Se especificar o parâmetro -Força e especificar um nome de entrada existente, a entrada será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura e conteúdo do ficheiro JSON, consulte a secção [Create Input (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] da Biblioteca de Referência REST API de [Gestão de Análise sonante][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Este comando PowerShell cria uma nova entrada a partir do ficheiro Input.json. Se uma entrada existente com o nome especificado no ficheiro de definição de entrada já estiver definida, o cmdlet perguntará se deve ou não substituí-la.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Este comando PowerShell cria uma nova entrada no trabalho chamado EntryStream. Se já estiver definida uma entrada existente com este nome, o cmdlet perguntará se deve ou não substituí-lo.

**Exemplo 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Este comando PowerShell substitui a definição da fonte de entrada existente chamada EntryStream com a definição do ficheiro.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob [ New-AzStreamAnalyticsJob
Cria um novo trabalho no Stream Analytics no Microsoft Azure, ou atualiza a definição de um trabalho especificado existente.

O nome do trabalho pode ser especificado no ficheiro .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do ficheiro.

Se especificar um nome de trabalho que já existe e não especificar o parâmetro -Força, o cmdlet perguntará se substitui ou não o trabalho existente.

Se especificar o parâmetro -Força e especificar um nome de trabalho existente, a definição de trabalho será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura e conteúdo do ficheiro JSON, consulte a secção [Create Stream Analytics Job][msdn-rest-api-create-stream-analytics-job] da Biblioteca de Referência REST API de [Gestão de Análise de Fluxo.][stream.analytics.rest.api.reference]

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Este comando PowerShell cria um novo trabalho a partir da definição em JobDefinition.json. Se um trabalho existente com o nome especificado no ficheiro de definição de trabalho já estiver definido, o cmdlet perguntará se deve ou não substituí-lo.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Este comando PowerShell substitui a definição de trabalho para streamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput [ New-AzStreamAnalyticsOutput
Cria uma nova saída dentro de um trabalho de Stream Analytics, ou atualiza uma saída existente.  

O nome da saída pode ser especificado no ficheiro .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do ficheiro.

Se especificar uma saída que já existe e não especificar o parâmetro -Força, o cmdlet perguntará se substitui ou não a saída existente.

Se especificar o parâmetro -Força e especificar um nome de saída existente, a saída será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura e conteúdo do ficheiro JSON, consulte a secção [Create Output (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] da Biblioteca de Referência REST API de [Gestão de Análise sonante][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Este comando PowerShell cria uma nova saída chamada "output" no trabalho StreamingJob. Se uma saída existente com este nome já estiver definida, o cmdlet perguntará se deve ou não substituí-lo.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Este comando PowerShell substitui a definição de "output" no trabalho StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>Transformação New-AzureStreamAnalytics Transformação New-AzStreamAnalytics
Cria uma nova transformação dentro de um trabalho de Stream Analytics, ou atualiza a transformação existente.

O nome da transformação pode ser especificado no ficheiro .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do ficheiro.

Se especificar uma transformação que já existe e não especificar o parâmetro -Força, o cmdlet perguntará se substitui ou não a transformação existente.

Se especificar o parâmetro -Força e especificar um nome de transformação existente, a transformação será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura e conteúdo do ficheiro JSON, consulte a secção [Create Transformation (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] da Biblioteca de Referência REST API de [Gestão de Análise sonante][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Este comando PowerShell cria uma nova transformação chamada StreamingJobTransform no trabalho StreamingJob. Se uma transformação existente já estiver definida com este nome, o cmdlet perguntará se deve ou não substituí-lo.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Este comando PowerShell substitui a definição de StreamingJobTransform no trabalho StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput [ Remover-AzStreamAnalyticsInputput
Asincronicamente elimina uma entrada específica de um trabalho de Stream Analytics no Microsoft Azure.  
Se especificar o parâmetro -Força, a entrada será eliminada sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Este comando PowerShell remove a entrada EventStream no trabalho StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob [ Remover-AzStreamAnalyticsJob
Asincronicamente elimina um trabalho específico do Stream Analytics no Microsoft Azure.  
Se especificar o parâmetro -Força, o trabalho será apagado sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Este comando PowerShell remove o trabalho StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput [ Remover-AzStreamAnalyticsOutput
Asincronicamente elimina uma saída específica de um trabalho de Stream Analytics no Microsoft Azure.  
Se especificar o parâmetro -Força, a saída será eliminada sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Este comando PowerShell remove a saída no trabalho StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob [ Start-AzStreamAnalyticsJob
A sincronizada implementa e inicia um trabalho de Stream Analytics no Microsoft Azure.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Este comando PowerShell inicia o trabalho StreamingJob com uma hora de início de saída personalizada definida para 12 de dezembro de 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob [ Stop-AzStreamAnalyticsJob
Assincronicamente impede que um trabalho de Stream Analytics funcionasse no Microsoft Azure e desloque recursos que estavam a ser utilizados. A definição de emprego e metadados permanecerão disponíveis dentro da sua subscrição através do portal Azure e das APIs de gestão, de modo a que o trabalho possa ser editado e reiniciado. Não será acusado de um emprego no estado de paragem.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Este comando PowerShell para o trabalho streamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput / Teste-AzStreamAnalyticsInput
Testa a capacidade do Stream Analytics de se ligar a uma entrada especificada.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Este comando PowerShell testa o estado de ligação da entrada EntryStream em StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput [ Test-AzStreamAnalyticsOutput
Testa a capacidade do Stream Analytics de se ligar a uma saída especificada.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Este comando PowerShell testa o estado de ligação da saída em StreamingJob.  

## <a name="get-support"></a>Obter suporte
Para mais assistência, experimente o nosso [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: https://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

