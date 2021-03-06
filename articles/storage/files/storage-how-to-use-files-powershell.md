---
title: Início rápido para gerir partilhas de ficheiros do Azure com o Azure PowerShell
description: Utilize este início rápido para saber como gerir partilhas de ficheiros do Azure com o Azure PowerShell.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c419c2127b1c5fe3aaa60c6e828ff0c5a6676c07
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77598549"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Início Rápido: Criar e gerir uma partilha de ficheiros do Azure com o Azure PowerShell 
Este guia orienta-o pelas noções básicas da utilização de [partilhas de ficheiros do Azure](storage-files-introduction.md) com o PowerShell. As partilhas de ficheiros do Azure são como outras partilhas de ficheiros, mas armazenadas na cloud e apoiadas pela plataforma do Azure. As partilhas de ficheiros do Azure suportam o protocolo SMB padrão do setor e permite a partilha de ficheiros entre várias máquinas, aplicações e instâncias. 

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se quiser instalar e utilizar o PowerShell localmente, este guia requer o módulo Azure PowerShell a versão Az 0.7 ou posterior. Para saber qual é a versão do módulo do Azure PowerShell que está a executar, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também terá de executar `Login-AzAccount` para iniciar sessão na sua conta do Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Se ainda não tiver um grupo de recursos Azure, pode criar um novo com o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) 

O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na região oeste dos EUA 2:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>Criar uma conta do Storage
Uma conta de armazenamento é um conjunto partilhado de armazenamento que pode usar para implementar ações de ficheiros Azure. Uma conta de armazenamento pode conter um número ilimitado de partilhas e uma partilha pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento. Este exemplo cria uma versão geral da versão 2 (conta de armazenamento GPv2), que pode armazenar ações padrão de ficheiros Azure ou outros recursos de armazenamento, tais como bolhas ou filas, em meios de rotação de disco rígido (HDD). O Azure Files também suporta discos de estado sólido premium (SSDs); as ações de ficheiro saqueadas premium Azure podem ser criadas em contas de armazenamento de FileStorage.

Este exemplo cria uma conta de armazenamento utilizando o cmdlet [New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount) A conta de armazenamento é nomeada *número\<de armazenamento aleatório>* e uma referência a essa conta de armazenamento é armazenada na **variável $storageAcct**. Os nomes de contas de armazenamento têm de ser exclusivos, por isso, utilize `Get-Random` para anexar um número ao nome e torná-lo exclusivo. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> As ações superiores a 5 TiB (até um máximo de 100 TiB por ação) só estão disponíveis em contas de armazenamento redundantes localmente redundantes (LRS) e zonas redundantes (ZRS). Para criar uma conta de armazenamento geo-redundante (GRS) ou geozona-redundante (GZRS), remova o `-EnableLargeFileShare` parâmetro.

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Agora, pode criar a sua primeira partilha de ficheiros do Azure. Pode criar uma partilha de ficheiros utilizando o cmdlet [New-AzRmStorageShare.](/powershell/module/az.storage/New-AzRmStorageShare) Este exemplo cria uma partilha com o nome `myshare`.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

Os nomes das partilhas só podem ter letras minúsculas, números e hífenes, mas não podem começar com um hífen. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Utilizar a partilha de ficheiros do Azure
O serviço Ficheiros do Azure fornece dois métodos para utilizar ficheiros e pastas na sua partilha de ficheiros do Azure : o [protocolo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) padrão do setor e o [protocolo REST de Ficheiros](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Para montar uma partilha de ficheiros com SMB, veja o documento seguinte com base no seu SO:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Utilizar uma partilha de ficheiros do Azure com o protocolo REST de Ficheiros 
É possível trabalhar diretamente com o protocolo FILE REST (isto é, artesanato REST HTTP chama-se), mas a forma mais comum de utilizar o protocolo File REST é utilizar o módulo Azure PowerShell, o [Azure CLI,](storage-how-to-use-files-cli.md)ou um Azure Storage SDK, todos os quais fornecem um invólucro agradável em torno do protocolo File REST na linguagem de script/programação da sua escolha.  

Na maioria dos casos, irá utilizar a partilha de ficheiros do Azure através do protocolo SMB, pois permite-lhe utilizar as aplicações e ferramentas existentes, mas existem vários motivos pelos quais é vantajoso utilizar a API REST de Ficheiros em vez do SMB, tais como:

- Se estiver a navegar na partilha de ficheiros a partir do PowerShell Cloud Shell (que não pode montar partilhas de ficheiros através de SMB).
- Se estiver a tirar partido de recursos sem servidor, como o serviço [Funções do Azure](../../azure-functions/functions-overview.md). 
- Está a criar um serviço de valor acrescentado que irá interagir com muitas partilhas de ficheiros Azure, tais como a realização de backups ou antivírus.

Os exemplos seguintes mostram como usar o módulo Azure PowerShell para manipular a sua partilha de ficheiros Azure com o protocolo File REST. O `-Context` parâmetro é utilizado para recuperar a chave da conta de armazenamento para executar as ações indicadas contra a parte do ficheiro. Para recuperar a chave da conta de armazenamento, deve ter o papel RBAC na conta de `Owner` armazenamento.

#### <a name="create-directory"></a>Criar um diretório
Para criar um novo diretório nomeado *myDirectory* na raiz da sua partilha de ficheiros Azure, use o cmdlet [New-AzStorageDirectory.](/powershell/module/az.storage/New-AzStorageDirectory)

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Carregar um ficheiro
Para demonstrar como carregar um ficheiro utilizando o [cmdlet Set-AzStorageFileContentContent,](/powershell/module/az.storage/Set-AzStorageFileContent) primeiro precisamos de criar um ficheiro dentro da unidade de risco da PowerShell Cloud Shell para carregar. 

Este exemplo coloca a data e hora atuais num novo ficheiro no disco e, em seguida, carrega o ficheiro para a partilha de ficheiros.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Se estiver a executar o PowerShell localmente, deve substituir `~/CloudDrive/` por um caminho existente no seu computador.

Depois de fazer o upload do ficheiro, pode utilizar o [cmdlet Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) para verificar se o ficheiro foi enviado para a sua parte de ficheiro Azure. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Transferir um ficheiro
Pode utilizar o cmdlet [Get-AzStorageFileContent Content](/powershell/module/az.storage/Get-AzStorageFilecontent) para descarregar uma cópia do ficheiro que acabou de enviar para a unidade de risco da sua Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

Depois de transferir o ficheiro, pode utilizar `Get-ChildItem` para ver se o ficheiro foi transferido para a unidade do PowerShell Cloud Shell.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>Copiar ficheiros
Uma tarefa comum é copiar ficheiros de uma parte de ficheiro para outra partilha de ficheiros. Para demonstrar esta funcionalidade, pode criar uma nova partilha e copiar o ficheiro que acabou de enviar para esta nova partilha utilizando o cmdlet [Start-AzStorageFileCopy.](/powershell/module/az.storage/Start-AzStorageFileCopy) 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Agora, se listar os ficheiros na nova partilha, deverá ver o ficheiro copiado.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

Embora `Start-AzStorageFileCopy` o cmdlet seja conveniente para movimentos de ficheiros ad hoc entre as `robocopy` partilhas `rsync` de ficheiros Azure, para migrações e movimentos de dados maiores, recomendamos no Windows e no macOS e Linux. `robocopy`e `rsync` utilize o SMB para executar os movimentos de dados em vez da API FileREST.

## <a name="create-and-manage-share-snapshots"></a>Criar e gerir instantâneos de partilha
Uma tarefa útil adicional que pode fazer com uma partilha de ficheiros do Azure é criar instantâneos de partilha. Um instantâneo preserva um ponto no tempo para uma partilha de ficheiros do Azure. Os instantâneos de partilha são semelhantes às tecnologias de sistema operativo com as quais pode já estar familiarizado, tais como:

- Serviço de cópia de [sombra de volume (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) para sistemas de ficheiros Windows tais como NTFS e ReFS.
- Imagens de Gestor de [Volume Lógico (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux.
- Instantâneos do [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS. 

Pode criar uma imagem de partilha `Snapshot` para uma parte utilizando o método no objeto PowerShell para uma partilha de ficheiros, que é recuperada com o cmdlet [Get-AzStorageShare.](/powershell/module/az.storage/Get-AzStorageShare) 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Procurar instantâneos de partilha
Pode procurar o conteúdo do instantâneo de partilha ao transmitir a referência do instantâneo (`$snapshot`) ao parâmetro `-Share` do cmdlet `Get-AzStorageFile`.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Listar instantâneos de partilha
Pode ver a lista de instantâneos que criou para a sua partilha com o seguinte comando.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Restaurar a partir de um instantâneo de partilha
Pode restaurar um ficheiro com o comando `Start-AzStorageFileCopy` utilizado anteriormente. Para efeitos deste início rápido, vamos eliminar primeiro o ficheiro `SampleUpload.txt` carregado anteriormente para podermos restaurá-lo a partir do instantâneo.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Eliminar um instantâneo de partilha
Pode eliminar uma fotografia de partilha utilizando o cmdlet [Remove-AzStorageShare,](/powershell/module/az.storage/Remove-AzStorageShare) com a variável que contém a `$snapshot` referência ao `-Share` parâmetro.

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Limpar recursos
Quando terminar, pode utilizar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos relacionados. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Em alternativa, pode remover recursos um a um:

- Para remover as partilhas de ficheiros do Azure criadas para este início rápido.

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Tem de eliminar todas as imagens de partilha das ações do ficheiro Azure que criou antes de eliminar a parte do ficheiro Azure.

- Para remover a própria conta de armazenamento (isto removerá implicitamente as partilhas de ficheiros do Azure, bem como quaisquer outros recursos de armazenamento que possa ter criado, como um contentor de armazenamento de Blobs do Azure).

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [O que são os Ficheiros do Azure?](storage-files-introduction.md)