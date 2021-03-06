---
title: Funções agregadas em Azure Cosmos DB
description: Conheça a sintaxe de função agregada SQL, tipos de funções agregadas suportadas pela Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464466"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funções agregadas em Azure Cosmos DB

As funções agregadas realizam um `SELECT` cálculo sobre um conjunto de valores na cláusula e devolvem um único valor. Por exemplo, a seguinte consulta devolve a contagem `Families` de itens dentro do recipiente:

## <a name="examples"></a>Exemplos

```sql
    SELECT COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [{
        "$1": 2
    }]
```

Também pode devolver apenas o valor escalar do agregado utilizando a palavra-chave VALUE. Por exemplo, a seguinte consulta devolve a contagem de valores como um único número:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [ 2 ]
```

Também pode combinar agregações com filtros. Por exemplo, a seguinte consulta devolve a contagem de `WA`itens com o estado de endereço de .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Os resultados são:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Tipos de funções agregadas

A API SQL suporta as seguintes funções agregadas. `SUM`e `AVG` operar em valores `COUNT` `MIN`numéricos, e , e `MAX` trabalhar em números, cordas, booleans e nulos.

| Função | Descrição |
|-------|-------------|
| COUNT | Devolve o número de itens na expressão. |
| SUM   | Devolve a soma de todos os valores da expressão. |
| MIN   | Devolve o valor mínimo na expressão. |
| MAX   | Devolve o valor máximo na expressão. |
| AVG   | Devolve a média dos valores na expressão. |

Também pode agregar os resultados de uma iteração de matriz.

> [!NOTE]
> No Data Explorer do portal Azure, as consultas de agregação podem agregar resultados parciais em apenas uma página de consulta. O SDK produz um único valor cumulativo em todas as páginas. Para efetuar consultas de agregação utilizando código, necessita de .NET SDK 1.12.0, .NET Core SDK 1.1.0 ou Java SDK 1.9.5 ou superior.

## <a name="remarks"></a>Observações

Estas funções agregadas do sistema beneficiarão de um índice de [gama.](index-policy.md#includeexclude-strategy) Se você espera `COUNT`fazer `SUM` `MIN`um, , , `MAX`ou `AVG` em um imóvel, você deve incluir o caminho relevante na política de [indexação](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções de sistema](sql-query-system-functions.md)
- [Funções definidas pelo utilizador](sql-query-udfs.md)