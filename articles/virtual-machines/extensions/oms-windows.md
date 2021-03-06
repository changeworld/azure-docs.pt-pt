---
title: Log Analytics virtual machine extension for Windows (Extensão de máquina virtual do Log Analytics para Windows)
description: Implemente o agente Log Analytics na máquina virtual do Windows utilizando uma extensão virtual da máquina.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/30/2020
ms.author: akjosh
ms.openlocfilehash: 85977819d30ddc8745eb9231242eb1990222676c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530993"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Log Analytics virtual machine extension for Windows (Extensão de máquina virtual do Log Analytics para Windows)

O Azure Monitor Logs fornece capacidades de monitorização em todos os ativos da nuvem e no local. A extensão virtual da máquina do agente Log Analytics para windows é publicada e suportada pela Microsoft. A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve máquinas virtuais num espaço de trabalho existente no Log Analytics. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão da máquina virtual Log Analytics para windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Para mais detalhes sobre os sistemas operativos Windows suportados, consulte o artigo de visão geral do [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Versão de extensão de agente e VM
A tabela seguinte fornece um mapeamento da versão da extensão VM do Windows Log Analytics e do pacote de agentes Log Analytics para cada lançamento. 

| Log Analytics Windows versão pacote de pacote | Versão de extensão do Windows VM de Log Analytics | Data de Lançamento | Notas de Versão |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18029 | 1.0.18029 | março de 2020   | <ul><li>Adiciona suporte de assinatura de código SHA-2</li><li>Melhora a instalação e gestão da extensão VM</li><li>Resolve um bug em Azure Arc para integração de Servidores</li><li>Adiciona uma ferramenta incorporada de resolução de problemas para apoio ao cliente</li><li>Acrescenta apoio a regiões adicionais do Governo azure</li> |
| 10.20.18018 | 1.0.18018 | Outubro de 2019 | <ul><li> Pequenas correções de insetos e melhorias de estabilização </li></ul> |
| 10.20.18011 | 1.0.18011 | Julho de 2019 | <ul><li> Pequenas correções de insetos e melhorias de estabilização </li><li> Aumento da MaxExpressionDepth para 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Junho de 2019 | <ul><li> Pequenas correções de insetos e melhorias de estabilização </li><li> Capacidade acrescida de desativar credenciais predefinidas ao elo de ligação proxy (suporte para WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Março de 2019 | <ul><li>Pequenas correções de estabilização </li></ul> |
| 10.19.10006 | n/d | Dez 2018 | <ul><li> Pequenas correções de estabilização </li></ul> | 
| 8.0.11136 | n/d | Setembro de 2018 |  <ul><li> Suporte adicional para detetar mudança de ID de recursos em movimento VM </li><li> Suporte adicionado para reportar ID de recursos ao utilizar instalação não-extensão </li></ul>| 
| 8.0.11103 | n/d |  Abril de 2018 | |
| 8.0.11081 | 1.0.11081 | Nov 2017 | | 
| 8.0.11072 | 1.0.11072 | Setembro de 2017 | |
| 8.0.11049 | 1.0.11049 | Fev 2017 | |


### <a name="azure-security-center"></a>Centro de Segurança do Azure

O Azure Security Center disponibiliza automaticamente o agente Log Analytics e liga-o ao espaço de trabalho padrão do Log Analytics da subscrição Azure. Se estiver a utilizar o Azure Security Center, não corra os passos deste documento. Ao fazê-lo substitui o espaço de trabalho configurado e rompe a ligação com o Azure Security Center.

### <a name="internet-connectivity"></a>Conectividade Internet
A extensão do agente Log Analytics para windows requer que a máquina virtual alvo esteja ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

O Seguinte JSON mostra o esquema para a extensão do agente Log Analytics. A extensão requer o ID do espaço de trabalho e a chave espaço de trabalho do espaço de trabalho do log analytics alvo. Estes podem ser encontrados nas definições para o espaço de trabalho no portal Azure. Uma vez que a chave do espaço de trabalho deve ser tratada como dados sensíveis, deve ser armazenada numa configuração de configuração protegida. Os dados de definição protegidos por extensão Azure VM são encriptados e apenas desencriptados na máquina virtual alvo. Note que o **espaço de trabalhoId** e **workspaceKey** são sensíveis a casos.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Valores patrimoniais

| Nome | Valor / Exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publicador | Microsoft.EnterpriseCloud.Monitoring |
| tipo | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (por exemplo)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\*O workspaceId é chamado de consumerId na API log analytics.

> [!NOTE]
> Para obter propriedades adicionais consulte Azure [Connect Windows Computers to Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

## <a name="template-deployment"></a>Implementação de modelos

As extensões VM azure podem ser implantadas com modelos de Gestor de Recursos Azure. O esquema JSON detalhado na secção anterior pode ser usado num modelo de Gestor de Recursos Azure para executar a extensão do agente Log Analytics durante uma implementação do modelo do Gestor de Recursos Azure. Um modelo de amostra que inclui a extensão VM do agente Log Analytics pode ser encontrado na [Galeria Quickstart Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>O modelo não suporta especificar mais do que uma chave de ID e espaço de trabalho quando pretende configurar o agente para reportar a vários espaços de trabalho. Para configurar o agente para reportar a vários espaços de trabalho, consulte [adicionar ou remover um espaço](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace)de trabalho .  

O JSON para uma extensão virtual da máquina pode ser aninhado dentro do recurso virtual da máquina, ou colocado no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação do JSON afeta o valor do nome e do tipo de recursos. Para mais informações, consulte o nome e o [tipo de definição para os recursos infantis.](../../azure-resource-manager/templates/child-resource-name-type.md) 

O exemplo que se segue pressupõe que a extensão Log Analytics está aninhada dentro do recurso virtual da máquina. Ao nidificar o recurso de extensão, `"resources": []` o JSON é colocado no objeto da máquina virtual.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Ao colocar a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual dos pais, e o tipo reflete a configuração aninhada. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Implementação powerShell

O `Set-AzVMExtension` comando pode ser utilizado para implantar a extensão virtual da máquina virtual do agente Log Analytics a uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam de ser armazenadas numa tabela de hash PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das implementações de extensões podem ser recuperados a partir do portal Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implantação das extensões para um determinado VM, execute o seguinte comando utilizando o módulo PowerShell Azure.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A saída de execução de extensão é registada em ficheiros encontrados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
