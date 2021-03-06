---
title: Criação, atualização de estatísticas
description: Recomendações e exemplos para a criação e atualização de estatísticas de otimização de consultas em tabelas em piscina SYnapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f2af87cf5cef1b5a80bc16d962fba579b4ff309
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985869"
---
# <a name="table-statistics-in-synapse-sql-pool"></a>Estatísticas de tabelas em piscina SYnapse SQL

Neste artigo, você encontrará recomendações e exemplos para criar e atualizar estatísticas de otimização de consultas em tabelas em piscina SQL.

## <a name="why-use-statistics"></a>Por que usar estatísticas

Quanto mais sQL se souber sobre os seus dados, mais rápido poderá executar consultas contra ele. Depois de carregar dados no pool SQL, recolher estatísticas sobre os seus dados é uma das coisas mais importantes que pode fazer para otimizar as suas consultas.

O optimizador de consulta de piscina SQL é um otimizador baseado em custos. Compara o custo de vários planos de consulta, e depois escolhe o plano com o menor custo. Na maioria dos casos, escolhe o plano que executará o mais rápido.

Por exemplo, se o optimizador estimar que a data em que a sua consulta está a ser filtrada, irá devolver uma linha, escolherá um plano. Se estimar que a data selecionada devolverá 1 milhão de linhas, devolverá um plano diferente.

## <a name="automatic-creation-of-statistic"></a>Criação automática de estatística

Quando a base de dados AUTO_CREATE_STATISTICS opção está posta, o pool SQL analisa as consultas de entrada dos utilizadores para estatísticas em falta.

Se faltarem estatísticas, o optimizador de consultas cria estatísticas sobre colunas individuais no predicado ou junta-se às condições para melhorar as estimativas de cardinalidade para o plano de consulta.

> [!NOTE]
> A criação automática de estatísticas é atualmente ligada por defeito.

Pode verificar se a sua piscina SQL AUTO_CREATE_STATISTICS configurada executando o seguinte comando:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Se o seu pool SQL não tiver AUTO_CREATE_STATISTICS configurado, recomendamos que ative esta propriedade executando o seguinte comando:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Estas declarações desencadearão a criação automática de estatísticas:

- SELECIONAR
- INSERIR-SESE
- CTAS
- UPDATE
- DELETE
- EXPLIQUE quando contenha uma adesão ou a presença de um predicado é detetada

> [!NOTE]
> A criação automática de estatísticas não é criada em tabelas temporárias ou externas.

A criação automática de estatísticas é feita de forma sincronizada para que possa incorrer num desempenho de consulta ligeiramente degradado se as suas colunas faltarem estatísticas. O tempo para criar estatísticas para uma única coluna depende do tamanho da tabela.

Para evitar uma degradação mensurável do desempenho, deve certificar-se de que as estatísticas foram criadas primeiro executando a carga de trabalho de referência antes de perfilar o sistema.

> [!NOTE]
> A criação de estatísticas será registada em [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) num contexto de utilizador diferente.

Quando as estatísticas automáticas forem criadas, assumirão o formulário: _WA_Sys<_ id de coluna de 8 dígitos em Hex>_<id de tabela de 8 dígitos em Hex>. Pode ver estatísticas que já foram criadas executando o comando [de SHOW_STATISTICS DBCC:](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

O table_name é o nome da tabela que contém as estatísticas a apresentar. Esta mesa não pode ser uma mesa externa. O alvo é o nome do índice alvo, estatísticas ou coluna para o qual apresentar informações estatísticas.

## <a name="update-statistics"></a>Estatísticas de atualização

Uma das melhores práticas é atualizar as estatísticas sobre as colunas de datas todos os dias à medida que são adicionadas novas datas. Cada vez que novas filas são carregadas no pool SQL, novas datas de carga ou datas de transação são adicionadas. Estes acréscimos alteram a distribuição de dados e tornam as estatísticas desatualizadas.

As estatísticas sobre uma coluna país/região numa tabela de clientes podem nunca precisar de ser atualizadas, uma vez que a distribuição de valores geralmente não muda. Assumindo que a distribuição é constante entre os clientes, adicionar novas linhas à variação da tabela não vai alterar a distribuição de dados.

No entanto, se o seu pool SQL contém apenas um país/região, e você trouxer dados de um novo país/região, resultando em dados de vários países/regiões armazenados, então você precisa atualizar estatísticas sobre a coluna país/região.

Seguem-se as recomendações que atualizam as estatísticas:

|||
|-|-|
| **Frequência das atualizações de estatísticas**  | Conservador: Diariamente </br> Depois de carregar ou transformar os seus dados |
| **Amostragem** |  Menos de mil milhões de linhas, utilize amostras por defeito (20 por cento). </br> Com mais de 1 bilião de filas, use amostras de 2%. |

Uma das primeiras perguntas a fazer quando se está a resolver uma consulta **é: "As estatísticas estão atualizadas?"**

Esta pergunta não é uma que possa ser respondida pela idade dos dados. Um objeto estatístico atualizado pode ser antigo se não houver alterações materiais nos dados subjacentes.

> [!TIP]
> Quando o número de linhas mudou substancialmente, ou há uma mudança material na distribuição de valores para uma coluna, *então* é hora de atualizar as estatísticas.

Não existe uma visão dinâmica da gestão para determinar se os dados dentro da tabela mudaram desde a última vez que as estatísticas foram atualizadas. Conhecer a idade das suas estatísticas pode fornecer-lhe parte do quadro.

Pode utilizar a seguinte consulta para determinar a última vez que as suas estatísticas foram atualizadas em cada tabela.

> [!NOTE]
> Se houver uma alteração material na distribuição de valores para uma coluna, deve atualizar as estatísticas independentemente da última vez que foram atualizadas.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**As colunas** de data sql, por exemplo, geralmente precisam de atualizações estatísticas frequentes. Cada vez que novas filas são carregadas no pool SQL, novas datas de carga ou datas de transação são adicionadas. Estes acréscimos alteram a distribuição de dados e tornam as estatísticas desatualizadas.

Inversamente, as estatísticas sobre uma coluna de género numa tabela de clientes podem nunca precisar de ser atualizadas. Assumindo que a distribuição é constante entre os clientes, adicionar novas linhas à variação da tabela não vai alterar a distribuição de dados.

Se o seu pool SQL contém apenas um sexo e um novo requisito resulta em múltiplos sexos, então precisa atualizar estatísticas sobre a coluna de género.

Para mais informações, consulte a orientação geral para [as estatísticas.](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="implementing-statistics-management"></a>Implementação da gestão estatística

Muitas vezes é uma boa ideia alargar o seu processo de carregamento de dados para garantir que as estatísticas são atualizadas no final da carga para evitar/minimizar o bloqueio ou a contenção de recursos entre consultas simultâneas.  

A carga de dados é quando as tabelas mudam mais frequentemente o seu tamanho e/ou a sua distribuição de valores. O carregamento de dados é um local lógico para implementar alguns processos de gestão.

Estão previstos os seguintes princípios orientadores para atualizar as suas estatísticas:

- Certifique-se de que cada tabela carregada tem pelo menos um objeto estatístico atualizado. Isto atualiza a informação sobre o tamanho da tabela (contagem de linhas e contagem de páginas) como parte da atualização estatística.
- Foco nas colunas que participam nas cláusulas JOIN, GROUP BY, ORDER BY e DISTINCT.
- Considere atualizar colunas "chave ascendente", como datas de transação com mais frequência, porque estes valores não serão incluídos no histograma estatístico.
- Considere atualizar as colunas de distribuição estática com menos frequência.
- Lembre-se, cada objeto estatístico é atualizado em sequência. Simplesmente implementar `UPDATE STATISTICS <TABLE_NAME>` nem sempre é ideal, especialmente para amplas tabelas com muitos objetos estatísticos.

Para mais informações, consulte [a Estimativa de Cardinalidade.](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="examples-create-statistics"></a>Exemplos: Criar estatísticas

Estes exemplos mostram como usar várias opções para criar estatísticas. As opções que utiliza para cada coluna dependem das características dos seus dados e de como a coluna será utilizada em consultas.

### <a name="create-single-column-statistics-with-default-options"></a>Criar estatísticas de coluna única com opções padrão

Para criar estatísticas sobre uma coluna, forneça um nome para o objeto estatístico e o nome da coluna.

Esta sintaxe utiliza todas as opções padrão. Por padrão, a piscina SQL amostra **20%** da tabela quando cria estatísticas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Criar estatísticas de coluna única examinando cada linha

A taxa de amostragem padrão de 20% é suficiente para a maioria das situações. No entanto, pode ajustar a taxa de amostragem.

Para provar a tabela completa, utilize esta sintaxe:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Criar estatísticas de coluna única especificando o tamanho da amostra

Em alternativa, pode especificar o tamanho da amostra em percentagem:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Criar estatísticas de uma coluna única sobre apenas algumas das linhas

Também pode criar estatísticas sobre uma parte das linhas da sua mesa. Isto chama-se estatística filtrada.

Por exemplo, pode utilizar estatísticas filtradas quando planeia consultar uma partição específica de uma grande tabela dividida. Ao criar estatísticas apenas sobre os valores de partição, a precisão das estatísticas melhorará e, portanto, melhorará o desempenho da consulta.

Este exemplo cria estatísticas sobre uma gama de valores. Os valores podem ser facilmente definidos para corresponder à gama de valores numa partição.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Para que o optimizador de consultas considere a utilização de estatísticas filtradas quando escolhe o plano de consulta distribuído, a consulta deve caber dentro da definição do objeto estatístico. Utilizando o exemplo anterior, a cláusula WHERE da consulta precisa especificar valores col1 entre 2000101 e 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Criar estatísticas de coluna única com todas as opções

Também pode combinar as opções. O exemplo seguinte cria um objeto estatístico filtrado com um tamanho de amostra personalizado:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para obter a referência completa, consulte [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="create-multi-column-statistics"></a>Criar estatísticas multi-colunas

Para criar um objeto estatístico multi-colunas, use os exemplos anteriores, mas especifique mais colunas.

> [!NOTE]
> O histograma, que é utilizado para estimar o número de linhas no resultado da consulta, só está disponível para a primeira coluna listada na definição de objetos estatísticos.

Neste exemplo, o histograma está na *categoria de produto\_*. As estatísticas transversais são calculadas na categoria do *produto\_* e *\_no produto sub_category:*

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Uma vez que existe uma correlação entre a categoria do *\_produto* e a *subcategoria\_\_* do produto, um objeto estatístico multi-colunapode ser útil se estas colunas forem acedidas ao mesmo tempo.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Criar estatísticas sobre todas as colunas numa tabela

Uma forma de criar estatísticas é emitir comandos DE ESTATÍSTICAS CREATE após a criação da tabela:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Utilize um procedimento armazenado para criar estatísticas sobre todas as colunas numa base de dados

A piscina SQL não possui um procedimento armazenado pelo sistema equivalente a sp_create_stats no Servidor SQL. Este procedimento armazenado cria um único objeto de estatísticas de colunas em todas as colunas da base de dados que ainda não tem estatísticas.

O exemplo seguinte irá ajudá-lo a começar com o design da sua base de dados. Sinta-se à vontade para adaptá-lo às suas necessidades.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Para criar estatísticas sobre todas as colunas da tabela utilizando os predefinidos, execute o procedimento armazenado.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Para criar estatísticas sobre todas as colunas da tabela usando um fullscan, chame este procedimento.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Para criar estatísticas amostradas em todas as colunas da tabela, insira 3, e a amostra por cento. Este procedimento usa uma taxa de amostra de 20%.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Exemplos: Estatísticas de atualização

Para atualizar as estatísticas, pode:

- Atualize um objeto estatístico. Especifique o nome do objeto estatístico que pretende atualizar.
- Atualize todos os objetos estatísticos numa mesa. Especifique o nome da tabela em vez de um objeto estatístico específico.

### <a name="update-one-specific-statistics-object"></a>Atualizar um objeto de estatística específica

Utilize a seguinte sintaxe para atualizar um objeto estatístico específico:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por exemplo:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Ao atualizar objetos estatísticos específicos, pode minimizar o tempo e os recursos necessários para gerir as estatísticas. Fazê-lo requer algum pensamento para escolher os melhores objetos estatísticos para atualizar.

### <a name="update-all-statistics-on-a-table"></a>Atualizar todas as estatísticas numa tabela

Um método simples para atualizar todos os objetos estatísticos numa tabela é:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por exemplo:

```sql
UPDATE STATISTICS dbo.table1;
```

A declaração de estatísticas atualizada é fácil de utilizar. Lembre-se apenas que atualiza *todas as* estatísticas em cima da mesa e, portanto, pode realizar mais trabalho do que o necessário. Se o desempenho não for um problema, esta é a forma mais fácil e completa de garantir que as estatísticas estão atualizadas.

> [!NOTE]
> Ao atualizar todas as estatísticas numa tabela, o pool SQL faz uma digitalização para provar a tabela para cada objeto estatístico. Se a tabela for grande e tiver muitas colunas e muitas estatísticas, poderá ser mais eficiente atualizar as estatísticas individuais com base nas necessidades.

Para uma implementação de um `UPDATE STATISTICS` procedimento, consulte [Tabelas Temporárias.](sql-data-warehouse-tables-temporary.md) O método de execução é `CREATE STATISTICS` ligeiramente diferente do procedimento anterior, mas o resultado é o mesmo.

Para obter a sintaxe completa, consulte [Estatísticas atualizadas](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="statistics-metadata"></a>Metadados estatísticos

Existem várias visões e funções do sistema que pode usar para encontrar informações sobre estatísticas. Por exemplo, pode ver se um objeto estatístico pode estar desatualizado usando a função de data de estatísticas para ver quando as estatísticas foram criadas ou atualizadas pela última vez.

### <a name="catalog-views-for-statistics"></a>Pontos de vista do catálogo para estatísticas

Estes pontos de vista do sistema fornecem informações sobre estatísticas:

| Vista do catálogo | Descrição |
|:--- |:--- |
| [sys.colunas](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada coluna. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada objeto na base de dados. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma fila para cada esquema na base de dados. |
| [sys.estatísticas](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada objeto estatístico. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada coluna no objeto estatístico. Liga-se de volta às colunas sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada tabela (inclui tabelas externas). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada tipo de dados. |

### <a name="system-functions-for-statistics"></a>Funções do sistema para estatísticas

Estas funções do sistema são úteis para trabalhar com estatísticas:

| Função do sistema | Descrição |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Data o objeto estatístico foi atualizado pela última vez. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Nível resumo e informação detalhada sobre a distribuição de valores, tal como compreendido pelo objeto estatístico. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combine colunas e funções estatísticas numa só vista

Esta visão reúne colunas relacionadas com estatísticas e resultados da função STATS_DATE() em conjunto.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS() exemplos

O DBCC SHOW_STATISTICS() mostra os dados guardados dentro de um objeto estatístico. Estes dados vêm em três partes:

- Cabeçalho
- Vetor de densidade
- Histograma

Os metadados sobre as estatísticas. O histograma exibe a distribuição de valores na primeira coluna-chave do objeto estatístico. O vetor de densidade mede a correlação entre colunas.

> [!NOTE]
> O pool SQL calcula estimativas de cardinalidade com qualquer um dos dados do objeto estatístico.

### <a name="show-header-density-and-histogram"></a>Mostrar cabeçalho, densidade e histograma

Este simples exemplo mostra as três partes de um objeto estatístico:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Mostrar uma ou mais partes de SHOW_STATISTICS dBCC()

Se estiver apenas interessado em visualizar `WITH` peças específicas, use a cláusula e especifique quais as partes que pretende ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>Diferenças de SHOW_STATISTICS dBCC

DBCC SHOW_STATISTICS() é mais rigorosamente implementado em piscina SQL em comparação com O Servidor SQL:

- As funcionalidades não documentadas não são suportadas.
- Não pode usáStats_stream.
- Não pode aderir a resultados para subconjuntos específicos de dados estatísticos. Por exemplo, STAT_HEADER junte-se DENSITY_VETOR.
- NO_INFOMSGS não pode ser definido para a supressão de mensagens.
- Os parênteses quadrados em torno das estatísticas não podem ser usados.
- Não é possível usar nomes de colunas para identificar objetos estatísticos.
- Erro personalizado 2767 não é suportado.

## <a name="next-steps"></a>Passos seguintes

Para melhorar ainda mais o desempenho da consulta, consulte [Monitorize a sua carga de trabalho](sql-data-warehouse-manage-monitor.md)
