---
title: Otimização do desempenho com vistas materializadas
description: Recomendações e considerações que deve saber ao usar vistas materializadas para melhorar o seu desempenho de consulta.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e942130d9acf803665e52498ef6a4976cc9ade7
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743169"
---
# <a name="performance-tuning-with-materialized-views"></a>Otimização do desempenho com vistas materializadas

As vistas materializadas na piscina SYnapse SQL fornecem um método de baixa manutenção para consultas analíticas complexas para obter um desempenho rápido sem qualquer alteração de consulta. Este artigo discute a orientação geral sobre a utilização de pontos de vista materializados.

As vistas materializadas no Azure SQL Data Warehouse fornecem um método de baixa manutenção para consultas analíticas complexas para obter um desempenho rápido sem qualquer alteração de consulta. Este artigo discute a orientação geral sobre a utilização de pontos de vista materializados.

## <a name="materialized-views-vs-standard-views"></a>Vistas materializadas vs. vistas padrão

A piscina SQL suporta vistas padrão e materializadas.  Ambas são tabelas virtuais criadas com expressões SELECT e apresentadas a consultas como tabelas lógicas.  As opiniões encapsulam a complexidade da computação comum de dados e adicionam uma camada de abstração às alterações de cálculo, pelo que não há necessidade de reescrever consultas.  

Uma visão padrão calcula os seus dados sempre que a vista é utilizada.  Não há dados armazenados no disco. As pessoas normalmente usam vistas padrão como uma ferramenta que ajuda a organizar os objetos lógicos e consultas numa base de dados.  Para utilizar uma visão padrão, uma consulta precisa fazer referência direta à sua.

Uma vista materializada pré-computação, lojas e mantém os seus dados em piscina SQL como uma mesa.  Não é necessária recomputação de cada vez que uma vista materializada é usada.  É por isso que as consultas que usam todos ou subconjuntos dos dados em vistas materializadas podem obter um desempenho mais rápido.  Ainda melhor, as consultas podem usar uma visão materializada sem fazer referência direta a ela, por isso não há necessidade de alterar o código de aplicação.  

A maioria dos requisitos numa vista padrão ainda se aplicam a uma visão materializada. Para mais detalhes sobre a sintaxe de visualização materializada e outros requisitos, consulte a [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

| Comparação                     | Vista                                         | Vista materializada
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Ver definição                 | Armazenado em piscina SQL.              | Armazenado em piscina SQL.
|Ver conteúdos                    | Gerada sempre que a vista é utilizada.   | Pré-processado e armazenado em piscina SQL durante a criação de visualização. Atualizado à medida que os dados são adicionados às tabelas subjacentes.
|Atualização de dados                    | Sempre atualizado                               | Sempre atualizado
|Velocidade para recuperar dados de visualização de consultas complexas     | Devagar                                         | Rápido  
|Armazenamento extra                   | Não                                           | Sim
|Sintaxe                          | CRIAR VISTA                                  | CRIAR VISTA MATERIALIZADA COMO SELECIONADO

## <a name="benefits-of-using-materialized-views"></a>Benefícios da utilização de vistas materializadas

Uma vista materializada devidamente concebida pode proporcionar os seguintes benefícios:

- Reduza o tempo de execução para consultas complexas com JOINs e funções agregadas. Quanto mais complexa a consulta, maior o potencial de poupança de tempo de execução. O maior benefício é obtido quando o custo de cálculo de uma consulta é elevado e o conjunto de dados resultante é pequeno.  
- O optimizador na piscina SQL pode utilizar automaticamente vistas materializadas para melhorar os planos de execução da consulta.  Este processo é transparente para os utilizadores que fornecem um desempenho de consulta mais rápido e não requer consultas para fazer referência direta às vistas materializadas.
- Requerem baixa manutenção nas vistas.  Todas as alterações incrementais de dados das tabelas base são adicionadas automaticamente às vistas materializadas de forma sincronizada.  Este design permite que as vistas materializadas de consulta devolva os mesmos dados que consultando diretamente as tabelas base.
- Os dados numa vista materializada podem ser distribuídos de forma diferente das tabelas base.  
- Os dados em vistas materializadas recebem os mesmos benefícios de alta disponibilidade e resiliência que os dados em tabelas regulares.  

As vistas materializadas implementadas na piscina SQL também proporcionam os seguintes benefícios adicionais:

Comparando com outros fornecedores de armazéns de dados, as vistas materializadas implementadas no Azure SQL Data Warehouse também proporcionam os seguintes benefícios adicionais:

- Os dados automáticos e sincronizados atualizam-se com alterações de dados nas tabelas base. Não é necessária qualquer ação do utilizador.
- Suporte de função agregado amplo. Consulte a [VISÃO MATERIALIZADA COMO SELECT (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
- O apoio à recomendação de vista materializada específica da consulta.  Ver [EXPLAIN (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Cenários comuns  

As vistas materializadas são normalmente utilizadas nos seguintes cenários:

**Necessidade de melhorar o desempenho de consultas analíticas complexas contra grandes dados em tamanho**

Consultas analíticas complexas normalmente usam mais funções de agregação e juntas de mesa, causando operações mais computadas como baralhadas e juntas na execução de consultas.  É por isso que essas consultas demoram mais tempo a ser completadas, especialmente em mesas grandes.  

Os utilizadores podem criar vistas materializadas para os dados devolvidos a partir das computações comuns de consultas, pelo que não é necessária recomputação quando estes dados são necessários por consultas, permitindo um menor custo de computação e uma resposta mais rápida à consulta.

**Precisa de um desempenho mais rápido sem alterações de consulta mínimas ou sem alterações mínimas**

As alterações de schema e consulta nas piscinas SQL são normalmente mantidas ao mínimo para suportar operações e relatórios regulares da ETL.  As pessoas podem usar pontos de vista materializados para a sintonização do desempenho da consulta, se o custo incorrido pelas vistas pode ser compensado pelo ganho de desempenho da consulta.

Em comparação com outras opções de afinação, como a escala e a gestão estatística, é uma mudança de produção muito menos impactante para criar e manter uma visão materializada e o seu potencial ganho de desempenho também é maior.

- A criação ou manutenção de pontos de vista materializados não afeta as consultas que correm contra as tabelas base.
- O optimizador de consultas pode utilizar automaticamente as vistas materializadas implantadas sem referência de visualização direta numa consulta. Esta capacidade reduz a necessidade de mudança de consulta na afinação de desempenho.

**Precisa de uma estratégia de distribuição de dados diferente para um desempenho mais rápido da consulta**

A piscina SQL é um sistema de processamento maciço e paralelo distribuído (MPP).   Os dados numa tabela de bilhar SQL são distribuídos por 60 nós utilizando uma das três estratégias de [distribuição](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (hash, round_robin ou replicado).  

A distribuição de dados é especificada no tempo de criação da tabela e permanece inalterada até que a tabela seja abandonada. A visão materializada é uma tabela virtual sobre suportes de hash e round_robin distribuição de dados.  Os utilizadores podem escolher uma distribuição de dados diferente das tabelas base, mas ideal para o desempenho de consultas que mais utilizam as vistas.  

## <a name="design-guidance"></a>Orientação de design

Aqui está a orientação geral sobre a utilização de pontos de vista materializados para melhorar o desempenho da consulta:

**Design para a sua carga de trabalho**

Antes de começar a criar vistas materializadas, é importante ter uma compreensão profunda da sua carga de trabalho em termos de padrões de consulta, importância, frequência e o tamanho dos dados resultantes.  

Os utilizadores podem executar explicações WITH_RECOMMENDATIONS <SQL_statement> para as vistas materializadas recomendadas pelo optimizador de consultas.  Uma vez que estas recomendações são específicas da consulta, uma visão materializada que beneficia uma única consulta pode não ser o ideal para outras consultas na mesma carga de trabalho.  

Avalie estas recomendações com as suas necessidades de carga de trabalho em mente.  As vistas ideais materializadas são aquelas que beneficiam o desempenho da carga de trabalho.  

**Esteja atento à troca entre consultas mais rápidas e o custo**

Para cada vista materializada, há um custo de armazenamento de dados e um custo para manter a vista.  À medida que os dados mudam nas tabelas base, o tamanho da vista materializada aumenta e a sua estrutura física também muda.  Para evitar a degradação do desempenho da consulta, cada vista materializada é mantida separadamente pelo motor de piscina SQL.  

A carga de trabalho de manutenção aumenta quando o número de vistas materializadas e alterações na tabela base aumentam.   Os utilizadores devem verificar se o custo incorrido de todas as vistas materializadas pode ser compensado pelo ganho de desempenho da consulta.  

Pode executar esta consulta para a lista de visualizações materializadas numa base de dados:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Opções para reduzir o número de pontos de vista materializados:

- Identifique conjuntos de dados comuns frequentemente utilizados pelas consultas complexas na sua carga de trabalho.  Crie vistas materializadas para armazenar esses conjuntos de dados para que o optimizador possa usá-los como blocos de construção ao criar planos de execução.  

- Largue as vistas materializadas que têm baixa utilização ou que já não são necessárias.  Uma vista materializada para deficientes não é mantida, mas continua a incorrer no custo de armazenamento.  

- Combine vistas materializadas criadas nas mesmas tabelas base ou similares, mesmo que os seus dados não se sobreponham.  A pentear vistas materializadas poderia resultar numa visão maior em tamanho do que a soma das vistas separadas, no entanto, o custo de manutenção da vista deve reduzir-se.  Por exemplo:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Nem toda a afinação de desempenho requer mudança de consulta**

O optimizador de piscinaS SQL pode utilizar automaticamente vistas materializadas implantadas para melhorar o desempenho da consulta.  Este suporte é aplicado de forma transparente a consultas que não referenciam as opiniões e consultas que utilizam agregados não suportados na criação de pontos de vista materializados.  Não é necessária nenhuma mudança de consulta. Pode verificar o plano de execução estimado de uma consulta para confirmar se uma vista materializada é usada.  

**Monitorizar vistas materializadas**

Uma vista materializada é armazenada na piscina SQL como uma tabela com índice de colunas agrupadas (CCI).  A leitura de dados de uma visão materializada inclui a digitalização dos segmentos de índice CCI e a aplicação de quaisquer alterações incrementais a partir das tabelas base. Quando o número de alterações incrementais é demasiado elevado, a resolução de uma consulta de uma vista materializada pode demorar mais do que consultar diretamente as tabelas base.  

Para evitar a degradação do desempenho, é uma boa prática executar [o DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) monitorizar a overhead_ratio da vista (total_rows/máx(1, base_view_row)).  Os utilizadores devem reconstruir a visão materializada se o seu overhead_ratio for demasiado elevado.

**Vista materializada e conjunto de resultados caching**

Estas duas funcionalidades são introduzidas na piscina SQL ao mesmo tempo para a sintonização do desempenho da consulta.  O corte de resultados é usado para obter alta conmoedação e resposta rápida de consultas repetitivas contra dados estáticos.  

Para utilizar o resultado em cache, a forma da consulta de cache solicitando deve coincidir com a consulta que produziu a cache.  Além disso, o resultado em cache deve aplicar-se a toda a consulta.  

As vistas materializadas permitem alterações de dados nas tabelas base.  Os dados em vistas materializadas podem ser aplicados a uma peça de consulta.  Este suporte permite que as mesmas vistas materializadas sejam usadas por diferentes consultas que partilham alguma computação para um desempenho mais rápido.

## <a name="example"></a>Exemplo

Este exemplo usa uma consulta semelhante ao TPCDS que encontra clientes que gastam mais dinheiro através de catálogo do que nas lojas, identificam os clientes preferidos e o seu país de origem.   A consulta envolve a seleção dos registos TOP 100 da UNIÃO de três declarações sub-SELECIONADAs envolvendo SUM() e GROUP BY.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Verifique o plano de execução estimado da consulta.  Há 18 baralhadas e 17 operacionais, que demoram mais tempo a ser executados. Agora vamos criar uma vista materializada para cada uma das três declarações sub-SELECT.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Verifique o plano de execução da consulta original novamente.  Agora o número de juntas muda de 17 para 5 e já não há confusão.  Clique no ícone de funcionamento do Filtro no plano, a sua Lista de Saída mostra que os dados são lidos a partir das vistas materializadas em vez de tabelas base.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

Com vistas materializadas, a mesma consulta é muito mais rápida sem qualquer alteração de código.  

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a visão geral do desenvolvimento da [piscina Synapse SQL.](sql-data-warehouse-overview-develop.md)
