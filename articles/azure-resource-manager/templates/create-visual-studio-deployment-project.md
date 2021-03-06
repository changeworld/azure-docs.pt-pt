---
title: Criar & implementar projetos do grupo de recursos do Estúdio Visual
description: Use o Estúdio Visual para criar um projeto de grupo de recursos Azure e implementar os recursos para o Azure.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 5127732ac0c33d4b27f70bd616fb23aaec5c871f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152736"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Criar e implementar grupos de recursos do Azure através do Visual Studio

Com o Visual Studio, pode criar um projeto que implementa a sua infraestrutura e o código para o Azure. Por exemplo, pode implementar o web host, website e código para o site. O Visual Studio fornece muitos modelos de introdução diferentes para cenários comuns de implementação. Neste artigo, você implementa uma aplicação web.

Este artigo mostra como usar o [Visual Studio 2019 ou mais tarde com o desenvolvimento do Azure e ASP.NET cargas de trabalho instaladas.](/visualstudio/install/install-visual-studio?view=vs-2019) Se utilizar o Visual Studio 2017, a sua experiência é em grande parte a mesma.

## <a name="create-azure-resource-group-project"></a>Criar projeto do Grupo de Recursos do Azure

Nesta secção, cria-se um projeto do Grupo de Recursos Azure com um modelo de **aplicação Web.**

1. No Estúdio Visual, escolha **File**>**New**>**Project**.
1. Selecione o modelo de projeto do Grupo de **Recursos Azure** e **Seguinte**.

    ![Criar o projeto](./media/create-visual-studio-deployment-project/create-project.png)

1. Dê um nome ao seu projeto. As outras definições padrão provavelmente são boas, mas revê-las para que funcionem para o seu ambiente. Quando terminar, selecione **Criar**.

    ![Criar o projeto](./media/create-visual-studio-deployment-project/name-project.png)

1. Escolha o modelo que pretende implementar no Azure Resource Manager. Tenha em atenção que existem muitas opções diferentes com base no tipo de projeto que pretende implementar. Para este artigo, escolha o modelo de **aplicação Web** e **OK**.

    ![Escolher um modelo](./media/create-visual-studio-deployment-project/select-project.png)

    O modelo que escolher é apenas um ponto de partida; pode adicionar e remover recursos para satisfazer o seu cenário.

1. O Visual Studio cria um projeto de implantação de grupos de recursos para a aplicação web. Para ver os ficheiros do seu projeto, veja o nó no projeto de implantação.

    ![Mostrar nódosos](./media/create-visual-studio-deployment-project/show-items.png)

    Uma vez que escolheu o modelo de aplicação Web, vê os seguintes ficheiros:

   | Nome de ficheiro | Descrição |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Um script do PowerShell que executa comandos do PowerShell para implementar no Azure Resource Manager. O Visual Studio usa este script PowerShell para implementar o seu modelo. |
   | WebSite.json |O modelo do Resource Manager que define a infraestrutura que pretende implementar no Azure e os parâmetros que pode fornecer durante a implementação. Define as dependências entre os recursos para que o Resource Manager os implemente na ordem correta. |
   | WebSite.parameters.json |Um ficheiro de parâmetros que tem os valores que são precisos para o modelo. Passa nos valores dos parâmetros para personalizar cada implementação. |

    Todos os projetos de implementação do grupo de recursos têm estes ficheiros básicos. Outros projetos poderão ter ficheiros adicionais para suportar outras funcionalidades.

## <a name="customize-resource-manager-template"></a>Personalizar modelo de gestor de recursos

Pode personalizar um projeto de implementação modificando o modelo de Gestor de Recursos que descreve os recursos que pretende implementar. Para saber mais sobre os elementos do modelo do Resource Manager, consulte [Criação de modelos do Azure Resource Manager](template-syntax.md).

1. Para trabalhar no seu modelo, abra **webSite.json**.

1. O editor do Visual Studio fornece ferramentas para o ajudar a editar o modelo do Resource Manager. A janela **Contorno de JSON** faz com que seja mais fácil ver os elementos definidos no seu modelo.

   ![Mostrar contorno jSON](./media/create-visual-studio-deployment-project/show-json-outline.png)

1. Selecione um elemento no contorno para ir a essa parte do modelo.

   ![Navegar JSON](./media/create-visual-studio-deployment-project/navigate-json.png)

1. Pode adicionar um recurso, selecionando o botão **Adicionar Recurso** na parte superior da janela Contorno de JSON ou clicando com o botão direito do rato em **recursos** e selecionando **Adicionar Novo Recurso**.

   ![Adicionar recurso](./media/create-visual-studio-deployment-project/add-resource.png)

1. Selecione **Conta de Armazenamento** e dê-lhe um nome. Forneça um nome com um máximo de 11 carateres e apenas números e letras minúsculas.

   ![Adicionar armazenamento](./media/create-visual-studio-deployment-project/add-storage.png)

1. Tenha em atenção que o recurso foi adicionado, mas também um parâmetro para a conta do Storage do tipo e uma variável para o nome da conta do Storage.

   ![Mostrar contorno](./media/create-visual-studio-deployment-project/show-new-items.png)

1. O parâmetro para o tipo de conta de armazenamento é pré-definido com tipos permitidos e um tipo predefinido. Pode deixar estes valores ou editá-los para o seu cenário. Se não pretender que ninguém implemente uma conta de armazenamento **Premium_LRS** através deste modelo, remova-a dos tipos permitidos.

   ```json
   "demoaccountType": {
     "type": "string",
     "defaultValue": "Standard_LRS",
     "allowedValues": [
       "Standard_LRS",
       "Standard_ZRS",
       "Standard_GRS",
       "Standard_RAGRS"
     ]
   }
   ```

1. O Visual Studio também fornece intellisense para ajudá-lo a entender as propriedades que estão disponíveis ao editar o modelo. Por exemplo, para editar as propriedades para o seu plano do Serviço de Aplicações, navegue até ao recurso **HostingPlan** e adicione um valor para as **propriedades**. Repare que o intellisense mostra os valores disponíveis e fornece uma descrição desse valor.

   ![Mostrar intellisense](./media/create-visual-studio-deployment-project/show-intellisense.png)

   Pode definir **o número OfWorkers** para 1 e guardar o ficheiro.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Abra o ficheiro **WebSite.parameters.json.** Utiliza o ficheiro de parâmetros para passar em valores durante a implementação que personalizam o recurso que está a ser implantado. Dê um nome ao plano de acolhimento e guarde o ficheiro.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       }
     }
   }
   ```

## <a name="deploy-project-to-azure"></a>Implementar projeto para o Azure

Está pronto para lançar o seu projeto para um grupo de recursos.

Por predefinição, o script PowerShell (Deploy-AzureResourceGroup.ps1) no projeto utiliza o módulo AzureRM. Se ainda tiver o módulo AzureRM instalado e pretender continuar a utilizá-lo, pode utilizar este script predefinido. Com este script, pode utilizar a interface Do Estúdio Visual para implementar a sua solução.

No entanto, se emigrou para o novo [módulo Az,](/powershell/azure/new-azureps-module-az)precisa adicionar um novo script ao seu projeto. Para adicionar um script que utiliza o módulo Az, copie o script [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) e adicione-o ao seu projeto. Para utilizar este script para implementação, deve executá-lo a partir de uma consola PowerShell, em vez de usar a interface de implementação do Visual Studio.

Ambas as abordagens são mostradas neste artigo. Este artigo refere-se ao script padrão como o script do módulo AzureRM, e o novo script como o script do módulo Az.

### <a name="az-module-script"></a>Script de módulo Az

Para o script do módulo Az, abra uma consola PowerShell e corra:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>Script de módulo AzureRM

Para o script do módulo AzureRM, utilize o Estúdio Visual:

1. No menu de atalho do nó do projeto de implantação, escolha **Deploy** > **New**.

    ![Novo item do menu de implementação](./media/create-visual-studio-deployment-project/deploy.png)

1. É apresentada a caixa de diálogo **Implementar no Grupo de Recursos**. Na caixa de diálogo pendente **Grupo de recursos**, escolha um grupo de recursos existente ou crie um novo. Selecione **Implementar**.

    ![Desdobre para caixa de diálogo de grupo de recursos](./media/create-visual-studio-deployment-project/show-deployment.png)

1. O estado da implementação é apresentado nas janelas **Saída**. Após concluir a implementação, a última mensagem indicará uma implementação bem sucedida com algo semelhante a:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Ver recursos implantados

Vamos verificar os resultados.

1. Num browser, abra o [Portal do Azure](https://portal.azure.com/) e inicie sessão na sua conta. Para ver o grupo de recursos, selecione **Grupos de recursos** e o grupo de recursos no qual implementou.

1. Veja todos os recursos implementados. Repare que o nome da conta de armazenamento não é exatamente aquele que especificou quando adicionou esse recurso. A conta de armazenamento deve ser exclusiva. O modelo adiciona automaticamente uma série de caracteres ao nome que forneceu para criar um nome único.

    ![Mostrar recursos](./media/create-visual-studio-deployment-project/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Adicionar código ao projeto

Neste momento, implementou a infraestrutura para a sua aplicação, mas não existe qualquer código real implementado com o projeto.

1. Adicione um projeto à sua solução do Visual Studio. Clique na solução e selecione **Adicionar** > **Novo Projeto**.

    ![Adicionar projeto](./media/create-visual-studio-deployment-project/add-project.png)

1. Adicione uma **aplicação web ASP.NET Core**.

    ![Adicionar aplicativo web](./media/create-visual-studio-deployment-project/add-app.png)

1. Dê um nome à sua aplicação web e selecione **Criar**.

    ![Aplicativo web de nome](./media/create-visual-studio-deployment-project/name-web-app.png)

1. Selecione **aplicação web** e **criar**.

    ![Selecione aplicação Web](./media/create-visual-studio-deployment-project/select-project-type.png)

1. Depois de o Visual Studio ter criado a sua aplicação Web, verá ambos os projetos na solução.

    ![Mostrar projetos](./media/create-visual-studio-deployment-project/show-projects.png)

1. Agora, tem de verificar se o projeto do grupo de recursos deteta o novo projeto. Volte ao seu projeto de grupo de recursos (ExemploAppDeploy). Clique com o botão direito do rato em **Referências** e selecione **Adicionar Referência**.

    ![Adicionar referência](./media/create-visual-studio-deployment-project/add-new-reference.png)

1. Selecione o projeto da aplicação Web criado.

   ![Adicionar referência](./media/create-visual-studio-deployment-project/add-reference.png)

   Ao adicionar uma referência, liga o projeto da aplicação web ao projeto do grupo de recursos e configura automaticamente algumas propriedades. Pode ver estas propriedades na janela **Propriedades** da referência. O **Incluir Caminho do Ficheiro** tem o caminho onde o pacote é criado. Anote a pasta (ExampleApp) e o ficheiro (package.zip). É necessário saber estes valores, uma vez que os fornece como parâmetros ao implementar a aplicação.

   ![Ver referência](./media/create-visual-studio-deployment-project/see-reference.png)

1. Volte para o seu modelo (WebSite.json) e adicione um recurso ao modelo.

    ![Adicionar recurso](./media/create-visual-studio-deployment-project/add-resource-2.png)

1. Desta vez, selecione **Implementação Web de Aplicações Web**.

    ![Adicionar implementação web](./media/create-visual-studio-deployment-project/add-web-deploy.png)

   Guarde o seu modelo.

1. Há alguns novos parâmetros no seu modelo. Foram adicionados no passo anterior. Não é necessário fornecer valores para **_artifactsLocation** ou **_artifactsLocationSasToken** porque esses valores são gerados automaticamente. No entanto, tem de definir a pasta e o nome do ficheiro para o caminho que contém o pacote de implementação. Os nomes destes parâmetros terminam com **PackageFolder** e **PackageFileName**. A primeira parte do nome é o nome do recurso Web Deploy que adicionou. Neste artigo, são nomeados **ExemploAppPackageFolder** e **ExampleAppPackageFileName**.

   Abra o **Site.parameters.json** e defina esses parâmetros para os valores que viu nas propriedades de referência. Defino **o ExemploAppPackageFolder** para o nome da pasta. **Delineie o nome do** Ficheiro Pacote de Pacotes de Exemplo para o nome do ficheiro zip.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       },
       "ExampleAppPackageFolder": {
         "value": "ExampleApp"
       },
       "ExampleAppPackageFileName": {
         "value": "package.zip"
       }
     }
   }
   ```

## <a name="deploy-code-with-infrastructure"></a>Implementar código com infraestrutura

Como adicionou código ao projeto, a sua implementação é um pouco diferente desta vez. Durante a implantação, você encena artefactos para o seu projeto para um lugar a que o Gestor de Recursos pode aceder. Os artefactos são encenados numa conta de armazenamento.

### <a name="az-module-script"></a>Script de módulo Az

Há uma pequena mudança que precisa fazer ao seu modelo se estiver a usar o script do módulo Az. Este script adiciona um corte à localização dos artefactos, mas o seu modelo não espera que corte. Abra o WebSite.json e encontre as propriedades para a extensão MSDeploy. Tem uma propriedade chamada **packageUri.** Retire a barra entre a localização dos artefactos e a pasta do pacote.

Deverá ter o seguinte aspeto:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Note no exemplo anterior `'/',` que não há entre **os parâmetros ('_artifactsLocation')** e os **parâmetros ('ExampleAppPackageFolder')**.

Reconstruir o projeto. A construção do projeto garante que os ficheiros necessários para implantar são adicionados à pasta de encenação.

Agora, abra uma consola PowerShell e corra:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>Script de módulo AzureRM

Para o script do módulo AzureRM, utilize o Estúdio Visual:

1. Para reimplantar, escolha **deploy**, e o grupo de recursos que implementou anteriormente.

    ![Projeto de reimplantação](./media/create-visual-studio-deployment-project/redeploy.png)

1. Selecione a conta de armazenamento que implementou com este grupo de recursos para a conta de **armazenamento de Artefactos**.

   ![Reimplantar a web deploy](./media/create-visual-studio-deployment-project/redeploy-web-app.png)

## <a name="view-web-app"></a>Ver aplicativo web

1. Após concluir a implementação, selecione a aplicação Web no portal. Selecione o URL para navegar para o site.

   ![Procurar no site](./media/create-visual-studio-deployment-project/browse-site.png)

1. Repare que implementou com sucesso a aplicação ASP.NET predefinida.

   ![Mostrar aplicativo implantado](./media/create-visual-studio-deployment-project/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Adicionar painel de operações

Não está limitado apenas aos recursos disponíveis através da interface do Visual Studio. Pode personalizar a sua implementação, ao adicionar um recurso personalizado ao seu modelo. Para mostrar a adição de um recurso, adicione um dashboard operacional para gerir os recursos que implementou.

1. Abra o ficheiro WebSite.json e adicione o seguinte JSON `]` após o recurso da conta de armazenamento, mas antes do fecho da secção de recursos.

   ```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "type": "Microsoft.Portal/dashboards",
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Reimplemente o seu projeto.

1. Depois de a implantação ter terminado, veja o seu painel no portal. Selecione **Dashboard** e escolha o que implementou.

   ![Dashboard Personalizado](./media/create-visual-studio-deployment-project/view-custom-dashboards.png)

1. Vê o painel personalizado.

   ![Dashboard Personalizado](./media/create-visual-studio-deployment-project/Ops-DemoSiteGroup-dashboard.png)

Pode gerir o acesso ao dashboard através de grupos do RBAC. Também pode personalizar a aparência do dashboard após a sua implementação. No entanto, se voltar a implementar o grupo de recursos, o dashboard ficará no estado predefinido no seu modelo. Para obter mais informações sobre a criação de dashboards, veja [Criar Dashboards do Azure programaticamente](../../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupos de Recursos** do menu esquerdo.

1. Selecione o nome do grupo de recursos.

1. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar e implementar modelos usando o Visual Studio. Para saber mais sobre o desenvolvimento do modelo, consulte a nossa nova série tutorial de principiante:

> [!div class="nextstepaction"]
> [Tutoriais para principiantes](./template-tutorial-create-first-template.md)
