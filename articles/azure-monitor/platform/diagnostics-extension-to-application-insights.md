---
title: Envie dados de Diagnóstico Azure para Insights de Aplicação
description: Atualize a configuração pública do Azure Diagnostics para enviar dados para A aplicação Insights.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: 80d971abd248ca8253a374b488c693ea9aa2ea3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672332"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Envie dados de diagnóstico de serviço em nuvem, máquina virtual ou tecido de serviço para Insights de Aplicação
Serviços em nuvem, máquinas virtuais, conjuntos de escala de máquina virtual e tecido de serviço todos utilizam a extensão de Diagnóstico Azure para recolher dados.  O diagnóstico azure envia dados para as tabelas de Armazenamento Azure.  No entanto, também pode canalizar todos ou um subconjunto dos dados para outros locais utilizando a extensão 1.5 ou posterior do Azure Diagnostics.

Este artigo descreve como enviar dados da extensão de Diagnóstico sinuoso do Azure para os Insights de Aplicação.

## <a name="diagnostics-configuration-explained"></a>Configuração de diagnóstico explicada
A extensão de diagnóstico Azure 1.5 introduziu pias, que são locais adicionais onde pode enviar dados de diagnóstico.

Configuração de exemplo de um lavatório para Insights de Aplicação:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- O atributo do *nome* **Sink** é um valor de cadeia que identifica exclusivamente a pia.

- O elemento **ApplicationInsights** especifica a chave de instrumentação do recurso de insights da Aplicação para onde os dados de diagnóstico do Azure são enviados.
    - Se não tiver um recurso de Insights de Aplicação existente, consulte Criar um novo recurso De sininsights de [aplicação](../../azure-monitor/app/create-new-resource.md ) para obter mais informações sobre a criação de um recurso e obter a chave de instrumentação.
    - Se estiver a desenvolver um Serviço cloud com Azure SDK 2.8 e mais tarde, esta chave de instrumentação é automaticamente povoada. O valor **baseia-se** na definição de configuração do serviço APPINSIGHTS_INSTRUMENTATIONKEY ao embalar o projeto Cloud Service. Consulte [a utilização de insights de aplicação com serviços](../../azure-monitor/app/cloudservices.md)em nuvem .

- O elemento **Canais** contém um ou mais elementos **do Canal.**
    - O *atributo* de nome refere-se exclusivamente a esse canal.
    - O atributo *de loglevel* permite especificar o nível de registo que o canal permite. Os níveis de registo disponíveis por ordem da maioria a menos informação são:
        - Verboso
        - Informações
        - Aviso
        - Erro
        - Crítica

Um canal age como um filtro e permite selecionar níveis de registo específicos para enviar para o lavatório alvo. Por exemplo, pode recolher registos verbosos e enviá-los para armazenamento, mas enviar apenas Erros para a pia.

O gráfico seguinte mostra esta relação.

![Configuração pública de Diagnósticos](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

O gráfico seguinte resume os valores de configuração e como funcionam. Pode incluir vários lavatórios na configuração a diferentes níveis da hierarquia. A pia ao mais alto nível funciona como um cenário global e a especificada no elemento individual age como uma sobreposição a esse cenário global.

![Diagnóstico sumidouros configuração com insights de aplicação](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Exemplo completo de configuração do lavatório
Aqui está um exemplo completo do arquivo de configuração pública que
1. envia todos os erros para Insights de Aplicação (especificado no nó **de Configuração DiagnosticMonitor)**
2. também envia registos de nível Verbose para os Registos de Aplicação (especificados no nó de **Registos).**

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
Na configuração anterior, as seguintes linhas têm os seguintes significados:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Envie todos os dados que estão a ser recolhidos pelos diagnósticos do Azure

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Envie apenas registos de erro para o afundatório de Insights de Aplicação

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Envie registos de aplicação verbose para Insights de Aplicação

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Limitações

- **Os canais apenas tipo de log e não contadores de desempenho.** Se especificar um canal com um elemento contador de desempenho, é ignorado.
- **O nível de registo de um canal não pode exceder o nível de registo para o que está a ser recolhido pelos diagnósticos do Azure.** Por exemplo, não é possível recolher erros de Registo de Aplicação no elemento Logs e tentar enviar registos verbose para o afundatório Devisão de Aplicações. O atributo *agendadoDoTransferLogLevelFilter* deve sempre recolher registos iguais ou mais do que os registos que está a tentar enviar para uma pia.
- **Não é possível enviar dados blob recolhidos pela extensão de diagnóstico do Azure aos Insights de Aplicação.** Por exemplo, qualquer coisa especificada no nó dos *Diretórios.* Para dumps crash, o depósito de lixo de colisão real é enviado para armazenamento de bolhas e apenas uma notificação de que o dump de crash foi gerado é enviado para Application Insights.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como ver a informação de [diagnóstico do Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices) em Insights de Aplicação.
* Utilize o [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) para ativar a extensão de diagnóstico azure para a sua aplicação.
* Utilize o [Estúdio Visual](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) para ativar a extensão de diagnóstico do Azure para a sua aplicação

