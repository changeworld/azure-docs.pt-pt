---
title: Compreenda como funciona o processo de migração automática para os seus alertas clássicos do Monitor Azure
description: Saiba como funciona o processo de migração automática.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668252"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Compreenda o processo de migração automática para as suas regras clássicas de alerta

Como [já foi anunciado,](monitoring-classic-retirement.md)os alertas clássicos no Azure Monitor estão a ser retirados em setembro de 2019 (foi originalmente julho de 2019). No âmbito do processo de reforma, está disponível no portal Azure [uma ferramenta de migração](alerts-using-migration-tool.md) para os clientes desencadearem a migração. Se não usou a ferramenta de migração até 31 de agosto de 2019, o Azure Monitor iniciará o processo de migração automática dos seus alertas clássicos a partir de 1 de setembro de 2019.
Este artigo acompanha-o através do processo de migração automática e ajuda-o a resolver quaisquer problemas que possa encontrar.

  > [!NOTE]
  > Este artigo só se aplica à nuvem pública de Azure. O processo de aposentação dos alertas clássicos do Azure Monitor na nuvem do Governo de Azure e na Azure China 21Vianet será anunciado na data futura.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>O que vai acontecer durante o processo de migração automática?

- A partir de 1 de setembro de **2019,** os clientes não poderão criar novas regras clássicas de alerta, exceto em [determinadas métricas.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)
  - Para as exceções, o cliente pode continuar a criar novas regras clássicas de alerta e usar os seus alertas clássicos até junho de 2020.
- A partir de 1 de setembro de **2019,** a migração de alertas clássicos será desencadeada em lotes para qualquer cliente que tenha alertas clássicos.
- Tal como acontece com a ferramenta de migração voluntária, algumas regras clássicas de alerta que não são migratórias serão deixadas como estão. Estas regras clássicas de alerta continuarão a ser apoiadas até junho de 2020. No entanto, quaisquer regras de alerta clássicas inválidas serão eliminadas por não serem funcionais.
Quaisquer regras clássicas de alerta que estejam a monitorizar os recursos-alvo eliminados ou em [métricas que já não são suportadas](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) são consideradas inválidas.
- Uma vez iniciada a migração para a sua subscrição, a menos que haja algum problema, a migração deve estar completa dentro de uma hora. Os clientes podem monitorizar o estado da migração na lâmina de [migração no Monitor Azure](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Os proprietários de subscrições receberão um e-mail sobre a conclusão bem sucedida da migração.
- Se houver algum problema durante a migração, os proprietários de subscrições também receberão um e-mail informando-os do mesmo. Os clientes podem usar a lâmina de migração para ver todos os detalhes do problema.
- Caso seja necessária uma ação por parte do cliente, como desativar temporariamente um bloqueio de recursos ou alterar uma atribuição de políticas, os clientes terão de resolver quaisquer problemas até 31 de outubro de 2019. Se os problemas não forem resolvidos até lá, a migração bem sucedida dos seus alertas clássicos não pode ser garantida.

    > [!NOTE]
    > Se não quiser esperar pelo início do processo de migração automática, ainda pode desencadear a migração usando a ferramenta de migração.

## <a name="important-things-to-note"></a>Coisas importantes a notar

O processo de migração converte regras clássicas de alerta para novas regras de alerta equivalentes e cria grupos de ação. Em preparação, esteja atento aos seguintes pontos:

- Os formatos de carga de notificação para novas regras de alerta são diferentes dos das regras clássicas de alerta porque suportam mais funcionalidades. Se tiver aplicações lógicas, livros de execução ou webhooks que são desencadeados pela regra clássica de alerta, podem parar de funcionar como esperado uma vez que a migração esteja completa devido a diferenças nas cargas de notificação. [Aprenda a preparar-se para a migração.](alerts-prepare-migration.md)

- Algumas regras clássicas de alerta não podem ser migradas usando a ferramenta. [Saiba quais as regras que não podem ser migradas e o que fazer com elas.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > O processo de migração não terá impacto na avaliação das suas regras clássicas de alerta. Continuarão a correr e a enviar alertas até que sejam migrados e as novas regras de alerta entrem em vigor.

## <a name="what-if-the-automatic-migration-fails"></a>E se a migração automática falhar?

Quando o processo de migração automática falhar, os proprietários de subscrições receberão um e-mail notificando-os do problema. Pode utilizar a lâmina de migração no Monitor Azure para ver todos os detalhes do problema.

Consulte o guia de resolução de [problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que poderá enfrentar durante a migração.

  > [!NOTE]
  > Caso seja necessária uma ação por parte do cliente, como desativar temporariamente um bloqueio de recursos ou alterar uma atribuição de políticas, os clientes terão de resolver quaisquer problemas até 31 de outubro de 2019. Se as questões não forem resolvidas até lá, a migração bem sucedida dos seus alertas clássicos não pode ser garantida.

## <a name="next-steps"></a>Passos seguintes

- [Preparar para a migração](alerts-prepare-migration.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)
