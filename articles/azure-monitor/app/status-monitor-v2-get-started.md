---
title: Agente de Insights de Aplicação Azure - a começar Microsoft Docs
description: Um guia de início rápido para agente de insights de aplicação. Monitorize o desempenho do site sem reimplantar o website. Trabalha com ASP.NET aplicações web hospedadas no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7819de1f3dfab7f934421de86c0481d2e063f7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671193"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Inicie-se com o Agente de Insights de Aplicação do Monitor Azure para servidores no local

Este artigo contém os comandos quickstart esperados para a maioria dos ambientes.
As instruções dependem da Galeria PowerShell para distribuir atualizações.
Estes comandos suportam `-Proxy` o parâmetro PowerShell.

Para obter uma explicação destes comandos, instruções de personalização e informações sobre resolução de problemas, consulte as [instruções detalhadas](status-monitor-v2-detailed-instructions.md).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="download-and-install-via-powershell-gallery"></a>Descarregue e instale através da Galeria PowerShell

### <a name="install-prerequisites"></a>Pré-requisitos da instalação
Executar powerShell como Administrador.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Close PowerShell.

### <a name="install-application-insights-agent"></a>Instalar agente de insights de aplicação
Executar powerShell como Administrador.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Ativar monitorização
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Descarregue e instale manualmente (opção offline)
### <a name="download-the-module"></a>Descarregue o módulo
Descarregue manualmente a versão mais recente do módulo a partir da [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Desapertar e instalar agente de insights de aplicação
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Ativar monitorização
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Passos seguintes

 Ver a telemetria:

- [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e o uso.
- [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Use o Analytics](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).

 Adicionar mais telemetria:

- [Criar testes Web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicione telemetria](../../azure-monitor/app/javascript.md) de cliente web para ver exceções do código da página web e para ativar chamadas de rastreio.
- [Adicione o SDK de Insights de Aplicação ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir chamadas de rastreio e log.

Faça mais com o Agente de Insights de Aplicação:

- Reveja as [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para uma explicação dos comandos aqui encontrados.
- Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente insights de aplicação.
