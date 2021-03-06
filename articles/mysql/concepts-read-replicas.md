---
title: Ler réplicas - Base de Dados Azure para MySQL.
description: 'Saiba mais sobre as réplicas de leitura na Base de Dados Azure para o MySQL: escolher regiões, criar réplicas, conectar-se a réplicas, monitorizar a replicação e parar a replicação.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 98461928e465a103f73761afce5270234224fbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167352"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Réplicas de leitura na Base de Dados do Azure para MySQL

A funcionalidade de réplica de leitura permite replicar dados de uma Base de Dados do Azure para servidor MySQL para um servidor só de leitura. Pode replicar do servidor mestre para até cinco réplicas. As réplicas são atualizadas de forma assíncrona com a tecnologia de replicação baseada na posição dos ficheiros de registo binário nativo (binlog) do motor MySQL. Para saber mais sobre a replicação do binlog, consulte a visão geral da replicação do [binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

As réplicas são novos servidores que geres similares à base de dados regular do Azure para servidores MySQL. Para cada réplica de leitura, você é cobrado para a computação provisionada em vCores e armazenamento em GB/mês.

Para saber mais sobre as funcionalidades e problemas de replicação mySQL, consulte a documentação de [replicação MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

A funcionalidade de réplica de leitura ajuda a melhorar o desempenho e a escala de cargas de trabalho intensivas de leitura. As cargas de trabalho de leitura podem ser isoladas das réplicas, enquanto as cargas de trabalho de escrita podem ser direcionadas para o mestre.

Um cenário comum é ter bi e cargas de trabalho analíticas usar a réplica de leitura como fonte de dados para reportar.

Como as réplicas são apenas de leitura, não reduzem diretamente os encargos de capacidade de escrita sobre o mestre. Esta funcionalidade não é direcionada para cargas de trabalho intensivas.

A réplica da leitura utiliza replicação assíncrona MySQL. A funcionalidade não se destina a cenários de replicação sincronizados. Haverá um atraso mensurável entre o mestre e a réplica. Os dados da réplica acabam por se tornar consistentes com os dados do mestre. Utilize esta funcionalidade para cargas de trabalho que possam acomodar este atraso.

## <a name="cross-region-replication"></a>Replicação transversal
Pode criar uma réplica de leitura numa região diferente do seu servidor principal. A replicação transversal pode ser útil para cenários como o planeamento de recuperação de desastres ou aproximar os dados dos seus utilizadores.

Pode ter um servidor principal em qualquer Base de [Dados Azure para a região mySQL](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Um servidor principal pode ter uma réplica na sua região emparelhada ou nas regiões de réplica universal. A imagem abaixo mostra quais as regiões réplicas disponíveis dependendo da sua região principal.

[![Ler regiões de réplica](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplica seleções universais
Pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente do local onde o seu servidor principal esteja localizado. As regiões de réplica universal apoiadas incluem:

Austrália Leste, Austrália Sudeste, Centro dos EUA, Ásia Oriental, Leste dos EUA, Leste DOS 2, Japão Leste, Japão Oeste, Coreia Central, Coreia do Sul, Norte Dos EUA, Norte Da Europa, Centro-Sul dos EUA, Sudeste Asiático, Reino Unido Sul, Reino Unido Oeste, Europa Ocidental, Oeste dos EUA.

*West US 2 está temporariamente indisponível como uma réplica de região transversal.


### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, pode criar uma réplica de leitura na região de Azure emparelhada com o seu servidor principal. Se não conhece o par da sua região, pode aprender mais com o [artigo Das Regiões Pares de Azure.](../best-practices-availability-paired-regions.md)

Se estiver a utilizar réplicas de regiões cruzadas para planeamento de recuperação de desastres, recomendamos que crie a réplica na região emparelhada em vez de uma das outras regiões. As regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

No entanto, existem limitações a ter em conta: 

* Disponibilidade regional: Azure Database for MySQL está disponível em West US 2, France Central, UAE North e Germany Central. No entanto, as suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões azure são emparelhadas numa só direção. Estas regiões incluem a Índia Ocidental, o Brasil Sul e os EUA Gov Virginia. 
   Isto significa que um servidor principal na Índia Ocidental pode criar uma réplica no sul da Índia. No entanto, um servidor principal no Sul da Índia não pode criar uma réplica na Índia Ocidental. Isto porque a região secundária da Índia Ocidental é o sul da Índia, mas a região secundária do Sul da Índia não é a Índia Ocidental.


## <a name="create-a-replica"></a>Criar uma réplica

Se um servidor principal não tiver servidores de réplica existentes, o mestre reiniciará primeiro para se preparar para a replicação.

Quando iniciar o fluxo de trabalho de réplica de réplica, é criada uma base de dados Azure em branco para o servidor MySQL. O novo servidor está cheio de dados que estavam no servidor principal. O tempo de criação depende da quantidade de dados sobre o mestre e o tempo desde o último backup semanal completo. O tempo pode variar de alguns minutos a várias horas.

Cada réplica está ativada para armazenamento [de automóveis.](concepts-pricing-tiers.md#storage-auto-grow) A função de crescimento automático permite que a réplica acompanhe os dados que lhe são replicados e evitar uma interrupção na replicação causada por erros fora de armazenamento.

Aprenda a [criar uma réplica de leitura no portal Azure.](howto-read-replicas-portal.md)

## <a name="connect-to-a-replica"></a>Ligar a uma réplica

Na criação, uma réplica herda as regras de firewall ou o ponto final do serviço VNet do servidor principal. Depois, estas regras são independentes do servidor principal.

A réplica herda a conta de administração do servidor principal. Todas as contas de utilizador no servidor principal são replicadas para as réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador que estão disponíveis no servidor principal.

Pode ligar-se à réplica utilizando o seu nome de anfitrião e uma conta de utilizador válida, como faria numa base de dados azure regular para o servidor MySQL. Para um servidor chamado **myreplica** com o nome de utilizador do administrador **myadmin,** pode ligar-se à réplica utilizando o cli mysql:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

No momento, introduza a palavra-passe para a conta de utilizador.

## <a name="monitor-replication"></a>Monitorizar a replicação

A Base de Dados Azure para MySQL fornece o lag de **replicação em segundos** métrico no Monitor Azure. Esta métrica está disponível apenas para réplicas.

Esta métrica é `seconds_behind_master` calculada utilizando a métrica disponível no comando da `SHOW SLAVE STATUS` MySQL.

Detete um alerta para informá-lo quando o lag de replicação atingir um valor que não é aceitável para a sua carga de trabalho.

## <a name="stop-replication"></a>Parar replicação

Podes parar a replicação entre um mestre e uma réplica. Após a replicação ser interrompida entre um servidor principal e uma réplica de leitura, a réplica torna-se um servidor autónomo. Os dados no servidor autónomo são os dados que estavam disponíveis na réplica no momento em que o comando de replicação stop foi iniciado. O servidor autónomo não alcança o servidor principal.

Quando escolheparar a replicação a uma réplica, perde todas as ligações ao seu anterior mestre e outras réplicas. Não há falha automatizada entre um mestre e a sua réplica.

> [!IMPORTANT]
> O servidor autónomo não pode voltar a ser transformado numa réplica.
> Antes de parar a replicação numa réplica de leitura, certifique-se de que a réplica tem todos os dados que necessita.

Aprenda a parar a [replicação a uma réplica.](howto-read-replicas-portal.md)

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Escalões de preço

Atualmente, as réplicas de leitura só estão disponíveis nos níveis de preços otimizados para fins gerais e memória.

### <a name="master-server-restart"></a>Reinício do servidor principal

Quando criar uma réplica para um mestre que não tem réplicas existentes, o mestre recomeçará primeiro a preparar-se para a replicação. Tome isto em consideração e execute estas operações durante um período fora do pico.

### <a name="new-replicas"></a>Novas réplicas

Uma réplica de leitura é criada como uma nova Base de Dados Azure para o servidor MySQL. Um servidor existente não pode ser transformado numa réplica. Não se pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração de réplica

Uma réplica é criada usando a mesma configuração do servidor que o mestre. Após a criação de uma réplica, várias definições podem ser alteradas independentemente do servidor principal: geração de computação, vCores, armazenamento e período de retenção de backup. O nível de preços também pode ser alterado de forma independente, exceto para ou a partir do nível Básico.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre ser atualizada para novos valores, atualize a configuração de réplica para valores iguais ou superiores. Esta ação garante que a réplica pode acompanhar quaisquer alterações feitas no mestre.

As regras de firewall, as regras de rede virtual e as definições de parâmetros são herdadas do servidor principal para a réplica quando a réplica é criada. Depois, as regras da réplica são independentes.

### <a name="stopped-replicas"></a>Réplicas paradas

Se parar a replicação entre um servidor principal e uma réplica de leitura, a réplica parada torna-se um servidor autónomo que aceita tanto as leituras como as escritas. O servidor autónomo não pode voltar a ser transformado numa réplica.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestres e autónomos eliminados

Quando um servidor principal é eliminado, a replicação é interrompida para todas as réplicas lidas. Estas réplicas tornam-se automaticamente servidores autónomos e podem aceitar leituras e escritos. O servidor principal em si é eliminado.

### <a name="user-accounts"></a>Contas de utilizador

Os utilizadores do servidor principal são replicados para as réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador disponíveis no servidor principal.

### <a name="server-parameters"></a>Parâmetros do servidor

Para impedir que os dados fiquem dessincronizados e evitar potenciais perdas de dados ou corrupção, a atualização de alguns parâmetros de servidor é bloqueada ao utilizar réplicas de leitura.

Os seguintes parâmetros do servidor estão bloqueados nos servidores master e replica:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

O [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) parâmetro está bloqueado nos servidores de réplicas. 

### <a name="other"></a>Outros

- Os identificadores globais de transações (GTID) não são suportados.
- A criação de uma réplica de uma réplica não é suportada.
- As tabelas de memória podem fazer com que as réplicas fiquem dessincronizadas. Esta é uma limitação da tecnologia de replicação MySQL. Leia mais na documentação de [referência mySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) para mais informações.
- Certifique-se de que as tabelas do servidor principal têm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre o mestre e as réplicas.
- Reveja a lista completa das limitações de replicação do MySQL na [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar e gerir réplicas de leitura usando o portal Azure](howto-read-replicas-portal.md)
- Aprenda a [criar e gerir réplicas de leitura usando a API Azure CLI e REST](howto-read-replicas-cli.md)
