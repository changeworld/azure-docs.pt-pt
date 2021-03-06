---
title: Otimizar a recolha de estatísticas de consulta - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode otimizar a recolha de estatísticas de consulta numa Base de Dados Azure para PostgreSQL - Servidor Único
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f467f01118470eb51f7decf3bd6457917c566723
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770174"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Otimizar a recolha de estatísticas de consulta numa Base de Dados Azure para PostgreSQL - Servidor Único
Este artigo descreve como otimizar a recolha de estatísticas de consulta numa Base de Dados Azure para servidor PostgreSQL.

## <a name="use-pg_stats_statements"></a>Use pg_stats_statements
**Pg_stat_statements** é uma extensão PostgreSQL que é ativada por padrão na Base de Dados Azure para PostgreSQL. A extensão fornece um meio para rastrear as estatísticas de execução de todas as declarações SQL executadas por um servidor. Este módulo prende-se a cada execução de consulta e vem com um custo de desempenho não trivial. Permitir **pg_stat_statements** obriga a consultar textos para ficheiros no disco.

Se tiver consultas únicas com texto de longa consulta ou não monitorizar ativamente **pg_stat_statements,** desative **pg_stat_statements** para um melhor desempenho. Para isso, altere a `pg_stat_statements.track = NONE`definição para .

Algumas cargas de trabalho dos clientes viram até 50% de melhoria de desempenho quando **pg_stat_statements** está desativada. A troca que se faz quando desativa pg_stat_statements é a incapacidade de resolver problemas de desempenho.

Para `pg_stat_statements.track = NONE`definir:

- No portal Azure, vá à página de gestão de [recursos PostgreSQL e selecione a lâmina de parâmetros do servidor](howto-configure-server-parameters-using-portal.md).

  ![Lâmina de parâmetro do servidor PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Utilize o conjunto de configuração do servidor `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE` [Azure CLI](howto-configure-server-parameters-using-cli.md) az postgres para .

## <a name="use-the-query-store"></a>Use a Loja de Consulta 
A funcionalidade [Query Store](concepts-query-store.md) na Base de Dados Azure para PostgreSQL fornece um método mais eficaz para rastrear as estatísticas de consulta. Recomendamos esta funcionalidade como alternativa à utilização *pg_stats_statements*. 

## <a name="next-steps"></a>Passos seguintes
Considere `pg_stat_statements.track = NONE` a definição no [portal Azure](howto-configure-server-parameters-using-portal.md) ou utilizando o [Azure CLI](howto-configure-server-parameters-using-cli.md).

Para obter mais informações, consulte: 
- [Cenários de utilização da Loja de Consultas](concepts-query-store-scenarios.md) 
- [Melhores práticas do Query Store](concepts-query-store-best-practices.md) 
