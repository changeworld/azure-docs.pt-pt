---
title: Visão geral da Saúde dos Recursos Azure
description: Visão geral da Saúde dos Recursos Azure
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 7a1dfe5e93d0e19aeb343d113a24ed882a5b3f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159256"
---
# <a name="resource-health-overview"></a>Visão geral da Saúde dos Recursos
 
A Azure Resource Health ajuda-o a diagnosticar e a obter apoio para problemas de serviço que afetam os seus recursos Azure. Informa a saúde atual e passada dos seus recursos.

[O estado do Azure](https://status.azure.com) reporta problemas de serviço que afetam um vasto conjunto de clientes Azure. A Resource Health dá-lhe um dashboard personalizado da saúde dos seus recursos. A Resource Health mostra todas as vezes que os seus recursos estiveram indisponíveis devido a problemas de serviço azure. Estes dados facilitam-lhe a visualização de um SLA.

## <a name="resource-definition-and-health-assessment"></a>Definição de recursos e avaliação da saúde

Um *recurso* é uma instância específica de um serviço Azure, como uma máquina virtual, web app ou base de dados SQL. A Resource Health baseia-se em sinais de diferentes serviços Azure para avaliar se um recurso é saudável. Se um recurso não for saudável, a Resource Health analisa informações adicionais para determinar a origem do problema. Também relata as ações que a Microsoft está a tomar para corrigir o problema e identifica coisas que pode fazer para o resolver.

Para obter mais informações sobre a forma como a saúde é avaliada, consulte a lista de tipos de recursos e verificações de saúde na [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Estado de funcionamento

A saúde de um recurso é apresentada como um dos seguintes estados.

### <a name="available"></a>Disponível

*A disposição* significa que não são detetados eventos que afetem a saúde do recurso. Nos casos em que o recurso recuperou do tempo de paragem não planeado durante as últimas 24 horas, verá uma notificação "recentemente resolvida".

![Estado de *Disponível* para uma máquina virtual que tenha uma notificação "recentemente resolvida"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Indisponível

*Indisponível* significa que o serviço detetou uma plataforma em curso ou evento não plataforma que afeta a saúde do recurso.

#### <a name="platform-events"></a>Eventos de plataforma

Os eventos da plataforma são desencadeados por vários componentes da infraestrutura Azure. Incluem ambas as ações programadas (por exemplo, manutenção planeada) e incidentes inesperados (por exemplo, um reboot não planeado do hospedeiro ou hardware de anfitrião degradado que se prevê que falhe após uma janela de tempo especificada).

A Resource Health fornece detalhes adicionais sobre o evento e o processo de recuperação. Também permite contactar o Microsoft Support mesmo que não tenha um acordo de suporte ativo.

![Estado de *Indisponível* para uma máquina virtual por causa de um evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos não-plataforma

Os eventos não-plataforma são desencadeados por ações do utilizador. Exemplos incluem parar uma máquina virtual ou atingir o número máximo de ligações a Azure Cache para Redis.

![Estado de "Indisponível" para uma máquina virtual por causa de um evento sem plataforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Desconhecido

*Desconhecido* significa que a Saúde de Recursos não recebe informações sobre o recurso há mais de 10 minutos. Embora este estado não seja uma indicação definitiva do estado do recurso, é um importante ponto de dados para resolução de problemas.

Se o recurso estiver a funcionar como esperado, o estado do recurso mudará para *Disponível* após alguns minutos.

Se tiver problemas com o recurso, o estado de saúde *desconhecido* pode significar que um evento na plataforma está a afetar o recurso.

![Estado de *Desconhecido* para uma máquina virtual](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degradado

*Degradado* significa que o seu recurso detetou uma perda de desempenho, embora ainda esteja disponível para uso.

Os diferentes recursos têm os seus próprios critérios para quando reportam que estão degradados.

![Estado de *Degradado* para uma máquina virtual](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Reportar um estado incorreto

Se achar que o estado de saúde atual está incorreto, pode dizer-nos selecionando o Estado de **saúde incorreto do Relatório**. Nos casos em que um problema do Azure o está a afetar, encorajamo-lo a contactar o Apoio da Saúde de Recursos.

![Formulário para submeter informações sobre um estado incorreto](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Informação de história

Pode aceder até 30 dias de história na secção de História da **Saúde** da Saúde.

![Lista de eventos de saúde de recursos nas últimas duas semanas](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Introdução

Para abrir a Saúde dos Recursos para um recurso:

1. Inicie sessão no Portal do Azure.
2. Navegue até ao recurso.
3. No menu de recursos no painel esquerdo, selecione **Saúde de Recursos**.

![Abertura da Saúde dos Recursos a partir da vista de recursos](./media/resource-health-overview/from-resource-blade.png)

Também pode aceder à Saúde dos Recursos selecionando **todos os serviços** e digitando a **saúde** dos recursos na caixa de texto do filtro. No painel **de suporte Ajuda +,** selecione [Saúde de Recursos](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Abertura da Saúde dos Recursos de "Todos os serviços"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Passos seguintes

Confira estas referências para saber mais sobre a Saúde dos Recursos:
-  [Tipos de recursos e verificações de saúde na Saúde dos Recursos Azure](resource-health-checks-resource-types.md)
-  [Perguntas frequentes sobre a Saúde dos Recursos Azure](resource-health-faq.md)
