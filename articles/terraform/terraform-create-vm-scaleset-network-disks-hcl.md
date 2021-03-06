---
title: Tutorial - Criar um conjunto de escala de máquina virtual Azure usando terrafora
description: Aprenda a usar terrafora para configurar e verum conjunto de escala de máquina virtual Azure.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 4e445d5e6ae4b7fc4528c6d61ee2bc86870827b1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472235"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-using-terraform"></a>Tutorial: Criar um conjunto de escala de máquina virtual Azure usando terrafora

Os conjuntos de escala de [máquinas virtuais Azure](/azure/virtual-machine-scale-sets) permitem configurar VMs idênticos. O número de casos de VM pode ajustar-se com base na procura ou num horário. Para mais informações, consulte [Automaticamente uma escala de máquina virtual definida no portal Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal).

Neste tutorial, aprende-se a usar a [Azure Cloud Shell](/azure/cloud-shell/overview) para fazer as seguintes tarefas:

> [!div class="checklist"]
> * Configurar uma implementação do Terraform
> * Utilizar variáveis e resultados para a implementação do Terraform
> * Criar e implementar a infraestrutura de rede
> * Criar e implementar um conjunto de dimensionamento de máquinas virtuais e ligá-lo à rede
> * Criar e implementar uma jumpbox para ligar às VMs através de SSH

> [!NOTE]
> A versão mais recente dos ficheiros de configuração Terraform utilizados neste artigo está no [repositório Terraform Incrível no GitHub](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Instalar o Terraform**: Siga as instruções no artigo [Terraform e configuração do acesso ao Azure](terraform-install-configure.md)

- **Crie um par de chaves SSH**: Para mais informações, consulte Como criar e utilizar um par de chaves [públicos e privados SSH para VMs Linux em Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios

1. Navegue pelo [portal Azure.](https://portal.azure.com)

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se ainda não tiver selecionado um ambiente, selecione **Bash** como o seu ambiente.

    ![Comando do Cloud Shell](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Mude para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório denominado `vmss`.

    ```bash
    mkdir vmss
    ```

1. Mude para o diretório novo:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Criar o ficheiro de definições de variáveis
Nesta secção, vai definir as variáveis que personalizam os recursos criados pelo Terraform.

Dentro da Casca de Nuvem Azure, faça os seguintes passos:

1. Crie um ficheiro com o nome `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Cole o seguinte código no editor:

   ```hcl
   variable "location" {
    description = "The location where resources will be created"
   }

   variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
   }

   variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
   }
   ```

1. Guarde o ficheiro**&lt;(CTRL>S)** e saia do editor (**&lt;Ctrl>Q**).

## <a name="create-the-output-definitions-file"></a>Criar o ficheiro de definições de saída
Nesta secção, vai criar o ficheiro que descreve a saída após a implementação.

Dentro da Casca de Nuvem Azure, faça os seguintes passos:

1. Crie um ficheiro com o nome `output.tf`.

    ```bash
    code output.tf
    ```

1. Cole o seguinte código no editor para expor o nome de domínio completamente qualificado (FQDN) para as máquinas virtuais.
   :

   ```hcl
    output "vmss_public_ip" {
        value = azurerm_public_ip.vmss.fqdn
    }
   ```

1. Guarde o ficheiro**&lt;(CTRL>S)** e saia do editor (**&lt;Ctrl>Q**).

## <a name="define-the-network-infrastructure-in-a-template"></a>Definir a infraestrutura de rede num modelo
Nesta secção, vai criar a seguinte infraestrutura de rede num novo grupo de recursos do Azure:

  - Uma rede virtual (VNET) com o espaço de endereços 10.0.0.0/16
  - Uma sub-rede com o espaço de endereços 10.0.2.0/24
  - Dois endereços IP públicos. Um utilizado pelo balanceador de carga do conjunto de dimensionamento de máquinas virtuais e o outro utilizado para ligar à jumpbox SSH.

Dentro da Casca de Nuvem Azure, faça os seguintes passos:

1. Crie um ficheiro com o nome `vmss.tf` para descrever a infraestrutura do conjunto de dimensionamento de máquinas virtuais.

    ```bash
    code vmss.tf
    ```

1. Cole o seguinte código no fim do ficheiro para expor o nome de domínio completamente qualificado (FQDN) para as máquinas virtuais.

   ```hcl
   resource "azurerm_resource_group" "vmss" {
    name     = var.resource_group_name
    location = var.location
    tags     = var.tags
   }

   resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
   }

   resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    tags                = var.tags
   }

   resource "azurerm_subnet" "vmss" {
    name                 = "vmss-subnet"
    resource_group_name  = azurerm_resource_group.vmss.name
    virtual_network_name = azurerm_virtual_network.vmss.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "vmss" {
    name                         = "vmss-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = random_string.fqdn.result
    tags                         = var.tags
   }
   ```

1. Guarde o ficheiro**&lt;(CTRL>S)** e saia do editor (**&lt;Ctrl>Q**).

## <a name="provision-the-network-infrastructure"></a>Aprovisionar a infraestrutura de rede
Utilizando a Casca de Nuvem Azure a partir do diretório onde criou os ficheiros de configuração (.tf) fazem os seguintes passos:

1. Inicie o Terraform.

   ```bash
   terraform init
   ```

1. Execute o seguinte comando para implementar a infraestrutura definida no Azure.

   ```bash
   terraform apply
   ```

   Terraform pede-lhe `location` um valor `location` à medida `variables.tf`que a variável é definida em , mas nunca está definida. Pode introduzir qualquer localização válida, como, por exemplo, "E.U.A. Oeste", seguido de Enter (utilize parênteses em qualquer valor com espaços).

1. O Terraform imprime a saída conforme definido no ficheiro `output.tf`. Como mostra a seguinte imagem, o FQDN `<ID>.<location>.cloudapp.azure.com`assume o seguinte formulário: . O ID é um valor computacional e a localização é o valor proporcionado ao executar terrafora.

   ![Conjunto de escala de máquina virtual totalmente qualificado nome de domínio para endereço IP público](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. No menu do portal do Azure, selecione **Grupos de recursos** no menu principal.

1. No separador **Grupos de recursos**, selecione **myResourceGroup** para ver os recursos que foram criados pelo Terraform.
   ![Recursos de rede do conjunto de dimensionamento de máquinas virtuais](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Adicionar um conjunto de dimensionamento de máquinas virtuais

Nesta secção, vai aprender a adicionar os seguintes recursos ao modelo:

- Um balanceador de carga do Azure e regras para servir a aplicação e ligá-la ao endereço IP público configurado anteriormente neste artigo
- Um conjunto de endereços de back-end do Azure e atribuí-lo ao balanceador de carga
- Uma porta de sonda de estado de funcionamento utilizada pela aplicação e configurada no balanceador de carga
- Um conjunto de dimensionamento de máquinas virtuais por detrás do balanceador de carga que é executado na VNET implementada anteriormente neste artigo
- [Nginx](https://nginx.org/) nos nós do conjunto de dimensionamento de máquinas virtuais através de [cloud-init](https://cloudinit.readthedocs.io/en/latest/).

Na Cloud Shell, faça os seguintes passos:

1. Abra o ficheiro de configuração `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Vá para o fim do ficheiro e selecione a tecla A para entrar no modo de acrescentar.

1. Cole o seguinte código no fim do ficheiro:

   ```hcl
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = azurerm_public_ip.vmss.id
    }

    tags = var.tags
   }

   resource "azurerm_lb_backend_address_pool" "bpepool" {
    resource_group_name = azurerm_resource_group.vmss.name
    loadbalancer_id     = azurerm_lb.vmss.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_lb_probe" "vmss" {
    resource_group_name = azurerm_resource_group.vmss.name
    loadbalancer_id     = azurerm_lb.vmss.id
    name                = "ssh-running-probe"
    port                = var.application_port
   }

   resource "azurerm_lb_rule" "lbnatrule" {
      resource_group_name            = azurerm_resource_group.vmss.name
      loadbalancer_id                = azurerm_lb.vmss.id
      name                           = "http"
      protocol                       = "Tcp"
      frontend_port                  = var.application_port
      backend_port                   = var.application_port
      backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
      frontend_ip_configuration_name = "PublicIPAddress"
      probe_id                       = azurerm_lb_probe.vmss.id
   }

   resource "azurerm_virtual_machine_scale_set" "vmss" {
    name                = "vmscaleset"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    upgrade_policy_mode = "Manual"

    sku {
      name     = "Standard_DS1_v2"
      tier     = "Standard"
      capacity = 2
    }

    storage_profile_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_profile_os_disk {
      name              = ""
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    storage_profile_data_disk {
      lun          = 0
      caching        = "ReadWrite"
      create_option  = "Empty"
      disk_size_gb   = 10
    }

    os_profile {
      computer_name_prefix = "vmlab"
      admin_username       = var.admin_user
      admin_password       = var.admin_password
      custom_data          = file("web.conf")
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    network_profile {
      name    = "terraformnetworkprofile"
      primary = true

      ip_configuration {
        name                                   = "IPConfiguration"
        subnet_id                              = azurerm_subnet.vmss.id
        load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
        primary = true
      }
    }

    tags = var.tags
   }
   ```

1. Guarde o ficheiro e saia do editor vi ao inserir o seguinte comando:

    ```bash
    :wq
    ```

1. Crie um ficheiro com o nome `web.conf` para servir de configuração cloud-init para as máquinas virtuais que fazem parte do conjunto de dimensionamento.

    ```bash
    code web.conf
    ```

1. Cole o seguinte código no editor:

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. Guarde o ficheiro e saia do editor vi ao inserir o seguinte comando:

     ```bash
     :wq
     ```

1. Abra o ficheiro de configuração `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Vá para o fim do ficheiro e selecione a tecla A para entrar no modo de acrescentar.

1. Personalize a implementação ao colar o seguinte código no fim do ficheiro:

    ```hcl
    variable "application_port" {
       description = "The port that you want to expose to the external load balancer"
       default     = 80
    }

    variable "admin_user" {
       description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
       default     = "azureuser"
    }

    variable "admin_password" {
       description = "Default password for admin account"
    }
    ```

1. Guarde o ficheiro**&lt;(CTRL>S)** e saia do editor (**&lt;Ctrl>Q**).

1. Crie um plano do Terraform para visualizar a implementação do conjunto de dimensionamento de máquinas virtuais (tem de especificar uma palavra-passe escolhida por si, bem como a localização para os seus recursos).

    ```bash
    terraform plan
    ```

    A saída do comando deve ser semelhante a esta captura de ecrã:

    ![Saída da criação do conjunto de dimensionamento de máquinas virtuais](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Implemente os novos recursos no Azure.

    ```bash
    terraform apply
    ```

    A saída do comando deve ser semelhante a esta captura de ecrã:

    ![Grupo de recursos do conjunto de dimensionamento de máquinas virtuais do Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Abra um browser e ligue ao FQDN que foi devolvido pelo comando.

    ![Resultados da navegação para o FQDN](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Adicionar uma jumpbox SSH
Uma caixa de *salto* SSH é um único servidor pelo qual "salta" para aceder a outros servidores da rede. Neste passo, vai configurar os seguintes recursos:

- Uma interface de rede (ou jumpbox) ligada à mesma sub-rede que o conjunto de dimensionamento de máquinas virtuais.

- Uma máquina virtual ligada com esta interface de rede. Esta "jumpbox" é acessível remotamente. Quando estiver ligado, pode aceder por SSH a quaisquer máquinas virtuais no conjunto de dimensionamento.

1. Abra o ficheiro de configuração `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Vá para o fim do ficheiro e selecione a tecla A para entrar no modo de acrescentar.

1. Cole o seguinte código no fim do ficheiro:

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = var.tags
   }

   resource "azurerm_network_interface" "jumpbox" {
    name                = "jumpbox-nic"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    ip_configuration {
      name                          = "IPConfiguration"
      subnet_id                     = azurerm_subnet.vmss.id
      private_ip_address_allocation = "dynamic"
      public_ip_address_id          = azurerm_public_ip.jumpbox.id
    }

    tags = var.tags
   }

   resource "azurerm_virtual_machine" "jumpbox" {
    name                  = "jumpbox"
    location              = var.location
    resource_group_name   = azurerm_resource_group.vmss.name
    network_interface_ids = [azurerm_network_interface.jumpbox.id]
    vm_size               = "Standard_DS1_v2"

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "jumpbox-osdisk"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    os_profile {
      computer_name  = "jumpbox"
      admin_username = var.admin_user
      admin_password = var.admin_password
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = var.tags
   }
   ```

1. Abra o ficheiro de configuração `output.tf`.

   ```bash
   code output.tf
   ```

1. Vá para o fim do ficheiro e selecione a tecla A para entrar no modo de acrescentar.

1. Cole o seguinte código no fim do ficheiro para apresentar o nome do anfitrião da jumpbox quando a implementação estiver concluída:

   ```hcl
   output "jumpbox_public_ip" {
      value = azurerm_public_ip.jumpbox.fqdn
   }
   ```

1. Guarde o ficheiro**&lt;(CTRL>S)** e saia do editor (**&lt;Ctrl>Q**).

1. Implemente a jumpbox.

   ```bash
   terraform apply
   ```

Depois de concluída a implementação, o conteúdo do grupo de recursos é semelhante ao mostrado nesta captura de ecrã:

![Grupo de recursos do conjunto de dimensionamento de máquinas virtuais do Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> A capacidade de iniciar sessão com uma palavra-passe está desativada na jumpbox e no conjunto de dimensionamento de máquinas virtuais que implementou. Inicie sessão com SSH para aceder à máquina ou máquinas virtuais.

## <a name="environment-cleanup"></a>Limpeza de ambiente

Para eliminar os recursos do Terraform criados neste tutorial, introduza o seguinte comando no Cloud Shell:

```bash
terraform destroy
```

O processo de destruição pode demorar vários minutos a ser concluído.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre a utilização da Terraform em Azure](/azure/terraform)
