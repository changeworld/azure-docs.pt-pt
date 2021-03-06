---
title: Consulta Store - Base de Dados Azure para MySQL
description: Saiba mais sobre a funcionalidade Query Store na Base de Dados Azure para MySQL para ajudá-lo a rastrear o desempenho ao longo do tempo.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d138c2fb8ed667d5b3c961c9f567264fa40edaee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537045"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Monitor Azure Database para desempenho mySQL com A Loja de Consulta

**Aplica-se a:** Base de Dados Azure para MySQL 5.7

A funcionalidade Da Loja de Consultas na Base de Dados Azure para MySQL fornece uma forma de acompanhar o desempenho da consulta ao longo do tempo. A Consulta Store simplifica a resolução de problemas de desempenho, ajudando-o rapidamente a encontrar as consultas de corrida mais longas e intensivas de recursos. A Consulta Store captura automaticamente um histórico de consultas e estatísticas de tempo de execução, e mantém-nas para a sua revisão. Separa os dados por janelas de tempo para que possa ver padrões de utilização da base de dados. Os dados para todos os utilizadores, bases de dados e consultas são armazenados na base de dados de schema **mysql** na base de dados Azure para a instância MySQL.

## <a name="common-scenarios-for-using-query-store"></a>Cenários comuns para a utilização da Loja de Consultas

A loja de consultas pode ser usada em vários cenários, incluindo o seguinte:

- Deteção de consultas regredidas
- Determinar o número de vezes que uma consulta foi executada em uma determinada janela do tempo
- Comparando o tempo médio de execução de uma consulta através das janelas do tempo para ver grandes deltas

## <a name="enabling-query-store"></a>Loja de Consulta ativa

A Consulta Store é uma funcionalidade de opt-in, pelo que não está ativa por padrão num servidor. A loja de consultas está ativada ou desativada globalmente para todas as bases de dados de um determinado servidor e não pode ser ligada ou desligada por base de dados.

### <a name="enable-query-store-using-the-azure-portal"></a>Ativar a Loja de Consultas utilizando o portal Azure

1. Inscreva-se no portal Azure e selecione a sua Base de Dados Azure para o servidor MySQL.
1. Selecione **Parâmetros** do servidor na secção **Definições** do menu.
1. Procure o parâmetro query_store_capture_mode.
1. Desloque o valor para ALL e **Poupe**.

Para ativar estatísticas de espera na sua Loja de Consulta:

1. Procure o parâmetro query_store_wait_sampling_capture_mode.
1. Desloque o valor para ALL e **Poupe**.

Deixe até 20 minutos para que o primeiro lote de dados permaneça na base de dados mysql.

## <a name="information-in-query-store"></a>Informação na Loja de Consulta

A Consulta Store tem duas lojas:

- Uma loja de estatísticas de tempo de execução para persistir a informação estatística de execução de consulta.
- Uma loja de estatísticas de espera para informações estatísticas de espera persistentes.

Para minimizar o uso do espaço, as estatísticas de execução do tempo de execução na loja de estatísticas de tempo de execução são agregadas sobre uma janela de tempo fixa e configurável. A informação nestas lojas é visível consultando as vistas da loja de consulta.

A seguinte consulta devolve informações sobre consultas na Consulta Store:

```sql
SELECT * FROM mysql.query_store;
```

Ou esta consulta de estatísticas de espera:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Encontrar consultas de espera

> [!NOTE]
> As estatísticas de espera não devem ser ativadas durante as horas de trabalho máximas ou ser ligadas indefinidamente para cargas de trabalho sensíveis. <br>Para cargas de trabalho em funcionamento com alta utilização de CPU ou em servidores configurados com vCores mais baixos, tenha cuidado ao ativar estatísticas de espera. Não deve ser ligado indefinidamente. 

Os tipos de eventos de espera combinam diferentes eventos de espera em baldes por semelhança. A Consulta Store fornece o tipo de evento de espera, nome específico do evento de espera e a consulta em questão. Ser capaz de correlacionar esta informação de espera com as estatísticas de tempo de execução da consulta significa que você pode obter uma compreensão mais profunda do que contribui para a consulta características de desempenho.

Aqui estão alguns exemplos de como pode obter mais informações sobre a sua carga de trabalho usando as estatísticas de espera na Consulta Store:

| **Observação** | **Ação** |
|---|---|
|High Lock espera | Consulte os textos de consulta para as consultas afetadas e identifique as entidades-alvo. Procure na Consulta Store para outras consultas que modifiquem a mesma entidade, que é executada com frequência e/ou tem alta duração. Depois de identificar estas consultas, considere alterar a lógica da aplicação para melhorar a conmoedação, ou usar um nível de isolamento menos restritivo. |
|Alto Tampão IO espera | Encontre as consultas com um elevado número de leituras físicas na Loja de Consultas. Se combinarem com as consultas com altas esperas de IO, considere introduzir um índice sobre a entidade subjacente, fazer procura em vez de digitalização. Isto minimizaria a sobrecarga das consultas. Consulte as **Recomendações de Desempenho** do seu servidor no portal para ver se existem recomendações de índice para este servidor que otimizariam as consultas. |
|Alta Memória espera | Encontre as principais consultas de consumo de memória na Loja de Consultas. Estas consultas estão provavelmente a atrasar o progresso das consultas afetadas. Consulte as **Recomendações de Desempenho** do seu servidor no portal para ver se existem recomendações de índice que otimizariam estas consultas.|

## <a name="configuration-options"></a>Opções de configuração

Quando a Consulta Store está ativada, guarda dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela.

As seguintes opções estão disponíveis para configurar os parâmetros da Consulta Store.

| **Parâmetro** | **Descrição** | **Padrão** | **Alcance** |
|---|---|---|---|
| query_store_capture_mode | Rode a função de loja de consulta ON/OFF com base no valor. Nota: Se performance_schema estiver DESLIGADO, ligar query_store_capture_mode ligará performance_schema e um subconjunto de instrumentos de esquema de desempenho necessários para esta funcionalidade. | TUDO | NENHUM, TODOS. |
| query_store_capture_interval | O intervalo de captura da loja de consultas em minutos. Permite especificar o intervalo em que as métricas de consulta são agregadas | 15 | 5 - 60 |
| query_store_capture_utility_queries | Ligar ou DESLIGAR para capturar todas as consultas de utilidade que está a executar no sistema. | NO | SIM, NÃO. |
| query_store_retention_period_in_days | Janela de tempo em dias para reter os dados na loja de consultas. | 7 | 1 - 30 |

As seguintes opções aplicam-se especificamente às estatísticas de espera.

| **Parâmetro** | **Descrição** | **Padrão** | **Alcance** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Permite ligar /OFF as estatísticas de espera. | NENHUM | NENHUM, TODOS. |
| query_store_wait_sampling_frequency | Altera a frequência da amostragem de espera em segundos. 5 a 300 segundos. | 30 | 5-300 |

> [!NOTE]
> Atualmente **query_store_capture_mode** substitui esta configuração, o que significa que tanto **query_store_capture_mode** como **query_store_wait_sampling_capture_mode** têm de ser habilitados a TODAS para que as estatísticas de espera funcionem. Se **query_store_capture_mode** for desligado, as estatísticas de espera também são desligadas, uma vez que as estatísticas de espera utilizam o performance_schema habilitado, e o query_text capturado pela loja de consultas.

Utilize o [portal](howto-server-parameters.md) Azure ou [o Azure CLI](howto-configure-server-parameters-using-cli.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Vistas e funções

Ver e gerir a Consulta Store utilizando as seguintes vistas e funções. Qualquer pessoa no [papel público de privilégio selecionado](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql) pode usar estas vistas para ver os dados na Consulta Store. Estas opiniões só estão disponíveis na base de dados **mysql.**

As consultas são normalizadas olhando para a sua estrutura após a remoção de literals e constantes. Se duas consultas forem idênticas, exceto valores literais, terão o mesmo hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Esta vista devolve todos os dados da Consulta Store. Há uma linha para cada id de base de dados distinta, identificação do utilizador e identificação de consulta.

| **Nome** | **Tipo de Dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Nome do esquema |
| `query_id`| bigint(20) | NO| ID único gerado para a consulta específica, se a mesma consulta executa em diferentes esquemas, um novo ID será gerado |
| `timestamp_id` | carimbo de data/hora| NO| Carimbo de tempo em que a consulta é executada. Isto baseia-se na configuração query_store_interval|
| `query_digest_text`| texto longo| NO| O texto de consulta normalizado após remover todos os literais|
| `query_sample_text` | texto longo| NO| Primeira aparição da consulta real com os literais|
| `query_digest_truncated` | bit| SIM| Se o texto da consulta foi truncado. Valor será Sim se a consulta for superior a 1 KB|
| `execution_count` | bigint(20)| NO| O número de vezes que a consulta foi executada para este ID/timestamp /durante o período de intervalo configurado|
| `warning_count` | bigint(20)| NO| Número de advertências esta consulta gerada durante o interna|
| `error_count` | bigint(20)| NO| Número de erros que esta consulta gerou durante o intervalo|
| `sum_timer_wait` | double| SIM| Tempo total de execução desta consulta durante o intervalo|
| `avg_timer_wait` | double| SIM| Tempo médio de execução para esta consulta durante o intervalo|
| `min_timer_wait` | double| SIM| Tempo mínimo de execução para esta consulta|
| `max_timer_wait` | double| SIM| Tempo máximo de execução|
| `sum_lock_time` | bigint(20)| NO| Quantidade total de tempo gasto para todas as fechaduras para esta execução de consulta durante esta janela de tempo|
| `sum_rows_affected` | bigint(20)| NO| Número de linhas afetadas|
| `sum_rows_sent` | bigint(20)| NO| Número de filas enviadas ao cliente|
| `sum_rows_examined` | bigint(20)| NO| Número de linhas examinadas|
| `sum_select_full_join` | bigint(20)| NO| Número de adesões completas|
| `sum_select_scan` | bigint(20)| NO| Número de scans selecionados |
| `sum_sort_rows` | bigint(20)| NO| Número de linhas ordenadas|
| `sum_no_index_used` | bigint(20)| NO| Número de vezes que a consulta não usou quaisquer índices|
| `sum_no_good_index_used` | bigint(20)| NO| Número de vezes em que o motor de execução de consulta não usou quaisquer bons índices|
| `sum_created_tmp_tables` | bigint(20)| NO| Número total de tabelas temporárias criadas|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Número total de tabelas temporárias criadas em disco (gera I/O)|
| `first_seen` | carimbo de data/hora| NO| A primeira ocorrência (UTC) da consulta durante a janela de agregação|
| `last_seen` | carimbo de data/hora| NO| A última ocorrência (UTC) da consulta durante esta janela de agregação|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Esta vista devolve dados de eventos de espera na Loja de Consultas. Há uma linha para cada id de base de dados distinta, ID do utilizador, id consulta e evento.

| **Nome**| **Tipo de Dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `interval_start` | carimbo de data/hora | NO| Início do intervalo (incremento de 15 minutos)|
| `interval_end` | carimbo de data/hora | NO| Fim do intervalo (incremento de 15 minutos)|
| `query_id` | bigint(20) | NO| Id único gerado na consulta normalizada (a partir de consulta)|
| `query_digest_id` | varchar(32) | NO| O texto de consulta normalizado após a remoção de todos os literais (da loja de consulta) |
| `query_digest_text` | texto longo | NO| Primeira aparição da consulta real com os literais (da loja de consultas) |
| `event_type` | varchar(32) | NO| Categoria do evento de espera |
| `event_name` | varchar(128) | NO| Nome do evento de espera |
| `count_star` | bigint(20) | NO| Número de eventos de espera amostrados durante o intervalo para a consulta |
| `sum_timer_wait_ms` | double | NO| Tempo total de espera (em milissegundos) desta consulta durante o intervalo |

### <a name="functions"></a>Funções

| **Nome**| **Descrição** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Purga todos os dados da loja de consultas antes do carimbo de tempo dado |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Purga todos os dados do evento de espera antes do carimbo de tempo dado |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Purga recomendações cuja expiração é antes do carimbo de tempo dado |

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

- Se um servidor MySQL `default_transaction_read_only` tiver o parâmetro ligado, a Consulta Store não pode capturar dados.
- A funcionalidade da Consulta Store pode ser interrompida se\>encontrar longas consultas Unicode ( = 6000 bytes).
- O período de retenção das estatísticas de espera é de 24 horas.
- As estatísticas de espera usam amostra para capturar uma fração de eventos. A frequência pode ser modificada `query_store_wait_sampling_frequency`utilizando o parâmetro .

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as Ideias](concepts-query-performance-insight.md) de Desempenho da Consulta
