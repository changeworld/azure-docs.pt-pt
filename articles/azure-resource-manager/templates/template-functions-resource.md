---
title: Funções do modelo - recursos
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure para recuperar valores sobre recursos.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 90cee78c29c26c88d808cdef798e74a2184a5fcf
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804763"
---
# <a name="resource-functions-for-arm-templates"></a>Funções de recursos para modelos ARM

O Gestor de Recursos fornece as seguintes funções para obter valores de recursos no seu modelo De gestor de recursos Azure (ARM):

* [extensãoResourceId](#extensionresourceid)
* [lista*](#list)
* [fornecedores](#providers)
* [referência](#reference)
* [resourceGroup](#resourcegroup)
* [recursosId](#resourceid)
* [assinatura](#subscription)
* [subscriçãoResourceId](#subscriptionresourceid)
* [inquilinoResourceId](#tenantresourceid)

Para obter valores a partir de parâmetros, variáveis ou a implementação atual, consulte funções de valor de [implantação](template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensãoResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Devolve o ID de recurso para um recurso de [extensão,](../management/extension-resource-types.md)que é um tipo de recurso que é aplicado a outro recurso para adicionar às suas capacidades.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| resourceId |Sim |string |O ID de recurso para o recurso a que o recurso de extensão é aplicado. |
| resourceType |Sim |string |Tipo de recurso, incluindo espaço de nome do fornecedor de recursos. |
| recursoName1 |Sim |string |Nome de recurso. |
| recursoName2 |Não |string |Próximo segmento de nome de recursos, se necessário. |

Continue a adicionar nomes de recursos como parâmetros quando o tipo de recursos inclui mais segmentos.

### <a name="return-value"></a>Valor devolvido

O formato básico do ID de recurso devolvido por esta função é:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

O segmento de âmbito varia pelo recurso que está a ser alargado.

Quando o recurso de extensão é aplicado a um **recurso,** o ID do recurso é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a um grupo de **recursos,** o formato é:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a uma **subscrição,** o formato é:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a um grupo de **gestão,** o formato é:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>exemplo de extensãoResourceId

O exemplo seguinte devolve o ID de recurso para um bloqueio de grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>lista*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

A sintaxe para esta função varia em função do nome das operações da lista. Cada implementação devolve valores para o tipo de recurso que suporta uma operação de lista. O nome de `list`funcionamento deve começar por . Alguns usos `listKeys` `listSecrets`comuns são e .

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| recursoNome ou recursoIdentificador |Sim |string |Identificador único para o recurso. |
| apiVersion |Sim |string |Versão API do estado de execução de recursos. Tipicamente, no formato, **yyy-mm-dd**. |
| funçãoValores |Não |objeto | Um objeto que tem valores para a função. Apenas forneça este objeto para funções que suportem a receção de um objeto com valores de parâmetros, como **listaSDeSas** numa conta de armazenamento. Um exemplo de valores de função de passagem é mostrado neste artigo. |

### <a name="valid-uses"></a>Utilizações válidas

As funções da lista só podem ser utilizadas nas propriedades de uma definição de recurso e na secção de saídas de um modelo ou de implantação. Quando usado com [iteração de propriedade,](copy-properties.md)você pode usar as funções da lista para `input` porque a expressão é atribuída à propriedade do recurso. Não pode usá-los `count` porque a contagem deve ser determinada antes que a função da lista seja resolvida.

### <a name="implementations"></a>Implementações

As possíveis utilizações da lista* são mostradas na tabela seguinte.

| Tipo de recurso | Nome da função |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servidores | [listaGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configuraçãoStores | ListKeys |
| Microsoft.Automation/automationAccounts | [listaKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [ficheiros de saída de listas](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [listaKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listaKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registos | [listaBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registos | [listCredenciais](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registos | [listaUtilizações](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listaEventos](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registros/executagens | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registos/tarefas | [listaDetalhes](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listaClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listaClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listaCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredenciais |
| Microsoft.DataFactory/datafactors/gateways | listaulistas |
| Microsoft.DataFactory/fábricas/integraçõesruntime | [listaulistas](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/contas/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/contas/ações | [listSincronizações](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/contas/subscrições de ações | [listaSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/contas/subscrições de ações | [listaSincronizaçãoDetalhes](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/contas/subscrições de ações | [listSincronizações](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Dispositivos/provisionamentoServiços/teclas | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Dispositivos/serviços de provisionamento | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/laboratórios | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/laboratórios/horários | [ListaAplicável](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListaAgendas Aplicáveis](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/laboratórios/máquinas virtuais | [ListaAgendas Aplicáveis](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listaDeCadeias de Ligação](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listaKeys](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listaRecomendações domínio](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listaAcordos](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domínios | [listaKeys](/rest/api/eventgrid/version2019-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/tópicos | [listaKeys](/rest/api/eventgrid/version2019-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/regras de autorização | [listkeys](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listaBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Bases de Dados | [Diretores de Listas](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/utilizadores | [ListAmbientes](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/utilizadores | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listaContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assembles | [listaContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listaCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listaKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listaContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listaContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listaContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listaCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listaExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetição | [listaExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listaCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versões/triggers | [listaCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Espaços de Trabalho | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listaKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/espaços de trabalho | [listaKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/contas | [listaKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listaContentorsas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listaStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listaContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfiguras |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/espaços de trabalho | [listaKeys](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/remediações | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/regras de autorização | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/filas/autorizaçõesRegras | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/tópicos/autorizaçõesRegras | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/Signalr | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listaServiçoSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listaFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listaFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listaActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listaPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/funções | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/funções | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relés | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/funções | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/funções | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Para determinar quais os tipos de recursos que têm uma operação de lista, tem as seguintes opções:

* Consulte as operações da [API REST](/rest/api/) para um fornecedor de recursos e procure operações de lista. Por exemplo, as contas de armazenamento têm a [operação listKeys](/rest/api/storagerp/storageaccounts).
* Utilize o cmdlet [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell. O exemplo seguinte obtém todas as operações da lista para contas de armazenamento:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Utilize o seguinte comando Azure CLI para filtrar apenas as operações da lista:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Valor devolvido

O objeto devolvido varia pela função da lista que utiliza. Por exemplo, a listaKeys para uma conta de armazenamento devolve o seguinte formato:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Outras funções da lista têm diferentes formatos de retorno. Para ver o formato de uma função, inclua-a na secção de saídas, como mostra o modelo de exemplo.

### <a name="remarks"></a>Observações

Especifique o recurso utilizando o nome do recurso ou a [função resourceId](#resourceid). Quando utilizar uma função de lista no mesmo modelo que implementa o recurso referenciado, utilize o nome do recurso.

Se utilizar uma função **de lista** num recurso que é implantado condicionalmente, a função é avaliada mesmo que o recurso não seja implantado. Obtém-se um erro se a função **da lista** se referir a um recurso que não existe. Utilize a função **se** para se certificar de que a função só é avaliada quando o recurso estiver a ser implantado. Consulte a [função se](template-functions-logical.md#if) para um modelo de amostra que utiliza se e lista com um recurso implantado condicionalmente.

### <a name="list-example"></a>Exemplo de lista

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) de exemplo seguinte mostra como devolver as chaves primárias e secundárias de uma conta de armazenamento na secção de saídas. Também devolve um token SAS para a conta de armazenamento.

Para obter o símbolo SAS, passe um objeto pelo tempo de validade. O prazo de validade deve ser no futuro. Este exemplo destina-se a mostrar como utiliza as funções da lista. Normalmente, utilizaria o token SAS num valor de recurso em vez de devolvê-lo como um valor de saída. Os valores de saída são armazenados no histórico de implantação e não são seguros.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>fornecedores

```json
providers(providerNamespace, [resourceType])
```

Devolve informações sobre um fornecedor de recursos e os seus tipos de recursos suportados. Se não fornecer um tipo de recurso, a função devolve todos os tipos suportados para o fornecedor de recursos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| fornecedorNamespace |Sim |string |Espaço de nome do fornecedor |
| resourceType |Não |string |O tipo de recurso dentro do espaço de nome especificado. |

### <a name="return-value"></a>Valor devolvido

Cada tipo suportado é devolvido no seguinte formato:

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

A ordem dos valores devolvidos não está garantida.

### <a name="providers-example"></a>Exemplo de fornecedores

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) de exemplo mostra como utilizar a função do fornecedor:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Para o fornecedor de recursos **Microsoft.Web** e tipo de recursos de **sites,** o exemplo anterior devolve um objeto no seguinte formato:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>referência

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Devolve um objeto que representa o estado de execução de um recurso.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| recursoNome ou recursoIdentificador |Sim |string |Nome ou identificador único de um recurso. Ao fazer referência a um recurso no modelo atual, forneça apenas o nome do recurso como parâmetro. Ao fazer referência a um recurso previamente implantado ou quando o nome do recurso for ambíguo, forneça o ID do recurso. |
| apiVersion |Não |string |Versão API do recurso especificado. **Este parâmetro é necessário quando o recurso não é aprovisionado dentro do mesmo modelo.** Tipicamente, no formato, **yyy-mm-dd**. Para versões API válidas para o seu recurso, consulte a [referência do modelo](/azure/templates/). |
| 'Cheio' |Não |string |Valor que especifica se deve devolver o objeto de recursos completo. Se não especificar, `'Full'`apenas o objeto de propriedades do recurso é devolvido. O objeto completo inclui valores como o ID de recurso e a localização. |

### <a name="return-value"></a>Valor devolvido

Cada tipo de recurso devolve propriedades diferentes para a função de referência. A função não devolve um único formato predefinido. Além disso, o valor devolvido difere `'Full'` com base no valor do argumento. Para ver as propriedades para um tipo de recurso, devolva o objeto na secção de saídas, como mostra o exemplo.

### <a name="remarks"></a>Observações

A função de referência recupera o estado de tempo de funcionamento de um recurso previamente implantado ou de um recurso implantado no modelo atual. Este artigo mostra exemplos para ambos os cenários.

Normalmente, utiliza-se a função **de referência** para devolver um valor específico a um objeto, como o ponto final de blob URI ou o nome de domínio totalmente qualificado.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Use `'Full'` quando precisa de valores de recursos que não fazem parte do esquema de propriedades. Por exemplo, para definir as políticas de acesso ao cofre chave, obtenha as propriedades de identidade para uma máquina virtual.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>Utilizações válidas

A função de referência só pode ser utilizada nas propriedades de uma definição de recurso e na secção de saídas de um modelo ou de implantação. Quando utilizado com [iteração de propriedade,](copy-properties.md)pode utilizar a função de referência para `input` porque a expressão é atribuída à propriedade do recurso.

Não pode utilizar a função de referência `count` para definir o valor da propriedade num ciclo de cópia. Pode utilizar para definir outras propriedades no loop. A referência está bloqueada para a propriedade de contagem porque esse imóvel deve ser determinado antes da função de referência ser resolvida.

Não pode utilizar a função de referência nas saídas de um [modelo aninhado](linked-templates.md#nested-template) para devolver um recurso que implementou no modelo aninhado. Em vez disso, use um [modelo ligado](linked-templates.md#linked-template).

Se utilizar a função de **referência** num recurso que é implantado condicionalmente, a função é avaliada mesmo que o recurso não seja implantado.  Obtém um erro se a função **de referência** se referir a um recurso que não existe. Utilize a função **se** para se certificar de que a função só é avaliada quando o recurso estiver a ser implantado. Consulte a [função se](template-functions-logical.md#if) para um modelo de amostra que utiliza se e referência com um recurso implantado condicionalmente.

### <a name="implicit-dependency"></a>Dependência implícita

Ao utilizar a função de referência, declara implicitamente que um recurso depende de outro recurso se o recurso referenciado estiver aprovisionado dentro do mesmo modelo e se referir ao recurso pelo seu nome (não id de recurso). Você não precisa usar também a propriedade dependsOn. A função não é avaliada até que o recurso referenciado tenha concluído a implantação.

### <a name="resource-name-or-identifier"></a>Nome de recurso ou identificador

Ao fazer referência a um recurso que é implantado no mesmo modelo, forneça o nome do recurso.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Ao fazer referência a um recurso que não seja implantado `apiVersion`no mesmo modelo, forneça o ID de recurso e .

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Para evitar ambiguidadesobre o recurso a que se refere, pode fornecer um identificador de recursos totalmente qualificado.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

Ao construir uma referência totalmente qualificada a um recurso, a ordem para combinar segmentos do tipo e nome não é simplesmente uma concatenação dos dois. Em vez disso, após o espaço de nome, utilize uma sequência de pares de *tipo/nome* de menos específicos para os mais específicos:

**{espaço de nomedo para fornecedor de recursos}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

Por exemplo:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`é `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` correto não é correto

Para simplificar a criação de `resourceId()` qualquer ID de recurso, `concat()` utilize as funções descritas neste documento em vez da função.

### <a name="get-managed-identity"></a>Obter identidade gerida

[As identidades geridas para os recursos Do Azure](../../active-directory/managed-identities-azure-resources/overview.md) são tipos de recursos de [extensão](../management/extension-resource-types.md) que são criados implicitamente para alguns recursos. Como a identidade gerida não está explicitamente definida no modelo, deve fazer referência ao recurso a que a identidade é aplicada. Use `Full` para obter todas as propriedades, incluindo a identidade criada implicitamente.

Por exemplo, para obter o ID do inquilino para uma identidade gerida que é aplicada a um conjunto de escala de máquina virtual, use:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Exemplo de referência

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) de exemplo seguinte implanta um recurso, e referências que o recurso.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": {
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

O exemplo anterior devolve os dois objetos. O objeto de propriedades está no seguinte formato:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

O objeto completo encontra-se no seguinte formato:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) de exemplo seguinte refere uma conta de armazenamento que não é implementada neste modelo. A conta de armazenamento já existe dentro da mesma subscrição.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

Devolve um objeto que representa o grupo de recursos atual.

### <a name="return-value"></a>Valor devolvido

O objeto devolvido encontra-se no seguinte formato:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

A propriedade **gerida By** é devolvida apenas para grupos de recursos que contêm recursos que são geridos por outro serviço. Para Aplicações Geridas, Databricks e AKS, o valor da propriedade é o iD de recursos de gestão.

### <a name="remarks"></a>Observações

A `resourceGroup()` função não pode ser usada num modelo que é [implantado ao nível de subscrição](deploy-to-subscription.md). Só pode ser usado em modelos que são implantados num grupo de recursos. Pode utilizar `resourceGroup()` a função num [modelo ligado ou aninhado (com âmbito interno)](linked-templates.md) que visa um grupo de recursos, mesmo quando o modelo de progenitor é implantado na subscrição. Nesse cenário, o modelo ligado ou aninhado é implantado ao nível do grupo de recursos. Para obter mais informações sobre o alvo de um grupo de recursos numa implementação de nível de subscrição, consulte [os recursos do Deploy Azure para mais do que um grupo de subscrição ou recursos.](cross-resource-group-deployment.md)

Um uso comum da função resourceGroup é criar recursos no mesmo local que o grupo de recursos. O exemplo seguinte utiliza a localização do grupo de recursos para um valor de parâmetro predefinido.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

Também pode utilizar a função resourceGroup para aplicar tags do grupo de recursos a um recurso. Para mais informações, consulte [Apply tags do grupo de recursos](../management/tag-resources.md#apply-tags-from-resource-group).

Ao utilizar modelos aninhados para implantar em vários grupos de recursos, pode especificar o âmbito para avaliar a função do Grupo de recursos. Para mais informações, consulte a Implantação de [recursos Azure para mais do que um grupo de subscrição ou recursos.](cross-resource-group-deployment.md)

### <a name="resource-group-example"></a>Exemplo de grupo de recursos

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) de exemplo devolve as propriedades do grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

O exemplo anterior devolve um objeto no seguinte formato:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Devolve o identificador único de um recurso. Utilize esta função quando o nome do recurso é ambíguo ou não é provisionado dentro do mesmo modelo. O formato do identificador devolvido varia em função de se a implantação ocorre no âmbito de um grupo de recursos, subscrição, grupo de gestão ou inquilino.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| subscriptionId |Não |cadeia (Em formato GUID) |O valor predefinido é a subscrição atual. Especifique este valor quando necessitar de recuperar um recurso noutra subscrição. Apenas forneça este valor ao ser implantado no âmbito de um grupo de recursos ou subscrição. |
| resourceGroupName |Não |string |O valor padrão é o grupo de recursos atual. Especifique este valor quando necessitar de recuperar um recurso noutro grupo de recursos. Apenas forneça este valor ao ser implantado no âmbito de um grupo de recursos. |
| resourceType |Sim |string |Tipo de recurso, incluindo espaço de nome do fornecedor de recursos. |
| recursoName1 |Sim |string |Nome de recurso. |
| recursoName2 |Não |string |Próximo segmento de nome de recursos, se necessário. |

Continue a adicionar nomes de recursos como parâmetros quando o tipo de recursos inclui mais segmentos.

### <a name="return-value"></a>Valor devolvido

Quando o modelo é implantado no âmbito de um grupo de recursos, o ID de recurso é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Quando utilizado numa implementação de [nível de subscrição,](deploy-to-subscription.md)o ID de recurso é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Quando utilizado numa implantação de [nível de grupo de gestão](deploy-to-management-group.md) ou implantação ao nível do inquilino, o ID de recurso é devolvido no seguinte formato:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Para obter a identificação em outros formatos, consulte:

* [extensãoResourceId](#extensionresourceid)
* [subscriçãoResourceId](#subscriptionresourceid)
* [inquilinoResourceId](#tenantresourceid)

### <a name="remarks"></a>Observações

O número de parâmetros que fornece varia com base no facto de o recurso ser um recurso pai ou filho, e se o recurso está no mesmo grupo de subscrição ou recursos.

Para obter o ID de recurso para um recurso-mãe no mesmo grupo de subscrição e recursos, forneça o tipo e o nome do recurso.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Para obter o ID de recurso para um recurso infantil, preste atenção ao número de segmentos do tipo de recurso. Forneça um nome de recurso para cada segmento do tipo de recurso. O nome do segmento corresponde ao recurso que existe para aquela parte da hierarquia.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Para obter o ID de recurso para um recurso na mesma subscrição, mas diferente grupo de recursos, forneça o nome do grupo de recursos.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Para obter o ID de recurso para um recurso em um grupo de subscrição e recursos diferente, fornecer o id de subscrição e nome do grupo de recursos.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Muitas vezes, é necessário utilizar esta função quando se utiliza uma conta de armazenamento ou rede virtual num grupo de recursos alternativos. O exemplo que se segue mostra como um recurso de um grupo de recursos externos pode ser facilmente utilizado:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>Exemplo de ID de recursos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) de exemplo seguinte devolve o ID de recurso para uma conta de armazenamento no grupo de recursos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| mesmoRGOutput | String | /subscrições/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.StorageAccounts/examplestorage |
| diferenteRGOutput | String | /subscrições/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.StorageAccounts/examplestorage |
| subprodução diferente | String | /subscrições/11111111-1111-1111-11111111111/recursosGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| aninhadaResourceOutput | String | /subscrições/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>subscrição

```json
subscription()
```

Devolve detalhes sobre a subscrição para a implementação atual.

### <a name="return-value"></a>Valor devolvido

A função devolve o seguinte formato:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Observações

Ao utilizar modelos aninhados para implantar em várias subscrições, pode especificar o âmbito para avaliar a função de subscrição. Para mais informações, consulte a Implantação de [recursos Azure para mais do que um grupo de subscrição ou recursos.](cross-resource-group-deployment.md)

### <a name="subscription-example"></a>Exemplo de subscrição

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) de exemplo seguinte mostra a função de subscrição chamada na secção de saídas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>subscriçãoResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Devolve o identificador único para um recurso implantado ao nível da subscrição.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| subscriptionId |Não |cadeia (em formato GUID) |O valor predefinido é a subscrição atual. Especifique este valor quando necessitar de recuperar um recurso noutra subscrição. |
| resourceType |Sim |string |Tipo de recurso, incluindo espaço de nome do fornecedor de recursos. |
| recursoName1 |Sim |string |Nome de recurso. |
| recursoName2 |Não |string |Próximo segmento de nome de recursos, se necessário. |

Continue a adicionar nomes de recursos como parâmetros quando o tipo de recursos inclui mais segmentos.

### <a name="return-value"></a>Valor devolvido

O identificador é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Observações

Você usa esta função para obter o ID de recursos para recursos que são [implantados para a subscrição](deploy-to-subscription.md) em vez de um grupo de recursos. O ID devolvido difere do valor devolvido pela função [resourceId,](#resourceid) não incluindo um valor de grupo de recursos.

### <a name="subscriptionresourceid-example"></a>exemplo de Recursode

O seguinte modelo atribui uma função incorporada. Pode implantá-lo para um grupo de recursos ou subscrição. Utiliza a função de subscriçãoResourceId para obter o ID de recurso para funções incorporadas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>inquilinoResourceId

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Devolve o identificador único para um recurso implantado ao nível do inquilino.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| resourceType |Sim |string |Tipo de recurso, incluindo espaço de nome do fornecedor de recursos. |
| recursoName1 |Sim |string |Nome de recurso. |
| recursoName2 |Não |string |Próximo segmento de nome de recursos, se necessário. |

Continue a adicionar nomes de recursos como parâmetros quando o tipo de recursos inclui mais segmentos.

### <a name="return-value"></a>Valor devolvido

O identificador é devolvido no seguinte formato:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Observações

Você usa esta função para obter o ID de recursos para um recurso que é implantado para o inquilino. O ID devolvido difere dos valores devolvidos por outras funções de ID de recurso, não incluindo os valores do grupo de recursos ou da subscrição.

## <a name="next-steps"></a>Passos seguintes

* Para uma descrição das secções num modelo de Gestor de Recursos Azure, consulte os modelos de [Gestor de Recursos Azure da Autoria](template-syntax.md).
* Para fundir vários modelos, consulte [Utilizar modelos ligados com](linked-templates.md)o Gestor de Recursos Azure .
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [Criar múltiplas instâncias de recursos no Gestor de Recursos Azure](copy-resources.md).
* Para ver como implementar o modelo que criou, consulte [implementar uma aplicação com o modelo de Gestor](deploy-powershell.md)de Recursos Azure .

