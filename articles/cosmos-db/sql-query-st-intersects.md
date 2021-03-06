---
title: ST_INTERSECTS na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_INTERSECTS em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f3c3878956b90ffb45556ed819046af9eb7618f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303142"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
 Devolve uma expressão booleana indicando se o objeto GeoJSON (Ponto, Polígono ou LineString) especificado no primeiro argumento intersecta o GeoJSON (Ponto, Polígono ou LineString) no segundo argumento.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É uma expressão de objeto GeoJSON Point, Polygon ou LineString.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve um valor booleano.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que se segue mostra como encontrar todas as áreas que se cruzam com o polígono dado.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um [índice geoespacial.](index-policy.md#spatial-indexes)

## <a name="next-steps"></a>Passos seguintes

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
