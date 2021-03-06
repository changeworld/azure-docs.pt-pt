---
title: Construir, testar e colocar contentores no Serviço Azure Kubernetes utilizando ações gitHub
description: Saiba como usar as Ações GitHub para implantar o seu contentor na Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595370"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Ações gitHub para implantação para o serviço Kubernetes

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) dá-lhe a flexibilidade para construir um fluxo de trabalho de ciclo de vida de desenvolvimento de software automatizado. A ação [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) kubernetes facilita as implantações para os clusters do Serviço Azure Kubernetes. A ação define o contexto de cluster AKS alvo, que poderia ser usado por outras ações como [azure/k8s-deploy,](https://github.com/Azure/k8s-deploy/tree/master) [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) etc. ou executar quaisquer comandos kubectl.

Um fluxo de trabalho é definido por um ficheiro `/.github/workflows/` YAML (.yml) no caminho do seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho direcionado para aks, o ficheiro tem três secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | Iniciar sessão num registo de contentores privados (ACR) |
|**Compilação** | Construir & empurrar a imagem do recipiente  |
|**Implementar** | 1. Definir o cluster AKS alvo |
| |2. Criar um segredo genérico/estivador no cluster Kubernetes  |
||3. Implante-se para o cluster Kubernetes|

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Pode criar um diretor de [serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) utilizando o comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](https://docs.microsoft.com/cli/azure/). Pode executar este comando utilizando a [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Experimente..**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

No comando acima, substitua os espaços reservados pelo seu ID de subscrição e pelo grupo de recursos. A saída são as credenciais de atribuição de papéis que fornecem acesso ao seu recurso. O comando deve obter um objeto JSON semelhante a este.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Copie este objeto JSON, que pode usar para autenticar a partir do GitHub.

## <a name="configure-the-github-secrets"></a>Configure os segredos do GitHub

Siga os passos para configurar os segredos:

1. No [GitHub,](https://github.com/)navegue até ao seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**.

    ![segredos](media/kubernetes-action/secrets.png)

2. Colar o conteúdo do `az cli` comando acima como o valor da variável secreta. Por exemplo, `AZURE_CREDENTIALS`.

3. Da mesma forma, defina os seguintes segredos adicionais para as credenciais de registo de contentores e coloque-os em ação de login do Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Verá os segredos como mostrado abaixo uma vez definidos.

    ![kubernetes-segredos](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Construa uma imagem de contentor e desloque-se para o cluster do Serviço Azure Kubernetes

A construção e impulso das `Azure/docker-login@v1` imagens do recipiente é feita com recurso à ação. Para implantar uma imagem de contentor para aks, terá de utilizar a `Azure/k8s-deploy@v1` ação. Esta ação tem cinco parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **espaço de nome** | (Opcional) Escolha o espaço de nome kubernetes alvo. Se o espaço de nome não for fornecido, os comandos serão executados no espaço de nome padrão | 
| **manifesta-se** |  (Obrigatório) Caminho para os ficheiros manifestos, que serão usados para implantação |
| **imagens** | (Opcional) URL de recursos totalmente qualificados das imagens a utilizar para substituições nos ficheiros manifestos |
| **imagempullsecrets** | (Opcional) Nome de um segredo de registo de estivadores que já foi criado dentro do aglomerado. Cada um destes nomes secretos é adicionado sob o campo ImagePullSecrets para as cargas de trabalho encontradas nos ficheiros manifestos de entrada |
| **kubectl-versão** | (Opcional) Instala uma versão específica do kubectl binary |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Desdobre para o cluster de serviço saque de Azure Kubernetes

Fluxo de trabalho de ponta a ponta para construir imagens de contentores e implantação para um cluster de serviço Azure Kubernetes.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>Passos seguintes

Pode encontrar o nosso conjunto de Ações em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar o GitHub para CI/CD e implementar as suas aplicações para o Azure.

- [setup-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-context](https://github.com/Azure/k8s-set-context)

- [aks-set-context](https://github.com/Azure/aks-set-context)

- [k8s-create-secret](https://github.com/Azure/k8s-create-secret)

- [k8s-deploy](https://github.com/Azure/k8s-deploy)

- [webapps-contentor-deploy](https://github.com/Azure/webapps-container-deploy)

- [ações-amostras de fluxo de trabalho](https://github.com/Azure/actions-workflow-samples)
