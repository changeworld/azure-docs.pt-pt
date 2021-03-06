---
title: Implante com Terraform de Azure Cloud Shell [ Microsoft Docs
description: Desdobre com Terraform de Azure Cloud Shell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: 8bacadd8941131f608411e61cc15c120c1b2bc60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458159"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Desdobre com Terraform de Bash em Azure Cloud Shell
Este artigo acompanha-o através da criação de um grupo de recursos com o [fornecedor Terraform AzureRM.](https://www.terraform.io/docs/providers/azurerm/index.html)

[A Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de código aberto que codifica APIs em ficheiros de configuração declarativa que podem ser partilhados entre membros da equipa para serem editados, revistos e versonizados. O fornecedor Microsoft AzureRM é utilizado para interagir com recursos suportados pelo Azure Resource Manager através das APIs AzureRM.

## <a name="automatic-authentication"></a>Autenticação automática
A Terraform está instalada em Bash in Cloud Shell por padrão. Além disso, a Cloud Shell autentica automaticamente a subscrição padrão do Azure CLI para implantar recursos através dos módulos Terraform Azure.

A Terraform utiliza a subscrição padrão do Azure CLI que está definida. Para atualizar subscrições predefinidas, executar:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Instruções
### <a name="launch-bash-in-cloud-shell"></a>Bash de lançamento na Cloud Shell
1. Lance Cloud Shell a partir da sua localização preferida
2. Verifique se a subscrição preferida está definida

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Criar um modelo terraforme
Crie um novo modelo Terraform chamado main.tf com o seu editor de texto preferido.

```
vim main.tf
```

Copiar/colar o seguinte código na Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Guarde o seu ficheiro e saia do seu editor de texto.

### <a name="terraform-init"></a>Terraform init
Comece por `terraform init`correr.

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

O [comando terraforme init](https://www.terraform.io/docs/commands/init.html) é utilizado para inicializar um diretório de trabalho contendo ficheiros de configuração Terraform. O `terraform init` comando é o primeiro comando que deve ser executado após a escrita de uma nova configuração Terraform ou clonagem de um existente a partir do controlo da versão. É seguro executar este comando várias vezes.

### <a name="terraform-plan"></a>Comando plan do Terraform
Pré-visualizar os recursos a criar `terraform plan`pelo modelo Terraform com .

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

O [comando terraform plan](https://www.terraform.io/docs/commands/plan.html) é utilizado para criar um plano de execução. A Terraform realiza uma atualização, a menos que explicitamente desativada, e depois determina quais as ações necessárias para alcançar o estado desejado especificado nos ficheiros de configuração. O plano pode ser guardado usando-out, e depois fornecido à terraforme aplicar-se para garantir que apenas as ações pré-planeadas são executadas.

### <a name="terraform-apply"></a>Comando apply do Terraform
Fornecer os recursos `terraform apply`Azure com .

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

O [comando de aplicação terraforme](https://www.terraform.io/docs/commands/apply.html) é utilizado para aplicar as alterações necessárias para atingir o estado desejado da configuração.

### <a name="verify-deployment-with-azure-cli"></a>Verificar a implantação com o Azure CLI
Corra `az group show -n myRgName` para verificar se o recurso conseguiu o provisionamento.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Limpar com terraforma destruir
Limpe o grupo de recursos criado com o [Terraform destruir o comando](https://www.terraform.io/docs/commands/destroy.html) para limpar a infraestrutura criada pela Terraform.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Criou com sucesso um recurso Azure através da Terraform. Visite os próximos passos para continuar a aprender sobre cloud shell.

## <a name="next-steps"></a>Passos seguintes
[Conheça o fornecedor Terraform Azure](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash in Cloud Shell quickstart](quickstart.md)
