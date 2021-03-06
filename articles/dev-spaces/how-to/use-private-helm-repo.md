---
title: Como usar um repositório privado Helm em Espaços Azure Dev
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Use um repositório de Helm privado num espaço Azure Dev.
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contentores Azure, contentores, Helm
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240476"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Use um repositório de Helm privado em Espaços Azure Dev

[Helm][helm] é um gestor de pacotes para kubernetes. Helm usa um formato [gráfico][helm-chart] para embalar dependências. Os gráficos de leme são armazenados num repositório, que pode ser público ou privado. A Azure Dev Spaces só recupera gráficos helm de repositórios públicos ao executar a sua aplicação. Nos casos em que o repositório Helm é privado ou o Azure Dev Spaces não pode aceder-lhe, pode adicionar um gráfico desse repositório diretamente à sua aplicação. A adição do gráfico permite que a Azure Dev Spaces execute a sua aplicação sem ter acesso ao repositório privado Helm.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Adicione o repositório privado Helm à sua máquina local

Utilize a atualização de [repo de helm e][helm-repo-add] helm [repo][helm-repo-update] para aceder ao repositório helm privado da sua máquina local.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Adicione o gráfico à sua aplicação

Navegue para o diretório `azds prep`do seu projeto e corra.

```cmd
azds prep --enable-ingress
```

> [!TIP]
> O `prep` comando tenta gerar [um dockerfile e um gráfico helm](../how-dev-spaces-works-prep.md#prepare-your-code) para o seu projeto. A Azure Dev Spaces utiliza estes ficheiros para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

Crie um ficheiro [requisitos.yaml][helm-requirements] com a sua ficha no diretório de gráficos da sua aplicação. Por exemplo, se a sua aplicação for nomeada *app1,* criará *gráficos/app1/requisitos.yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Navegue para o diretório de gráficos da sua aplicação e use a [atualização][helm-dependency-update] da dependência do leme para atualizar as dependências helm para a sua aplicação e descarregue o gráfico do repositório privado.

```cmd
helm dependency update
```

Verifique se um subdiretório de *gráficos* com um ficheiro *TGZ* foi adicionado ao diretório de gráficos da sua aplicação. Por exemplo, *gráficos/app1/charts/mychart-0.1.0.tgz*.

A ficha do seu repositório privado Helm foi descarregada e adicionada ao seu projeto. Remova os *requisitos.yaml* ficheiro para que a Dev Spaces não tente atualizar esta dependência.

## <a name="run-your-application"></a>Executar a aplicação

Navegue para o diretório raiz `azds up` do seu projeto e corra para verificar se a sua aplicação corre com sucesso no seu espaço de dev.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [Helm e como funciona.][helm]

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
