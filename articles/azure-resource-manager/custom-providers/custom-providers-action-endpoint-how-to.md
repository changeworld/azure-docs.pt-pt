---
title: Adicionar ações personalizadas à API Do REST Azure
description: Saiba como adicionar ações personalizadas à API Do REST Azure. Este artigo irá percorrer os requisitos e as melhores práticas para pontos finais que desejem implementar ações personalizadas.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650399"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Adicionar ações personalizadas à API do REST Azure

Este artigo irá analisar os requisitos e as melhores práticas para criar pontos finais do Fornecedor de Recursos Personalizados Azure que implementam ações personalizadas. Se não está familiarizado com os Fornecedores de Recursos Personalizados Da Azure, consulte a visão geral sobre os fornecedores de [recursos personalizados.](overview.md)

## <a name="how-to-define-an-action-endpoint"></a>Como definir um ponto final de ação

Um **ponto final** é um URL que aponta para um serviço, que implementa o contrato subjacente entre ele e o Azure. O ponto final é definido no fornecedor de recursos personalizados e pode ser qualquer URL acessível ao público. A amostra abaixo tem `myCustomAction` uma `endpointURL` **ação** chamada implementada por .

**Provedor de Recursos**da Amostra:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>Construindo um ponto final de ação

Um **ponto final** que implemente uma **ação** deve tratar do pedido e da resposta para a nova API em Azure. Quando um fornecedor de recursos personalizados com uma **ação** é criado, irá gerar um novo conjunto de APIs em Azure. Neste caso, a ação gerará uma nova `POST` Ação Azure API para chamadas:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Pedido de entrada da API Azure:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Este pedido será então encaminhado para o **ponto final** sob a forma:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Da mesma forma, a resposta do **ponto final** é então reencaminhada para o cliente. A resposta do ponto final deve voltar:

- Um documento válido de objeto JSON. Todas as matrizes e cordas devem ser aninhadas sob um objeto superior.
- O `Content-Type` cabeçalho deve ser definido para "aplicação/json; charset=utf-8".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Resposta do fornecedor de recursos personalizados Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Chamando uma ação personalizada

Existem duas formas principais de chamar uma ação personalizada de um fornecedor de recursos personalizados:

- CLI do Azure
- Modelos de gestor de recursos azure

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parâmetro | Necessário | Descrição
---|---|---
action | *Sim,* | O nome da ação definida no **ResourceProvider**.
ids | *Sim,* | O ID de recurso do Fornecedor de **Recursos.**
solicitar corpo | *Não* | O órgão de pedido que será enviado para o **ponto final.**

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

> [!NOTE]
> As ações têm suporte limitado nos modelos de gestor de recursos do Azure. Para que a ação seja chamada dentro de [`list`](../templates/template-functions-resource.md#list) um modelo, deve conter o prefixo em seu nome.

Sample **ResourceProvider** com Ação de Lista:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Modelo de gestor de recursos azure da amostra:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parâmetro | Necessário | Descrição
---|---|---
recursoIdentificador | *Sim,* | O ID de recurso do Fornecedor de **Recursos.**
apiVersion | *Sim,* | A versão API do tempo de execução do recurso. Esta deve ser sempre "2018-09-01-preview".
funçãoValores | *Não* | O órgão de pedido que será enviado para o **ponto final.**

## <a name="next-steps"></a>Passos seguintes

- [Visão geral sobre fornecedores de recursos personalizados Azure](overview.md)
- [Quickstart: Criar o Fornecedor de Recursos Personalizados Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](./tutorial-get-started-with-custom-providers.md)
- [Como: Adicionar recursos personalizados à API Do REST Azure](./custom-providers-resources-endpoint-how-to.md)
