---
title: Extensão VM de Diagnóstico de Desempenho Azure para Windows/ Microsoft Docs
description: Introduz extensão VM de Diagnóstico de Desempenho Azure para Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6f104fc6513874bfef5f4bf9fe7f536c3e3d69cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057550"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Extensão de VM de Diagnóstico de Desempenho do Azure para Windows

A extensão VM de Diagnóstico de Desempenho Azure ajuda a recolher dados de diagnóstico de desempenho a partir de VMs do Windows. A extensão realiza análises e fornece um relatório de conclusões e recomendações para identificar e resolver problemas de desempenho na máquina virtual. Esta extensão instala uma ferramenta de resolução de problemas chamada [PerfInsights](https://aka.ms/perfinsights).

> [!NOTE]
> Se quiser eexecutar diagnósticos no seu VM a partir do portal Azure para VMs não clássicos, é aconselhável utilizar a nova experiência. Para mais informações, consulte [Performance Diagnostics para máquinas virtuais Azure](performance-diagnostics.md) 

## <a name="prerequisites"></a>Pré-requisitos

Esta extensão pode ser instalada no Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Também pode ser instalado no Windows 8.1 e Windows 10.

## <a name="extension-schema"></a>Esquema de extensão
O seguinte JSON mostra o esquema para a extensão VM de Diagnóstico de Desempenho Azure. Esta extensão requer o nome e a chave para uma conta de armazenamento para armazenar a saída e relatório de diagnóstico. Estes valores são sensíveis. A chave da conta de armazenamento deve ser armazenada dentro de uma configuração de definição protegida. Os dados de definição protegidos por extensão Azure VM são encriptados e só são desencriptados na máquina virtual alvo. Note que **o nome** da conta de armazenamento e **armazenamentoAccountKey** são sensíveis a casos. Outros parâmetros necessários estão listados na seguinte secção.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Valores patrimoniais

|   **Nome**   |**Valor / Exemplo**|       **Descrição**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|A versão da API.
|publicador|Microsoft.Azure.Performance.Diagnostics|O espaço de nome da editora para a extensão.
|tipo|AzurePerformanceDiagnostics|O tipo de extensão VM.
|typeHandlerVersion|1.0|A versão do manipulador de extensão.
|desempenhoCenário|básico|O cenário de desempenho para capturar dados. Os valores válidos são: **básico,** **vmslow,** **azurefiles,** e **personalizado**.
|traceDurationInSeconds|300|A duração dos vestígios, se forem selecionadas alguma das opções de vestígios.
|perfCounterTrace|p|Opção para ativar o Rastreio do Contador de Desempenho. Os valores válidos são **p** ou valor vazio. Se não quiser capturar este vestígio, deixe o valor vazio.
|redeTrace|n|Opção para ativar o Rastreio da Rede. Os valores válidos são **n** ou valor vazio. Se não quiser capturar este vestígio, deixe o valor vazio.
|xperfTrace|x|Opção para ativar o XPerf Trace. Os valores válidos são **x** ou valor vazio. Se não quiser capturar este vestígio, deixe o valor vazio.
|storPortTrace|t|Opção para ativar o StorPort Trace. Valores válidos são **s** ou valor vazio. Se não quiser capturar este vestígio, deixe o valor vazio.
|srNumber|123452016365929|O número do bilhete de apoio, se disponível. Deixe o valor vazio se não o tiver.
|solicitarTimeUtc|2017-09-28T22:08:53.736Z|Hora da data atual em Utc. Se estiver a utilizar o portal para instalar esta extensão, não precisa de fornecer este valor.
|resourceId|/subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|O identificador único de um VM.
|storageAccountName|mystorageaccount|O nome da conta de armazenamento para armazenar os registos e resultados de diagnóstico.
|armazenamentoAccountKey|LDuVvxuZB28NNP... hAiRF3voADxLBTcc==|A chave para a conta de armazenamento.

## <a name="install-the-extension"></a>Instalar a extensão

Siga estas instruções para instalar a extensão nas máquinas virtuais do Windows:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione a máquina virtual onde pretende instalar esta extensão.

    ![Screenshot do portal Azure, com máquinas virtuais em destaque](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Selecione a lâmina de **extensões** e selecione **Adicionar**.

    ![Screenshot da lâmina de extensões, com Adicionar em destaque](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Selecione Diagnósticos de **Desempenho Azure,** reveja os termos e condições e selecione **Criar**.

    ![Screenshot do novo ecrã de recursos, com diagnósticos de desempenho azure em destaque](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Forneça os valores do parâmetro para a instalação e selecione **OK** para instalar a extensão. Para obter mais informações sobre cenários suportados, consulte [Como utilizar o PerfInsights](how-to-use-perfinsights.md#supported-troubleshooting-scenarios). 

    ![Screenshot da caixa de diálogo de extensão de instalação](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Quando a instalação for bem sucedida, vê uma mensagem indicando este estado.

    ![Screenshot de Mensagem bem sucedida de Provisioning](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > A prorrogação é termina quando o fornecimento tiver sido bem sucedido. Leva dois minutos ou menos para completar para o cenário básico. Para outros cenários, estende-se pela duração especificada durante a instalação.

## <a name="remove-the-extension"></a>Remover a extensão
Para remover a extensão de uma máquina virtual, siga estes passos:

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione a máquina virtual a partir da qual pretende remover esta extensão e, em seguida, selecione a lâmina de **extensões.** 
2. Selecione a (**...**) para a entrada de extensão de diagnóstico de desempenho da lista e selecione **Desinstalar**.

    ![Screenshot da lâmina de extensões, com desinstalar em destaque](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Também pode selecionar a entrada de extensão e selecionar a opção **Desinstalar.**

## <a name="template-deployment"></a>Implementação de modelos
As extensões de máquinas virtuais Azure podem ser implantadas com modelos de Gestor de Recursos Azure. O esquema JSON detalhado na secção anterior pode ser usado num modelo de Gestor de Recursos Azure. Isto executa a extensão VM de Diagnóstico de Desempenho Azure durante uma implementação do modelo do Gestor de Recursos Azure. Aqui está um modelo de amostra:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>Implementação powerShell
O `Set-AzVMExtension` comando pode ser utilizado para implantar a extensão VM de Diagnóstico de Desempenho Azure para uma máquina virtual existente.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Informação sobre os dados capturados
A ferramenta PerfInsights recolhe vários registos, configuração e dados de diagnóstico, dependendo do cenário selecionado. Para mais informações, consulte a [documentação perfInsights](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Ver e partilhar os resultados

A saída da extensão pode ser encontrada num ficheiro zip que foi enviado para a conta de armazenamento especificada durante a instalação e é partilhada durante 30 dias utilizando assinaturas de [acesso partilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Este ficheiro zip contém registos de diagnóstico e um relatório com resultados e recomendações. Uma ligação SAS ao ficheiro zip de saída pode ser encontrada dentro de um ficheiro de texto chamado *zipfilename*_saslink.txt sob a pasta **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\\\<versão>**. Qualquer pessoa que tenha este link é capaz de descarregar o ficheiro zip.

Para ajudar o engenheiro de suporte a trabalhar no seu bilhete de suporte, a Microsoft poderá utilizar este link SAS para descarregar os dados de diagnóstico.

Para visualizar o relatório, extrai o ficheiro zip e abra o ficheiro **PerfInsights Report.html.**

Também deverá ser capaz de descarregar o ficheiro zip diretamente do portal selecionando a extensão.

![Screenshot do estado detalhado de Diagnósticode Desempenho](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> A ligação SAS exibida no portal pode não funcionar às vezes. Isto pode ser causado por um URL mal formado durante as operações de codificação e descodificação. Em vez disso, pode obter o link diretamente a partir do ficheiro *_saslink.txt a partir do VM.

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

- O estado de implementação da extensão (na área de notificação) pode mostrar "Implantação em curso", mesmo que a extensão seja aprovisionada com sucesso.

    Esta questão pode ser ignorada com segurança, desde que o estado de extensão indique que a extensão é aprovisionada com êxito.
- Pode resolver alguns problemas durante a instalação utilizando os registos de extensão. A saída de execução de extensão é registada em ficheiros encontrados no seguinte diretório:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/), e selecione **Obter suporte**. Para obter informações sobre a utilização do suporte do Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
