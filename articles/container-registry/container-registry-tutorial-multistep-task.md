---
title: Tutorial - Tarefa ACR em várias etapas
description: Neste tutorial, aprende-se a configurar uma Tarefa de Registo de Contentores Azure para acionar automaticamente um fluxo de trabalho em várias etapas para construir, executar e empurrar imagens de contentores na nuvem quando compromete o código fonte a um repositório Git.
ms.topic: tutorial
ms.date: 05/09/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: ff32b3095638af6b2b246b99a5dc9219e0020782
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78402312"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Tutorial: Faça um fluxo de trabalho de contentores em várias etapas na nuvem quando cometer código fonte

Além de uma [tarefa rápida,](container-registry-tutorial-quick-task.md)a ACR Tasks suporta fluxos de trabalho multi-etapas e multi-contentores que podem disparar automaticamente quando compromete o código fonte a um repositório Git. 

Neste tutorial, aprende-se a usar ficheiros YAML exemplos para definir tarefas em várias etapas que constroem, executam e empurram uma ou mais imagens de contentores para um registo quando se compromete código fonte. Para criar uma tarefa que apenas automatiza uma única imagem baseada no compromisso de código, consulte [Tutorial: Automatizar imagem de contentor constrói-se na nuvem quando comete código fonte](container-registry-tutorial-build-task.md). Para uma visão geral das tarefas ACR, consulte [o Automate OS e a correção de quadros com tarefas ACR,](container-registry-tasks-overview.md)

Neste tutorial:

> [!div class="checklist"]
> * Defina uma tarefa em várias etapas utilizando um ficheiro YAML
> * Criar uma tarefa
> * Adicione opcionalmente credenciais à tarefa para permitir o acesso a outro registo
> * Testar a tarefa
> * Ver estado da tarefa
> * Acionar a tarefa com uma consolidação do código

Este tutorial parte do princípio de que já concluiu os passos no [tutorial anterior](container-registry-tutorial-quick-task.md). Se ainda não o fez, conclua os passos na secção [Pré-requisitos](container-registry-tutorial-quick-task.md#prerequisites) do tutorial anterior antes de continuar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se quiser utilizar o Azure CLI localmente, tem de ter a versão Azure CLI **2.0.62** ou posteriormente instalada e sessão com [login az][az-login]. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI do Azure][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Criar uma tarefa em várias etapas

Agora que completou os passos necessários para permitir que as Tarefas ACR leiam o estado do compromisso e criem webhooks num repositório, criem uma tarefa em várias etapas que desencadeie a construção, a execução e o impulso de uma imagem de contentor.

### <a name="yaml-file"></a>Ficheiro YAML

Define os passos para uma tarefa em várias etapas num [ficheiro YAML](container-registry-tasks-reference-yaml.md). O primeiro exemplo de tarefa em várias `taskmulti.yaml`etapas para este tutorial está definido no ficheiro, que está na raiz do repo GitHub que clonou:

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Esta tarefa em várias etapas faz o seguinte:

1. Corre `build` um passo para construir uma imagem do Dockerfile no diretório de trabalho. A imagem visa `Run.Registry`o , o registo onde a tarefa é executada, e é marcado com um ID único de executo De Tarefas ACR. 
1. Corre `cmd` um passo para executar a imagem em um recipiente temporário. Este exemplo inicia um recipiente de longa duração em segundo plano e devolve o ID do recipiente e, em seguida, para o recipiente. Num cenário real, pode incluir passos para testar o recipiente de corrida para garantir que funciona corretamente.
1. Num `push` passo, empurra a imagem que foi construída para o registo de execução.

### <a name="task-command"></a>Comando de tarefas

Em primeiro lugar, preencha estas variáveis de ambiente da shell com os valores adequados para o seu ambiente. Este passo não é estritamente necessário, mas facilita um pouco a execução dos comandos da CLI do Azure com várias linhas neste tutorial. Se não povoar estas variáveis ambientais, deve substituir manualmente cada valor onde quer que apareça nos comandos de exemplo.

[![Lançamento emcamado](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Agora, crie a tarefa executando a seguinte [tarefa az acr criar][az-acr-task-create] comando:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Esta tarefa especifica que qualquer código *master* de tempo está comprometido com o `--context`ramo principal no repositório especificado por , As Tarefas ACR executarão a tarefa em várias etapas a partir do código nesse ramo. O ficheiro YAML `--file` especificado pela raiz do repositório define os passos. 

O resultado de um comando [az acr task create][az-acr-task-create] com êxito é semelhante ao seguinte:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>Testar o fluxo de trabalho em várias etapas

Para testar a tarefa em várias etapas, desencadeie-a manualmente executando o comando de execução de [tarefas az acr:][az-acr-task-run]

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Por predefinição, o comando `az acr task run` transmite a saída de registo para a consola quando executar o comando. A saída mostra o progresso de executar cada uma das etapas de tarefa. A saída abaixo é condensada para mostrar passos-chave.

```output
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Acionar uma compilação com uma consolidação

Agora que testou a tarefa através de uma execução manual, acione-a automaticamente com uma alteração do código de origem.

Em primeiro lugar, confirme que está no diretório que contém o clone local do [repositório][sample-repo]:

```console
cd acr-build-helloworld-node
```

Em seguida, execute os comandos seguintes para criar, consolidar e emitir um novo ficheiro para a bifurcação do repositório no GitHub:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Poderá ser-lhe pedido para fornecer as credenciais do GitHub, quando executar o comando `git push`. Forneça o seu nome de utilizador do GitHub e introduza o token de acesso pessoal (PAT) que criou anteriormente para a palavra-passe.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Depois de ter pressionado um compromisso com o seu repositório, o webhook criado pela ACR Tasks dispara e inicia a tarefa no Registo de Contentores Azure. Apresente os registos da compilação atualmente em execução para verificar e monitorizar o progresso da mesma:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

O resultado é semelhante ao seguinte, apresentando a compilação atualmente em execução (ou a última executada):

```output
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Listar as compilações

Para ver uma lista das execuções de tarefas que o ACR Tasks concluiu para o seu registo, execute o comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

O resultado do comando deve ter um aspeto semelhante ao que se segue. As execuções do ACR Tasks são apresentadas e “Consolidação de Git” aparece na coluna ACIONAR da tarefa mais recente:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Criar uma tarefa multi-registrada em várias etapas

A ACR Tasks, por padrão, tem permissões para empurrar ou retirar imagens do registo onde a tarefa é executado. É melhor executar uma tarefa em várias etapas que visa um ou mais registos para além do registo de execução. Por exemplo, você pode precisar de construir imagens em um registo, e armazenar imagens com diferentes tags em um segundo registro que é acedido por um sistema de produção. Este exemplo mostra-lhe como criar tal tarefa e fornecer credenciais para outro registo.

Se ainda não tem um segundo registo, crie um para este exemplo. Se precisar de um registo, veja o [tutorial anterior](container-registry-tutorial-quick-task.md) ou [Início Rápido: Criar um registo de contentor com a CLI do Azure](container-registry-get-started-azure-cli.md).

Para criar a tarefa, precisa do nome do servidor de login de registo, que é do formulário *mycontainerregistrydate.azurecr.io* (todas as minúsculas). Neste exemplo, utiliza-se o segundo registo para armazenar imagens marcadas por data de construção.

### <a name="yaml-file"></a>Ficheiro YAML

A segunda tarefa em várias etapas para `taskmulti-multiregistry.yaml`este tutorial está definida no ficheiro, que está na raiz do repo GitHub que clonou:

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Esta tarefa em várias etapas faz o seguinte:

1. Corre `build` dois passos para construir imagens do Dockerfile no diretório de trabalho:
    * O primeiro visa `Run.Registry`o , o registo onde a tarefa é executada, e é marcado com o ID executa as Tarefas ACR. 
    * O segundo visa o registo identificado pelo `regDate`valor de , que define quando cria `values.yaml` a tarefa `az acr task create`(ou fornece através de um ficheiro externo passado para ). Esta imagem está marcada com a data de execução.
1. Corre `cmd` um passo para executar um dos recipientes construídos. Este exemplo inicia um recipiente de longa duração em segundo plano e devolve o ID do recipiente e, em seguida, para o recipiente. Num cenário real, poderá testar um recipiente de corrida para garantir que funciona corretamente.
1. Num `push` passo, empurra as imagens que foram construídas, a primeira para o registo `regDate`de execução, a segunda para o registo identificado por .

### <a name="task-command"></a>Comando de tarefas

Utilizando as variáveis ambientais da concha definidas anteriormente, crie a tarefa executando a seguinte [tarefa az acr criar][az-acr-task-create] comando. Substitua o nome do seu registo por data de *registo de mycontainers*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Adicionar credencial de tarefa

Para empurrar as imagens para o `regDate`registo identificado pelo valor de , use o comando de adição de [tarefas az acr][az-acr-task-credential-add] para adicionar credenciais de login para esse registo à tarefa.

Para este exemplo, recomendamos que crie um diretor de [serviço](container-registry-auth-service-principal.md) com acesso ao registo com o papel *AcrPush.* Para criar o diretor de serviço, consulte este [script Azure CLI](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Passe o ID de aplicação `az acr task credential add` principal do serviço e a palavra-passe no seguinte comando:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

O CLI devolve o nome do servidor de login de registo que adicionou.

### <a name="test-the-multi-step-workflow"></a>Testar o fluxo de trabalho em várias etapas

Tal como no exemplo anterior, para testar a tarefa em várias etapas, desencadeie-a manualmente executando o comando de execução de [tarefas az acr.][az-acr-task-run] Para desencadear a tarefa com um compromisso com o repositório Git, consulte a secção [Desencadear uma construção com um compromisso](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Por predefinição, o comando `az acr task run` transmite a saída de registo para a consola quando executar o comando. Como antes, a saída mostra o progresso de executar cada uma das etapas de tarefa. A saída é condensada para mostrar passos-chave.

Saída:

```output
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar tarefas multi-etapas baseadas em vários contentores que disparam automaticamente quando compromete o código fonte a um repositório Git. Para características avançadas de tarefas em várias etapas, incluindo execução paralela e dependente de etapas, consulte a [referência YAML das Tarefas ACR.](container-registry-tasks-reference-yaml.md) Avance para o tutorial seguinte para saber como criar tarefas que acionam compilações quando a imagem de base de uma imagem do contentor é atualizada.

> [!div class="nextstepaction"]
> [Automatizar compilações ao atualizar a imagem de base](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
