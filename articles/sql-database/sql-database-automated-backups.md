---
title: Backups automáticos e geo-redundantes
description: A Base de Dados SQL cria automaticamente uma cópia de dados local a cada poucos minutos e utiliza armazenamento geo-redundante de acesso à leitura azure para geo-redundância.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 9ac6927df63d51830a58773e32ad0968920c0867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061768"
---
# <a name="automated-backups"></a>Cópias de segurança automatizadas

A Base de Dados Azure SQL cria automaticamente as cópias de segurança da base de dados que são mantidas durante o período de retenção configurado. Utiliza armazenamento geo-redundante de acesso à leitura Azure [(RA-GRS)](../storage/common/storage-redundancy.md) para garantir que as cópias de segurança são preservadas mesmo que o datacenter não esteja disponível.

Os backups de bases de dados são uma parte essencial de qualquer estratégia de continuidade do negócio e recuperação de desastres porque protegem os seus dados de corrupção acidental ou eliminação. Se as suas regras de segurança exigirem que as suas cópias de segurança estejam disponíveis por um período prolongado (até 10 anos), pode configurar a [retenção a longo prazo](sql-database-long-term-retention.md) em bases de dados singleton e piscinas de bases de dados elásticas.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é uma cópia de segurança da Base de Dados SQL?

A Base de Dados SQL utiliza a tecnologia SQL Server para criar cópias de [segurança completas](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) todas as semanas, [backups diferenciais](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) a cada 12 horas e backups de registo de [transações](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) a cada 5 a 10 minutos. As cópias de segurança são armazenadas em bolhas de [armazenamento RA-GRS](../storage/common/storage-redundancy.md) que são replicadas a um centro de [dados emparelhado](../best-practices-availability-paired-regions.md) para proteção contra uma falha no datacenter. Quando restaura uma base de dados, o serviço determina quais cópias de segurança completas, diferenciais e de registo de transações precisam de ser restauradas.

Pode utilizar estas cópias de segurança para:

- **Restaurar uma base** de dados existente até um ponto no tempo no passado no período de retenção utilizando o portal Azure, Azure PowerShell, Azure CLI ou a API REST. Para bases de dados únicas e bases de dados elásticas, esta operação criará uma nova base de dados no mesmo servidor que a base de dados original. Em caso gerido, esta operação pode criar uma cópia da base de dados ou o mesmo ou um caso gerido diferente sob a mesma subscrição.
- **Restaurar uma base de dados eliminada no momento da eliminação** ou em qualquer momento dentro do período de retenção. A base de dados eliminada só pode ser restaurada no mesmo servidor lógico ou na instância gerida onde a base de dados original foi criada.
- **Restaurar uma base de dados para outra região geográfica.** O geo-restauro permite-lhe recuperar de um desastre geográfico quando não consegue aceder ao seu servidor e base de dados. Cria uma nova base de dados em qualquer servidor existente, em qualquer lugar do mundo.
- **Restaurar uma base** de dados a partir de uma cópia de segurança específica a longo prazo numa única base de dados ou num conjunto de bases de dados elástica, se a base de dados estiver configurada com uma política de retenção a longo prazo (LTR). O LTR permite-lhe restaurar uma versão antiga da base de dados utilizando [o portal Azure](sql-database-long-term-backup-retention-configure.md#using-azure-portal) ou [o Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) para satisfazer um pedido de conformidade ou executar uma versão antiga da aplicação. Para obter mais informações, veja [Retenção de longa duração](sql-database-long-term-retention.md).

Para efetuar um restauro, consulte restaurar a [base de dados a partir de cópias de segurança](sql-database-recovery-using-backups.md).

> [!NOTE]
> No Armazenamento Azure, o termo *replicação* refere-se a copiar ficheiros de um local para outro. A *replicação* da base de dados do Servidor SQL refere-se a manter várias bases de dados secundárias sincronizadas com uma base de dados primária.

Pode experimentar algumas destas operações utilizando os seguintes exemplos:

| | O portal do Azure | Azure PowerShell |
|---|---|---|
| Alterar a retenção de backup | [Base de dados individual](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Instância gerida](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Base de dados individual](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Alterar a retenção de backup a longo prazo | [Base de dados individual](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Instância gerida - N/A  | [Base de dados individual](sql-database-long-term-backup-retention-configure.md)<br/>Instância gerida - N/A  |
| Restaurar uma base de dados a partir de um ponto no tempo | [Base de dados individual](sql-database-recovery-using-backups.md#point-in-time-restore) | [Base de dados individual](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Restaurar uma base de dados eliminada | [Base de dados individual](sql-database-recovery-using-backups.md) | [Base de dados individual](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restaurar uma base de dados do armazenamento de Azure Blob | Base de dados única - N/A <br/>Instância gerida - N/A  | Base de dados única - N/A <br/>[Instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Frequência de cópia de segurança

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

A Base de Dados SQL suporta o self-service para restauro pontual (PITR) criando automaticamente cópias de segurança completas, backups diferenciais e backups de registo de transações. As cópias de dados completas são criadas semanalmente, e as cópias de segurança diferenciais são geralmente criadas a cada 12 horas. As cópias de segurança do registo de transações são geralmente criadas a cada 5 a 10 minutos. A frequência das cópias de segurança do registo de transações baseia-se no tamanho do cálculo e na quantidade de atividade da base de dados. 

O primeiro backup completo é agendado imediatamente após a criação de uma base de dados. Esta cópia de segurança geralmente completa dentro de 30 minutos, mas pode demorar mais tempo quando a base de dados é grande. Por exemplo, a cópia inicial de cópia de segurança pode demorar mais tempo numa base de dados restaurada ou numa cópia da base de dados. Depois da primeira cópia de segurança completa, todas as restantes cópias de segurança serão agendadas automaticamente e geridas silenciosamente em segundo plano. O prazo exato de todas as cópias de segurança de base de dados é determinado pelo serviço de Base de Dados SQL, pois este equilibra a carga de trabalho global do sistema. Não pode alterar ou desativar tarefas de cópia de segurança.

As cópias de segurança DA PITR estão protegidas com armazenamento geo-redundante. Para obter mais informações, veja [Redundância do Armazenamento do Microsoft Azure](../storage/common/storage-redundancy.md).

Para mais informações sobre o PITR, consulte [a restauração ponto-a-tempo](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Retenção de longa duração

Para bases de dados individuais e reunidas, pode configurar a retenção a longo prazo (LTR) de cópias de segurança completas até 10 anos no armazenamento do Azure Blob. Se ativar a política LTR, as cópias de backup semanal são automaticamente copiadas para um recipiente de armazenamento RA-GRS diferente. Para satisfazer vários requisitos de conformidade, pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. O consumo de armazenamento depende da frequência selecionada de cópias de segurança e do período de retenção ou períodos. Pode utilizar a calculadora de [preços LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento LTR.

Tal como as cópias de segurança PITR, as cópias de segurança LTR estão protegidas com armazenamento geo-redundante. Para obter mais informações, veja [Redundância do Armazenamento do Microsoft Azure](../storage/common/storage-redundancy.md).

Para obter mais informações sobre o LTR, consulte a [retenção de backup a longo prazo](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Consumo de armazenamento de reserva

Para bases de dados únicas, esta equação é utilizada para calcular o uso total de armazenamento de cópia seleções:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Para piscinas de base de dados elásticas, o tamanho total de armazenamento de backup é agregado ao nível da piscina e é calculado da seguinte forma:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

As cópias de segurança que ocorrem antes do período de retenção são automaticamente purgadas com base na sua marca de tempo. Como cópias de segurança diferenciais e backups de registo requerem uma cópia de segurança mais cedo para ser útil, são purgadas juntas em pedaços semanais.

A Base de Dados Azure SQL calcula o seu armazenamento total de reserva de retenção como um valor cumulativo. A cada hora, este valor é reportado ao oleoduto de faturação Azure, que é responsável por agregar este uso de hora em hora para calcular o seu consumo no final de cada mês. Após a queda da base de dados, o consumo diminui à medida que os backups envelhecem. Depois de os backups se tornarem mais antigos do que o período de retenção, a faturação para.

   > [!IMPORTANT]
   > As cópias de segurança de uma base de dados são retidas durante o período de retenção especificado, mesmo que a base de dados tenha sido retirada. Ao deixar cair e recriar uma base de dados pode frequentemente economizar nos custos de armazenamento e cálculo, pode aumentar os custos de armazenamento de backup porque a Microsoft mantém uma cópia de segurança para o período de retenção especificado (que é de 7 dias no mínimo) para cada base de dados abandonada, cada uma tempo que é derrubado.

### <a name="monitor-consumption"></a>Monitorizar o consumo

Cada tipo de cópia de segurança (completa, diferencial e registo) é reportada na lâmina de monitorização da base de dados como uma métrica separada. O diagrama seguinte mostra como monitorizar o consumo de armazenamento de cópia de segurança para uma única base de dados. Esta funcionalidade não está atualmente disponível para casos geridos.

![Monitorize o consumo de backup da base de dados no portal Azure](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Consumo de armazenamento de reserva fino

O consumo excessivo de armazenamento de reserva dependerá da carga de trabalho e do tamanho das bases de dados individuais. Considere algumas das seguintes técnicas de afinação para reduzir o seu consumo de armazenamento de backup:

* Reduza o período de [retenção](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) de reserva para o mínimo possível para as suas necessidades.
* Evite fazer grandes operações de escrita, como reconstruções de índices, com mais frequência do que o necessário.
* Para grandes operações de carga de dados, considere a utilização de índices de lojas de [colunas agrupadas,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)reduza o número de índices não agrupados e considere as operações de carga a granel com a contagem de fileiras em torno de 1 milhão.
* No nível de serviço de finalidade geral, o armazenamento de dados provisionado é mais barato do que o preço do armazenamento de cópia de segurança em excesso. Se tiver custos de armazenamento de backup em excesso continuamente elevados, poderá considerar aumentar o armazenamento de dados para economizar no armazenamento de backup.
* Utilize tempDB em vez de tabelas permanentes na sua lógica ETL para armazenar resultados temporários. (Aplicável apenas à instância gerida.)
* Considere desligar a encriptação TDE para bases de dados que não contenham dados sensíveis (bases de dados de desenvolvimento ou teste, por exemplo). As cópias de segurança para bases de dados não encriptadas são normalmente comprimidas com uma relação de compressão mais elevada.

> [!IMPORTANT]
> Para dados analíticos mart \ cargas de trabalho de armazém de dados, recomendamos vivamente que utilize índices de lojas de [colunas agrupadas,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)reduza o número de índices não agrupados e considere as operações de carga a granel com a contagem de fileiras em torno de 1 milhão para reduzir o consumo excessivo de armazenamento de backup.

## <a name="storage-costs"></a>Custos de armazenamento

O preço do armazenamento varia consoante esteja a usar o modelo DTU ou o modelo vCore.

### <a name="dtu-model"></a>Modelo de DTU

Não há nenhuma taxa adicional para armazenamento de backup para bases de dados e piscinas de bases de dados elásticas se estiver usando o modelo DTU.

### <a name="vcore-model"></a>Modelo de vCore

Para bases de dados únicas, um valor mínimo de armazenamento de cópia seletiva igual a 100% do tamanho da base de dados não é fornecido sem custos adicionais. Para piscinas de base de dados elásticas e instâncias geridas, um valor mínimo de armazenamento de cópia de segurança igual a 100% do armazenamento de dados atribuído saqueado para o pool ou tamanho da instância, respectivamente, não é fornecido a custo adicional. O consumo adicional de armazenamento de cópias de segurança será cobrado por GB/mês. Este consumo adicional dependerá da carga de trabalho e da dimensão das bases de dados individuais.

A Base de Dados Azure SQL calculará o seu armazenamento total de reserva de retenção como um valor cumulativo. A cada hora, este valor é reportado ao oleoduto de faturação Azure, que é responsável por agregar este uso de hora em hora para obter o seu consumo no final de cada mês. Após a queda da base de dados, a Microsoft diminui o consumo à medida que as cópias de segurança envelhecem. Depois de os backups se tornarem mais antigos do que o período de retenção, a faturação para. Como todas as cópias de segurança de registo e cópias de segurança diferenciais são retidas durante todo o período de retenção, as bases de dados fortemente modificadas terão encargos de backup mais elevados.

Assuma que uma base de dados acumulou 744 GB de armazenamento de backup e que este valor permanece constante durante um mês inteiro. Para converter este consumo acumulado de armazenamento em uso horária, divida-o em 744,0 (31 dias por mês * 24 horas por dia). Assim, a Base de Dados SQL informará que a base de dados consumia 1 GB de reserva PITR a cada hora. A faturação azure irá agregar este consumo e mostrar um uso de 744 GB durante todo o mês. O custo será baseado na taxa de $/GB/mês na sua região.

Agora, um exemplo mais complexo. Suponha que a base de dados tenha a sua retenção aumentada para 14 dias a meio do mês. Assuma que este aumento (hipoteticamente) resulta no armazenamento total de cópia de segurança duplicando para 1.488 GB. A Base de Dados SQL reportaria 1 GB de utilização durante as horas 1 a 372. Relataria o uso como 2 GB durante as horas 373 a 744. Este uso seria agregado a uma nota final de 1.116 GB/mês.

### <a name="monitor-costs"></a>Monitorizar os custos

Para compreender os custos de armazenamento de backup, vá à **Cost Management + Faturação** no portal Azure, selecione **Cost Management,** e depois selecione Análise de **Custos**. Selecione a subscrição desejada como **O Âmbito**, e, em seguida, filtrar para o período de tempo e serviço que lhe interessa.

Adicione um filtro para **o nome de serviço**e, em seguida, selecione uma base de dados **sql** na lista de lançamentos. Utilize o filtro **de subcategoria do medidor** para escolher o contador de faturação para o seu serviço. Para uma única base de dados ou uma piscina de base de dados elástica, selecione armazenamento de **reserva de reserva pitr de piscina única/elástica**. Para uma instância gerida, selecione depósito de **backup mi pitr**. As subcategorias de **Armazenamento** e **computação** podem interessar-lhe também, mas não estão associadas a custos de armazenamento de backup.

![Análise de custos de armazenamento de backup](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Retenção da cópia de segurança

Todas as bases de dados Azure SQL (bases de dados únicas, reunidas e geridas) têm um período de retenção de backup padrão de 7 dias. Pode [alterar o período](#change-the-pitr-backup-retention-period) de retenção de reserva para 35 dias.

Se eliminar uma base de dados, a Base de Dados SQL manterá as cópias de segurança da mesma forma que seria para uma base de dados online. Por exemplo, se eliminar uma base de dados básica com um período de retenção de sete dias, uma cópia de segurança com quatro dias de vida é guardada por mais três dias.

Se necessitar de manter as cópias de segurança por mais tempo do que o período máximo de retenção, pode modificar as propriedades de backup para adicionar um ou mais períodos de retenção a longo prazo à sua base de dados. Para obter mais informações, veja [Retenção de longa duração](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se eliminar o servidor Azure SQL que acolhe bases de dados SQL, todas as bases de dados elásticas e bases de dados que pertencem ao servidor também são eliminadas. Não podem ser recuperados. Não pode restaurar um servidor apagado. Mas se configurar a retenção a longo prazo, as cópias de segurança das bases de dados com LTR não serão eliminadas e estas bases de dados podem ser restauradas.

## <a name="encrypted-backups"></a>Backups encriptados

Se a sua base de dados estiver encriptada com TDE, as cópias de segurança são automaticamente encriptadas em repouso, incluindo cópias de segurança LTR. Quando o TDE está ativado para uma base de dados Azure SQL, as cópias de segurança também são encriptadas. Todas as novas bases de dados Azure SQL estão configuradas com TDE ativado por padrão. Para obter mais informações sobre o TDE, consulte encriptação transparente de dados com base de [dados Azure SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integridade de backup

Numa base de dados Azure SQL testa automaticamente a restauração de bases de dados automatizadas de bases de dados colocadas em servidores lógicos e piscinas de bases de dados elásticas. (Este teste não está disponível em instância gerida.) Após a restauração do ponto-a-dia, as bases de dados também recebem controlos de integridade do DBCC CHECKDB.

A instância gerida requer `CHECKSUM` cópia de segurança inicial `RESTORE` automática com bases de dados restauradas com o comando nativo ou com o Serviço de Migração de Dados Azure após a migração.

Quaisquer problemas encontrados durante a verificação de integridade resultarão num alerta para a equipa de engenharia. Para mais informações, consulte a Integridade dos Dados na Base de [Dados Azure SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Conformidade

Quando migra a sua base de dados de um nível de serviço baseado em DTU para um nível de serviço baseado em vCore, a retenção PITR é preservada para garantir que a política de recuperação de dados da sua aplicação não está comprometida. Se a retenção por defeito não cumprir os seus requisitos de conformidade, pode alterar o período de retenção do PITR utilizando a PowerShell ou a API REST. Para mais informações, consulte Alterar o período de retenção de [backup PITR](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Alterar o período de retenção de backup PITR

Pode alterar o período de retenção de backup PITR padrão utilizando o portal Azure, PowerShell ou a API REST. Os exemplos que se seguem ilustram como alterar a retenção do PITR para 28 dias.

> [!WARNING]
> Se reduzir o atual período de retenção, todos os backups existentes que são mais antigos do que o novo período de retenção já não estão disponíveis. Se aumentar o atual período de retenção, a Base de Dados SQL manterá as cópias de segurança existentes até ao final do período de retenção mais longo.

> [!NOTE]
> Estas APIs afetarão apenas o período de retenção do PITR. Se configurar o LTR para a sua base de dados, não será afetado. Para obter informações sobre como alterar os períodos de retenção lTR, consulte a [retenção a longo prazo](sql-database-long-term-retention.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Alterar o período de retenção de backup PITR utilizando o portal Azure

Para alterar o período de retenção de backup PITR utilizando o portal Azure, vá ao objeto do servidor cujo período de retenção pretende alterar no portal. Em seguida, selecione a opção apropriada com base no objeto do servidor que está a modificar.

#### <a name="single-database-and-elastic-database-pools"></a>[Bases de dados únicas e bases de dados elásticas](#tab/single-database)

As alterações à retenção de backup PITR para bases de dados Únicas Azure SQL são feitas ao nível do servidor. As alterações efetuadas ao nível do servidor aplicam-se às bases de dados do servidor. Para alterar a retenção do PITR para um servidor de base de dados Azure SQL a partir do portal Azure, vá para a lâmina de visão geral do servidor. Selecione **'Gerir Backups'** no painel esquerdo e, em seguida, **selecione a retenção** de Configuração na parte superior do ecrã:

![Alterar a retenção do PITR, o nível do servidor](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Instância gerida](#tab/managed-instance)

As alterações à retenção de backup PITR para as instâncias geridas pela Base de Dados SQL são efetuadas a nível individual de base de dados. Para alterar a retenção de backup PITR para uma base de dados por exemplo do portal Azure, vá à lâmina de visão geral da base de dados individual. Em seguida, **selecione a retenção** de cópia de segurança Configurar na parte superior do ecrã:

![Alterar a retenção do PITR, instância gerida](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Alterar o período de retenção de backup PITR utilizando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell AzureRM ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para mais informações, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az são substancialmente idênticos aos dos módulos AzureRm.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Alterar o período de retenção de backup PITR utilizando a API REST

#### <a name="sample-request"></a>Pedido de amostra

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Corpo do pedido

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Resposta de amostra

Código de estado: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Para mais informações, consulte [Backup Retention REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Passos seguintes

- Os backups de bases de dados são uma parte essencial de qualquer estratégia de continuidade do negócio e recuperação de desastres porque protegem os seus dados de corrupção acidental ou eliminação. Para conhecer as outras soluções de continuidade de negócio sql da Base de dados Azure, consulte a [visão geral da continuidade do Negócio.](sql-database-business-continuity.md)
- Obtenha mais informações sobre como restaurar uma base de [dados a um ponto no tempo utilizando o portal Azure](sql-database-recovery-using-backups.md).
- Obtenha mais informações sobre como restaurar uma base de [dados a um ponto no tempo utilizando o PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para obter informações sobre como configurar, gerir e restaurar a longo prazo a retenção de backups automatizados no armazenamento do Azure Blob utilizando o portal Azure, consulte Gerir a retenção de backup a [longo prazo utilizando o portal Azure.](sql-database-long-term-backup-retention-configure.md)
- Para obter informações sobre como configurar, gerir e restaurar a longo prazo a retenção de backups automatizados no armazenamento do Blob Azure utilizando o PowerShell, consulte Gerir a [retenção de cópias](sql-database-long-term-backup-retention-configure.md)de segurança a longo prazo utilizando o PowerShell .
