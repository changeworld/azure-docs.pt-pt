---
title: Upgrade para uma conta de armazenamento v2 de uso geral
titleSuffix: Azure Storage
description: Atualize para contas de armazenamento v2 de uso geral.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/25/2019
ms.author: tamram
ms.openlocfilehash: 9afbade408d6f95fcd3a61aa1ba65bc09c7a875b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067221"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Upgrade para uma conta de armazenamento v2 de uso geral

As contas de armazenamento v2 de uso geral suportam as mais recentes funcionalidades de Armazenamento Azure e incorporam toda a funcionalidade das contas de armazenamento v1 e Blob de uso geral. As contas v2 de uso geral são recomendadas para a maioria dos cenários de armazenamento. As contas v2 de uso geral fornecem os preços de capacidade mais baixos por gigabyte para o Armazenamento Azure, bem como os preços de transação competitivos da indústria. As contas v2 de uso geral suportam níveis de acesso à conta por defeito de níveis de nível quente ou fresco e blob entre o nível quente, fresco ou arquivado.

A atualização para uma conta de armazenamento v2 de propósito geral a partir das suas contas de armazenamento v1 ou Blob de propósito geral é simples. Pode fazer upgrade utilizando o portal Azure, PowerShell ou Azure CLI. Não existe tempo de inatividade ou risco de perda de dados associado à atualização para uma conta de armazenamento v2 de uso geral. A atualização da conta ocorre através de uma simples operação do Gestor de Recursos Azure que altera o tipo de conta.

> [!IMPORTANT]
> A atualização de uma conta de armazenamento v1 ou Blob para o v2 de fim geral é permanente e não pode ser desfeita.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue até à sua conta de armazenamento.
3. Na secção **Definições,** clique em **Configuração**.
4. Em **Tipo de conta**, clique em **Atualizar**.
5. Em **Confirmar atualização**, introduza o nome da sua conta.
6. Clique em **Upgrade** na parte inferior da lâmina.

    ![Atualizar conta tipo](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para atualizar uma conta v1 de propósito geral para uma conta v2 de propósito geral usando powerShell, primeiro atualizar powerShell para usar a versão mais recente do módulo **Az.Storage.** Veja [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) para obter informações sobre a instalação do PowerShell.

Em seguida, ligue para o seguinte comando para atualizar a conta, substituindo o nome do grupo de recursos, o nome da conta de armazenamento e o nível de acesso à conta pretendido.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para atualizar uma conta v1 de uso geral para uma conta v2 de uso geral utilizando o Azure CLI, instale primeiro a versão mais recente do Azure CLI. Veja [Install the Azure CLI 2.0 ](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (Instalar a CLI 2.0 do Azure) para obter informações sobre a instalação da CLI.

Em seguida, ligue para o seguinte comando para atualizar a conta, substituindo o nome do grupo de recursos, o nome da conta de armazenamento e o nível de acesso à conta pretendido.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Especificar um nível de acesso para dados blob

As contas v2 de uso geral suportam todos os serviços de armazenamento e objetos de dados do Azure, mas os níveis de acesso estão disponíveis apenas se aplicam para bloquear bolhas no armazenamento blob. Ao atualizar para uma conta de armazenamento v2 de uso geral, pode especificar um nível de acesso à conta por defeito de quente ou fresco, o que indica que os seus dados blob serão carregados como se o parâmetro de nível de acesso de blob individual não estivesse especificado.

Os níveis de acesso blob permitem-lhe escolher o armazenamento mais rentável com base nos seus padrões de utilização antecipados. As bolhas de bloco podem ser armazenadas em camadas quentes, frescas ou de arquivo. Para obter mais informações sobre os níveis de acesso, consulte o [armazenamento Azure Blob: Hot, Cool e Archive.](../blobs/storage-blob-storage-tiers.md)

Por predefinição, uma nova conta de armazenamento é criada no nível de acesso quente, e uma conta de armazenamento v1 de uso geral pode ser atualizada para o nível de conta quente ou cool. Se um nível de acesso à conta não for especificado na atualização, será atualizado para quente por padrão. Se estiver a explorar o nível de acesso a utilizar para a sua atualização, considere o seu cenário de utilização de dados atuais. Existem dois cenários típicos de utilizador para migrar para uma conta v2 de propósito geral:

* Tem uma conta de armazenamento v1 de uso geral existente e pretende avaliar uma atualização para uma conta de armazenamento v2 de uso geral, com o nível de acesso ao armazenamento certo para dados blob.
* Decidiu utilizar uma conta de armazenamento v2 de uso geral ou já tem uma e pretende avaliar se deve utilizar o nível de acesso ao armazenamento quente ou fresco para dados blob.

Em ambos os casos, a primeira prioridade é estimar o custo de armazenamento, acesso e operação nos seus dados armazenados numa conta de armazenamento v2 de uso geral e compará-lo com os custos atuais.

## <a name="pricing-and-billing"></a>Preços e faturação

A atualização de uma conta de armazenamento v1 para uma conta v2 de uso geral é gratuita. Pode especificar o nível de conta pretendido durante o processo de atualização. Se um nível de conta não for especificado na atualização, o nível de conta por defeito da conta atualizada será `Hot`. No entanto, alterar o nível de acesso ao armazenamento após a atualização pode resultar em alterações na sua conta, pelo que é aconselhável especificar o novo nível de conta durante a atualização.

Todas as contas de armazenamento utilizam um modelo de preços para o armazenamento de blobs com base na camada de cada blob. Ao utilizar uma conta de armazenamento, aplicam-se as seguintes considerações de faturação:

* **Custos**de armazenamento : Para além da quantidade de dados armazenados, o custo de armazenamento de dados varia consoante o nível de acesso ao armazenamento. O custo por gigabyte diminui conforme a camada se torna mais esporádica.

* **Custos de acesso a dados**: os custos de acesso a dados aumenta conforme a camada se torna mais esporádica. Para dados no nível de acesso de armazenamento fresco e de arquivo, é-lhe cobrada uma taxa de acesso de dados por gigabyte para leituras.

* **Custos de transação**: há um encargo por transação para todas as camadas que aumenta cada vez que a camada é mais esporádica.

* **Custos de transferência de dados de georreplicação**: este custo aplica-se apenas às contas que têm a georreplicação configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.

* **Custos de transferência de dados de saída**: as transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) estão sujeitas a uma cobrança pela utilização de largura de banda por gigabyte, tal como as contas do Storage para fins gerais.

* **Alteração do nível**de acesso ao armazenamento: Mudar o nível de acesso ao armazenamento da conta de fresco para quente incorre numa carga igual à leitura de todos os dados existentes na conta de armazenamento. No entanto, mudar o nível de acesso à conta de quente para frio incorre numa taxa igual a escrever todos os dados para o nível cool (apenas contas GPv2).

> [!NOTE]
> Para obter mais informações sobre o modelo de preços das contas de armazenamento, veja a página [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Para obter mais informações sobre as taxas aplicáveis às transferências de dados de saída, veja a página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Estimar os custos dos seus padrões de utilização atuais

Para estimar o custo de armazenamento e acesso a dados blob numa conta de armazenamento v2 de uso geral num determinado nível, avalie o seu padrão de utilização existente ou se aconselhe o padrão de utilização esperado. Em geral, precisa de saber o seguinte:

* O seu consumo de armazenamento Blob, em gigabytes, incluindo:
  * Que quantidade de dados está a ser armazenada na conta de armazenamento?
  * De que forma o volume de dados é alterado mensalmente: os novos dados substituem constantemente os dados antigos?

* O padrão de acesso primário para os seus dados de armazenamento Blob, incluindo:
  * De que dados estão a ser lidos e escritos na conta de armazenamento?
  * Quantas operações de leitura versus write ocorrem nos dados da conta de armazenamento?

Para decidir sobre o melhor nível de acesso para as suas necessidades, pode ser útil determinar a sua capacidade de dados blob, e como esses dados estão sendo usados. Isto pode ser melhor feito olhando para as métricas de monitorização para a sua conta.

### <a name="monitoring-existing-storage-accounts"></a>Monitorizar contas de armazenamento existentes

Para monitorizar as suas contas de armazenamento existentes e recolher estes dados, pode utilizar a Análise do Armazenamento do Azure, que efetua o registo e fornece dados de métricas para uma conta de armazenamento. A Análise de Armazenamento pode armazenar métricas que incluem estatísticas agregadas de transações e dados de capacidade sobre pedidos enviados para o serviço de armazenamento relativamente aos tipos de contas de Armazenamento de blobs, GPv1 e GPv2. Estes dados são armazenados em tabelas bem conhecidas na mesma conta de armazenamento.

Para obter mais informações, veja [About Storage Analytics Metrics (Sobre Métricas da Análise de Armazenamento)](https://msdn.microsoft.com/library/azure/hh343258.aspx) e [Storage Analytics Metrics Table Schema (Esquema da Tabela de Métricas da Análise de Armazenamento)](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> As contas de Armazenamento de blobs expõem o ponto final do serviço Tabela apenas para armazenar e aceder aos dados de métricas relativos a essa conta.

Para monitorizar o consumo de armazenamento do Armazenamento de blobs, tem de ativar as métricas de capacidade.
Com esta opção ativada, os dados de capacidade são registados diariamente para um serviço Blob de uma conta de armazenamento e registados como uma entrada que é escrita na tabela *$MetricsCapacityBlob* dentro da mesma conta de armazenamento.

Para monitorizar os padrões de acesso a dados do Armazenamento de blobs, tem de ativar as métricas de transação horária a partir da API. Com as métricas de transação horária ativadas, as transações por API são agregadas ao fim de cada hora e registadas como uma entrada que é escrita na tabela *$MetricsHourPrimaryTransactionsBlob* dentro da mesma conta de armazenamento. A tabela *$MetricsHourSecondaryTransactionsBlob* regista as transações para o ponto final secundário quando são utilizadas contas de armazenamento RA-GRS.

> [!NOTE]
> Se tiver uma conta de armazenamento de fins gerais na qual armazenou blobs de páginas e discos de máquinas virtuais ou filas, ficheiros ou tabelas, juntamente com dados de blobs de blocos e de acréscimo, este processo de estimativa não é aplicável. Os dados de capacidade não distinguem os blobs de blocos dos outros tipos e não disponibilizam dados de capacidade para outros tipos de dados. Se utilizar estes tipos, uma metodologia alternativa é ver as quantidades na sua fatura mais recente.

Para obter uma boa aproximação do seu consumo de dados e padrão de acesso, recomendamos que escolha um período de retenção para as métricas que seja representativo da sua utilização normal e o utilize para tirar conclusões. Uma opção é manter os dados das métricas durante sete dias e recolher os dados todas as semanas, para análise no fim do mês. Outra opção é manter os dados das métricas para os últimos 30 dias e recolher e analisar os dados no final desse período de 30 dias.

Para obter mais detalhes sobre a ativação, recolha e visualização de dados de métricas, consulte [as métricas](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)de análise de armazenamento.

> [!NOTE]
> O armazenamento, o acesso e a transferência de dados de análise são cobrados também como dados normais do utilizador.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilizar métricas de utilização para estimar os custos

#### <a name="capacity-costs"></a>Custos de capacidade

A entrada mais recente na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'data'* mostra a capacidade de armazenamento consumida pelos dados do utilizador. A entrada mais recente na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'analytics'* mostra a capacidade de armazenamento consumida pelos registos de análise.

Esta capacidade total consumida pelos dados de utilizador e pelos registos de análise (se ativados) pode depois ser utilizada para estimar o custo do armazenamento dos dados na conta de armazenamento. O mesmo método também pode ser utilizado para estimar os custos de armazenamento nas contas GPv1.

#### <a name="transaction-costs"></a>Custos de transação

A soma dos *'TotalBillableRequests'*, em todas as entradas de uma API na tabela de métricas de transação indica o número total de transações para essa API específica. *Por exemplo,*, o número total de transações *'GetBlob'* num determinado período pode ser calculado pela soma do total de pedidos faturáveis para todas as entradas com a chave de linha *'user;GetBlob'*.

Para que possa estimar os custos de transação para as contas do Armazenamento de blobs, tem de dividir as transações em três grupos, uma vez que têm um preço diferente.

* Transações de escrita como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* e *'CopyBlob'*.
* Transações de eliminação como *'DeleteBlob'* e *'DeleteContainer'*.
* Todas as outras transações.

Para estimar os custos de transação nas contas de armazenamento GPv1, tem de agregar todas as transações, independentemente da operação/API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Custos de acesso a dados e custos de transferência de dados de georreplicação

Embora a análise de armazenamento não forneça a quantidade de dados lidos e escritos numa conta de armazenamento, esta pode ser aproximadamente estimada ao consultar a tabela de métricas de transação. A soma de *'TotalIngress'* de todas as entradas de uma API na tabela de métricas de transação indica a quantidade total de dados de entrada em bytes para essa API específica. Do mesmo modo, a soma de *'TotalEgress'* indica a quantidade total de dados de saída, em bytes.

Para que possa estimar os custos de acesso a dados para as contas do Armazenamento de blobs, precisa de dividir as transações em dois grupos.

* A quantidade de dados obtidos a partir da conta de armazenamento pode ser estimada ao ver a soma de *'TotalEgress'* para, principalmente, as operações *'GetBlob'* e *'CopyBlob'*.

* A quantidade de dados escritos na conta de armazenamento pode ser estimada ao ver a soma de *'TotalIngress'* para, principalmente, as operações *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'*.

Também pode ser calculado o custo de transferência de dados de georreplicação para contas do Armazenamento de blobs mediante a utilização da estimativa da quantidade de dados escritos se for utilizada uma conta de armazenamento GRS ou RA-GRS.

> [!NOTE]
> Para um exemplo mais detalhado sobre o cálculo dos custos para a utilização do nível de acesso ao armazenamento quente ou fresco, dê uma olhada nas FAQ intituladas "O que são os níveis de *acesso Hot and Cool e como devo determinar qual deve usar?".* na [Página de Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta de armazenamento](storage-account-create.md)
