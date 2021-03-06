---
title: Anexar um disco de dados gerido a um Windows VM - Azure
description: Como anexar um disco de dados gerido a um VM do Windows utilizando o portal Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0fe04941821de2ac6e4e873e8d073c3e9b9d9508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919384"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Fixe um disco de dados gerido a um VM do Windows utilizando o portal Azure

Este artigo mostra-lhe como anexar um novo disco de dados gerido a uma máquina virtual do Windows (VM) utilizando o portal Azure. O tamanho do VM determina quantos discos de dados pode anexar. Para mais informações, consulte [Tamanhos para máquinas virtuais](sizes.md).


## <a name="add-a-data-disk"></a>Adicione um disco de dados

1. Vá ao [portal Azure](https://portal.azure.com) para adicionar um disco de dados. Procure e selecione **máquinas Virtuais**.
2. Selecione uma máquina virtual da lista.
3. Na página da **máquina Virtual,** selecione **Discos**.
4. Na página **De Discos,** selecione **Adicionar disco de dados**.
5. Na gota para o novo disco, selecione **Criar disco**.
6. Na página de **disco gerida** create, digite um nome para o disco e ajuste as outras definições conforme necessário. Quando terminar, selecione **Criar**.
7. Na página **De Discos,** selecione **Guardar** para guardar a nova configuração do disco para o VM.
8. Depois de o Azure criar o disco e ligá-lo à máquina virtual, o novo disco está listado nas definições do disco da máquina virtual sob **os discos data**.


## <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Ligue à VM.
1. Selecione o menu Windows **Iniciar** dentro do VM em execução e introduza **diskmgmt.msc** na caixa de pesquisa. A consola **de Gestão de Discos** abre.
2. A Disk Management reconhece que tem um disco novo e não inicializado e aparece a janela **do Disco Inicialize.**
3. Verifique se o novo disco está selecionado e, em seguida, selecione **OK** para inicializá-lo.
4. O novo disco aparece como **não atribuído**. Clique à direita em qualquer lugar do disco e selecione **Novo volume simples**. A janela **do Novo Assistente** de Volume Simples abre.
5. Proceda através do assistente, mantendo todos os predefinições, e quando terminar, selecione **Finish**.
6. Fechar **a Gestão do Disco.**
7. Uma janela pop-up aparece notificando-o de que precisa de formatar o novo disco antes de o poder utilizar. Selecione **o disco de formato**.
8. Na nova janela do **formato,** verifique as definições e, em seguida, selecione **Iniciar**.
9. Aparece um aviso a notificá-lo de que a formatação dos discos apaga todos os dados. Selecione **OK**.
10. Quando a formatação estiver completa, selecione **OK**.

## <a name="next-steps"></a>Passos seguintes

- Também pode anexar um disco de [dados utilizando powerShell](attach-disk-ps.md).
- Se a sua aplicação necessitar de utilizar o *D:* dirija para armazenar dados, pode [alterar a letra de unidade do disco temporário windows](change-drive-letter.md).
