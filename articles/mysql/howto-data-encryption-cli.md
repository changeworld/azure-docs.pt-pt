---
title: Encriptação de dados - Azure CLI - Base de Dados Azure para MySQL
description: Aprenda a configurar e gerir a encriptação de dados para a sua Base de Dados Azure para mySQL utilizando o Azure CLI.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: fca9b9eea3697a10650e5dbf0522f795fe0a8e0b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522215"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Encriptação de dados para Base de Dados Azure para MySQL utilizando o Azure CLI

Aprenda a utilizar o Azure CLI para configurar e gerir a encriptação de dados para a sua Base de Dados Azure para o MySQL.

## <a name="prerequisites-for-azure-cli"></a>Pré-requisitos para o Azure CLI

* Você deve ter uma assinatura Azure e ser um administrador nessa subscrição.
* Crie um cofre chave e uma chave para usar para uma chave gerida pelo cliente. Também ative a proteção da purga e a eliminação suave no cofre da chave.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* No cofre azure criado, crie a chave que será usada para a encriptação de dados da Base de Dados Azure para MySQL.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* Para utilizar um cofre chave existente, deve ter as seguintes propriedades para usar como chave gerida pelo cliente:
  * [Eliminação suave](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Purga protegida](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A chave deve ter os seguintes atributos a utilizar como chave gerida pelo cliente:
  * Sem data de validade
  * Não incapacitado
  * Executar **executar executar,** **embrulhar,** **desembrulhar** operações

## <a name="set-the-right-permissions-for-key-operations"></a>Detete as permissões certas para operações-chave

1. Existem duas formas de obter a identidade gerida para a sua Base de Dados Azure para o MySQL.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Crie uma nova Base de Dados Azure para o servidor MySQL com uma identidade gerida.

    ```azurecli-interactive
    az mysql server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Atualize uma base de dados Azure existente para o servidor MySQL para obter uma identidade gerida.

    ```azurecli-interactive
    az mysql server update --name  <server name>  -g <resource_group> --assign-identity
    ```

2. Detete as **permissões chave** (**Get,** **Wrap,** **Desembrulhar)** para o **principal,** que é o nome do servidor MySQL.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Definir encriptação de dados para base de dados Azure para MySQL

1. Ativar a encriptação de dados para a Base de Dados Azure para MySQL utilizando a chave criada no Cofre chave Azure.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Url chave:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Utilização da encriptação de Dados para restaurar ou replicar servidores

Depois de a Base de Dados Azure para MySQL ser encriptada com a chave gerida por um cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é encriptada. Pode fazer esta nova cópia através de uma operação local ou geo-restauro, ou através de uma operação de réplica (local/região transversal). Assim, para um servidor MySQL encriptado, pode utilizar os seguintes passos para criar um servidor restaurado encriptado.

### <a name="creating-a-restoredreplica-server"></a>Criação de um servidor restaurado/réplica

  *  [Criar um servidor de restauro](howto-restore-server-cli.md) 
  *  [Criar um servidor de réplica de leitura](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Uma vez restaurado o servidor, revalida a encriptação de dados do servidor restaurado

    ```azurecli-interactive
    az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Capacidade adicional para a chave que está a ser utilizada para a Base de Dados Azure para mySQL

### <a name="get-the-key-used"></a>Obter a chave usada

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>Lista rindo a chave utilizada

    ```azurecli-interactive
    az mysql server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>Largue a chave que está a ser usada

    ```azurecli-interactive
    az mysql server key delete -g <resource_group> --kid <key url> 
    ```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Usando um modelo de Gestor de Recursos Azure para ativar a encriptação de dados

Além do portal Azure, também pode ativar a encriptação de dados na sua Base de Dados Azure para o servidor MySQL utilizando modelos do Gestor de Recursos Azure para servidores novos e existentes.

### <a name="for-a-new-server"></a>Para um novo servidor

Utilize um dos modelos pré-criados do Gestor de Recursos Azure para fornecer o servidor com encriptação de dados ativado: [Exemplo com encriptação de dados](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Este modelo do Gestor de Recursos Azure cria uma Base de Dados Azure para o servidor MySQL e utiliza o **KeyVault** e **key** pass como parâmetros para permitir a encriptação de dados no servidor.

### <a name="for-an-existing-server"></a>Para um servidor existente
Além disso, pode utilizar modelos do Gestor de Recursos Do Azure para ativar a encriptação de dados na base de dados azure existente para servidores MySQL.

* Passe a identificação de recursos da chave azure key `Uri` vault que copiou anteriormente sob a propriedade no objeto de propriedades.

* Utilize a *pré-visualização 2020-01-01* como versão API.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>Passos seguintes

 Para saber mais sobre encriptação de dados, consulte a Base de [Dados Azure para encriptação de dados MySQL com a chave gerida pelo cliente](concepts-data-encryption-mysql.md).
