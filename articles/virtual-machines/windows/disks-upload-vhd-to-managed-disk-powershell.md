---
title: Faça upload de um VHD para Azure ou copie um disco em todas as regiões - Azure PowerShell
description: Aprenda a carregar um VHD para um disco gerido pelo Azure e copie um disco gerido em todas as regiões, utilizando o Azure PowerShell, através do upload direto.
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 55606aeeb9f6445027f5da49821dbc4970764ade
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421053"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Faça upload de um VHD para Azure ou copie um disco gerido para outra região - Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Descarregue a versão mais recente [do AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Instale o módulo PowerShell Azure](/powershell/azure/install-Az-ps).
- Se pretender fazer o upload de um VHD a partir do local: Um VHD de tamanho fixo que [foi preparado para o Azure,](prepare-for-upload-vhd-image.md)armazenado localmente.
- Ou, um disco gerido em Azure, se pretender realizar uma ação de cópia.

## <a name="getting-started"></a>Introdução

Se preferir fazer o upload de discos através de um GUI, pode fazê-lo usando o Azure Storage Explorer. Para mais detalhes consulte: [Use Azure Storage Explorer para gerir discos geridos pelo Azure](disks-use-storage-explorer-managed-disks.md)

Para fazer o upload do seu VHD para o Azure, terá de criar um disco gerido vazio que esteja configurado para este processo de upload. Antes de criar um, há algumas informações adicionais que deve saber sobre estes discos.

Este tipo de disco gerido tem dois estados únicos:

- ReadToUpload, o que significa que o disco está pronto para receber um upload mas não foi gerada nenhuma assinatura de [acesso segura](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, o que significa que o disco está pronto para receber um upload e o SAS foi gerado.

> [!NOTE]
> Enquanto em qualquer um destes estados, o disco gerido será faturado com [preços HDD padrão,](https://azure.microsoft.com/pricing/details/managed-disks/)independentemente do tipo real de disco. Por exemplo, um P10 será cobrado como um S10. Isto será verdade `revoke-access` até que seja chamado no disco gerido, que é necessário para ligar o disco a um VM.

## <a name="create-an-empty-managed-disk"></a>Criar um disco gerido vazio

Antes de poder criar um HDD padrão vazio para o upload, vai precisar do tamanho do ficheiro do VHD que pretende carregar, em bytes. O código de exemplo vai dar-lhe isso, mas, para fazê-lo por si mesmo, pode usar: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. Este valor é utilizado ao especificar o parâmetro **-UploadSizeInBytes.**

Agora, na sua concha local, crie um HDD padrão vazio para carregar especificando a definição de **Upload** no parâmetro **-CreateOption,** bem como o parâmetro **-UploadSizeInBytes** no cmdlet [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Em seguida, ligue para [o New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) para criar o disco.

Substitua, `<yourdiskname>` `<yourresourcegroupname>` `<yourregion>` e, em seguida, executar os seguintes comandos:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Se quiser fazer o upload de um SSD premium ou de um SSD padrão, substitua **Standard_LRS** por **Premium_LRS** ou **StandardSSD_LRS**. Os discos ultra ainda não são suportados.

Agora que criou um disco gerido vazio que está configurado para o processo de upload, pode enviar-lhe um VHD. Para fazer o upload de um VHD para o disco, você precisará de um SAS rectilável, para que possa referenciar como o destino para o seu upload.

Para gerar um SAS de supreensível `<yourdiskname>` `<yourresourcegroupname>`do seu disco gerido vazio, substitua e , em seguida, use os seguintes comandos:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Carregar um VHD

Agora que tem um SAS para o seu disco gerido vazio, pode usá-lo para definir o disco gerido como destino para o seu comando de upload.

Utilize o AzCopy v10 para fazer o upload do ficheiro VHD local para um disco gerido, especificando o SAS URI que gerou.

Este upload tem a mesma entrada que o [HDD padrão](disks-types.md#standard-hdd)equivalente . Por exemplo, se tiver um tamanho que equivale a S4, terá uma potência de até 60 MiB/s. Mas, se tiver um tamanho que equivale a S70, terá uma potência de até 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Depois de o upload estar completo e já não precisar de escrever mais dados para o disco, revogar o SAS. A revogação do SAS alterará o estado do disco gerido e permitirá que prenda o disco a um VM.

`<yourdiskname>`Substitua `<yourresourcegroupname>`e, em seguida, execute o seguinte comando:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Copiar um disco gerido

O upload direto também simplifica o processo de cópia de um disco gerido. Pode copiar dentro da mesma região ou copiar o disco gerido para outra região.

O guião seguinte fará isto por si, o processo é semelhante aos passos descritos anteriormente, com algumas diferenças, uma vez que está a trabalhar com um disco existente.

> [!IMPORTANT]
> Tem de adicionar uma compensação de 512 quando estiver a fornecer o tamanho do disco em bytes de um disco gerido a partir do Azure. Isto porque Azure omite o rodapé ao devolver o tamanho do disco. A cópia falhará se não fizer isto. O seguinte guião já faz isto por ti.

Substitua `<sourceResourceGroupHere>` `<sourceDiskNameHere>`o `<targetDiskNameHere>` `<targetResourceGroupHere>`, `<yourOSTypeHere>` `<yourTargetLocationHere>` , , e (um exemplo de um valor de localização seria uswest2) com os seus valores, em seguida, executar o seguinte script para copiar um disco gerido.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Passos seguintes

Agora que carregou com sucesso um VHD para um disco gerido, pode ligar o disco a um VM e começar a usá-lo.

Para aprender a anexar um disco de dados a um VM, consulte o nosso artigo sobre o assunto: Fixe um disco de [dados a um VM windows com PowerShell](attach-disk-ps.md). Para utilizar o disco como disco OS, consulte [Criar um VM windows a partir de um disco especializado](create-vm-specialized.md#create-the-new-vm).
