---
title: Autenticação externa da tarefa ACR
description: Configure uma tarefa de registo de contentores Azure (Tarefa ACR) para ler as credenciais do Docker Hub armazenadas num cofre chave Azure, utilizando uma identidade gerida para os recursos Azure.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47d3d643ee1287ef4f444095a2c6cfe6dcab294b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842525"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autenticação externa numa tarefa ACR utilizando uma identidade gerida pelo Azure 

Numa [tarefa ACR,](container-registry-tasks-overview.md)pode [permitir uma identidade gerida para os recursos Azure.](container-registry-tasks-authentication-managed-identity.md) A tarefa pode usar a identidade para aceder a outros recursos Do Azure, sem necessidade de fornecer ou gerir credenciais. 

Neste artigo, aprende-se a permitir uma identidade gerida numa tarefa que acede a segredos armazenados num cofre chave Azure. 

Para criar os recursos Do Azure, este artigo requer que execute a versão Azure CLI 2.0.68 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

## <a name="scenario-overview"></a>Descrição geral do cenário

A tarefa de exemplo diz as credenciais do Docker Hub armazenadas num cofre de chaves Azure. As credenciais são para uma conta Docker Hub com permissões escritas (push) para um repositório privado Docker Hub. Para ler as credenciais, configura a tarefa com uma identidade gerida e atribui-lhe permissões adequadas. A tarefa associada à identidade constrói uma imagem, e assina no Docker Hub para empurrar a imagem para o repo privado. 

Este exemplo mostra passos utilizando uma identidade gerida atribuída pelo utilizador ou atribuída ao sistema. A sua escolha de identidade depende das necessidades da sua organização.

Num cenário real, uma empresa pode publicar imagens a um repo privado em Docker Hub como parte de um processo de construção. 

## <a name="prerequisites"></a>Pré-requisitos

Precisa de um registo de contentores Azure no qual executa a tarefa. Neste artigo, este registo chama-se *meu registo.* Substitua-o pelo seu próprio nome de registo em etapas posteriores.

Se ainda não tiver um registo de contentores Azure, consulte [Quickstart: Crie um registo de contentores privado sintetizador com o Azure CLI](container-registry-get-started-azure-cli.md). Ainda não precisas de levar imagens para o registo.

Você também precisa de um repositório privado em Docker Hub, e uma conta Docker Hub com permissões para escrever para o repo. Neste exemplo, este repo é nomeado *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Crie um cofre chave e guarde segredos

Primeiro, se precisar, crie um grupo de recursos chamado *myResourceGroup* no local *leste* com o seguinte [grupo az criar][az-group-create] comando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Use o [cofre az criar][az-keyvault-create] comando para criar um cofre chave. Certifique-se de especificar um nome único do cofre chave. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Guarde as credenciais necessárias do Docker Hub no cofre da chave usando o comando secreto do [cofre az keyvault.][az-keyvault-secret-set] Nestes comandos, os valores são passados em variáveis ambientais:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

Num cenário real, os segredos seriam provavelmente definidos e mantidos num processo separado.

## <a name="define-task-steps-in-yaml-file"></a>Definir passos de tarefa no ficheiro YAML

Os passos para esta tarefa de exemplo são definidos num [ficheiro YAML](container-registry-tasks-reference-yaml.md). Crie um `dockerhubtask.yaml` ficheiro nomeado num diretório de trabalho local e repasse os seguintes conteúdos. Certifique-se de substituir o nome do cofre chave no ficheiro com o nome do seu cofre chave.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

Os passos de tarefa fazem o seguinte:

* Gerencie credenciais secretas para autenticar com Docker Hub.
* Autenticar com Docker Hub passando `docker login` os segredos para o comando.
* Construa uma imagem utilizando uma amostra Do Dockerfile no repo [de azure-samples/acr-tasks.](https://github.com/Azure-Samples/acr-tasks.git)
* Empurre a imagem para o repositório privado do Docker Hub.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Opção 1: Criar tarefa com identidade atribuída ao utilizador

Os passos nesta secção criam uma tarefa e permitem uma identidade atribuída ao utilizador. Se pretender ativar uma identidade atribuída ao sistema, consulte [a Opção 2: Criar tarefa com identidade atribuída ao sistema](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *de dockerhubtask* executando a seguinte [tarefa az acr criar][az-acr-task-create] comando. A tarefa funciona sem um contexto de código `dockerhubtask.yaml` fonte, e o comando refere o ficheiro no diretório de trabalho. O `--assign-identity` parâmetro passa a identificação de recursos da identidade atribuída ao utilizador. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opção 2: Criar tarefa com identidade atribuída ao sistema

Os passos nesta secção criam uma tarefa e permitem uma identidade atribuída ao sistema. Se pretender ativar uma identidade atribuída ao utilizador, consulte [a Opção 1: Criar tarefa com identidade atribuída](#option-1-create-task-with-user-assigned-identity)ao utilizador . 

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *de dockerhubtask* executando a seguinte [tarefa az acr criar][az-acr-task-create] comando. A tarefa funciona sem um contexto de código `dockerhubtask.yaml` fonte, e o comando refere o ficheiro no diretório de trabalho. O `--assign-identity` parâmetro sem valor permite a identidade atribuída ao sistema na tarefa.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>Conceder acesso de identidade ao cofre chave

Execute o seguinte comando [de definição de teclado az keyvault][az-keyvault-set-policy] para definir uma política de acesso no cofre chave. O exemplo que se segue permite que a identidade leia segredos do cofre chave. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Executar manualmente a tarefa

Para verificar se a tarefa em que permitiu uma identidade gerida funciona com sucesso, desencadeie manualmente a tarefa com o comando de execução de [tarefas az acr.][az-acr-task-run] O `--set` parâmetro é usado para passar o nome de repo privado para a tarefa. Neste exemplo, o nome de repo do espaço reservado é *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Quando a tarefa corre com sucesso, a saída mostra autenticação bem sucedida para Docker Hub, e a imagem é construída com sucesso e empurrada para o repo privado:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Para confirmar que a imagem é empurrada, verifique a etiqueta (neste`cf24` exemplo) no repo privado Do Docker Hub.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [permitir uma identidade gerida numa tarefa ACR.](container-registry-tasks-authentication-managed-identity.md)
* Consulte a [referência YAML de Tarefas ACR](container-registry-tasks-reference-yaml.md)


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
