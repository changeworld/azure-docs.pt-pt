---
title: Série NCv2 - Máquinas Virtuais Azure
description: Especificações para os VMs da série NCv2.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 3643fbabef08d890ce121d41a9bc1eb40c88459d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273918"
---
# <a name="ncv2-series"></a>Série NCv2

Os VMs da série NCv2 são alimentados por [GPUs NVIDIA Tesla P100.](https://www.nvidia.com/data-center/tesla-p100/) Estas GPUs podem fornecer mais de 2x o desempenho computacional da série NC. Os clientes podem tirar partido destas GPUs atualizadas para cargas de trabalho tradicionais de HPC, tais como modelação de reservatórios, sequenciação de ADN, análise de proteínas, simulações de Monte Carlo, entre outros. Além das GPUs, os VMs da série NCv2 também são alimentados por CpUs Intel Xeon E5-2690 v4 (Broadwell).

A configuração NC24rs v2 proporciona uma interface de rede de baixa latência e alta potência otimizada para trabalhos de computação paralela salutares.

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração Ao Vivo: Não Suportado

Atualizações de preservação da memória: não suportadas

> [!IMPORTANT]
> Para esta série VM, a quota vCPU (core) na sua subscrição está inicialmente definida para 0 em cada região. [Solicite um aumento de quota vCPU](../azure-supportability/resource-manager-core-quotas-request.md) para esta série numa [região disponível.](https://azure.microsoft.com/regions/services/)
>
| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | Potência de disco max uncached: IOPS/MBps | NICs máximos |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = um cartão P100.

*Com capacidade RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

Para tirar partido das capacidades de GPU das VMs da série N Azure, os condutores de GPU da NVIDIA devem ser instalados.

A Extensão do [Condutor GPU da NVIDIA](./extensions/hpccompute-gpu-windows.md) instala os condutores adequados da NVIDIA CUDA ou grid num VM da série N. Instale ou gerea extensão utilizando o portal Azure ou ferramentas como os modelos Azure PowerShell ou Azure Resource Manager. Consulte a documentação de extensão do [condutor da NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) para sistemas operativos suportados e etapas de implementação. Para obter informações gerais sobre extensões VM, consulte [extensões e funcionalidades da máquina virtual Azure.](./extensions/overview.md)

Se optar por instalar manualmente os controladores GPU da NVIDIA, consulte a configuração do controlador GPU da série N para a configuração do controlador [GPU da série N](./windows/n-series-driver-setup.md) para o [Linux](./linux/n-series-driver-setup.md) para sistemas operativos suportados, controladores, instalação e verificação.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.
