---
title: Executar contentores privilegiados num aglomerado de OpenShift de chapéu vermelho azul [ Microsoft Docs
description: Executar contentores privilegiados para monitorizar a segurança e o cumprimento.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, aquasec, twistlock, chapéu vermelho
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271363"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Executar contentores privilegiados num cluster do Azure Red Hat OpenShift

Não se pode executar recipientes privilegiados arbitrários em aglomerados Azure Red Hat OpenShift.
Duas soluções de monitorização de segurança e conformidade são autorizadas a funcionar em clusters ARO.
Este documento descreve as diferenças da documentação genérica de implementação openshift dos fornecedores de produtos de segurança.


Leia estas instruções antes de seguir as instruções do vendedor.
Os títulos da secção em passos específicos do produto abaixo referem-se diretamente aos títulos da secção na documentação dos fornecedores.

## <a name="before-you-begin"></a>Antes de começar

A documentação da maioria dos produtos de segurança pressupõe que você tem privilégios de administração de cluster.
Os administradores dos clientes não têm todos os privilégios no Azure Red Hat OpenShift. As permissões necessárias para modificar os recursos em todo o cluster são limitadas.

Em primeiro lugar, certifique-se de que o utilizador está `oc get scc`ligado ao cluster como administrador do cliente, executando . Todos os utilizadores que são membros do grupo de administração do cliente têm permissões para visualizar os Constrangimentos de Contexto de Segurança (SCCs) no cluster.

Em seguida, `oc` certifique-se `3.11.154`de que a versão binária é .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Passos específicos do produto para a Segurança Aqua
As instruções de base que serão modificadas podem ser encontradas na documentação de [implementação](https://docs.aquasec.com/docs/openshift-red-hat)da Segurança Aqua . Os passos aqui serão executados em conjunto com a documentação de implantação do Aqua.

O primeiro passo é anotar os CSE necessários que serão atualizados. Estas anotações impedem que o Sync Pod do cluster reverta quaisquer alterações a estes SSCs.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Passo 1: Preparar pré-requisitos
Lembre-se de iniciar sessão no cluster como administrador de clientes aro em vez da função de administrador de cluster.

Crie o projeto e a conta de serviço.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Em vez de atribuir o papel de leitor de cluster, atribuir a função de cluster de cliente-administrador à conta aqua com o seguinte comando.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Continue a seguir as instruções restantes no passo 1.  Estas instruções descrevem a criação do segredo para o registo aqua.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Passo 2: Implementar o Servidor Aqua, Base de Dados e Gateway
Siga os passos fornecidos na documentação Aqua para a instalação do aqua-console.yaml.

Modificar o `aqua-console.yaml`. fornecido .  Retire os dois objetos `kind: ClusterRole` `kind: ClusterRoleBinding`superiores rotulados e .  Estes recursos não serão criados, uma vez que o administrador `ClusterRole` `ClusterRoleBinding` do cliente não tem permissão neste momento para modificar e objetos.

A segunda modificação `kind: Route` será na `aqua-console.yaml`parte do . Substitua o seguinte yaml para o `kind: Route` objeto no `aqua-console.yaml` ficheiro.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Siga as instruções restantes.

### <a name="step-3-login-to-the-aqua-server"></a>Passo 3: Iniciar sessão no Aqua Server
Esta secção não é modificada de forma alguma.  Siga a documentação aqua.

Utilize o seguinte comando para obter o endereço Aqua Console.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Passo 4: Implementar executores aquáticas
Detete os seguintes campos ao utilizar os executores:

| Campo          | Valor         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiçoConta | aqua-conta  |
| Project        | aqua-segurança |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Passos específicos do produto para prisma cloud / Twistlock

As instruções de base que vamos modificar podem ser encontradas na documentação de implantação da [Nuvem prisma.](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Comece por instalar `twistcli` a ferramenta conforme descrito nas secções "Instalar prisma cloud" e "Descarregue o software Prisma Cloud".

Criar um novo projeto OpenShift
```
oc new-project twistlock
```

Saltar a secção opcional "Empurre as imagens da Nuvem Prisma para um registo privado". Não vai funcionar no Azure Red Hat Openshift. Em vez disso, utilize o registo online.

Pode seguir a documentação oficial aplicando as correções descritas abaixo.
Comece com a secção "Instalar consola".

### <a name="install-console"></a>Instalar consola

Durante `oc create -f twistlock_console.yaml` o Passo 2, terá um Erro ao criar o espaço de nome.
Pode ignorá-lo com segurança, o espaço de `oc new-project` nome foi criado anteriormente com o comando.

Utilização `azure-disk` para o tipo de armazenamento.

### <a name="create-an-external-route-to-console"></a>Criar uma rota externa para consola

Pode seguir a documentação ou as instruções abaixo se preferir o comando oc.
Copie a seguinte definição de Rota para um ficheiro chamado twistlock_route.yaml no seu computador
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
em seguida, executar:
```
oc create -f twistlock_route.yaml
```

Pode obter o URL atribuído à consola Twistlock com este comando:`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Configure consola

Siga a documentação Twistlock.

### <a name="install-defender"></a>Instalar Defender

Durante `oc create -f defender.yaml` o Passo 2, terá erros ao criar o Cluster Role e Cluster Role Binding.
Pode ignorá-los.

Os defensores serão implantados apenas em nódeos de computação. Não tens de os limitar com um selecionador de nó.
