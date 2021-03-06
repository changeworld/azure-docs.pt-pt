---
title: Monitor ize um cluster azure Kubernetes Service (AKS) implantado [ Microsoft Docs
description: Saiba como permitir a monitorização de um cluster do Serviço Azure Kubernetes (AKS) com o Monitor Azure para contentores já implantados na sua subscrição.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275454"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Permitir a monitorização do cluster Azure Kubernetes Service (AKS) já implantado

Este artigo descreve como configurar o Azure Monitor para contentores para monitorizar o cluster kubernetes gerido hospedado no [Serviço Azure Kubernetes](https://docs.microsoft.com/azure/aks/) que já foram implantados na sua subscrição.

Pode permitir a monitorização de um cluster AKS que já foi implantado utilizando um dos métodos suportados:

* CLI do Azure
* Terraform
* [Do Monitor Azure](#enable-from-azure-monitor-in-the-portal) ou [diretamente do cluster AKS](#enable-directly-from-aks-cluster-in-the-portal) no portal Azure
* Com o modelo de Gestor de [Recursos Azure](#enable-using-an-azure-resource-manager-template) fornecido `New-AzResourceGroupDeployment` utilizando o cmdlet Azure PowerShell ou com o Azure CLI.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Ativar a utilização do Azure CLI

O passo seguinte permite a monitorização do seu cluster AKS utilizando o Azure CLI. Neste exemplo, não é obrigado a criar ou especificar um espaço de trabalho existente. Este comando simplifica o processo para si criando um espaço de trabalho padrão no grupo de recursos padrão da subscrição do cluster AKS se não existir já na região.  O espaço de trabalho predefinido criado assemelha-se ao formato de *DefaultWorkspace-\<GUID\<>- Região>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

A saída assemelhar-se-á ao seguinte:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integrar com um espaço de trabalho existente

Se preferir integrar-se com um espaço de trabalho existente, execute os seguintes passos para `--workspace-resource-id` identificar primeiro o id completo do seu espaço de trabalho log Analytics necessário para o parâmetro e, em seguida, executar o comando para ativar o complemento de monitorização contra o espaço de trabalho especificado.  

1. Enumerar todas as subscrições a que tem acesso utilizando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída assemelhar-se-á ao seguinte:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Copiar o valor para **SubscriptionId**.

2. Mude para a subscrição que acolhe o espaço de trabalho log analytics utilizando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. O exemplo seguinte apresenta a lista de espaços de trabalho nas suas subscrições no formato Padrão JSON.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, encontre o nome do espaço de trabalho e, em seguida, copie o id completo do espaço de trabalho log Analytics sob o **id**de campo .

4. Executar o seguinte comando para ativar o complemento de `--workspace-resource-id` monitorização, substituindo o valor do parâmetro. O valor da cadeia deve estar dentro das cotações duplas:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    A saída assemelhar-se-á ao seguinte:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Ativar a utilização da Terraforma

1. Adicione o perfil **de adição oms_agent** ao recurso [azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile) existente

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Adicione o [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) seguindo os passos na documentação terraforme.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Ativar a partir do Monitor Azure no portal

Para permitir a monitorização do seu cluster AKS no portal Azure do Monitor Azure, faça o seguinte:

1. No portal Azure, selecione **Monitor**.

2. Selecione **Recipientes** da lista.

3. No Monitor - página de **recipientes,** selecione **clusters não monitorizados**.

4. A partir da lista de clusters não monitorizados, encontre o recipiente na lista e clique em **Ativar**.   

5. No **Onboarding para O Monitor Azure para a** página de contentores, se tiver um espaço de trabalho log analytics existente na mesma subscrição que o cluster, selecione-o a partir da lista de drop-down.  
    A lista pré-seleciona o espaço de trabalho padrão e a localização para a a que o contentor AKS é implantado na subscrição.

    ![Ativar a monitorização de insights do contentor AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se pretender criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitorização do cluster, siga as instruções em [Create a Log Analytics workspace](../../azure-monitor/learn/quick-create-workspace.md). Certifique-se de criar o espaço de trabalho na mesma subscrição para a qual o recipiente AKS está implantado.

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até ver as métricas de saúde para o cluster.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Ativar diretamente do cluster AKS no portal

Para permitir a monitorização diretamente de um dos seus clusters AKS no portal Azure, faça o seguinte:

1. No portal Azure, selecione **Todos os serviços.**

2. Na lista de recursos, comece a digitar **recipientes.**  A lista filtra com base na sua entrada.

3. Selecione **os serviços Kubernetes**.  

    ![A ligação de serviços da Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. Na lista de recipientes, selecione um recipiente.

5. Na página geral do recipiente, selecione **Monitor Containers**.  

6. No **Onboarding para O Monitor Azure para a** página de contentores, se tiver um espaço de trabalho log analytics existente na mesma subscrição que o cluster, selecione-o na lista de drop-down.  
    A lista pré-seleciona o espaço de trabalho padrão e a localização para a a que o contentor AKS é implantado na subscrição.

    ![Ativar a monitorização da saúde dos contentores AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Se pretender criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitorização do cluster, siga as instruções em [Create a Log Analytics workspace](../../azure-monitor/learn/quick-create-workspace.md). Certifique-se de criar o espaço de trabalho na mesma subscrição para a qual o recipiente AKS está implantado.

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder visualizar os dados operacionais do cluster.

## <a name="enable-using-an-azure-resource-manager-template"></a>Ativar usando um modelo de Gestor de Recursos Azure

Este método inclui dois modelos JSON. Um modelo especifica a configuração para ativar a monitorização, e o outro contém valores de parâmetros que configura para especificar o seguinte:

* O ID de recursos de contentores AKS.
* O grupo de recursos em que o cluster está implantado.

>[!NOTE]
>O modelo precisa de ser implantado no mesmo grupo de recursos que o cluster.
>

O espaço de trabalho Log Analytics tem de ser criado antes de ativar a monitorização utilizando o Azure PowerShell ou o CLI. Para criar o espaço de trabalho, pode instalá-lo através do [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), através do [PowerShell,](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure](../../azure-monitor/learn/quick-create-workspace.md).

Se não está familiarizado com o conceito de implantação de recursos utilizando um modelo, consulte:

* [Implementar recursos com modelos do Resource Manager e do Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Se optar por utilizar o Azure CLI, primeiro necessita de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.59 ou mais tarde. Para identificar a `az --version`sua versão, corra. Se precisar de instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Criar e executar um modelo

1. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Guarde este ficheiro como **ClusterOnboarding.json existente** para uma pasta local.

3. Colhe a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Editar os **valores para aksResourceId** e **aksResourceLocation** utilizando os valores na página **de visão geral aks** para o cluster AKS. O valor para o espaço de **trabalhoResourceId** é o id completo de recursos do seu espaço de trabalho Log Analytics, que inclui o nome do espaço de trabalho.

    Editar os **valores para aksResourceTagValues** para corresponder aos valores de etiqueta existentes especificados para o cluster AKS.

5. Guarde este ficheiro como **ClusterParam.json existente** para uma pasta local.

6. Está pronto para implementar este modelo.

   * Para implantar com o Azure PowerShell, utilize os seguintes comandos na pasta que contém o modelo:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       A mudança de configuração pode demorar alguns minutos a ser completada. Quando está concluída, é apresentada uma mensagem semelhante à seguinte e inclui o resultado:

       ```output
       provisioningState       : Succeeded
       ```

   * Para implantar com o Azure CLI, execute os seguintes comandos:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       A mudança de configuração pode demorar alguns minutos a ser completada. Quando está concluída, é apresentada uma mensagem semelhante à seguinte e inclui o resultado:

       ```output
       provisioningState       : Succeeded
       ```

       Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até ver as métricas de saúde para o cluster.

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implementação do agente e da solução

Com a versão do agente *06072018* ou posterior, pode verificar se tanto o agente como a solução foram implementados com sucesso. Com versões anteriores do agente, só pode verificar a colocação de agentes.

### <a name="agent-version-06072018-or-later"></a>Versão do agente 06072018 ou mais tarde

Executar o seguinte comando para verificar se o agente está implantado com sucesso.

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve assemelhar-se à seguinte, o que indica que foi corretamente implantada:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para verificar a implementação da solução, execute o seguinte comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A saída deve assemelhar-se à seguinte, o que indica que foi corretamente implantada:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versão do agente mais cedo do que 06072018

Para verificar se a versão do agente Log Analytics lançada antes de *06072018* está corretamente implantada, execute o seguinte comando:  

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve assemelhar-se à seguinte, o que indica que foi corretamente implantada:  

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Ver configuração com CLI

Utilize `aks show` o comando para obter detalhes como a solução ativada ou não, qual é o Serviço de Recursos do espaço de trabalho Log Analytics e detalhes sumários sobre o cluster.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Após alguns minutos, o comando completa e devolve informações formatadas da JSON sobre a solução.  Os resultados do comando devem mostrar o perfil de adição de monitorização e assemelhar-se à seguinte saída de exemplo:

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Passos seguintes

* Se tiver problemas ao tentar embarcar na solução, reveja o guia de resolução de [problemas](container-insights-troubleshoot.md)

* Com a monitorização habilitada a recolher a utilização da saúde e dos recursos do seu cluster AKS e as cargas de trabalho que os utilizam, aprenda [a utilizar o](container-insights-analyze.md) Monitor Azure para contentores.
