---
title: Quickstart - Biblioteca de clientes Azure Key Vault para Python
description: Aprenda a criar, recuperar e apagar segredos de um cofre chave Azure usando a biblioteca de clientes Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: fccb999be82978073b3db13eba224b08adba2538
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811619"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Quickstart: Biblioteca de clientes Azure Key Vault para Python

Começa com a biblioteca de clientes azure Key Vault para python. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Utilize a biblioteca de clientes Key Vault para Python para:

- Aumentar a segurança e o controlo sobre chaves e senhas.
- Crie e importe chaves de encriptação em minutos.
- Reduza a latência com a escala de nuvens e o despedimento global.
- Simplificar e automatizar tarefas para certificados TLS/SSL.
- Utilize HSMs validados de nível 2 fips 140-2.

[Documentação de](/python/api/overview/azure/key-vault?view=azure-python) | referência API Pacote de[código fonte](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [(Índice de Pacote Python)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição Azure - [crie uma gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Python 2.7, 3.5.3, ou mais tarde
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Este quickstart assume que está a executar [o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) numa janela terminal linux.

## <a name="setting-up"></a>Configuração

### <a name="install-the-package"></a>Instale o pacote

A partir da janela da consola, instale a biblioteca de segredos Azure Key Vault para Python.

```console
pip install azure-keyvault-secrets
```

Para este arranque rápido, terá de instalar também o pacote azure.identity:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre chave

Este quickstart usa um cofre chave Azure pré-criado. Pode criar um cofre chave seguindo os passos no [quickstart Azure CLI,](quick-create-cli.md) [Azure PowerShell quickstart](quick-create-powershell.md)ou [portal Azure quickstart](quick-create-portal.md). Em alternativa, pode executar os comandos Azure CLI abaixo.

> [!Important]
> Cada cofre deve ter um nome único. Substitua <seu> de nome único com o seu cofre único com o nome do seu cofre-chave nos seguintes exemplos.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Criar um principal de serviço

A forma mais simples de autenticar uma aplicação Python baseada em nuvem é com uma identidade gerida; ver Utilize um Serviço de [Aplicações gerido identidade para aceder ao Cofre de Chaves Azure](managed-identity.md) para obter mais detalhes. Por uma questão de simplicidade, no entanto, este quickstart cria uma aplicação de consola Python. A autenticação de uma aplicação de ambiente de trabalho com o Azure requer a utilização de um diretor de serviço e de uma política de controlo de acesso.

Crie um princípio de serviço utilizando o comando Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Esta operação devolverá uma série de pares chave/valor. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Tome nota do clienteId e clienteSecret, pois iremos usá-los no [Passo Variável Ambiental set](#set-environmental-variables) abaixo.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Dê ao principal serviço acesso ao seu cofre chave

Crie uma política de acesso para o seu cofre chave que concede permissão ao seu diretor de serviço, passando o clienteId para o comando [de definição de teclado az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Dê ao diretor de serviço obter, listar e definir permissões para chaves e segredos.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Definir variáveis ambientais

O método DefaultAzureCredential na nossa aplicação baseia-se em três variáveis ambientais: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`e `AZURE_TENANT_ID`. Detete estas variáveis para os valores clienteId, clientSecret e tenantId `export VARNAME=VALUE` que observou no passo principal do serviço [Create](#create-a-service-principal) usando o formato. (Este método apenas define as variáveis para a sua concha atual e os processos criados `/etc/environment ` a partir da concha; para adicionar permanentemente estas variáveis ao seu ambiente, editar o seu ficheiro.) 

Você também precisa guardar o seu nome chave `KEY_VAULT_NAME`do cofre como uma variável ambiental chamada .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes Azure Key Vault para python permite-lhe gerir chaves e bens relacionados, tais como certificados e segredos. As amostras de código abaixo mostrar-lhe-ão como criar um cliente, definir um segredo, recuperar um segredo e apagar um segredo.

Toda a aplicação https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-appde consola está disponível em .

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo do seu código:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Autenticar o seu cofre chave e criar um cliente chave vault depende das variáveis ambientais nas [variáveis ambientais definidas](#set-environmental-variables) acima. O nome do seu cofre chave é expandido para o cofre de chaves URI, no formato "https://<your-key-vault-name>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Salvar um segredo

Agora que a sua aplicação foi autenticada, pode colocar um segredo no seu cofre usando o cliente. Método SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) Isto requer um nome para o segredo -- estamos a usar "mySecret" nesta amostra.  

```python
client.set_secret(secretName, secretValue)
```

Pode verificar se o segredo foi definido com o comando secreto do [az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora pode recuperar o valor previamente definido com o [cliente. Método GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
retrieved_secret = client.get_secret(secretName)
 ```

O teu segredo `retrieved_secret.value`está agora guardado como.

### <a name="delete-a-secret"></a>Eliminar um segredo

Finalmente, vamos apagar o segredo do seu cofre com o [cliente. Método DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
client.delete_secret(secretName)
```

Pode verificar se o segredo se foi com o comando secreto do [az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover o cofre da chave e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Código de exemplo

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print("Your secret is '" + secretValue + "'.")

print("Retrieving your secret from " + keyVaultName + ".")

retrieved_secret = client.get_secret(secretName)

print("Your secret is '" + retrieved_secret.value + "'.")
print("Deleting your secret from " + keyVaultName + " ...")

client.delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque, criaste um cofre chave, guardaste um segredo e recuperaste esse segredo. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do Cofre chave Azure](key-vault-overview.md)
- Consulte o guia do desenvolvedor do Cofre de [Chaves Azure](key-vault-developers-guide.md)
- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- Rever [as melhores práticas do Cofre de Chaves Azure](key-vault-best-practices.md)
