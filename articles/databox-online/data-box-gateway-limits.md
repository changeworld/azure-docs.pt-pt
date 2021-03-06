---
title: Limites de Gateway da Caixa de Dados Azure Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para o Gateway da Caixa de Dados do Microsoft Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60755229"
---
# <a name="azure-data-box-gateway-limits"></a>Limites de Gateway da Caixa de Dados Azure

Considere estes limites à medida que implementa e opera a sua solução Microsoft Azure Data Box Gateway. 


## <a name="data-box-gateway-service-limits"></a>Limites de serviço de Data Box Gateway

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limites do dispositivo Gateway data Box

A tabela seguinte descreve os limites para o dispositivo Data Box Gateway.

| Descrição | Valor |
|---|---|
|Não. de ficheiros por dispositivo |100 milhões <br> Limite é ~ 25 milhões de ficheiros por cada 2 TB de espaço em disco com limite máximo de 100 milhões |
|Não. de ações por dispositivo |24 |
|Não. de ações por contentor de armazenamento Azure |1 |
|Tamanho máximo do ficheiro escrito a uma parte|Para um dispositivo virtual de 2 TB, o tamanho máximo do ficheiro é de 500 GB. <br> O tamanho máximo do ficheiro aumenta com o tamanho do disco de dados na razão anterior até atingir um máximo de 5 TB. |

## <a name="azure-storage-limits"></a>Limites de armazenamento azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações do carregamento de dados

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Tamanho da conta de armazenamento azure e limites de tamanho do objeto

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto azul

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Passos seguintes

- [Preparar para implementar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
