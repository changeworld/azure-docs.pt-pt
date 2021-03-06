---
title: Utilização de squemas definidos pelo utilizador
description: Dicas para utilizar schemas definidos pelo utilizador T-SQL para desenvolver soluções em piscina SYnapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633458"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Schemas definidos pelo utilizador na piscina SQL synapse
Este artigo centra-se em fornecer várias dicas para a utilização de schemas definidos pelo utilizador T-SQL para desenvolver soluções em piscina SYnapse SQL.

## <a name="schemas-for-application-boundaries"></a>Esquemios para limites de aplicação

Os armazéns de dados tradicionais usam frequentemente bases de dados separadas para criar limites de aplicação baseados na carga de trabalho, domínio ou segurança. 

Como exemplo, um armazém de dados sQL Server tradicional pode incluir uma base de dados de encenação, uma base de dados de armazém de dados de dados e algumas bases de dados de data mart. Nesta topologia, cada base de dados funciona como uma carga de trabalho e limite de segurança na arquitetura.

Em contraste, a piscina SQL executa toda a carga de trabalho do armazém de dados dentro de uma base de dados. Não são permitidas juntas de dados cruzadas. A piscina SQL espera que todas as mesas utilizadas pelo armazém sejam armazenadas dentro de uma base de dados.

> [!NOTE]
> O pool SQL não suporta consultas de base de dados cruzadas de qualquer tipo. Consequentemente, as implementações de armazéns de dados que alavancam este padrão terão de ser revistas.
> 
> 

## <a name="recommendations"></a>Recomendações
Seguem-se recomendações para consolidar cargas de trabalho, segurança, domínio e limites funcionais utilizando esquemas definidos pelo utilizador:

- Utilize uma base de dados de piscina SQL para executar toda a sua carga de trabalho de armazém de dados.
- Consolide o ambiente de armazém de dados existente para utilizar uma base de dados de piscinaS SQL.
- Alavancar **os esquemas definidos pelo utilizador** para fornecer o limite anteriormente implementado utilizando bases de dados.

Se os schemas definidos pelo utilizador não tiverem sido utilizados anteriormente, então tem uma ficha limpa. Utilize o nome da base de dados antiga como base para os seus schemas definidos pelo utilizador na base de dados de piscinas SQL.

Se os schemas já foram usados, então você tem algumas opções:

- Remova os nomes do esquema legado e comece de novo.
- Mantenha os nomes do esquema legado, aguardando o nome do esquema legado para o nome da mesa.
- Mantenha os nomes do esquema legado implementando pontos de vista sobre a mesa num esquema extra para recriar a antiga estrutura do esquema.

> [!NOTE]
> Na primeira opção de inspeção 3 pode parecer a opção mais apelativa. No entanto, o diabo está nos detalhes. As vistas são lidas apenas na piscina SQL. Quaisquer dados ou modificações de tabelas teriam de ser efetuadas contra a tabela base. A opção 3 também introduz uma camada de vistas no seu sistema. Você pode querer dar a este algum pensamento adicional se você já está usando vistas na sua arquitetura.
> 
> 

### <a name="examples"></a>Exemplos:
Implementar schemas definidos pelo utilizador com base em nomes de bases de dados:

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenha nomes de esquemas legados, aguardando-os ao nome da mesa. Utilize esquemmas para o limite da carga de trabalho:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenha nomes de esquemas legados usando vistas:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Qualquer alteração na estratégia de esquemas necessita de uma revisão do modelo de segurança para a base de dados. Em muitos casos, você pode ser capaz de simplificar o modelo de segurança atribuindo permissões ao nível do esquema. Se forem necessárias mais permissões granulares, pode utilizar funções de base de dados.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)

