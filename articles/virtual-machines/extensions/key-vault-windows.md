---
title: Extensão VM do cofre de chaves Azure para windows
description: Implemente um agente que realize uma atualização automática dos segredos do Cofre chave em máquinas virtuais utilizando uma extensão virtual da máquina.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 8e014e7a1c564377582e4503218c4129619daa91
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410742"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual do Cofre chave para Windows

A extensão VM do Cofre Chave fornece uma atualização automática de certificados armazenados num cofre de chaves Azure. Especificamente, a extensão monitoriza uma lista de certificados observados armazenados em cofres-chave e, ao detetar uma alteração, recupera e instala os certificados correspondentes. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão VM do Cofre chave para windows. 

### <a name="operating-system"></a>Sistema operativo

A extensão VM do Cofre Chave suporta versões abaixo do Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

### <a name="supported-certificate-content-types"></a>Tipos de conteúdo de certificado suportado

- PKCS #12
- PEM

## <a name="extension-schema"></a>Esquema de extensão

O seguinte JSON mostra o esquema para a extensão VM do cofre chave. A extensão não requer configurações protegidas - todas as suas configurações são consideradas informações públicas. A extensão requer uma lista de certificados monitorizados, frequência de votação e o certificado de destino. Mais concretamente:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> Os seus certificados observados URLs `https://myVaultName.vault.azure.net/secrets/myCertName`devem ser do formulário .
> 
> Isto porque `/secrets` o caminho devolve o certificado completo, `/certificates` incluindo a chave privada, enquanto o caminho não. Mais informações sobre certificados podem ser encontradas aqui: [Certificados de cofre chave](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)

### <a name="property-values"></a>Valores patrimoniais

| Nome | Valor / Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publicador | Microsoft.Azure.KeyVault | string |
| tipo | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | int |
| sondagensIntervalInS | 3600 | string |
| certificateStoreName | MY | string |
| linkOnRenovação | false | boolean |
| certificateStoreLocation  | Máquina Local | string |
| necessárioInitialSync | true | boolean |
| certificados observados  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | matriz de cordas


## <a name="template-deployment"></a>Implementação de modelos

As extensões VM azure podem ser implantadas com modelos de Gestor de Recursos Azure. Os modelos são ideais ao implantar uma ou mais máquinas virtuais que requerem a atualização pós-implantação de certificados. A extensão pode ser implantada em VMs individuais ou conjuntos de escala de máquinas virtuais. O esquema e a configuração são comuns a ambos os tipos de modelos. 

A configuração JSON para uma extensão virtual da máquina deve ser `"resources": []` aninhada dentro do fragmento de recurso `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` virtual da máquina do modelo, especificamente o objeto para o modelo de máquina virtual e no caso de uma escala de máquina virtual colocada sob objeto.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Implantação Azure PowerShell

O Azure PowerShell pode ser utilizado para implantar a extensão VM do Cofre chave para uma máquina virtual existente ou um conjunto de escala de máquina virtual. 

* Para implantar a extensão num VM:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Para implantar a extensão num conjunto de escala de máquina virtual:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Implantação Azure CLI

O Azure CLI pode ser utilizado para implantar a extensão VM do Cofre chave para uma máquina virtual existente ou conjunto de escala de máquina virtual. 
 
* Para implantar a extensão num VM:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Para implantar a extensão num conjunto de escala de máquina virtual:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Tenha em atenção as seguintes restrições/requisitos:
- Restrições do cofre chave:
  - Deve existir no momento da implantação 
  - A Política de Acesso ao Cofre chave está definida para identidade VM/VMSS usando MSI


## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das implementações de extensões podem ser recuperados a partir do portal Azure e utilizando o Azure PowerShell. Para ver o estado de implantação das extensões para um dado VM, execute o seguinte comando utilizando o Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>CLI do Azure
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

A saída de execução de extensão é registada no seguinte ficheiro:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
