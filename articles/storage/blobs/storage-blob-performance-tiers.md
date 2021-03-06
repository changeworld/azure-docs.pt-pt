---
title: Níveis de desempenho de armazenamento de blocos blob - Armazenamento Azure
description: Discute a diferença entre os níveis de desempenho premium e standard para o armazenamento de blob de bloco Sino Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270230"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Níveis de desempenho para armazenamento de blocos blob

À medida que as empresas implementam aplicações sensíveis ao desempenho, é importante ter opções para armazenamento de dados rentáveis em diferentes níveis de desempenho.

O armazenamento de blob de bloco azure oferece dois diferentes níveis de desempenho:

- **Premium**: otimizado para taxas de transação elevadas e latência de armazenamento consistente de um dígito
- **Padrão**: otimizado para alta capacidade e alta capacidade de entrada

As seguintes considerações aplicam-se aos diferentes níveis de desempenho:

| Área |Desempenho padrão  |Desempenho premium  |
|---------|---------|---------|
|Disponibilidade de região     |   Todas as regiões      | Em [regiões selecionadas](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Tipos de conta de [armazenamento](../common/storage-account-overview.md#types-of-storage-accounts) suportado     |     Finalidade geral v2, BlobStorage, propósito geral v1    |    BlockBlobStorage     |
|Suporta bolhas de [bloco de alta sueste de alta suver](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Sim, com mais de 4 tamanhos MiB PutBlock ou PutBlob     |    Sim, com mais de 256 tamanhos KiB PutBlock ou PutBlob    |
|Redundância     |     Ver [Tipos de contas de armazenamento](../common/storage-account-overview.md#types-of-storage-accounts)   |  Atualmente suporta apenas armazenamento redundante localmente (LRS) e armazenamento de zona-redudante (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> O armazenamento redundante em zonas (ZRS) está disponível em regiões selecionadas para contas de armazenamento de blocos de desempenho premium.</div>

No que diz respeito ao custo, o desempenho premium fornece preços otimizados para aplicações com altas taxas de transação para ajudar a [reduzir o custo total](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) de armazenamento destas cargas de trabalho.

## <a name="premium-performance"></a>Desempenho premium

O armazenamento de blob de bloco de desempenho premium disponibiliza dados através de hardware de alto desempenho. Os dados são armazenados em unidades de estado sólido (SSDs) que são otimizadas para baixa latência. Os SSDs fornecem uma maior entrada em comparação com os discos rígidos tradicionais.

O armazenamento de desempenho premium é ideal para cargas de trabalho que requerem tempos de resposta rápidos e consistentes. É melhor para cargas de trabalho que realizam muitas pequenas transações. As cargas de trabalho de exemplo incluem:

- **Cargas de trabalho interativas.** Estas cargas de trabalho requerem atualizações instantâneas e feedback do utilizador, tais como e-commerce e aplicações de mapeamento. Por exemplo, numa aplicação de e-commerce, os itens menos visualizados provavelmente não são cached. No entanto, devem ser imediatamente apresentados ao cliente a pedido.

- **Analíticos.** Num cenário de IoT, muitas operações de escrita mais pequenas podem ser empurradas para a nuvem a cada segundo. Grandes quantidades de dados podem ser recolhidas, agregadas para fins de análise, e depois eliminadas quase imediatamente. As elevadas capacidades de ingestão do armazenamento de blocos premium tornam-no eficiente para este tipo de carga de trabalho.

- **Inteligência artificial/aprendizagem automática (IA/ML)**. A IA/ML trata do consumo e processamento de diferentes tipos de dados, como visuais, fala e texto. Este tipo de carga de trabalho de alta performance lida com grandes quantidades de dados que requerem uma resposta rápida e tempos de ingestão eficientes para a análise de dados.

- **Transformação de dados.** Os processos que requerem edição, modificação e conversão constantede dados requerem atualizações instantâneas. Para uma representação precisa dos dados, os consumidores destes dados devem ver estas alterações refletidas imediatamente.

## <a name="standard-performance"></a>Desempenho padrão

O desempenho padrão suporta [diferentes níveis](storage-blob-storage-tiers.md) de acesso para armazenar dados da forma mais rentável. É otimizado para alta capacidade e alta entrada em grandes conjuntos de dados.

- Conjuntos de dados de backup e recuperação de **desastres.** O armazenamento de desempenho padrão oferece níveis rentáveis, tornando-o um caso de utilização perfeito para conjuntos de dados de recuperação de desastres a curto e longo prazo, backups secundários e arquivo de dados de conformidade.

- **Conteúdo mediático**. As imagens e vídeos são frequentemente acedidos com frequência quando são criados e armazenados, mas este tipo de conteúdo é usado menos frequentemente à medida que envelhece. O armazenamento de desempenho padrão oferece níveis adequados para as necessidades de conteúdo dos media. 

- **Processamento de dados a granel.** Este tipo de cargas de trabalho são adequadas para armazenamento padrão porque requerem armazenamento de alto-desempenho rentável em vez de latência consistente e baixa. Conjuntos de dados grandes e crus são encenados para processamento e eventualmente migram para níveis mais frios.

## <a name="migrate-from-standard-to-premium"></a>Migrar de padrão para premium

Não é possível converter uma conta de armazenamento padrão existente numa conta de armazenamento de blocos com desempenho premium. Para migrar para uma conta de armazenamento de desempenho premium, você deve criar uma conta BlockBlobStorage, e migrar os dados para a nova conta. Para mais informações, consulte [Criar uma conta BlockBlobStorage](storage-blob-create-account-block-blob.md).

Para copiar bolhas entre contas de armazenamento, pode utilizar a versão mais recente da ferramenta de linha de comando [AzCopy.](../common/storage-use-azcopy-blobs.md) Outras ferramentas, como a Azure Data Factory, também estão disponíveis para o movimento e transformação de dados.

## <a name="blob-lifecycle-management"></a>Gestão de ciclo de vida blob

A gestão do ciclo de vida do armazenamento blob oferece uma política rica e baseada em regras:

- **Premium**: Expirar os dados no final do seu ciclo de vida.
- **Standard**: Dados de transição para o melhor nível de acesso e dados caducados no final do seu ciclo de vida.

Para saber mais, consulte Gerir o ciclo de [vida de armazenamento de Blob Azure](storage-lifecycle-management-concepts.md).

Não é possível mover dados armazenados numa conta de armazenamento de blocos premium entre níveis quentes, frescos e de arquivo. No entanto, pode copiar bolhas de uma conta de armazenamento de blocos blob para o nível de acesso quente numa conta *diferente.* Para copiar dados para uma conta diferente, utilize o [Bloco de Colocação de URL](/rest/api/storageservices/put-block-from-url) API ou [AzCopy v10](../common/storage-use-azcopy-v10.md). O **bloco de colocação de URL** API copia sincronizadamente os dados no servidor. A chamada só termina depois de todos os dados ser movidos da localização original do servidor para o local do destino.

## <a name="next-steps"></a>Passos seguintes

Avalie as contas de armazenamento quentes, frescas e de arquivo sinuosas e blob.

- [Saiba mais sobre a reidratação de dados blob do nível de arquivo](storage-blob-rehydration.md)
- [Avaliar a utilização das suas contas do Storage atuais ao ativar as métricas do Storage do Azure](../common/storage-enable-and-view-metrics.md)
- [Verifique preços quentes, frescos e de arquivo no armazenamento blob e contas GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
- [Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
