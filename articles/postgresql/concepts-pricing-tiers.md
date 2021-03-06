---
title: Níveis de preços - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve as opções de cálculo e armazenamento na Base de Dados Azure para PostgreSQL - Servidor Único.
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 2e5b01a271eb290229904fc98d1268760e01620d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243565"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Escalões de preço na Base de Dados do Azure para PostgreSQL – Servidor Único

Pode criar uma Base de Dados Azure para servidor PostgreSQL num dos três diferentes níveis de preços: Básico, Propósito Geral e Memória Otimizada. Os níveis de preços são diferenciados pela quantidade de computação em vCores que pode ser provisionado, memória por vCore, e a tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são aprovisionados ao nível do servidor PostgreSQL. Um servidor pode ter uma ou muitas bases de dados.

|    | **Básico** | **Propósito Geral** | **Memória Otimizada** |
|:---|:----------|:--------------------|:---------------------|
| Geração computacional | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória por vCore | 2GB | 5 GB | 10 GB |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 16 TB | 5 GB a 16 TB |
| Período de retenção de backup de base de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um nível de preços, utilize a tabela seguinte como ponto de partida.

| Escalão de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Básico | Cargas de trabalho que requerem computação leve e desempenho em I/S. Exemplos incluem servidores usados para desenvolvimento ou teste ou aplicações de pequena escala pouco utilizadas. |
| Fins Gerais | A maioria das cargas de trabalho empresariais que requerem computação equilibrada e memória com uma potência de I/S escalável. Exemplos incluem servidores para hospedar aplicações web e móveis e outras aplicações empresariais.|
| Otimizada para Memória | Cargas de trabalho de base de dados de alto desempenho que requerem desempenho na memória para um processamento mais rápido de transações e uma maior conmoedação. Exemplos incluem servidores para processamento de dados em tempo real e aplicações transalíticas ou analíticas de alto desempenho.|

Depois de criar um servidor, o número de vCores, geração de hardware e nível de preços (exceto de e para o Basic) pode ser alterado para cima ou para baixo em segundos. Também pode ajustar a quantidade de armazenamento para cima e o período de retenção de reserva para cima ou para baixo sem tempo de inatividade de aplicação. Não é possível alterar o tipo de armazenamento de cópia de segurança depois de um servidor ser criado. Para mais informações, consulte a secção de [recursos da Escala.](#scale-resources)

## <a name="compute-generations-and-vcores"></a>Gerações computadas e vCores

Os recursos computacionais são fornecidos como vCores, que representam o CPU lógico do hardware subjacente. China East 1, China North 1, US DoD Central e US DoD East utilizam cpUs lógicos Gen 4 que são baseados em processadores Intel E5-2673 v3 (Haswell) 2.4-GHz. Todas as outras regiões utilizam cpUs lógicos da Gen 5 que se baseiam em processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz.

## <a name="storage"></a>Storage

O armazenamento que você disponibiliza é a quantidade de capacidade de armazenamento disponível na sua Base de Dados Azure para o servidor PostgreSQL. O armazenamento é utilizado para os ficheiros de base de dados, ficheiros temporários, registos de transações e registos de servidores PostgreSQL. A quantidade total de armazenamento que disponibiliza também define a capacidade de I/S disponível para o seu servidor.

|    | **Básico** | **Propósito Geral** | **Memória Otimizada** |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento Básico | Armazenamento de propósito geral | Armazenamento de propósito geral |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 16 TB | 5 GB a 16 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>Min 100 IOPS<br/>Máximo 20.000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Máximo 20.000 IOPS |

> [!NOTE]
> O armazenamento até 16TB e 20.000 IOPS é apoiado nas seguintes regiões: Leste dos EUA, Leste dos EUA 2, Centro dos EUA, Eua Ocidental, Norte dos EUA, Centro-Sul dos EUA, Norte da Europa, Europa Ocidental, Reino Unido Sul, Oeste do Reino Unido, Sudeste Asiático, Ásia Oriental, Japão Leste, Japão Ocidental, Coreia Central Coreia do Sul, Austrália Leste, Austrália Sudeste.
>
> Todas as outras regiões suportam até 4TB de armazenamento e 6000 IOPS.
>

Pode adicionar capacidade de armazenamento adicional durante e após a criação do servidor, e permitir que o sistema cresça o armazenamento automaticamente com base no consumo de armazenamento da sua carga de trabalho. 

>[!NOTE]
> O armazenamento só pode ser dimensionado, não para baixo.

O nível básico não fornece uma garantia iops. Nos níveis de preços otimizados para fins gerais e memória, a escala IOPS com o tamanho de armazenamento provisionado numa relação de 3:1.

Pode monitorizar o seu consumo de I/S no portal Azure ou utilizando comandos Azure CLI. As métricas relevantes para monitorizar são o limite de [armazenamento, a percentagem de armazenamento, o armazenamento utilizado, e a OI por cento](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Atingir o limite de armazenamento

Os servidores com armazenamento amenos de 100 GB são marcados apenas para leitura se o armazenamento gratuito for inferior a 512MB ou 5% do tamanho de armazenamento provisionado. Os servidores com mais de 100 GB de armazenamento aprovisionado serão marcados como só de leitura se o armazenamento livre for inferior a 5 GB.

Por exemplo, se tiver aprovisionado 110 GB de armazenamento, e a utilização real ultrapassar os 105 GB, o servidor é marcado apenas para leitura. Em alternativa, se tiver aprovisionado 5 GB de armazenamento, o servidor é marcado apenas quando o armazenamento gratuito atinge menos de 512 MB.

Quando o servidor está definido para apenas leitura, todas as sessões existentes são desligadas e as transações não comprometidas são reroladas. Quaisquer operações de escrita subsequentes e transações falhadas. Todas as consultas de leitura subsequentes funcionarão ininterruptamente.  

Pode aumentar a quantidade de armazenamento aprovisionado para o seu servidor ou iniciar uma nova sessão no modo de leitura e deixar cair dados para recuperar o armazenamento gratuito. Correr `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` define a sessão atual para ler o modo de escrita. Para evitar a corrupção de dados, não efetue quaisquer operações de escrita quando o servidor ainda estiver em estado de leitura.

Recomendamos que ligue o armazenamento de modo a crescer automaticamente ou que instale um alerta para o notificar quando o armazenamento do servidor se aproxima do limiar para evitar entrar no estado de leitura. Para mais informações, consulte a documentação sobre [como configurar um alerta](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Armazenamento de automóveis

O armazenamento de forma automática impede que o servidor fique sem armazenamento e se torne apenas para leitura. Se o armazenamento automático crescer, o armazenamento cresce automaticamente sem afetar a carga de trabalho. Para servidores com armazenamento inferior a 100 GB, o tamanho de armazenamento provisionado é aumentado em 5 GB assim que o armazenamento gratuito é inferior ao maior de 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento livre é inferior ao maior de 10 GB ou 5% do tamanho de armazenamento provisionado. Aplicam-se os limites máximos de armazenamento acima referidos.

Por exemplo, se tiver aprovisionado 1000 GB de armazenamento, e a utilização real ultrapassar os 950 GB, o tamanho do armazenamento do servidor é aumentado para 1050 GB. Em alternativa, se tiver previsto 10 GB de armazenamento, o tamanho do armazenamento aumenta para 15 GB quando menos de 1 GB de armazenamento é gratuito.

Lembre-se que o armazenamento só pode ser dimensionado para cima, não para baixo.

## <a name="backup"></a>Cópia de segurança

O serviço recebe automaticamente cópias de segurança do seu servidor. Pode selecionar um período de retenção entre um intervalo de 7 a 35 dias. Os servidores otimizados para fins gerais e memória podem optar por ter armazenamento geo-redundante para cópias de segurança. Saiba mais sobre backups no [artigo conceitos.](concepts-backup.md)

## <a name="scale-resources"></a>Dimensionar recursos

Depois de criar o seu servidor, pode alterar de forma independente os vCores, a geração de hardware, o nível de preços (exceto de e para o Basic), a quantidade de armazenamento e o período de retenção de backup. Não é possível alterar o tipo de armazenamento de cópia de segurança depois de um servidor ser criado. O número de vCores pode ser dimensionado para cima ou para baixo. O período de retenção de reserva pode ser dimensionado para cima ou para baixo de 7 a 35 dias. O tamanho do armazenamento só pode ser aumentado. A escala dos recursos pode ser feita através do portal ou do Azure CLI. Para um exemplo de escala utilizando o Azure CLI, consulte [monitor e escalone uma Base de Dados Azure para servidor PostgreSQL utilizando o Azure CLI](scripts/sample-scale-server-up-or-down.md).

> [!NOTE] 
> O tamanho do armazenamento só pode ser aumentado. Não pode voltar a um tamanho de armazenamento menor após o aumento.

Quando altera o número de vCores, a geração de hardware, ou o nível de preços, é criada uma cópia do servidor original com a nova alocação de cálculo. Depois de o novo servidor estar a funcionar em pleno, as ligações são passadas para o novo servidor. Durante o período em que o sistema muda para o novo servidor, não se pode estabelecer nenhuma nova ligação e todas as transações não confirmadas são revertidas. Esta janela varia mas, na maioria dos casos, é inferior a um minuto.

O armazenamento de escala e a alteração do período de retenção de cópias de segurança são verdadeiras operações online. Não há tempo de descanso, e a sua candidatura não foi afetada. À medida que a escala IOPS com o tamanho do armazenamento provisionado, pode aumentar o IOPS disponível para o seu servidor, aumentando o armazenamento.

## <a name="pricing"></a>Preços

Para obter as informações mais atualizadas sobre preços, consulte a página de [preços](https://azure.microsoft.com/pricing/details/PostgreSQL/)do serviço . Para ver o custo da configuração que deseja, o [portal Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal no separador **de nível** de preços com base nas opções que selecionar. Se não tiver uma subscrição Azure, pode utilizar a calculadora de preços Azure para obter um preço estimado. No site da calculadora de [preços Azure,](https://azure.microsoft.com/pricing/calculator/) selecione **Adicionar itens,** expandir a categoria Bases de **Dados** e escolher a Base de **Dados Azure para postgreSQL** para personalizar as opções.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [criar um servidor PostgreSQL no portal](tutorial-design-database-using-azure-portal.md).
- Conheça [os limites](concepts-limits.md)de serviço. 
- Aprenda a [escalar com réplicas de leitura.](howto-read-replicas-portal.md)
