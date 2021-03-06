---
title: Reforma do nível de serviço RS premium
description: O nível de serviço RS Premium está a ser reformado e o apoio está a terminar - ver opções de migração.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: f00ecd19877ba6236bde5de73d450967abc1fe15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821036"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL Database Premium RS (pré-visualização) está a ser retirado - opções para migração

Em fevereiro de 2018, a Microsoft anunciou que o nível de serviço RS Premium na Base de Dados Azure SQL não seria lançado para disponibilidade geral e deixaria de ser suportado após 31 de janeiro de 2019. Este prazo de fim de apoio foi prorrogado até 30 de junho de 2019. Este artigo explica as suas opções para migrar do nível de serviço Premium RS para outro nível de serviço. Depois de 30 de junho de 2019, a Microsoft migrará automaticamente as suas bases de dados Premium RS para um nível de serviço geralmente disponível que mais corresponda aos requisitos de desempenho da sua base de dados Premium RS.

Seguem-se os destinos de migração e as opções de preços que podem ser adequadas para clientes RS Premium:

- níveis de serviço vCore

  Os níveis de serviço **De Propósito Geral** e Business **Critical** no modelo de compra baseado [em vCore](sql-database-service-tiers-vcore.md). Estes dois níveis de serviço estão em geral. O modelo de compra baseado em vCore também oferece o nível de serviço **Hyperscale** que se adapta a pedido às necessidades da sua carga de trabalho com a escala automática até 100 TB por base de dados. O nível de serviço Hyperscale proporciona um desempenho IO comparável ao nível de serviço Premium no [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) a um preço mais próximo do nível de serviço Premium RS.
- Preços de Dev/Teste

  [Os preços de Dev/teste](https://azure.microsoft.com/pricing/dev-test/) proporcionam poupanças até 55% contra tarifas incluídas na licença com a subscrição do Estúdio Visual.
- Benefício Híbrido Azure e preços de capacidade reservados

  [O Azure Hybrid Benefit e os preços de capacidade reservados](https://azure.microsoft.com/pricing/details/sql-database/) proporcionam poupanças até 80% contra taxas incluídas na licença. Para obter mais informações sobre estas opções, consulte [O Benefício Híbrido Azure para o Servidor SQL](https://azure.microsoft.com/pricing/hybrid-benefit/) e a Base de [Dados Azure SQL reservada.](sql-database-reserved-capacity.md)

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Ata agora para migrar as suas bases de dados Premium RS para níveis de serviço alternativos da Base de Dados SQL

Reveja as orientações deste artigo juntamente com os nossos preços e documentação para determinar o destino(s) de migração adequado para as suas cargas de trabalho Premium RS.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrar cargas de trabalho intensivas em cálculoe e economizar

Para as suas cargas de trabalho Premium RS intensivas em computação, recomendamos migrar para o nosso nível de serviço de Propósito Geral baseado em VCore geralmente disponível e economizar mais tarifas versus licença incluídas usando o Benefício Híbrido Azure para o Servidor SQL e ofertas de capacidade reservadas. Se preferir permanecer numa opção de compra baseada em DTU, pode migrar as suas bases de dados Premium RS intensivas para um nível de serviço Standard e ainda economizar contra o preço de disponibilidade geral premium RS (se tivesse entrado na disponibilidade geral).

> [!WARNING]
> Migrar as suas cargas de trabalho Premium RS para os níveis de serviço Premium baseados em DTU pode aumentar os custos mensais em relação aos preços atuais do Premium RS. Recomendamos considerar os níveis Hyperscale ou Business Critical com Benefício Híbrido Azure e preços de capacidade reservados para manter custos semelhantes ou inferiores aos Premium RS.

### <a name="premium-rs-databases"></a>Bases de dados RS premium

|**Se está neste momento...**|**Migrar para um vCore comparável...**|**Migrar para dTU comparável...**|
|---|---|---|
|Premium RS 1|Propósito Geral 1 vCore (Gen4)|Padrão 3|
|Premium RS 2|Propósito Geral 2 vCores (Gen4)|Padrão 4|
|Premium RS 4|Propósito Geral 4 vCores (Gen4)|Padrão 6|
|Premium RS 6|Propósito Geral 6 vCores (Gen4)|Padrão 7|

### <a name="premium-rs-pools"></a>Piscinas RS Premium

|**Se está neste momento...**|**Migrar para um vCore comparável...**|**Migrar para dTU comparável...**|
|---|---|---|
|Premium RS piscina 125 DTU|Propósito Geral 1 vCore (Gen4)|Piscina padrão 100 eDTUs|
|Premium RS piscina 250 DTU|Propósito Geral 2 vCores (Gen4)|Piscina padrão 250 eDTUs|
|Piscina RS Premium 500 DTU|Propósito Geral 4 vCores (Gen4)|Piscina padrão 500 eDTUs|
|Piscina Premium RS 1000 DTU|Propósito Geral 8 vCores (Gen4)|Piscina padrão 1000 eDTUs|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Otimizar a poupança e o desempenho para as suas cargas de trabalho intensivas em IO

Recomendamos a migração das suas bases de dados únicas intensivas em IO para o nosso nível de Hiperescala baseado em VCore, atualmente em pré-visualização, e as suas bases de dados intensivas em IO para o nosso nível Business Critical geralmente disponível, para a combinação ideal de desempenho e custo.  As seguintes opções baseadas em vCore manterão ou melhorarão o seu desempenho atual e poderão economizar dinheiro quando combinados com o Azure Hybrid Benefit e o preço de capacidade reservado.

|**Se está neste momento...**|**Migrar para um vCore comparável...**|**Migrar para dTU comparável...**|
|---|---|---|
|Premium RS 1| Hiperescala 1 vCore (Gen4) ou Business Critical 1 vCore (Gen4)|Premium 1|
|Premium RS 2| Hiperescala 2 vCores (Gen4) ou Business Critical 2 vCores (Gen4)|Premium 2|
|Premium RS 4| Hiperescala 4 vCores (Gen4) ou Business Critical 4 vCores (Gen4)|Premium 4
|Premium RS 6| Hiperescala 6 vCores (Gen4) ou Business Critical 6 vCores (Gen4)|Premium 6|

|**Se está neste momento...**|**Migrar para um vCore comparável...**|**Migrar para dTU comparável...**|
|---|---|---|
|Premium RS piscina 125 DTU|Business Critical 2 vCores (Gen4)|Piscina premium 125 eDTUs|
|Premium RS piscina 250 DTU|Business Critical 2 vCores (Gen4)|Piscina premium 250 eDTUs|
|Piscina RS Premium 500 DTU|Business Critical 4 vCores (Gen4)|Piscina premium 500 eDTUs|
|Piscina Premium RS 1000 DTU|Business Critical 8 vCores (Gen4)|Piscina premium 1000 eDTUs|

## <a name="take-advantage-of-our-new-offers"></a>Aproveite as nossas novas ofertas

Os nossos níveis de serviço no modelo de compra baseado em vCore são elegíveis para ofertas especiais que podem economizar até 80% contra preços incluídos na licença. Utilize as suas licenças de edição SQL Server Standard ou Enterprise com garantia de software ativa para economizar até 55% contra preços incluídos na licença com o [Benefício Híbrido Azure para o Servidor SQL](https://azure.microsoft.com/pricing/hybrid-benefit/). Pode combinar o benefício híbrido com o [Azure SQL Database reservado](sql-database-reserved-capacity.md) preços de capacidade e economizar até 80% quando se compromete antecipadamente a um ou três anos de mandato.  Ativar ambos os benefícios hoje do portal Azure.

Se tiver alguma dúvida ou preocupação relativamente a esta alteração ou se necessitar de assistência à migração, contacte a [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migração de um nível de serviço RS Premium para um nível de serviço no Modelo DTU ou vCore

### <a name="migration-of-a-database"></a>Migração de uma base de dados

Migrar uma base de dados de um nível de serviço Premium RS para um nível de serviço no DTU ou no modelo vCore é semelhante à atualização ou degradação entre níveis de serviço no nível de serviço Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Utilização de cópia de base de dados para converter uma base de dados Premium RS numa base de dados baseada em DTU ou baseada em vCore

Pode copiar qualquer base de dados com um tamanho de computação RS Premium para uma base de dados com um tamanho de computação baseado em DTU ou baseado em vCore sem restrições ou sequenciação especial, desde que o tamanho da computação-alvo suporte o tamanho máximo da base de dados da base de dados fonte. A cópia da base de dados cria uma imagem instantânea de dados a partir do momento de início da operação de cópia e não realiza a sincronização de dados entre a fonte e o alvo.

## <a name="next-steps"></a>Passos seguintes

- Para obter detalhes sobre tamanhos específicos de cálculo e escolhas de tamanho de armazenamento disponíveis para uma única base de dados, consulte [os limites de recursos baseados em Bases de Dados SQL vCore para bases](sql-database-vcore-resource-limits-single-databases.md) de dados únicas
- Para mais detalhes sobre tamanhos específicos de cálculo e escolhas de tamanho de armazenamento disponíveis para piscinas elásticas, consulte [os limites de recursos baseados em SQL Database vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md).
