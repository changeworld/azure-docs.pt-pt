---
title: Encriptação do lado do servidor de Discos Geridos Azure - Azure CLI
description: O Azure Storage protege os seus dados encriptando-os em repouso antes de persistir nos clusters de Armazenamento. Pode contar com chaves geridas pela Microsoft para a encriptação dos seus discos geridos, ou pode utilizar chaves geridas pelo cliente para gerir a encriptação com as suas próprias chaves.
author: roygara
ms.date: 04/02/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: f7eb63d0bbdce86f4a7195430dc15d6873e9f6e6
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754310"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Encriptação do lado do servidor dos discos geridos pelo Azure

Os discos geridos pelo Azure encriptam automaticamente os seus dados por padrão quando os persistem na nuvem. A encriptação do lado do servidor protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade. Os dados em discos geridos pelo Azure são encriptados de forma transparente utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com O FIPS 140-2.   

A encriptação não afeta o desempenho dos discos geridos. Não há custos adicionais para a encriptação.

Para obter mais informações sobre os módulos criptográficos subjacentes aos discos geridos pelo Azure, consulte [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Sobre a gestão da chave de encriptação

Pode confiar em chaves geridas pela plataforma para a encriptação do seu disco gerido, ou pode gerir a encriptação usando as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, pode especificar uma *chave gerida pelo cliente* para encriptar e desencriptar todos os dados em discos geridos. 

As seguintes secções descrevem cada uma das opções para a gestão chave em maior detalhe.

## <a name="platform-managed-keys"></a>Chaves geridas pela plataforma

Por padrão, os discos geridos utilizam chaves de encriptação geridas pela plataforma. A partir de 10 de junho de 2017, todos os novos discos geridos, instantâneos, imagens e novos dados escritos aos discos geridos existentes são automaticamente encriptados em repouso com chaves geridas pela plataforma.

## <a name="customer-managed-keys"></a>Chaves geridas pelo cliente

Pode optar por gerir a encriptação ao nível de cada disco gerido, com as suas próprias chaves. A encriptação do lado do servidor para discos geridos com chaves geridas pelo cliente oferece uma experiência integrada com o Azure Key Vault. Pode importar [as suas chaves RSA](../../key-vault/key-vault-hsm-protected-keys.md) para o seu Cofre chave ou gerar novas chaves RSA no Cofre de Chaves Azure. 

Os discos geridos pelo Azure tratam da encriptação e da desencriptação de uma forma totalmente transparente usando [encriptação](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)de envelopes. Encripta dados utilizando uma chave de encriptação de dados baseada em [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (DEK), que está, por sua vez, protegida usando as suas chaves. O serviço de Armazenamento gera chaves de encriptação de dados e encripta-as com chaves geridas pelo cliente utilizando encriptação RSA. A encriptação do envelope permite-lhe rodar (alterar) as suas chaves periodicamente de acordo com as suas políticas de conformidade sem afetar os seus VMs. Quando roda as chaves, o serviço de armazenamento encripta as chaves de encriptação de dados com as novas chaves geridas pelo cliente. 

Tem de conceder acesso a discos geridos no seu Cofre de Chaves para utilizar as suas chaves para encriptar e desencriptar o DEK. Isto permite-lhe o controlo total dos seus dados e chaves. Pode desativar as chaves ou revogar o acesso aos discos geridos a qualquer momento. Também pode auditar o uso da chave de encriptação com monitorização do Cofre de Chaves Azure para garantir que apenas discos geridos ou outros serviços Azure fidedignos acedem às suas chaves.

Para SSDs premium, SSDs padrão e HDDs padrão: Quando desativar ou eliminar a sua chave, quaisquer VMs com discos que utilizem essa tecla desligam-se automaticamente. Depois disso, os VMs não serão utilizáveis a menos que a chave esteja ativada novamente ou atribua uma nova chave.

Para discos ultra, quando desativa ou elimina uma chave, quaisquer VMs com discos ultra que utilizem a tecla não desligam automaticamente. Assim que deslocar e reiniciar os VMs, os discos deixarão de usar a chave e os VMs não voltarão a funcionar. Para voltar a colocar os VMs online, deve atribuir uma nova tecla ou ativar a chave existente.

O diagrama seguinte mostra como os discos geridos utilizam o Azure Ative Directory e o Azure Key Vault para fazer pedidos utilizando a chave gerida pelo cliente:

![Fluxo de trabalho gerido pelo disco e chaves geridas pelo cliente. Um administrador cria um Cofre de Chave Azure, depois cria um conjunto de encriptação de disco e configura o conjunto de encriptação do disco. O Conjunto está associado a um VM, que permite ao disco fazer uso da AD Azure para autenticar](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


A seguinte lista explica o diagrama com ainda mais detalhes:

1. Um administrador do Cofre Chave Azure cria recursos chave do cofre.
1. O cofre chave importa as chaves RSA para o Cofre chave ou gera novas chaves RSA no Cofre chave.
1. Este administrador cria uma instância de recurso de Conjunto de Encriptação de Disco, especificando um ID do cofre de chaves Azure e um URL chave. O Conjunto de Encriptação do Disco é um novo recurso introduzido para simplificar a gestão da chave para discos geridos. 
1. Quando um conjunto de encriptação de disco é criado, uma [identidade gerida atribuída](../../active-directory/managed-identities-azure-resources/overview.md) pelo sistema é criada em Azure Ative Directory (AD) e associada ao conjunto de encriptação do disco. 
1. O administrador do cofre chave Azure concede então a permissão de identidade gerida para realizar operações no cofre chave.
1. Um utilizador vM cria discos associando-os ao conjunto de encriptação do disco. O utilizador VM também pode ativar a encriptação do lado do servidor com chaves geridas pelo cliente para os recursos existentes, associando-os com o conjunto de encriptação do disco. 
1. Os discos geridos usam a identidade gerida para enviar pedidos para o Cofre chave Azure.
1. Para ler ou escrever dados, os discos geridos enviam pedidos ao Azure Key Vault para encriptar (embrulhar) e desencriptar (desembrulhar) a chave de encriptação de dados para realizar encriptação e desencriptação dos dados. 

Para revogar o acesso às chaves geridas pelo cliente, consulte [o Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [o Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso bloqueia efetivamente o acesso a todos os dados da conta de armazenamento, uma vez que a chave de encriptação é inacessível pelo Armazenamento Azure.

### <a name="supported-regions"></a>Regiões suportadas

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Restrições

Por enquanto, as chaves geridas pelo cliente têm as seguintes restrições:

- Se esta função estiver ativada para o disco, não poderá desativá-la.
    Se precisa de trabalhar em torno disto, tem de [copiar todos os dados](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) para um disco gerido completamente diferente que não esteja a utilizar chaves geridas pelo cliente.
- Apenas são suportadas [teclas RSA "macias" e "duras"](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) do tamanho 2048, sem outras teclas ou tamanhos.
- Os discos criados a partir de imagens personalizadas que são encriptadas utilizando encriptação do lado do servidor e chaves geridas pelo cliente devem ser encriptados utilizando as mesmas chaves geridas pelo cliente e devem estar na mesma subscrição.
- As imagens criadas a partir de discos que são encriptados com encriptação do lado do servidor e chaves geridas pelo cliente devem ser encriptadas com as mesmas chaves geridas pelo cliente.
- Imagens personalizadas encriptadas utilizando encriptação do lado do servidor e chaves geridas pelo cliente não podem ser usadas na galeria de imagens partilhadas.
- Todos os recursos relacionados com as suas chaves geridas pelo cliente (Cofres chave Azure, conjuntos de encriptação de discos, VMs, discos e instantâneos) devem estar na mesma subscrição e região.
- Discos, instantâneos e imagens encriptadas com chaves geridas pelo cliente não podem mover-se para outra subscrição.
- Se utilizar o portal Azure para criar o seu conjunto de encriptação de discos, não pode utilizar instantâneos por enquanto.
- Os discos geridos encriptados utilizando chaves geridas pelo cliente também não podem ser encriptados com encriptação do disco Azure.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configuração do seu cofre de chave azure e conjunto de encriptação de disco

1. Certifique-se de que instalou o mais recente [Azure CLI](/cli/azure/install-az-cli2) e iniciou uma conta Azure com [login az](/cli/azure/reference-index).

1. Crie uma instância de Cofre chave Azure e chave de encriptação.

    Ao criar a instância Key Vault, deve ativar a proteção de eliminação suave e purga. A eliminação suave garante que o Cofre-Chave detém uma chave eliminada durante um determinado período de retenção (padrão de 90 dias). A proteção da purga garante que uma chave eliminada não pode ser eliminada permanentemente até que o período de retenção caduque. Estas definições protegem-no de perder dados devido à eliminação acidental. Estas definições são obrigatórias quando se utiliza um Cofre chave para encriptar discos geridos.

    > [!IMPORTANT]
    > Não case camelo na região, se o fizer poderá ter problemas ao atribuir discos adicionais ao recurso no portal Azure.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Crie uma instância de um DiskEncryptionSet. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Conceda o acesso ao recurso DiskEncryptionSet ao cofre da chave. 

        > [!NOTE]
        > Pode levar alguns minutos para o Azure criar a identidade do seu DiskEncryptionSetSet no seu Diretório Ativo Azure. Se tiver um erro como "Não encontrar o objeto de Diretório Ativo" ao executar o seguinte comando, aguarde alguns minutos e tente novamente.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Crie um VM utilizando uma imagem do Marketplace, encriptando o OS e os discos de dados com chaves geridas pelo cliente

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-unattached-managed-disks"></a>Criptografe discos geridos não ligados existentes 

Os seus discos existentes não devem ser ligados a um VM em execução para que os criptografe utilizando o seguinte script:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Crie um conjunto de escala de máquina virtual utilizando uma imagem do Marketplace, encriptando o OS e os discos de dados com chaves geridas pelo cliente

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Crie um disco vazio encriptado utilizando encriptação do lado do servidor com chaves geridas pelo cliente e fixe-o a um VM

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=westcentralus
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Altere a chave de um DiskEncryptionSet para rodar a chave para todos os recursos referentes ao DiskEncryptionSet

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Encontre o estado da encriptação do lado do servidor de um disco

```azurecli

az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv

```

> [!IMPORTANT]
> As chaves geridas pelo cliente baseiam-se em identidades geridas para os recursos Azure, uma característica do Azure Ative Directory (Azure AD). Quando configura as chaves geridas pelo cliente, uma identidade gerida é automaticamente atribuída aos seus recursos sob as capas. Se posteriormente deslocar a subscrição, o grupo de recursos ou o disco gerido de um diretório DaD Azure para outro, a identidade gerida associada aos discos geridos não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para mais informações, consulte a Transferência de uma subscrição entre os [diretórios da AD azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente baseiam-se em identidades geridas para os recursos Azure, uma característica do Azure Ative Directory (Azure AD). Quando configura as chaves geridas pelo cliente, uma identidade gerida é automaticamente atribuída aos seus recursos sob as capas. Se posteriormente deslocar a subscrição, o grupo de recursos ou o disco gerido de um diretório DaD Azure para outro, a identidade gerida associada aos discos geridos não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para mais informações, consulte a Transferência de uma subscrição entre os [diretórios da AD azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Encriptação do lado do servidor versus encriptação do disco Azure

[A encriptação do disco Azure para máquinas virtuais e conjuntos](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) de escala de máquinas virtuais aproveita a funcionalidade [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) do Windows e a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para encriptar discos geridos com chaves geridas pelo cliente dentro do VM convidado.  A encriptação do lado do servidor com as chaves geridas pelo cliente melhora no ADE, permitindo-lhe utilizar quaisquer tipos e imagens de SO para os seus VMs encriptando dados no serviço de Armazenamento.

## <a name="next-steps"></a>Passos seguintes

- [Explore os modelos do Gestor de Recursos Azure para criar discos encriptados com chaves geridas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o cofre de chave do Azure?](../../key-vault/key-vault-overview.md)
- [Replicar máquinas com chaves geridas pelo cliente discos habilitados](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurar a recuperação de desastres de VMware VMs para Azure com powerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurar a recuperação de desastres para o Azure para VMs hiper-V utilizando o PowerShell e o Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
