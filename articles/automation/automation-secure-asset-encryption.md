---
title: Criptografia ativos seguros na Automação Azure
description: A Azure Automation protege os ativos seguros usando vários níveis de encriptação. Por padrão, a encriptação é feita utilizando chaves geridas pela Microsoft. Os clientes podem configurar as suas contas de automação para usar chaves geridas pelo cliente para encriptação. Este artigo descreve os detalhes de ambos os modos de encriptação e como pode alternar entre os dois.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 0b1d194209122fa71272243c80a2d4c57f6834a1
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547761"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Criptografia ativos seguros na Automação Azure

Os ativos seguros na Automatização Azure incluem credenciais, certificados, ligações e variáveis encriptadas. Estes ativos estão protegidos na Automatização Azure utilizando vários níveis de encriptação. Com base na chave de nível superior utilizada para a encriptação, existem dois modelos para encriptação:
-    Utilização de chaves geridas pela Microsoft
-    Utilização de chaves geridas pelo cliente

## <a name="microsoft-managed-keys"></a>Chaves geridas pela Microsoft

Por predefinição, a sua conta Azure Automation utiliza chaves geridas pela Microsoft.

Cada ativo seguro é encriptado e armazenado na Automatização Azure utilizando uma chave única (chave de encriptação de dados) que é gerada para cada conta de automação. Estas chaves são encriptadas e armazenadas na Automatização Azure utilizando mais uma chave única que é gerada para cada conta chamada Chave de Encriptação de Conta (AEK). Estas chaves de encriptação de conta encriptadas e armazenadas no Azure Automation utilizando chaves geridas pela Microsoft. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Chaves geridas pelo cliente com cofre de chave (pré-visualização)

Pode gerir a encriptação de ativos seguros para a sua conta Automation com as suas próprias chaves. Quando especifica uma chave gerida pelo cliente ao nível da conta Automation, essa chave é usada para proteger e controlar o acesso à chave de encriptação da conta para a conta Automation. Isto, por sua vez, é usado para encriptar e desencriptar todos os ativos seguros. As chaves geridas pelo cliente oferecem uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os seus ativos seguros.

Utilize o Cofre de Chaves Azure para armazenar chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves.  Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre chave Azure?](../key-vault/key-vault-overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Ativar chaves geridas pelo cliente para uma conta De automação

Ao ativar a encriptação com chaves geridas pelo cliente para uma conta Automation, a Azure Automation envolve a chave de encriptação da conta com a chave gerida pelo cliente no cofre de chaves associado. Ativar as chaves geridas pelo cliente não afeta o desempenho, e a conta é encriptada com a nova chave imediatamente, sem qualquer atraso.

Uma nova conta Automation é sempre encriptada utilizando chaves geridas pela Microsoft. Não é possível ativar as chaves geridas pelo cliente no momento em que a conta é criada. As chaves geridas pelo cliente são armazenadas no Cofre chave Azure, e o cofre chave deve ser aprovisionado com políticas de acesso que concedem permissões-chave à identidade gerida que está associada à conta Automation. A identidade gerida só está disponível após a criação da conta de armazenamento.

Ao modificar a chave utilizada para encriptação de ativos seguros Azure Automation, permitindo ou desativando chaves geridas pelo cliente, atualizando a versão chave ou especificando uma chave diferente, a encriptação da chave de encriptação da conta muda, mas os ativos seguros na sua conta Azure Automation não precisam de ser novamente encriptados.

As três secções seguintes descrevem a mecânica de ativar as chaves geridas pelo cliente para uma conta De automação. 

> [!NOTE] 
> Para ativar as chaves geridas pelo cliente, é necessário fazer chamadas aPi do Azure Automation REST utilizando a versão API 2020-01-13-preview

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Pré-requisitos para a utilização de chaves geridas pelo Cliente na Automação Azure

Antes de ativar as chaves geridas pelo cliente para uma conta De automatização, deve assegurar-se de que os seguintes pré-requisitos são cumpridos:

 - A chave de mão-de-cliente está guardada num Cofre de Chaves Azure. 
 - Ative tanto o **Soft Delete** como **não expurgar** as propriedades no cofre da chave. Estas características são necessárias para permitir a recuperação das teclas em caso de supressão acidental.
 - Apenas as chaves RSA são suportadas com encriptação Azure Automation. Para mais informações sobre chaves, consulte [sobre chaves, segredos e certificados do Cofre chave Azure.](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)
- A conta Automation e o cofre chave podem estar em diferentes subscrições, mas precisam de estar no mesmo inquilino do Azure Ative Directory.

### <a name="assign-an-identity-to-the-automation-account"></a>Atribuir uma identidade à conta de automação

Para utilizar chaves geridas pelo cliente com uma conta Automation, a sua conta Automation precisa de autenticar contra o cofre de chaves que armazena chaves geridas pelo cliente. A Azure Automation utiliza o sistema de identidades geridas atribuídas para autenticar a conta com o Cofre chave Azure. Para obter mais informações sobre identidades geridas, veja [o que é gerida identidades para os recursos Do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Configure um sistema de identidade gerida atribuída à conta de automação utilizando a seguinte chamada REST API:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo do pedido:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

O sistema atribuído identidade para a conta Automation é devolvido numa resposta semelhante à seguinte:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>Configure a política de acesso ao cofre chave

Uma vez que uma identidade gerida é atribuída à conta Automation, configura o acesso ao cofre chave armazenando chaves geridas pelo cliente. A Azure Automation requer **obter,** **recuperar,** **embrulharKey**, **UnwrapKey** nas chaves geridas pelo cliente.

Tal política de acesso pode ser definida utilizando a seguinte chamada REST API:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Corpo do pedido:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> Os campos **tenantId** e **objectId** devem ser dotados de valores de **identidade.tenantId** e **identity.mainId,** respectivamente, a partir da resposta da identidade gerida para a conta Deautomação.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Alterar a configuração da conta Automation para utilizar a chave gerida pelo cliente

Por fim, pode trocar a sua conta de Automação de chaves geridas pela Microsft para chaves geridas pelo cliente, utilizando a seguinte chamada REST API:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo do pedido:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

Resposta de amostra

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>Gerir o ciclo de vida das chaves geridas pelo cliente

### <a name="rotate-customer-managed-keys"></a>Rode as chaves geridas pelo cliente

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Quando a tecla estiver rodada, tem de atualizar a conta Automation para utilizar a nova tecla URI.

A rotação da tecla não desencadeia a reencriptação de ativos seguros na conta Automation. Não são necessárias mais medidas.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Para revogar o acesso às chaves geridas pelo cliente, utilize a PowerShell ou o Azure CLI. Para mais informações, consulte [o Cofre de Chaves Azure PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) ou o Cofre de Chaves [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso bloqueia efetivamente o acesso a todos os ativos seguros na conta Automation, uma vez que a chave de encriptação é inacessível pela Azure Automation.

## <a name="next-steps"></a>Passos seguintes

- [O que é o cofre de chave do Azure?](../key-vault/key-vault-overview.md)

- [Recursos de certificados na Automatização do Azure](shared-resources/certificates.md)

- [Recursos de credenciais na Automatização do Azure](shared-resources/credentials.md)

- [Recursos de variáveis na Automatização do Azure](shared-resources/variables.md)
