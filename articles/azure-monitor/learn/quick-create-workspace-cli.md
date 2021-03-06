---
title: Criar um espaço de trabalho de Log Analytics utilizando o Azure CLI [ Microsoft Docs
description: Aprenda a criar um espaço de trabalho de Log Analytics para permitir soluções de gestão e recolha de dados a partir da sua nuvem e ambientes no local com o Azure CLI.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 89d397574c423e28bcbb0fec5ddd45959a737a93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659890"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Criar um espaço de trabalho de Log Analytics com o Azure CLI 2.0

A CLI 2.0 do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este quickstart mostra-lhe como utilizar o Azure CLI 2.0 para implantar um espaço de trabalho de Log Analytics no Monitor Azure. Um espaço de trabalho log Analytics é um ambiente único para dados de registo do Azure Monitor. Cada espaço de trabalho tem o seu próprio repositório e configuração de dados, e fontes de dados e soluções são configuradas para armazenar os seus dados num determinado espaço de trabalho. Necessita de um espaço de trabalho de Log Analytics se pretender recolher dados das seguintes fontes:

* Recursos Azure na sua subscrição  
* Computadores no local monitorizados pelo System Center Operations Manager  
* Coleções de dispositivos do Gestor de Configuração  
* Dados de diagnóstico ou de registo do armazenamento do Azure  

Para outras fontes, como VMs Azure e Windows ou VMs Linux no seu ambiente, consulte os seguintes tópicos:

* [Recolher dados de máquinas virtuais Azure](../learn/quick-collect-azurevm.md)
* [Recolher dados do computador híbrido Linux](../learn/quick-collect-linux-computer.md)
* [Recolher dados do computador híbrido windows](quick-collect-windows-computer.md)

Se não tiver uma subscrição Azure, crie [uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este guia de início rápido requer a execução da versão 2.0.30 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Criar uma área de trabalho
Criar um espaço de trabalho com a criação de uma implementação do [grupo Az.](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) O exemplo seguinte cria um espaço de trabalho na localização *oriental* usando um modelo de Gestor de Recursos da sua máquina local. O modelo JSON está configurado para apenas o solicitar o nome do espaço de trabalho, e especifica um valor predefinido para os outros parâmetros que provavelmente seriam usados como uma configuração padrão no seu ambiente. Ou pode armazenar o modelo numa conta de armazenamento Azure para acesso partilhado na sua organização. Para mais informações sobre o trabalho com modelos, consulte Implementar recursos com modelos de Gestor de [Recursos e ClI Azure](../../azure-resource-manager/templates/deploy-cli.md)

Para obter informações sobre regiões suportadas, consulte [regiões](https://azure.microsoft.com/regions/services/) log Analytics e procura o Monitor Azure a partir do campo **de pesquisa de um produto.**

Os seguintes parâmetros estabelecem um valor predefinido:

* localização - incumprimentos para os EUA Orientais
* sku - incumprimentos ao novo nível de preços Per-GB lançado no modelo de preços de abril de 2018

>[!WARNING]
>Se criar ou configurar um espaço de trabalho log Analytics numa subscrição que tenha optado pelo novo modelo de preços de abril de 2018, o único nível de preços válido do Log Analytics é **o PerGB2018.**
>

### <a name="create-and-deploy-template"></a>Criar e implementar modelo

1. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Editar o modelo para satisfazer os seus requisitos. Reveja a referência do [modelo Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) para saber quais as propriedades e valores suportados.
3. Guarde este ficheiro como **modelo espaço de implantação.json** para uma pasta local.   
4. Está pronto para implementar este modelo. Utilize os seguintes comandos da pasta que contém o modelo. Quando for solicitado um nome de espaço de trabalho, forneça um nome globalmente único em todas as subscrições do Azure.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

A implementação pode demorar alguns minutos a concluir. Quando termina, vê-se uma mensagem semelhante à seguinte que inclui o resultado:

![Resultado do exemplo quando a implementação está completa](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Passos seguintes
Agora que tem um espaço de trabalho disponível, pode configurar a recolha de telemetria de monitorização, executar pesquisas de registo para analisar esses dados e adicionar uma solução de gestão para fornecer dados adicionais e insights analíticos.  

* Para permitir a recolha de dados a partir de recursos Azure com armazenamento Azure Diagnostics ou Azure, consulte [Recolher registos e métricas de serviço azure para utilização em Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Adicione o Gestor de [Operações do System Center como fonte de dados](../platform/om-agents.md) para recolher dados de agentes que reportam o seu grupo de gestão de Gestor de Operações e armazená-lo no seu espaço de trabalho Log Analytics.  
* Ligue o Gestor de [Configuração](../platform/collect-sccm.md) para importar computadores que sejam membros de coleções na hierarquia.  
* Reveja as [soluções de monitorização](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do seu espaço de trabalho.
