---
title: Descarregue o modelo para um Azure VM
description: Descarregue o modelo de um VM para ajudar a automatizar implementações no modelo de implementação do Gestor de Recursos
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: c73026515f0d7fde4e2f82838696700b1bb17c77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033553"
---
# <a name="download-the-template-for-a-vm"></a>Transferir o modelo para uma VM
Quando cria um VM em Azure utilizando o portal ou powerShell, um modelo de Gestor de Recursos é automaticamente criado para si. Pode usar este modelo para duplicar rapidamente uma implementação. O modelo contém informações sobre todos os recursos de um grupo de recursos. Para uma máquina virtual, isto significa que o modelo contém tudo o que é criado para apoiar o VM nesse grupo de recursos, incluindo os recursos de rede.

## <a name="download-the-template-using-the-portal"></a>Descarregue o modelo usando o portal
1. Faça login no [portal Azure.](https://portal.azure.com/)
2. Um menu esquerdo, selecione **Máquinas Virtuais**.
3. Selecione a máquina virtual na lista.
4. Selecione **modelo de exportação**.
5. Selecione **Baixar** a partir do menu na parte superior e guardar o ficheiro .zip para o seu computador local.
6. Abra o ficheiro .zip e extrai os ficheiros para uma pasta. O ficheiro .zip contém:
   
   * parâmetros.json
   * modelo.json

O ficheiro modelo.json é o modelo.

## <a name="download-the-template-using-powershell"></a>Descarregue o modelo usando powerShell
Também pode descarregar o ficheiro de modelo .json utilizando o cmdlet [Export-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) Pode utilizar `-path` o parâmetro para fornecer o nome de ficheiro e o caminho para o ficheiro .json. Este exemplo mostra como descarregar o modelo para o grupo de recursos chamado **myResourceGroup** para a pasta **c:\users\public\downloads** no seu computador local.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a implementação de recursos usando modelos, consulte o [modelo de](../../azure-resource-manager/resource-manager-template-walkthrough.md)gestor de recursos .

