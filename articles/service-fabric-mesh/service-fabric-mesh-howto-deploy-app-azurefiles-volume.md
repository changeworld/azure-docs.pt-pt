---
title: Utilize um volume baseado em Ficheiros Azure numa aplicação de malha de tecido de serviço
description: Aprenda a armazenar o estado numa aplicação de malha de tecido de serviço Azure, montando um volume baseado em Ficheiros Azure dentro de um serviço utilizando o Azure CLI.
author: dkkapur
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 5bb7ab6c861d958f6811ca852363c59cfced3940
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718825"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Monte um volume baseado em Ficheiros Azure numa aplicação de malha de tecido de serviço 

Este artigo descreve como montar um volume baseado em Ficheiros Azure num serviço de uma aplicação de malha de tecido de serviço.  O controlador de volume Azure Files é um controlador de volume Docker usado para montar uma partilha de Ficheiros Azure num recipiente, que utiliza para persistir no estado de serviço. Os volumes dão-lhe armazenamento de ficheiros de uso geral e permitem-lhe ler/escrever ficheiros utilizando APIs de ficheiro sinuoso normal.  Para saber mais sobre volumes e opções para armazenar dados de aplicações, leia [o estado de armazenamento.](service-fabric-mesh-storing-state.md)

Para montar um volume num serviço, crie um recurso de volume na sua aplicação de malha de tecido de serviço e, em seguida, refira esse volume no seu serviço.  Declarar o recurso de volume e referenciar no recurso de serviço pode ser feito quer nos [ficheiros de recursos baseados na YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) quer no [modelo de implementação baseado em JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Antes de aumentar o volume, crie primeiro uma conta de armazenamento Azure e uma partilha de [ficheiros em Ficheiros Azure](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Pré-requisitos
> [!NOTE]
> **Problema conhecido com implementação na máquina de desenvolvimento Do Windows RS5:** Existe um bug aberto com powershell cmdlet New-SmbGlobalMapping em máquinas RS5 Windows que impedem a montagem de Volumes De FicheiroS Azurefile. Abaixo está o erro da amostra que é encontrado quando o volume baseado em AzureFile está a ser montado na máquina de desenvolvimento local.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
A suver para o problema é 1)Run abaixo do comando como administrador powershell e 2)Reiniciar a máquina.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Pode utilizar a Casca de Nuvem Azure ou uma instalação local do Azure CLI para completar este artigo. 

Para utilizar o Azure CLI localmente `az --version` com `azure-cli (2.0.43)`este artigo, certifique-se de que devolve pelo menos .  Instale (ou atualize) o módulo de extensão CLI de malha de tecido de tecido de serviço Azure seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

Para iniciar sessão no Azure e definir a sua subscrição:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Criar uma conta de armazenamento e uma partilha de ficheiros (opcional)
A montagem de um volume de Ficheiros Azure requer uma conta de armazenamento e uma partilha de ficheiros.  Pode utilizar uma conta de armazenamento Azure existente e uma partilha de ficheiros, ou criar recursos:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Obtenha o nome e a chave da conta de armazenamento e o nome da partilha de ficheiros
O nome da conta de armazenamento, a chave da `<storageAccountName>` `<storageAccountKey>`conta `<fileShareName>` de armazenamento e o nome da partilha do ficheiro são referenciados como , e nas seguintes secções. 

Liste as suas contas de armazenamento e obtenha o nome da conta de armazenamento com a parte do ficheiro que pretende utilizar:
```azurecli-interactive
az storage account list
```

Obtenha o nome da parte do ficheiro:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Obtenha a chave da conta de armazenamento ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Também pode encontrar estes valores no [portal Azure:](https://portal.azure.com)
* `<storageAccountName>`- Em Contas de **Armazenamento,** o nome da conta de armazenamento utilizada para criar a parte do ficheiro.
* `<storageAccountKey>`- Selecione a sua conta de armazenamento em Contas de Armazenamento e, em seguida, selecione as **teclas** **de acesso** e utilize o valor em baixo **da tecla1**.
* `<fileShareName>`- Selecione a sua conta de armazenamento em **Contas de Armazenamento** e, em seguida, selecione **Ficheiros**. O nome a usar é o nome da partilha de ficheiros que criou.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Declare um recurso de volume e atualize o recurso de serviço (JSON)

Adicione parâmetros `<fileShareName>`para `<storageAccountName>`os, e `<storageAccountKey>` valores que encontrou num passo anterior. 

Crie um recurso volume como um par do recurso Aplicação. Especifique um nome e o fornecedor ("SFAzureFile" para utilizar o volume baseado em Ficheiros Azure). Em `azureFileParameters`, especifique `<fileShareName>` `<storageAccountName>`os `<storageAccountKey>` parâmetros para os , e valores que encontrou num passo anterior.

Para montar o volume no `volumeRefs` seu `codePackages` serviço, adicione um ao elemento do serviço.  `name`é o ID de recurso para o volume (ou um parâmetro de modelo de implantação para o recurso de volume) e o nome do volume declarado no ficheiro de recursos volume.yaml.  `destinationPath`é o diretório local a que o volume será montado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Declare um recurso de volume e atualize o recurso de serviço (YAML)

Adicione um novo ficheiro *volume.yaml* no diretório de recursos da *App* para a sua aplicação.  Especifique um nome e o fornecedor ("SFAzureFile" para utilizar o volume baseado em Ficheiros Azure). `<fileShareName>`, `<storageAccountName>`e `<storageAccountKey>` são os valores que encontrou num passo anterior.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Atualize o ficheiro *service.yaml* no diretório de *Recursos de Serviço* para aumentar o volume no seu serviço.  Adicione `volumeRefs` o elemento `codePackages` ao elemento.  `name`é o ID de recurso para o volume (ou um parâmetro de modelo de implantação para o recurso de volume) e o nome do volume declarado no ficheiro de recursos volume.yaml.  `destinationPath`é o diretório local a que o volume será montado.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Passos seguintes

- Ver a aplicação da amostra de volume dos Ficheiros Azure no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o Modelo de Recursos do Service Fabric, consulte [Modelo de Recursos do Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
