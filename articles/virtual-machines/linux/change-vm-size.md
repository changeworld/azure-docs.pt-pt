---
title: Como redimensionar um VM Linux com o Azure CLI
description: Como escalar ou escalar uma máquina virtual Linux, alterando o tamanho vm.
author: mikewasson
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: mwasson
ms.openlocfilehash: 20e7db80b55347c4a4a76b7c95d4d8bec368abda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969267"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Redimensionar uma máquina virtual do Linux com a CLI do Azure 

Depois de fornecer uma máquina virtual (VM), pode escalar o VM para cima ou para baixo alterando o [tamanho VM][vm-sizes]. Em alguns casos, primeiro deve desalocar o VM. Você precisa desalojar o VM se o tamanho desejado não estiver disponível no cluster de hardware que está hospedando o VM. Este artigo detalha como redimensionar um VM Linux com o Azure CLI. 

## <a name="resize-a-vm"></a>Redimensionar uma VM
Para redimensionar um VM, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e registado numa conta Azure utilizando [login az](/cli/azure/reference-index).

1. Veja a lista de tamanhos VM disponíveis no cluster de hardware onde o VM está hospedado com opções de [lista az vm-vm-resize](/cli/azure/vm). O exemplo seguinte lista os tamanhos `myVM` vm para `myResourceGroup` o VM nomeado na região do grupo de recursos:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Se o tamanho de VM desejado estiver listado, redimensione o VM com [redimensionado az vm](/cli/azure/vm). O exemplo seguinte redimensiona `myVM` o `Standard_DS3_v2` VM nomeado para o tamanho:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    O VM reinicia durante este processo. Após o reinício, os seus sistemas operativos e de dados existentes são remapeados. Qualquer coisa no disco temporário está perdida.

3. Se o tamanho de VM desejado não estiver listado, é necessário primeiro desalojar o VM com [desalocado az vm](/cli/azure/vm). Este processo permite que o VM seja então redimensionado para qualquer tamanho disponível que a região apoie e depois tenha começado. Os seguintes passos desalocam, redimensionam e, em seguida, iniciem o VM nomeado `myVM` no grupo de recursos denominado: `myResourceGroup`
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > A locação do VM também lança quaisquer endereços IP dinâmicos atribuídos ao VM. O Sistema operativo e os discos de dados não são afetados.

## <a name="next-steps"></a>Passos seguintes
Para maior escalabilidade, executar várias instâncias vm e escalar para fora. Para mais informações, consulte [automaticamente as máquinas Linux num conjunto][scale-set]de escala de máquina virtual . 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
