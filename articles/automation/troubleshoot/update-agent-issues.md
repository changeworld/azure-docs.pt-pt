---
title: Diagnosticar Windows Hybrid Runbook Worker - Azure Update Management
description: Saiba como resolver problemas com o Trabalhador do Livro Híbrido de Automação Azure no Windows que suporta a Gestão de Atualizações.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: ec35d11eba59ea21947e2c3cd5286bababa4eabb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153859"
---
# <a name="understand-and-resolve-windows-hybrid-runbook-worker-health-in-update-management"></a>Compreender e resolver a saúde do trabalhador do livro de corridas híbrido do Windows na Gestão de Atualizações

Pode haver muitas razões para a sua máquina não mostrar **Ready** in Update Management. Na Gestão de Atualizações, pode verificar a saúde de um agente híbrido do Runbook Worker para determinar o problema subjacente. Este artigo discute como executar o resolução de problemas para máquinas Azure a partir do portal Azure e máquinas não-Azure no [cenário offline](#troubleshoot-offline).

A lista que se segue são os três estados de prontidão em que uma máquina pode estar:

* **Ready** - O Trabalhador híbrido do livro está implantado e foi visto pela última vez há menos de uma hora.
* **Desligado** - O Trabalhador híbrido do livro de corridas está implantado e foi visto pela última vez há mais de uma hora.
* **Não configurado** - O Trabalhador híbrido do runbook não é encontrado ou ainda não terminou o embarque.

> [!NOTE]
> Pode haver um ligeiro atraso entre o que o portal Azure mostra e o estado atual da máquina.

## <a name="start-the-troubleshooter"></a>Inicie o resolução de problemas

Para máquinas Azure, clique no link **Troubleshoot** sob a coluna **de prontidão** do agente de atualização no portal lança a página do Agente de Atualização de **Resolução** de Problemas. Para máquinas não-Azure, o link leva-o a este artigo. Consulte as [instruções offline](#troubleshoot-offline) para filmar uma máquina não Azure.

![Lista de gestão de atualizações de máquinas virtuais](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Para verificar a saúde do Trabalhador do Livro De Execução Híbrido, o VM deve estar em execução. Se o VM não estiver a funcionar, aparece um botão **VM.**

Na página do **Agente de Atualização de Problemas,** selecione **verificações de executar** para iniciar o resolução de problemas. O resoluto de problemas usa [o Comando de Execução](../../virtual-machines/windows/run-command.md) para executar um guião na máquina para verificar dependências. Quando o resoluto de problemas estiver terminado, devolve o resultado dos cheques.

![Página do Agente de Atualização de Resolução de Problemas](../media/update-agent-issues/troubleshoot-page.png)

Os resultados são mostrados na página quando estão prontos. As secções de verificação mostram o que está incluído em cada cheque.

![Verificações do Agente de Atualização de Problemas](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A verificação do sistema operativo verifica se o Trabalhador do Resta Híbrido está a executar um dos seguintes sistemas operativos:

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2012 e mais tarde |.NET Quadro 4.6 ou posterior é necessário. ([Descarregue o .NET Framework)](/dotnet/framework/install/guide-for-developers)<br/> É necessário o Windows PowerShell 5.1.  (Baixar a Estrutura de Gestão de[Janelas 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.net 4.6.2

A verificação de quadro .NET verifica que o sistema tem um mínimo de [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) instalado.

### <a name="wmf-51"></a>WMF 5.1

A verificação wmf verifica que o sistema tem a versão necessária da Estrutura de Gestão do Windows (WMF) - [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Esta verificação determina se está a utilizar o TLS 1.2 para encriptar as suas comunicações. O TLS 1.0 já não é suportado pela plataforma. Recomendamos que os clientes utilizem TLS 1.2 para comunicar com a Update Management.

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="registration-endpoint"></a>Ponto final de inscrição

Esta verificação determina se o agente pode comunicar corretamente com o serviço do agente.

As configurações de proxy e firewall devem permitir que o agente Hybrid Runbook Worker comunique com o ponto final de registo. Para abrir uma lista de endereços e portos, consulte o [planeamento da rede para trabalhadores híbridos.](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Ponto final de operações

Esta verificação determina se o agente pode comunicar corretamente com o Serviço de Dados do Funcionano de Trabalho.

As configurações de proxy e firewall devem permitir que o agente Hybrid Runbook Worker se comunique com o Serviço de Dados do Tempo de Execução de Emprego. Para abrir uma lista de endereços e portos, consulte o [planeamento da rede para trabalhadores híbridos.](../automation-hybrid-runbook-worker.md#network-planning)

## <a name="vm-service-health-checks"></a>Verificações de saúde de serviço VM

### <a name="monitoring-agent-service-status"></a>Estado do serviço do agente de monitorização

Esta verificação determina se `HealthService`o Agente de Monitorização da Microsoft está a funcionar na máquina.

Para saber mais sobre a resolução de problemas do serviço, consulte [o Agente de Monitorização](hybrid-runbook-worker.md#mma-not-running)da Microsoft não está a funcionar .

Para reinstalar o Agente de Monitorização da Microsoft, consulte [instalar e configurar o Agente de Monitorização da Microsoft](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Eventos de serviço de agente de monitorização

Esta verificação determina `4502` se algum evento aparece no registo do Gestor de Operações Azure na máquina nas últimas 24 horas.

Para saber mais sobre este evento, consulte o guia de resolução de [problemas](hybrid-runbook-worker.md#event-4502) para este evento.

## <a name="access-permissions-checks"></a>Verificações de permissões de acesso

### <a name="machinekeys-folder-access"></a>Acesso à pasta MachineKeys

A verificação de acesso da pasta Crypto determina se a Conta do Sistema Local tem acesso a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Resolução de problemas offline

Você pode usar o resoludor de problemas em um Hybrid Runbook Worker offline executando o script localmente. Pode obter o script, [Troubleshoot-WindowsUpdateAgentRegistration,](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)na Galeria PowerShell. Deve ter WMF 4.0, ou maior, instalado para executar o script. Para descarregar a versão mais recente do PowerShell, consulte [a instalação de várias versões do PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

A saída deste guião parece ser o seguinte exemplo:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Passos seguintes

Para resolver mais problemas com os seus Trabalhadores híbridos, consulte [troubleshoot Hybrid Runbook Workers](hybrid-runbook-worker.md).
