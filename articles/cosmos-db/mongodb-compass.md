---
title: Ligue-se ao Azure Cosmos DB usando a Bússola
description: Aprenda a usar a Bússola MongoDB para armazenar e gerir dados em Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: c683ec0c4b3a536b0627a7c1c8abf28ee4f83663
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757024"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Use a Bússola MongoDB para ligar à API da Azure Cosmos DB para o MongoDB

Este tutorial demonstra como usar a [Bússola MongoDB](https://www.mongodb.com/products/compass) ao armazenar e/ou gerir dados em Cosmos DB. Utilizamos a API do Azure Cosmos DB para o MongoDB para este walk-through. Para aqueles que não são familiares, a Bússola é um GUI para o MongoDB. É comumente usado para visualizar os seus dados, executar consultas ad-hoc, juntamente com a gestão dos seus dados.

Cosmos DB é o serviço de base de dados multimodelo distribuído globalmente pela Microsoft. Você pode rapidamente criar e consultar documentos, bases de dados chave/valor e gráficos, que beneficiam da distribuição global e capacidades de escala horizontal no núcleo da Cosmos DB.

## <a name="pre-requisites"></a>Pré-requisitos

Para se ligar à sua conta Cosmos DB usando a Bússola MongoDB, deve:

* Descarregue e instale [a Bússola](https://www.mongodb.com/download-center/compass?jmp=hero)
* Tenha a sua informação de cadeia de [conexão](connect-mongodb-account.md) Cosmos DB

> [!NOTE]
> Atualmente, a API do Azure Cosmos DB para a versão 3.2 do MongoDB Server é suportada com a Bússola MongoDB.

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Ligue-se à API da Cosmos DB para o MongoDB

Para ligar a sua conta Cosmos DB à Bússola, pode seguir os passos abaixo:

1. Recupere as informações de ligação para a sua conta Cosmos configuradas com o API MongoDB da Azure Cosmos DB utilizando as instruções [aqui](connect-mongodb-account.md).

    ![Screenshot da lâmina de corda de ligação](./media/mongodb-compass/mongodb-compass-connection.png)

2. Clique no botão que diz **Copiar para recortar** ao lado da sua cadeia de **ligação primária/secundária** em Cosmos DB. Clicar neste botão irá copiar toda a sua cadeia de ligação à sua área de redação.

    ![Screenshot da cópia para botão de clipboard](./media/mongodb-compass/mongodb-connection-copy.png)

3. Abra a Bússola no seu ambiente de trabalho/máquina e clique em **Connect** e, em seguida, **Ligue para...**.

4. A bússola detetará automaticamente uma cadeia de ligação na área de sobre-a-bordo e irá solicitar-lhe que pergunte se pretende utilizá-la para se ligar. Clique em **Sim** como mostrado na imagem abaixo.

    ![Screenshot da bússola pronta para ligar](./media/mongodb-compass/mongodb-compass-detect.png)

5. Ao clicar **Sim** no passo acima, os seus detalhes da cadeia de ligação serão automaticamente povoados. Retire o valor automaticamente povoado no campo **'Nome** do Conjunto de Réplicas' para garantir que fica em branco.

    ![Screenshot da bússola pronta para ligar](./media/mongodb-compass/mongodb-compass-replica.png)

6. Clique em **Connect** na parte inferior da página. A sua conta Cosmos DB e as bases de dados devem agora ser visíveis dentro da Bússola MongoDB.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para MongoDB.
- Explore [as amostras](mongodb-samples.md) de MongoDB com a API da Azure Cosmos DB para o MongoDB.
