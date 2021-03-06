---
title: Partilhe imagens de galerias entre inquilinos em Azure
description: Saiba como partilhar imagens vm em inquilinos do Azure usando Galerias de Imagem Partilhada.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 18337620a6f9506e402149909667026e4a8ba7eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034978"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Partilhar imagens vM da galeria através dos inquilinos do Azure

As Galerias de Imagem Partilhada permitem-lhe partilhar imagens usando o RBAC. Você pode usar RBAC para partilhar imagens dentro do seu inquilino, e até mesmo para indivíduos fora do seu inquilino. Para mais informações sobre esta simples opção de partilha, consulte a [Partilha da galeria.](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery)

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Não é possível usar o portal para implantar um VM a partir de uma imagem em outro inquilino azul. Para criar um VM a partir de uma imagem partilhada entre inquilinos, você deve usar o Azure CLI ou [Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Criar um VM usando o Azure CLI

Inscreva-se no principal de serviço para o inquilino 1 usando o appID, a chave da aplicação e a identificação do inquilino 1. Pode usar `az account show --query "tenantId"` para obter as identificações dos inquilinos, se necessário.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Inscreva-se no principal de serviço para inquilino 2 usando o appID, a chave da aplicação e a identificação do inquilino 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Crie o VM. Substitua a informação no exemplo com a sua.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Passos seguintes

Se tiver algum problema, pode resolver as galerias de [imagens partilhadas.](troubleshooting-shared-images.md)