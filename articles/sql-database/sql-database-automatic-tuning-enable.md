---
title: Ativar o ajuste automático
description: Pode ativar a sintonização automática na sua Base de Dados Azure SQL facilmente.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: eed839c277156046ff9b7d97c6e87636a0822889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299333"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Ativar a sintonização automática para monitorizar consultas e melhorar o desempenho da carga de trabalho

A Base de Dados Azure SQL é um serviço de dados gerido automaticamente que monitoriza constantemente as suas consultas e identifica a ação que pode realizar para melhorar o desempenho da sua carga de trabalho. Pode rever as recomendações e aplicá-las manualmente, ou deixar que a Base de Dados Azure SQL aplique automaticamente as ações corretivas - isto é conhecido como **modo de afinação automática**.

A afinação automática pode ser ativada ao nível do servidor ou da base de dados através do [portal Azure,](sql-database-automatic-tuning-enable.md#azure-portal)das chamadas [REST API](sql-database-automatic-tuning-enable.md#rest-api) e dos comandos [T-SQL.](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)

> [!NOTE]
> Para o Caso Gerido, a opção suportada FORCE_LAST_GOOD_PLAN pode ser configurada apenas através do [T-SQL.](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) A configuração baseada no portal e as opções automáticas de afinação de índices descritas neste artigo não se aplicam à Instância Gerida.

> [!NOTE]
> Configurar opções de afinação automática através do modelo ARM (Gestor de Recursos Azure) não é suportado neste momento.

## <a name="enable-automatic-tuning-on-server"></a>Ativar a sintonização automática no servidor

A nível do servidor pode optar por herdar a configuração de afinação automática a partir de "Predefinições De Sem Definição" ou não herdar a configuração. As predefinições do Azure são FORCE_LAST_GOOD_PLAN está ativada, CREATE_INDEX está ativada e DROP_INDEX está desativada.

> [!IMPORTANT]
> A partir de março de 2020, as alterações às incumprimentos do Azure para a sintonização automática entrarão em vigor da seguinte forma:
>
> - Os novos incumprimentos do Azure serão FORCE_LAST_GOOD_PLAN = ativados, CREATE_INDEX = desativados e DROP_INDEX = desativados.
> - Os servidores existentes sem preferências de afinação automática configuradas serão automaticamente configurados para HERDar as novas falhas do Azure. Isto aplica-se a todos os clientes que atualmente têm definições de servidor para afinação automática num estado indefinido.
> - Os novos servidores criados serão automaticamente configurados para HERDar os novos incumprimentos do Azure (ao contrário do que aconteceu anteriormente quando a configuração de afinação automática estava em estado indefinido após a criação de novos servidores).

### <a name="azure-portal"></a>Portal do Azure

Para permitir a sintonização automática no **servidor**lógico da Base de Dados Azure SQL, navegue para o servidor no portal Azure e, em seguida, selecione **a sintonização automática** no menu.

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Por favor, note que **DROP_INDEX** opção neste momento não é compatível com aplicações que usam comutação de divisórias e dicas de índice e não deve ser ativada nestes casos. A queda de índices não utilizados não é suportada para os níveis de serviço Premium e Business Critical.
>

Selecione as opções de afinação automática que pretende ativar e selecionar **Aplicar**.

As opções de afinação automática num servidor são aplicadas a todas as bases de dados deste servidor. Por padrão, todas as bases de dados herdam a configuração do seu servidor principal, mas esta pode ser ultrapassada e especificada para cada base de dados individualmente.

### <a name="rest-api"></a>API REST

Saiba mais sobre a utilização da API REST para permitir a sintonização automática num servidor, consulte a [atualização automática do Servidor SQL e os métodos GET HTTP](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Ativar a sintonização automática numa base de dados individual

A Base de Dados Azure SQL permite especificar individualmente a configuração de afinação automática para cada base de dados. No nível da base de dados pode optar por herdar a configuração de afinação automática do servidor dos pais, "Falhas de Azure" ou não herdar a configuração. Os Predefinidos Azure estão definidos para FORCE_LAST_GOOD_PLAN está ativado, CREATE_INDEX está ativado e DROP_INDEX está desativado.

> [!TIP]
> A recomendação geral é gerir a configuração de afinação automática ao **nível do servidor** para que as mesmas configurações de configuração possam ser aplicadas automaticamente em todas as bases de dados. Configure a sintonização automática numa base de dados individual apenas se precisar dessa base de dados para ter configurações diferentes das outras que herdam as definições do mesmo servidor.
>

### <a name="azure-portal"></a>Portal do Azure

Para permitir a sintonização automática numa única base de **dados,** navegue para a base de dados do portal Azure e selecione **a finação automática**.

As definições de afinação automática individual podem ser configuradas separadamente para cada base de dados. Pode configurar manualmente uma opção de afinação automática individual, ou especificar que uma opção herda as suas definições a partir do servidor.

![Base de Dados](./media/sql-database-automatic-tuning-enable/database.png)

Por favor, note que DROP_INDEX opção neste momento não é compatível com aplicações que usam comutação de divisórias e dicas de índice e não deve ser ativada nestes casos.

Depois de ter selecionado a configuração desejada, clique em **Aplicar**.

### <a name="rest-api"></a>API de descanso

Saiba mais sobre a utilização da API REST para permitir a sintonização automática numa única base de dados, consulte a Atualização automática de [afinação automática da Base de Dados SQL e os métodos GET HTTP](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Para permitir a sintonização automática numa única base de dados via T-SQL, ligue-se à base de dados e execute a seguinte consulta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

A definição de afinação automática para AUTO aplicará os Predefinidos Do Azure. Definindo-o para HERDa, a configuração de afinação automática será herdada do servidor dos pais. Escolhendo o CUSTOM, terá de configurar manualmente a afinação automática.

Para configurar opções individuais de afinação automática via T-SQL, ligue-se à base de dados e execute a consulta como esta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

A definição da opção de afinação individual para ON, irá sobrepor-se a qualquer definição que a base de dados herdou e permitirá a opção de afinação. Definindo-o para OFF, também irá anular qualquer definição que a base de dados herdou e desativar a opção de afinação. A opção de afinação automática, para a qual o DEFAULT é especificado, herdará a configuração de afinação automática a partir das definições do nível do servidor.  

> [!IMPORTANT]
> Em caso de [geo-replicação ativa,](sql-database-auto-failover-group.md)a afinação automática tem de ser configurada apenas na base de dados primária. As ações de afinação aplicadas automaticamente, por exemplo, a criação ou o exclusão de índices serão automaticamente replicadas para o secundário de leitura. Tentar ativar a sintonização automática via T-SQL no secundário apenas para leitura resultará numa falha, uma vez que não suporta uma configuração de afinação diferente no secundário de leitura.
>

Encontre as nossas opções mais abut T-SQL para configurar a afinação automática, consulte opções de CONJUNTO DE BASE DE [DADOS ALTER (Transact-SQL) para servidor de base de dados SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Desativado pelo sistema

A afinação automática está a monitorizar todas as ações que toma na base de dados e, em alguns casos, pode determinar que a afinação automática não pode funcionar corretamente na base de dados. Nesta situação, a opção de afinação será desativada pelo sistema. Na maioria dos casos isto acontece porque a Consulta Store não está ativada ou está em estado de leitura numa base de dados específica.

## <a name="permissions"></a>Permissões

Como a finação automática é a funcionalidade Azure, para usá-la terá de utilizar as funções RBAC incorporadas do Azure. A utilização da Autenticação SQL apenas não será suficiente para utilizar a funcionalidade do portal Azure.

Para utilizar a afinação automática, a permissão mínima necessária para conceder ao utilizador é a função de [contribuinte SQL DB](../role-based-access-control/built-in-roles.md#sql-db-contributor) incorporado da Azure. Também pode considerar a utilização de papéis de privilégio mais elevados, tais como SQL Server Contributor, Contributor e Owner.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Configurar notificações automáticas de e-mail de afinação

Consulte o guia de notificações de [e-mail de afinação automática.](sql-database-automatic-tuning-email-notifications.md)

## <a name="next-steps"></a>Passos seguintes

* Leia o artigo de [afinação automática](sql-database-automatic-tuning.md) para saber mais sobre a finação automática e como pode ajudá-lo a melhorar o seu desempenho.
* Consulte [as recomendações](sql-database-advisor.md) de Desempenho para uma visão geral das recomendações de desempenho da Base de Dados Azure SQL.
* Consulte [a Query Performance Insights](sql-database-query-performance.md) para aprender sobre o impacto de desempenho das suas principais consultas.
