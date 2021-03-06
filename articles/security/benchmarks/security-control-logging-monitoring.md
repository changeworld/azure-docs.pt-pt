---
title: Controlo de Segurança Azure - Exploração madeireira e monitorização
description: Registo e monitorização de controlo de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545505"
---
# <a name="security-control-logging-and-monitoring"></a>Controlo de Segurança: Exploração e Monitorização

O registo e monitorização de segurança centra-se em atividades relacionadas com a viabilizar, adquirir e armazenar registos de auditoria para os serviços Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

No entanto, a Microsoft mantém fontes de tempo para os recursos do Azure, no entanto, tem a opção de gerir as definições de sincronização de tempo para os seus recursos computacionais.

Como configurar a sincronização do tempo para os recursos computacionais do Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,2 | 6.5, 6.6 | Cliente |

Ingerir logs via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. Dentro do Monitor Azure, utilize log analytics workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Em alternativa, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros. Como embarcar no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Como recolher registos e métricas da plataforma com o Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Como recolher registos internos de hospedas da Máquina Virtual Azure com o Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Como começar com o Azure Monitor e a integração siem de terceiros:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.3 | 6.2, 6.3 | Cliente |

Ativar Definições de Diagnóstico nos recursos do Azure para acesso a registos de auditoria, segurança e diagnóstico. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, carimbo de tempo, endereços de origem, endereços de destino e outros elementos úteis.

Como recolher registos e métricas da plataforma com o Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Compreenda a exploração madeireira e diferentes tipos de registo em Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.4 | 6.2, 6.3 | Cliente |

Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pela sua monitorização. Se o recurso computacional é propriedade da sua organização, é da sua responsabilidade monitorá-lo. Pode utilizar o Centro de Segurança Azure para monitorizar o Sistema Operativo. Os dados recolhidos pelo Security Center do sistema operativo incluem o tipo e versão OS, registos OS (Registos de Eventos do Windows), processos de execução, nome da máquina, endereços IP e registos registados no utilizador. O Agente Delog Analytics também recolhe ficheiros de despejo de acidentes.

Como recolher registos internos de hospedas da Máquina Virtual Azure com o Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Compreender a recolha de dados do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.5 | 6.4 | Cliente |

Dentro do Monitor Azure, detete o seu período de retenção do Espaço de Trabalho de Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Como definir parâmetros de retenção de registos para espaços de trabalho de Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,6 | 6.7 | Cliente |

Analise e monitorize os registos para comportamentos anómalos e reveja regularmente os resultados. Utilize o log analytics workspace do Azure Monitor para rever os registos e executar consultas nos dados de registo.

Em alternativa, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros. 

Como embarcar no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Compreender o espaço de trabalho de Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como realizar consultas personalizadas no Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.7 | 6.8 | Cliente |

Utilize o Azure Security Center com log analytics Workspace para monitorização e alerta sobre a atividade anómala encontrada em registos e eventos de segurança.

Em alternativa, pode ativar e embarcar dados para o Azure Sentinel.

Como embarcar no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Como gerir alertas no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Como alertar sobre os dados de registo de análise de registo:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,8 | 8.6 | Cliente |

Ativar a recolha de eventos antimalware para Máquinas Virtuais Azure e Serviços cloud.

Como configurar o Microsoft Antimalware para Máquinas Virtuais:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Como configurar o Microsoft Antimalware para serviços em nuvem:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Compreenda o Antimalware da Microsoft:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.9 | 8.7 | Cliente |

Implementar uma solução de terceiros para a exploração madeireira DNS.

## <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.1 | 8.8 | Cliente |

Configure manualmente o registo de consolas e a transcrição powerShell numa base por nó.

## <a name="next-steps"></a>Passos seguintes

Consulte o próximo controlo de segurança: [Controlo de Identidade e Acesso](security-control-identity-access-control.md)
