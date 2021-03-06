---
title: Tutorial - Criar um cluster Kubernetes com o Serviço Azure Kubernetes (AKS) usando terrafora
description: Neste tutorial, você cria um Cluster Kubernetes com Serviço Azure Kubernetes e Terraform
keywords: azure devops terraform aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: b7a84d7562e99e53ff7be75b7d40795cd3f9e203
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618927"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Tutorial: Criar um cluster Kubernetes com o Serviço Azure Kubernetes usando terrafora

[O Serviço Azure Kubernetes (AKS)](/azure/aks/) gere o seu ambiente kubernetes hospedado. A AKS permite-lhe implementar e gerir aplicações contentorizadas sem conhecimentos de orquestração de contentores. A AKS também lhe permite fazer muitas operações de manutenção comuns sem desligar a sua aplicação. Estas operações incluem o fornecimento, a atualização e a escala ção de recursos a pedido.

Neste tutorial, aprende-se a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Utilizar o HCL (HashiCorp Language) para definir um cluster do Kubernetes
> * Utilizar o Terraform e o AKS para criar um cluster do Kubernetes
> * Utilizar a ferramenta kubectl para testar a disponibilidade de um cluster do Kubernetes

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Configurar o Terraform**: Siga as instruções no artigo [Terraform e configuração do acesso ao Azure](terraform-install-configure.md)

- **Diretor de serviço Azure**: Siga as instruções na secção **Principal Criar o serviço** principal no artigo, Criar um serviço [Azure com o Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Tome nota dos valores para appId, displayName, password, e tenant.

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios

O primeiro passo é criar o diretório que mantenha os seus ficheiros de configuração do Terraform para o exercício.

1. Navegue pelo [portal Azure.](https://portal.azure.com)

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se ainda não tiver selecionado um ambiente, selecione **Bash** como o seu ambiente.

    ![Comando do Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Mude para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório denominado `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Mude para o diretório novo:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Declarar o fornecedor do Azure

Crie o ficheiro de configuração Terraform que declara o fornecedor do Azure.

1. No Cloud Shell, crie um ficheiro com o nome `main.tf`.

    ```bash
    code main.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
    provider "azurerm" {
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Guarde o ficheiro**&lt;(CTRL>S)** e saia do editor (**&lt;Ctrl>Q**).

## <a name="define-a-kubernetes-cluster"></a>Definir um cluster do Kubernetes

Crie o ficheiro de configuração Terraform que declare os recursos para o cluster do Kubernetes.

1. No Cloud Shell, crie um ficheiro com o nome `k8s.tf`.

    ```bash
    code k8s.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        default_node_pool {
            name            = "agentpool"
            node_count      = var.agent_count
            vm_size         = "Standard_DS1_v2"
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    O código anterior define o nome do cluster, localização e nome do grupo de recursos. O prefixo para o nome de domínio totalmente qualificado (FQDN) também está definido. O FQDN é usado para aceder ao cluster.

    O `linux_profile` registo permite configurar as definições que permitem a sessão nos nódosos do trabalhador utilizando o SSH.

    No AKS, paga apenas os nós de trabalho. Os `default_node_pool` registos mostram os detalhes destes nódosos operários. O `default_node_pool record` número de nós operários para criar e o tipo de nós dos trabalhadores. Se precisar de escalar ou escalar o cluster no `count` futuro, modifica o valor neste registo.

1. Guarde o ficheiro**&lt;(CTRL>S)** e saia do editor (**&lt;Ctrl>Q**).

## <a name="declare-the-variables"></a>Declarar as variáveis

1. No Cloud Shell, crie um ficheiro com o nome `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Guarde o ficheiro**&lt;(CTRL>S)** e saia do editor (**&lt;Ctrl>Q**).

## <a name="create-a-terraform-output-file"></a>Criar um ficheiro de saída do Terraform

Os [ficheiros de saída do Terraform](https://www.terraform.io/docs/configuration/outputs.html) permitem-lhe definir valores que serão realçados para o utilizador quando o Terraform aplicar um plano e podem ser consultados com o comando `terraform output`. Nesta secção, vai criar um ficheiro de saída que permite o acesso ao cluster com [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. No Cloud Shell, crie um ficheiro com o nome `output.tf`.

    ```bash
    code output.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }
    ```

1. Guarde o ficheiro**&lt;(CTRL>S)** e saia do editor (**&lt;Ctrl>Q**).

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Configurar o armazenamento do Azure para armazenar estado do Terraform

O Terraform monitoriza o estado localmente através do ficheiro `terraform.tfstate`. Este padrão funciona bem num ambiente individual. Num ambiente multipessoal, o [armazenamento azure](/azure/storage/) é usado para rastrear o estado.

Nesta secção, vê como fazer as seguintes tarefas:
- Recuperar informações sobre a conta de armazenamento (nome da conta e chave da conta)
- Crie um recipiente de armazenamento no qual serão armazenadas informações do estado terraforme.

1. No portal do Azure, selecione **Todos os serviços** no menu à esquerda.

1. Selecione **Contas de armazenamento**.

1. No separador **Contas de armazenamento**, selecione o nome da conta de armazenamento na qual o Terraform deve armazenar o estado. Por exemplo, pode utilizar a conta de armazenamento criada quando abriu o Cloud Shell pela primeira vez.  O nome de conta de armazenamento criado pelo Cloud Shell costuma começar por `cs`, seguido de uma cadeia aleatória de números e letras. Tome nota da conta de armazenamento selecionada. Este valor é necessário mais tarde.

1. No separador de conta de armazenamento, selecione **Chaves de acesso**.

    ![Menu de conta de armazenamento](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Anote o valor **key1** **key**. (Selecionar o ícone à direita da chave copia o valor para a área de transferência.)

    ![Chaves de acesso da conta de armazenamento](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Na Cloud Shell, crie um recipiente na sua conta de armazenamento Azure. Substitua os espaços reservados por valores adequados para o seu ambiente.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Criar o cluster do Kubernetes

Nesta secção, vê como utilizar `terraform init` o comando para criar os recursos definidos nos ficheiros de configuração que criou nas secções anteriores.

1. Em Cloud Shell, inicialize terraforma. Substitua os espaços reservados por valores adequados para o seu ambiente.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    O `terraform init` comando mostra o sucesso de inicializar o backend e o plug-in do fornecedor:

    ![Exemplo de resultados "terraform init"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exporte as credenciais do principal de serviço. Substitua os espaços reservados por valores adequados do seu diretor de serviço.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. Execute o comando `terraform plan` para criar o plano do Terraform que define os elementos de infraestrutura. 

    ```bash
    terraform plan -out out.plan
    ```

    O comando `terraform plan` mostra os recursos que serão criados quando executar o comando `terraform apply`:

    ![Exemplo de resultados "terraform plan"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Execute o comando `terraform apply` para aplicar o plano para criar o cluster do Kubernetes. O processo para criar um cluster Kubernetes pode demorar vários minutos, resultando na sessão cloud Shell. Se a sessão cloud Shell passar, pode seguir os passos na secção "Recuperar de um intervalo de tempo da Cloud Shell" para lhe permitir completar o tutorial.

    ```bash
    terraform apply out.plan
    ```

    O comando `terraform apply` mostra os resultados de criar os recursos definidos nos seus ficheiros de configuração:

    ![Exemplo de resultados "terraform apply"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. No portal Azure, selecione **Todos os recursos** no menu esquerdo para ver os recursos criados para o seu novo cluster Kubernetes.

    ![Todos os recursos no portal Azure](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Recuperar de um tempo limite do Cloud Shell

Se a sessão cloud Shell se esgotar, pode fazer os seguintes passos para recuperar:

1. Inicie uma sessão do Cloud Shell.

1. Mude para o diretório com os seus ficheiros de configuração do Terraform.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Execute o seguinte comando:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Testar o cluster do Kubernetes

As ferramentas do Kubernetes podem ser utilizadas para verificar o cluster acabado de criar.

1. Obtenha a configuração do Kubernetes do estado Terraform e armazene-a num ficheiro que o kubectl possa ler.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Defina uma variável de ambiente para que o kubectl escolha a configuração correta.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Verifique o estado de funcionamento do cluster.

    ```bash
    kubectl get nodes
    ```

    Deverá ver os detalhes dos seus nós de trabalho e estes deverão ter um estado **Ready**, conforme mostrado na seguinte imagem:

    ![A ferramenta kubectl permite-lhe verificar o estado de funcionamento do seu cluster do Kubernetes](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Monitorizar o estado de funcionamento e os registos

Quando o cluster do AKS foi criado, a monitorização foi ativada para capturar métricas de estado de funcionamento dos nós do cluster e dos pods. Estas métricas de estado de funcionamento estão disponíveis no portal do Azure. Para obter mais informações sobre a monitorização do estado de funcionamento dos contentores, veja [Monitorizar o estado de funcionamento do Azure Kubernetes Service](/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre a utilização da Terraform em Azure](/azure/terraform)