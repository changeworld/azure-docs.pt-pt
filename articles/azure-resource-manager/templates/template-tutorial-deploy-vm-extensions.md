---
title: Implementar extensões VM com modelo
description: Saiba como implementar extensões de máquina virtual com modelos do Azure Resource Manager
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7397e9387fe3354a926ed607a9132ab6ddc7e785
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477587"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Tutorial: Implementar extensões de máquinavirtual com modelos ARM

Aprenda a utilizar [extensões de máquina virtual do Azure](../../virtual-machines/extensions/features-windows.md) para fazer tarefas de automatização e configuração de pós-implementação em VMs do Azure. Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. Neste tutorial, você implementa uma extensão de Script Personalizado a partir de um modelo de Gestor de Recursos Azure (ARM) para executar um script PowerShell em um Windows VM.  O script instala o Servidor Web na VM.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar o script do Powershell
> * Abrir um modelo de início rápido
> * Editar o modelo
> * Implementar o modelo
> * Verificar a implementação

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [o Use Visual Studio Code para criar modelos ARM](use-vs-code-to-create-template.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para mais informações, consulte [Tutorial: Integre o Cofre chave Azure na implementação](./template-tutorial-use-key-vault.md)do modelo ARM . Recomendamos também que atualize a sua palavra-passe de três em três meses.

## <a name="prepare-a-powershell-script"></a>Preparar o script do Powershell

Um script PowerShell com o seguinte conteúdo é partilhado a partir do [GitHub:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1)

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Se optar por publicar o ficheiro na sua `fileUri` própria localização, tem de atualizar o elemento no modelo mais tarde no tutorial.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Os modelos Azure Quickstart é um repositório para modelos ARM. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Código do Estúdio Visual, selecione **File** > **Open File**.
1. Na caixa de **nomes de ficheiros,** cola o seguinte URL:https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. Para abrir o ficheiro, selecione **Open**.
    O modelo define cinco recursos:

   * **Microsoft.Storage/storageAccounts**. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft.Network/publicIPAddresss**. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft.Network/virtualNetworks**. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft.Network/networkInterfaces**. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft.Compute/virtualMachines**. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     É útil obter alguma compreensão básica do modelo antes de personalizá-lo.

1. Guarde uma cópia do ficheiro para o seu computador local com o nome *azuredeploy.json* selecionando **O Guardar** > **ficheiros Como**.

## <a name="edit-the-template"></a>Editar o modelo

Adicione um recurso de extensão de máquina virtual ao modelo existente com o seguinte conteúdo:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2018-06-01",
  "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
  "location": "[parameters('location')]",
  "dependsOn": [
      "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
  ],
  "properties": {
      "publisher": "Microsoft.Compute",
      "type": "CustomScriptExtension",
      "typeHandlerVersion": "1.7",
      "autoUpgradeMinorVersion":true,
      "settings": {
        "fileUris": [
          "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
        ],
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
      }
  }
}
```

Para mais informações sobre esta definição de recursos, consulte a referência de [extensão](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Seguem alguns elementos importantes:

* **nome**: uma vez que o recurso de extensão é um recurso subordinado do objeto de máquina virtual, o nome tem de ter o prefixo do nome da máquina virtual. Consulte [o nome e o tipo de conjunto sinuosos para os recursos infantis](child-resource-name-type.md).
* **depende :** Crie o recurso de extensão depois de ter criado a máquina virtual.
* **fileUris**: Os locais onde os ficheiros de script são armazenados. Se optar por não utilizar a localização fornecida, tem de atualizar os valores.
* **comandoToExecutar**: Este comando invoca o script.

Também deve abrir a porta HTTP para que possa aceder ao servidor web.

1. Encontre regras de **segurança** no modelo.
1. Adicione a seguinte regra ao lado do **por defeito-permitir-3389**.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>Implementar o modelo

Para o procedimento de implantação, consulte a secção "Implementar o modelo" do [Tutorial: Criar modelos ARM com recursos dependentes](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Recomendamos que utilize uma palavra-passe gerada para a conta de administrador de máquinas virtuais. Consulte a secção [de pré-requisitos](#prerequisites) deste artigo.

## <a name="verify-the-deployment"></a>Verificar a implementação

1. No portal Azure, selecione o VM.
1. Na visão geral do VM, copie o endereço IP selecionando **Click to copy**, e, em seguida, cole-o num separador de navegador. A página de boas-vindas padrão dos Serviços de Informação da Internet (IIS) abre:

![Página de boas-vindas dos Serviços de Informação da Internet](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos Azure que implantou, limpe-os apagando o grupo de recursos.

1. No portal Azure, no painel esquerdo, selecione **Grupo Recurso**.
2. Na caixa **de nome filter** by, introduza o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
    Seis recursos são apresentados no grupo de recursos.
4. No menu superior, selecione **Eliminar o grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou uma máquina virtual e uma extensão de máquina virtual. A extensão instalou o servidor web dos IIS na máquina virtual. Para aprender a utilizar a extensão da Base de Dados Azure SQL para importar um ficheiro BACPAC, consulte:

> [!div class="nextstepaction"]
> [Implementar extensões de SQL](./template-tutorial-deploy-sql-extensions-bacpac.md)
