---
title: Utilize Azure AD e RBAC para clusters
titleSuffix: Azure Kubernetes Service
description: Saiba como utilizar a adesão do grupo Azure Ative Directory para restringir o acesso aos recursos de cluster utilizando o controlo de acesso baseado em papéis (RBAC) no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: ad195085c049776bf0db418c57f2c72830f1adff
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803574"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Controlar o acesso aos recursos de cluster utilizando o controlo de acesso baseado em funções e identidades do Azure Ative Directory no Serviço Azure Kubernetes

O Serviço Azure Kubernetes (AKS) pode ser configurado para utilizar o Azure Ative Directory (AD) para autenticação do utilizador. Nesta configuração, você instindo um cluster AKS usando um símbolo de autenticação Azure AD. Também pode configurar o controlo de acesso baseado em funções da Kubernetes (RBAC) para limitar o acesso aos recursos de cluster baseados na identidade ou na adesão de um utilizador.

Este artigo mostra-lhe como usar a adesão do grupo Azure AD para controlar o acesso a espaços de nome e recursos de cluster usando kubernetes RBAC em um cluster AKS. Grupos de exemplo e utilizadores são criados em Azure AD, então roles e RoleBindings são criados no cluster AKS para conceder as permissões apropriadas para criar e visualizar recursos.

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um cluster AKS existente habilitado com a integração de AD Azure. Se precisar de um cluster AKS, consulte [Integrate Azure Ative Directy com AKS][azure-ad-aks-cli].

Precisa da versão Azure CLI 2.0.61 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Criar grupos de demonstração em Azure AD

Neste artigo, vamos criar duas funções de utilizador que podem ser usadas para mostrar como kubernetes RBAC e Azure AD controlam o acesso aos recursos de cluster. São utilizadas duas funções exemplo seguem:

* **Desenvolvedor de aplicações**
    * Um utilizador chamado *aksdev* que faz parte do grupo *appdev.*
* **Engenheiro de fiabilidade do local**
    * Um utilizador chamado *akssre* que faz parte do grupo *opssre.*

Em ambientes de produção, pode utilizar utilizadores e grupos existentes dentro de um inquilino da AD Azure.

Primeiro, obtenha a identificação de recursos do seu cluster AKS usando o comando [az aks show.][az-aks-show] Atribuir o ID do recurso a uma variável chamada *AKS_ID* para que possa ser referenciado em comandos adicionais.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Crie o primeiro grupo de exemplo em Azure AD para os desenvolvedores de aplicações que usam o [grupo az ad criar][az-ad-group-create] comando. O exemplo seguinte cria um grupo chamado *appdev:*

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Agora, crie uma atribuição de funções Azure para o grupo *appdev* usando a atribuição de [funções az criar][az-role-assignment-create] comando. Esta atribuição permite que qualquer `kubectl` membro do grupo utilize para interagir com um cluster AKS, concedendo-lhes a função de utilizador do Cluster de Serviço Sapateado *Azure Kubernetes*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Se receber um erro `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`como , espere alguns segundos para que o id de objeto `az role assignment create` do grupo Azure AD propague através do diretório, tente novamente o comando.

Crie um segundo grupo de exemplo, este para ASS chamado *opssre:*

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Mais uma vez, crie uma atribuição de funções azure para conceder aos membros do grupo a Função de Utilizador do Cluster de *ServiçoS Azure Kubernetes:*

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Criar utilizadores de demonstração em Azure AD

Com dois grupos de exemplo criados em Azure AD para os nossos desenvolvedores de aplicações e SREs, agora permite criar dois exemplos de utilizadores. Para testar a integração rBAC no final do artigo, você inscreveu-se no cluster AKS com estas contas.

Crie a primeira conta de utilizador em Azure AD utilizando o [utilizador az ad criar][az-ad-user-create] comando.

O exemplo seguinte cria um utilizador com o nome de exibição *AKS Dev* e o nome principal do utilizador (UPN) de `aksdev@contoso.com`. Atualize a UPN para incluir um domínio verificado para o seu inquilino Azure AD (substitua *contoso.com* pelo seu próprio domínio) e forneça a sua própria credencial segura: `--password`

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Adicione agora o utilizador ao grupo *appdev* criado na secção anterior utilizando o comando de adição do membro do [grupo az ad:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Crie uma segunda conta de utilizador. O exemplo seguinte cria um utilizador com o nome de exibição *AKS SRE* e o nome principal do utilizador (UPN) de `akssre@contoso.com`. Mais uma vez, atualize a UPN para incluir um *contoso.com* domínio verificado para o seu inquilino `--password` Azure AD (substitua contoso.com pelo seu próprio domínio), e forneça a sua própria credencial segura:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Criar os recursos de cluster AKS para devs de apps

Os grupos e utilizadores da AD Azure estão agora criados. Foram criadas atribuições de funções Azure para que os membros do grupo se conectassem a um cluster AKS como um utilizador regular. Agora, vamos configurar o cluster AKS para permitir que estes diferentes grupos tenham acesso a recursos específicos.

Primeiro, obtenha as credenciais de administração do cluster usando o comando [az aks get-credentials.][az-aks-get-credentials] Numa das seguintes secções, obtém-se as credenciais regulares de cluster de *utilizadores* para ver o fluxo de autenticação da AD Azure em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Crie um espaço de nome no cluster AKS usando o [kubectl criar][kubectl-create] comando espaço de nome. O exemplo seguinte cria um nome *dev*nome:

```console
kubectl create namespace dev
```

Em Kubernetes, *as Funções* definem as permissões para conceder, e *as RoleBindings* aplicam-nas aos utilizadores ou grupos pretendidos. Estas atribuições podem ser aplicadas a um determinado espaço de nome, ou em todo o cluster. Para mais informações, consulte [a utilização da autorização RBAC][rbac-authorization].

Primeiro, crie um papel para o espaço de nome de *v.* Este papel concede permissões completas ao espaço de nome. Em ambientes de produção, pode especificar mais permissões granulares para diferentes utilizadores ou grupos.

Crie um `role-dev-namespace.yaml` ficheiro nomeado e cola o seguinte manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Crie a Função utilizando o [kubectl aplique][kubectl-apply] o comando e especifique o nome de ficheiro do seu manifesto YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Em seguida, obtenha o ID de recursos para o grupo *appdev* usando o comando de [show de grupo az az.][az-ad-group-show] Este grupo é definido como tema de uma RoleBinding no próximo passo.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Agora, crie um RoleBinding para o grupo *appdev* usar o Papel anteriormente criado para acesso ao espaço de nome. Crie um `rolebinding-dev-namespace.yaml` ficheiro nomeado e cola o seguinte manifesto YAML. Na última linha, substitua o *grupoObjectId* pela saída id do objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Crie o RoleBinding utilizando o [kubectl aplique][kubectl-apply] o comando e especifique o nome de ficheiro do seu manifesto YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Criar os recursos de cluster AKS para as SREs

Agora, repita os passos anteriores para criar um espaço de nome, role e RoleBinding para as SREs.

Em primeiro lugar, crie um espaço de nome para o *sre* usando o [kubectl criar][kubectl-create] comando espaço de nome:

```console
kubectl create namespace sre
```

Crie um `role-sre-namespace.yaml` ficheiro nomeado e cola o seguinte manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Crie a Função utilizando o [kubectl aplique][kubectl-apply] o comando e especifique o nome de ficheiro do seu manifesto YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Obtenha o ID de recursos para o grupo *opssre* usando o comando de [show de grupo az az:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Crie um RoleBinding para o grupo *opssre* usar o Papel anteriormente criado para acesso ao espaço de nome. Crie um `rolebinding-sre-namespace.yaml` ficheiro nomeado e cola o seguinte manifesto YAML. Na última linha, substitua o *grupoObjectId* pela saída id do objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Crie o RoleBinding utilizando o [kubectl aplique][kubectl-apply] o comando e especifique o nome de ficheiro do seu manifesto YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagir com recursos de cluster usando identidades azure AD

Agora, vamos testar as permissões esperadas funcionam quando se cria e gere recursos num cluster AKS. Nestes exemplos, você agenda e vê cápsulas no espaço de nome atribuído do utilizador. Depois, tenta agendar e ver cápsulas fora do espaço de nome designado.

Primeiro, redefinir o contexto *kubeconfig* usando o comando [az aks get-credentials.][az-aks-get-credentials] Numa secção anterior, define o contexto utilizando as credenciais de administração do cluster. O utilizador de administração contorna o sinal de Anúncio Azure em solicitações. Sem `--admin` o parâmetro, aplica-se o contexto do utilizador que exige que todos os pedidos sejam autenticados utilizando a AD Azure.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Agende uma cápsula básica NGINX utilizando o comando [de execução kubectl][kubectl-run] no espaço de nome de *v:*

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Como sinal em pronta, insira `appdev@contoso.com` as credenciais para a sua própria conta criadas no início do artigo. Uma vez assinado com sucesso, o token da `kubectl` conta está em cache para futuros comandos. O NGINX está programado com sucesso, como mostra a seguinte saída de exemplo:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Agora use o [kubectl obter comando de cápsulas][kubectl-get] para ver cápsulas no espaço de nome *de v.*

```console
kubectl get pods --namespace dev
```

Como mostra a seguinte saída de exemplo, a cápsula NGINX está a *funcionar*com sucesso:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Criar e ver recursos de cluster fora do espaço de nome atribuído

Agora tente ver cápsulas fora do espaço de nome *de v.* Use o [kubectl obter comando de casulos][kubectl-get] novamente, desta vez para ver `--all-namespaces` o seguinte:

```console
kubectl get pods --all-namespaces
```

A adesão ao grupo do utilizador não tem uma Função Kubernetes que permita esta ação, como mostra a seguinte saída de exemplo:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Da mesma forma, tente agendar uma cápsula em diferentes espaços de nome, como o espaço de nome *sre.* A adesão ao grupo do utilizador não se alinha com uma Função Kubernetes e RoleBinding para conceder estas permissões, como mostra a seguinte saída de exemplo:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Teste o acesso sre aos recursos de cluster AKS

Para confirmar que a nossa filiação no grupo Azure AD e kubernetes RBAC funcionam corretamente entre diferentes utilizadores e grupos, experimente os comandos anteriores quando assinado como utilizador *opssre.*

Redefinir o contexto *kubeconfig* utilizando o comando [az aks get-credentials][az-aks-get-credentials] que limpa o símbolo de autenticação previamente cached para o utilizador *aksdev:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Tente agendar e ver cápsulas no espaço de nome *sre* atribuído. Quando solicitado, inscreva-se `opssre@contoso.com` com as suas próprias credenciais criadas no início do artigo:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Como mostra a saída de exemplo seguinte, pode criar e ver com sucesso as cápsulas:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Agora, tente visualizar ou agendar cápsulas fora do espaço de nome SRE atribuído:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Estes `kubectl` comandos falham, como mostra a seguinte saída de exemplo. A filiação do grupo do utilizador e a Kubernetes Role and RoleBindings não concedem permissões para criar ou gestorde recursos em outros espaços de nome:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, criou recursos no cluster AKS e utilizadores e grupos em Azure AD. Para limpar todos estes recursos, executar os seguintes comandos:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como proteger os clusters kubernetes, consulte opções de [acesso e identidade para AKS)][rbac-authorization].

Para obter as melhores práticas de controlo de identidade e recursos, consulte [as melhores práticas de autenticação e autorização no AKS.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
