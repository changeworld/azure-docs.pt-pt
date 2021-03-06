---
title: Use parâmetros para criar plantas dinâmicas
description: Aprenda sobre parâmetros estáticos e dinâmicos e como usá-los para criar plantas seguras e dinâmicas.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: ed596db2050ac788c2d98c63cb7314de473b5f4e
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383604"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Criação de plantas dinâmicas através de parâmetros

Um projeto totalmente definido com vários artefactos (tais como grupos de recursos, modelos de Gestor de Recursos, políticas ou atribuições de papéis) oferece a rápida criação e criação consistente de objetos dentro do Azure. Para permitir uma utilização flexível destes padrões e contentores de design reutilizáveis, o Azure Blueprints suporta parâmetros. O parâmetro cria flexibilidade, tanto durante a definição como a atribuição, para alterar propriedades nos artefactos implantados pela planta.

Um exemplo simples é o artefacto do grupo de recursos. Quando um grupo de recursos é criado, tem dois valores necessários que devem ser fornecidos: nome e localização. Ao adicionar um grupo de recursos à sua planta, se os parâmetros não existissem, definiria esse nome e localização para cada utilização da planta. Esta repetição faria com que cada utilização da planta criasse artefactos no mesmo grupo de recursos. Os recursos dentro desse grupo de recursos seriam duplicados e provocariam um conflito.

> [!NOTE]
> Não é um problema para duas plantas diferentes incluir um grupo de recursos com o mesmo nome.
> Se um grupo de recursos incluído numa planta já existe, a planta continua a criar os artefactos relacionados nesse grupo de recursos. Isto poderia causar um conflito, uma vez que dois recursos com o mesmo nome e tipo de recurso não podem existir dentro de uma subscrição.

A solução para este problema são os parâmetros. O Azure Blueprints permite-lhe definir o valor de cada propriedade do artefacto durante a atribuição a uma subscrição. O parâmetro permite reutilizar uma planta que cria um grupo de recursos e outros recursos dentro de uma única subscrição sem ter conflitos.

## <a name="blueprint-parameters"></a>Parâmetros de esquema

Através da API REST, os parâmetros podem ser criados na própria planta. Estes parâmetros são diferentes dos parâmetros em cada um dos artefactos suportados. Quando um parâmetro é criado na planta, pode ser usado pelos artefactos nessa planta. Um exemplo pode ser o prefixo para nomear o grupo de recursos. O artefacto pode usar o parâmetro de planta para criar um parâmetro "maioritariamente dinâmico". Como o parâmetro também pode ser definido durante a atribuição, este padrão permite uma consistência que pode aderir às regras de nomeação. Para os passos, consulte a definição de [parâmetros estáticos - parâmetro de nível de planta](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Utilizando parâmetros SecureString e SecureObject

Enquanto um _artefacto_ de modelo de Gestor de Recursos suporta parâmetros dos tipos **secureString** e **secureObject,** as plantas azure exigem que cada um seja ligado a um Cofre de Chave Azure. Esta medida de segurança impede a prática insegura de armazenar segredos juntamente com o Projeto e incentiva o emprego de padrões seguros. A Azure Blueprints apoia esta medida de segurança, detetando a inclusão de um parâmetro seguro num _artefacto_de modelo do Gestor de Recursos. O serviço solicita então durante a atribuição das seguintes propriedades do Cofre chave por parâmetro seguro detetado:

- ID de recurso chave vault
- Nome secreto do cofre chave
- Versão secreta do Cofre chave

Se a atribuição da planta utilizar uma identidade gerida atribuída pelo **sistema,** o Cofre chave referenciado _deve_ existir na mesma subscrição a que a definição de projeto é atribuída.

Se a atribuição da planta utilizar uma identidade gerida atribuída pelo **utilizador,** o Cofre chave referenciado _pode_ existir numa subscrição centralizada. A identidade gerida deve ser concedida direitos adequados sobre o Cofre chave antes da atribuição do projeto.

> [!IMPORTANT]
> Em ambos os casos, o Cofre chave deve ter acesso ao Gestor de **Recursos Azure para implementação** de modelos configurado na página de políticas de **acesso.** Para obter instruções sobre como ativar esta funcionalidade, consulte o Cofre chave - Ativar a [implementação do modelo](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment).

Para mais informações sobre o Cofre de Chaves Azure, consulte a [visão geral do Cofre chave](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Tipos de parâmetros

### <a name="static-parameters"></a>Parâmetros estáticos

Um valor parâmetro definido na definição de uma planta é chamado **de parâmetro estático,** porque cada utilização da planta irá implantar o artefacto usando esse valor estático. No exemplo do grupo de recursos, embora não faça sentido para o nome do grupo de recursos, pode fazer sentido para a localização. Então, cada atribuição da planta criaria o grupo de recursos, seja qual for o nome durante a atribuição, no mesmo local. Esta flexibilidade permite-lhe ser seletivo naquilo que define como necessário vs o que pode ser alterado durante a atribuição.

#### <a name="setting-static-parameters-in-the-portal"></a>Definição de parâmetros estáticos no portal

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione **definições** de Blueprint a partir da página à esquerda.

1. Clique numa planta existente e, em seguida, clique em **Editar a planta** OU clique em ' Criar planta **e** preencher a informação no separador **Basics.**

1. Clique em **Seguinte: Artefactos** OU clique no separador **Artefactos.**

1. Artefactos adicionados à planta que têm opções de parâmetros mostram **parâmetros X de Y povoados** na coluna **Parâmetros.** Clique na linha do artefacto para editar os parâmetros do artefacto.

   :::image type="content" source="../media/parameters/parameter-column.png" alt-text="Parâmetros de planta numa definição de planta" border="false":::

1. A página **Editar Artefacto** exibe opções de valor adequadas ao artefacto clicado. Cada parâmetro no artefacto tem um título, uma caixa de valor e uma caixa de verificação. Coloque a caixa desmarcada para torná-la um **parâmetro estático**. No exemplo abaixo, apenas a _localização_ é um **parâmetro estático,** uma vez que não é verificado e o Nome do _Grupo de Recursos_ é verificado.

   :::image type="content" source="../media/parameters/static-parameter.png" alt-text="Parâmetros estáticos de planta em um artefacto de planta" border="false":::

#### <a name="setting-static-parameters-from-rest-api"></a>Definição de parâmetros estáticos da API REST

Em cada URI da API REST, existem variáveis que são utilizadas que precisa de substituir pelos seus próprios valores:

- `{YourMG}` - substituir pelo nome do seu grupo de gestão
- `{subscriptionId}` - substituir pelo ID da subscrição

##### <a name="blueprint-level-parameter"></a>Parâmetro de nível de planta

Ao criar uma planta através da API REST, é possível criar [parâmetros](#blueprint-parameters)de planta . Para tal, utilize o seguinte formato REST API URI e body:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Corpo do Pedido

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Uma vez criado um parâmetro de nível de planta, pode ser usado em artefactos adicionados a essa planta.
O exemplo da API REST seguinte cria um artefacto de atribuição de funções na planta e utiliza o parâmetro de nível de planta.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Corpo do Pedido

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

Neste exemplo, a propriedade **principalIds** utiliza o parâmetro de `[parameters('owners')]`nível de planta dos **proprietários** utilizando um valor de . A colocação de um parâmetro num artefacto utilizando um parâmetro de nível de planta continua a ser um exemplo de um **parâmetro estático**. O parâmetro de nível de planta não pode ser definido durante a atribuição da planta e será o mesmo valor em cada atribuição.

##### <a name="artifact-level-parameter"></a>Parâmetro de nível de artefacto

A criação de **parâmetros estáticos** num artefacto é `parameters()` semelhante, mas tem um valor reto em vez de usar a função. O exemplo seguinte cria dois parâmetros estáticos, **tagName** e **tagValue**. O valor em cada um é fornecido diretamente e não utiliza uma chamada de função.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Corpo do Pedido

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Parâmetros dinâmicos

O oposto de um parâmetro estático é um **parâmetro dinâmico.** Este parâmetro não está definido na planta, mas é definido durante cada atribuição da planta. No exemplo do grupo de recursos, a utilização de um **parâmetro dinâmico** faz sentido para o nome do grupo de recursos. Fornece um nome diferente para cada atribuição da planta. Para obter uma lista de funções de planta, consulte a referência das funções da [planta.](../reference/blueprint-functions.md)

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Definição de parâmetros dinâmicos no portal

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione **definições** de Blueprint a partir da página à esquerda.

1. Clique na planta que pretende atribuir. Selecione **Designde planta** ou clique na planta que pretende atribuir e, em seguida, clique no botão de **planta de atribuir.**

1. Na página de **design de atribuição,** encontre a secção de **parâmetros do Artefacto.** Cada artefacto com pelo menos um **parâmetro dinâmico** exibe o artefacto e as opções de configuração. Forneça os valores necessários aos parâmetros antes de atribuir a planta. No exemplo abaixo, o _Nome_ é um **parâmetro dinâmico** que deve ser definido para completar a atribuição do projeto.

   :::image type="content" source="../media/parameters/dynamic-parameter.png" alt-text="Parâmetro dinâmico de planta durante a atribuição da planta" border="false":::

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Definição de parâmetros dinâmicos da Rest API

A definição de **parâmetros dinâmicos** durante a atribuição é feita através da introdução direta do valor. Em vez de utilizar uma função, como [parâmetros,](../reference/blueprint-functions.md#parameters)o valor fornecido é uma corda adequada. Os artefactos para um grupo de recursos são definidos com um "nome de modelo", **nome,** e propriedades de **localização.** Todos os outros parâmetros para artefactoincluído são definidos em **parâmetros** com um ** \<\> nome** e par de chaves de **valor.** Se a planta estiver configurada para um parâmetro dinâmico que não é fornecido durante a atribuição, a atribuição falhará.

- URI da API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Corpo do Pedido

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Passos seguintes

- Consulte a lista de [funções](../reference/blueprint-functions.md)de plantas .
- Conheça o ciclo de vida da [planta.](lifecycle.md)
- Aprenda a personalizar a [ordem de sequenciação do esquema](sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).