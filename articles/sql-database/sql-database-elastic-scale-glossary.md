---
title: Glossário de ferramentas de base de dados elástica
description: Explicação dos termos utilizados para ferramentas de base de dados elásticas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: ab972db78cd213497fb96486b3e16b01f2c4c6eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823620"
---
# <a name="elastic-database-tools-glossary"></a>Glossário de ferramentas de base de dados elástica

Os seguintes termos são definidos para as [ferramentas Debase elástica,](sql-database-elastic-scale-introduction.md)uma característica da Base de Dados Azure SQL. As ferramentas são usadas para gerir mapas de [fragmentos,](sql-database-elastic-scale-shard-map-management.md)e incluem a biblioteca do [cliente,](sql-database-elastic-database-client-library.md)a [ferramenta de fusão dividida,](sql-database-elastic-scale-overview-split-and-merge.md) [piscinas elásticas](sql-database-elastic-pool.md)e [consultas.](sql-database-elastic-query-overview.md) 

Estes termos são utilizados na adição de [um fragmento utilizando ferramentas de base](sql-database-elastic-scale-add-a-shard.md) de dados elásticas e utilizando a classe [RecoveryManager para corrigir problemas](sql-database-elastic-database-recovery-manager.md)de mapa de fragmentos .

![Termos de Escala Elástica][1]

**Base de dados**: Uma base de dados Azure SQL. 

**Encaminhamento dependente**de dados : A funcionalidade que permite que uma aplicação se conectem a um fragmento dada uma chave específica de sharding. Ver [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). Compare com a **[consulta multi-fragmento](sql-database-elastic-scale-multishard-querying.md)**.

**Mapa global**do fragmento : O mapa entre as teclas sharding e os respetivos fragmentos dentro de um conjunto de **fragmentos**. O mapa global do fragmento está guardado no gestor de mapas de **fragmentos.** Compare-se com o mapa local do **fragmento.**

**Mapa da lista**: Um mapa em que as chaves de sharding são mapeadas individualmente. Compare com **o Mapa**do Fragmento de Alcance .   

**Mapa local**do fragmento : Armazenado num fragmento, o mapa local contém mapeamentos para os fragmentos que residem no fragmento.

**Consulta multi-fragmento**: A capacidade de emitir uma consulta contra vários fragmentos; os conjuntos de resultados são devolvidos usando semântica UNION ALL (também conhecida como "consulta de fan-out"). Compare com o **encaminhamento dependente**de dados .

**Multi-inquilino** e **inquilino único:** Isto mostra uma base de dados de inquilinoúnico único e uma base de dados multi-inquilinos:

![Bases de dados individuais e multi-inquilinos](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Aqui está uma representação **de** bases de dados de inquilinos individuais e multi-inquilinos. 

![Bases de dados individuais e multi-inquilinos](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapa**do fragmento de alcance : Um mapa em que a estratégia de distribuição de fragmentos é baseada em múltiplas gamas de valores contíguos. 

**Tabelas de referência**: Tabelas que não são escacadas mas que são replicadas em fragmentos. Por exemplo, os códigos postais podem ser armazenados numa tabela de referência. 

**Fragmento**: Uma base de dados Azure SQL que armazena dados de um conjunto de dados fragmentos. 

**Elasticidade do fragmento**: A capacidade de realizar tanto o **escalão horizontal como** a escala **vertical**.

**Tabelas estampadas**: Tabelas que são escacadas, ou seja, cujos dados são distribuídos por fragmentos com base nos seus valores-chave. 

**Tecla Sharding**: Um valor de coluna que determina como os dados são distribuídos por fragmentos. O tipo de valor pode ser um dos seguintes: **int,** **bigint,** **varbinary,** ou **identificador único.** 

Conjunto de **fragmentos**: A coleção de fragmentos que são atribuídos ao mesmo mapa de fragmentos no gestor de mapas de fragmentos.  

**Shardlet**: Todos os dados associados a um único valor de uma chave de sharding num fragmento. Um fragmento é a menor unidade de movimento de dados possível ao redistribuir as tabelas esfumaçadas. 

**Mapa**do fragmento : O conjunto de mapeamentos entre as teclas sharding e os respetivos fragmentos.

**Gestor de mapas de fragmentos**: Um objeto de gestão e uma loja de dados que contém o mapa de fragmentos, localizações de fragmentos e mapeamentos para um ou mais conjuntos de fragmentos.

![Mapeamentos][2]

## <a name="verbs"></a>Verbos
**Escala horizontal**: O ato de escalonar (ou in) uma coleção de fragmentos adicionando ou removendo fragmentos num mapa de fragmentos, como mostrado abaixo.

![Escala horizontal e vertical][3]

**Fusão**: O ato de mover fragmentos de dois fragmentos para um fragmento e atualizar o mapa do fragmento em conformidade.

**Movimento do shardlet**: O ato de mover um único fragmento para um fragmento diferente. 

**Fragmento**: O ato de dividir horizontalmente dados estruturados de forma idêntica em várias bases de dados com base numa chave de sharding.

**Split**: O ato de mover vários fragmentos de um fragmento para outro (tipicamente novo) fragmento. Uma tecla sharding é fornecida pelo utilizador como ponto de divisão.

**Escala vertical**: O ato de escalonar para cima (ou para baixo) o tamanho da computação de um fragmento individual. Por exemplo, mudar um fragmento de Standard para Premium (o que resulta em mais recursos informáticos). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

