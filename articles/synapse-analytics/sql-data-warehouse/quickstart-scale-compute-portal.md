---
title: Computação em escala para piscina Synapse SQL (portal Azure)
description: Pode escalar a computação para a piscina SYnapse SQL (data warehouse) utilizando o portal Azure.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f92152658b9db83740ffc2de2dc6956003849e06
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350825"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Quickstart: Scale compute for Synapse SQL pool with the Azure portal

Pode escalar a computação para a piscina SYnapse SQL (data warehouse) utilizando o portal Azure. [Dimensionar a computação](sql-data-warehouse-manage-compute-overview.md) para um melhor desempenho ou a escalar a computação novamente para reduzir os custos. 

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Você pode escalar um pool SQL que você já tem ou usar [Quickstart: criar e conectar - portal](create-data-warehouse-portal.md) para criar um pool SQL chamado **mySampleDataWarehouse**. Este início rápido dimensiona **mySampleDataWarehouse**.

>[!IMPORTANT] 
>A sua piscina SQL deve estar on-line à escala. 

## <a name="scale-compute"></a>Dimensionar computação

Os recursos de computação de piscina SQL podem ser dimensionados através do aumento ou diminuição das unidades de armazém de dados. O quickstart [criar e ligar - portal] quickstart (create-data-warehouse-portal.md) criou o **mySampleDataWarehouse** e ininicializou-o com 400 DWUs. Os seguintes passos ajustam as DWUs para **mySampleDataWarehouse**.

Para alterar as unidades do data warehouse:

1. Clique em **Azure Synapse Analytics (anteriormente SQL DW)** na página esquerda do portal Azure.
2. Selecione **mySampleDataWarehouse** da página **Azure Synapse Analytics (anteriormente SQL DW).** A piscina SQL abre.
3. Clique em **Escalar**.

    ![Clique em Escalar](./media/quickstart-scale-compute-portal/click-scale.png)

2. No painel Escalar, mova o controlo de deslize para a esquerda ou direita para alterar a definição de DWU. Em seguida, selecione a escala.

    ![Mova o Controlo de Deslize](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o pool SQL, continue a carregar os dados em tutorial de [piscina SQL.](load-data-from-azure-blob-storage-using-polybase.md) 
