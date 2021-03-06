---
title: FaQ de saúde de recursos azure
description: Visão geral da Saúde dos Recursos Azure
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 7459a29dca01dc186d75b4545f89068569975607
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985036"
---
# <a name="azure-resource-health-faq"></a>FaQ de saúde de recursos azure
Aprenda as respostas a perguntas comuns sobre a Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>O que é o Azure Resource Health?
O estado de funcionamento dos recursos ajuda-o a diagnosticar e a obter suporte quando um problema do Azure afeta os seus recursos. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>Para que se destina a Saúde dos Recursos?
Uma vez detetado um problema com um recurso, a Saúde de Recursos pode ajudá-lo a diagnosticar a causa principal. Ele fornece ajuda para mitigar a questão e apoio técnico quando você precisa de mais ajuda com questões de serviço Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Que controlos de saúde são realizados pela Resource Health?
A saúde dos recursos realiza vários controlos com base no tipo de [recurso](resource-health-checks-resource-types.md). Estes controlos destinam-se a implementar três tipos de questões: 
- Eventos não planeados, por exemplo, um reboot inesperado do hospedeiro
- Eventos planeados, como atualizações programadas para o sistema operativo
- Eventos desencadeados pelas ações do utilizador, por exemplo, um utilizador que reinicia uma máquina virtual

## <a name="what-does-each-of-the-health-status-mean"></a>O que significa cada um do estado de saúde?
Existem três estados de saúde diferentes:
- Disponível: Não existem problemas conhecidos na plataforma Azure que possam estar a ter impacto neste recurso
- Indisponível: A saúde dos recursos detetou problemas que estão a afetar o recurso
- Desconhecido: A saúde dos recursos não pode determinar a saúde de um recurso porque deixou de receber informações sobre o mesmo. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>O que significa o estatuto desconhecido? Há algo de errado com o meu recurso?
O estado de saúde é definido para desconhecido quando a Saúde dos Recursos deixa de receber informações sobre um recurso específico. Embora este estatuto não seja uma indicação definitiva do estado do recurso, nos casos em que se está a passar por problemas, pode indicar que existe um problema azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Como posso obter ajuda para um recurso que não está disponível?
Pode submeter um pedido de apoio da lâmina de saúde de recursos. Não precisa de um acordo de suporte com a Microsoft para abrir um pedido quando o recurso não está disponível porque os eventos da plataforma.

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>A Saúde dos Recursos diferencia entre indisponibilidade causada por problemas de plataforma versus algo que fiz?
Sim, quando um recurso está indisponível, a Resource Health identifica a causa principal dentro de uma destas categorias: 
-   Ação iniciada pelo utilizador
-   Evento planeado 
-   Evento não planeado

No portal, as ações iniciadas pelo utilizador são mostradas usando um ícone de notificação azul, enquanto eventos planeados e não planeados são mostrados usando um ícone de aviso vermelho. Mais detalhes são fornecidos na visão geral da Saúde dos [Recursos.](Resource-health-overview.md)  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Posso integrar a Saúde de Recursos com as minhas ferramentas de monitorização?
A saúde dos recursos tem [suporte de pré-visualização](resource-health-alert-arm-template-guide.md) para alertas baseados em Registo de Atividade. Os alertas de registo de atividade usam [grupos](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) de ação para notificar os utilizadores de que foi desencadeado um alerta. Os grupos de ação suportam uma variedade de canais de notificação, tais como e-mail, SMS, webhook e ações ITSM.

## <a name="where-do-i-find-resource-health"></a>Onde encontro a Saúde dos Recursos?
Depois de iniciar sessão no portal Azure, existem várias formas de aceder à Saúde dos Recursos:
- Navegue para o seu recurso. Na navegação à esquerda, selecione **Saúde de Recursos**
- Vá à lâmina de saúde de serviço Azure.  Na navegação à esquerda, selecione **Saúde de Recursos**.
- Abra a lâmina **de ajuda + suporte** selecionando o ponto de interrogação no canto superior direito do portal e, em seguida, selecionando Ajuda + **Suporte**. Assim que a lâmina abrir, selecione **Saúde de Recursos**

Também pode utilizar a API de Saúde de Recursos para obter informações sobre a saúde dos seus recursos.

## <a name="is-resource-health-available-for-all-resource-types"></a>A Saúde de Recursos está disponível para todos os tipos de recursos?
A lista de verificações de saúde e tipos de recursos suportados através da Saúde dos Recursos pode ser consultada [aqui](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>O que devo fazer se o meu recurso estiver disponível, mas acredito que não está?"
Ao verificar a saúde de um recurso, mesmo sob o estado de saúde pode clicar no Relatório estado de **saúde incorreto**. Antes de apresentar o relatório, tem a opção de fornecer detalhes adicionais sobre o porquê de acreditar que o estado de saúde atual está incorreto.

## <a name="is-resource-health-available-for-all-azure-regions"></a>A Saúde de Recursos está disponível para todas as regiões do Azure? 
A saúde dos recursos está disponível em todos os geos Azure.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Como é que a Saúde dos Recursos é diferente do estado do Azure ou do dashboard service Health?
A informação fornecida pela Resource Health é mais específica do que a prestada pelo estado do Azure ou pelo painel de assistência ao serviço.

Enquanto o [estado do Azure](https://status.azure.com) e o dashboard Service Health o informam sobre questões de serviço que afetam um vasto conjunto de clientes (por exemplo, uma região azure), a Resource Health expõe eventos mais granulares que são relevantes apenas para o recurso específico. Por exemplo, se um hospedeiro reiniciar inesperadamente, a Resource Health alerta apenas os clientes cujas máquinas virtuais estavam a funcionar naquele hospedeiro.

É importante notar que para lhe proporcionar uma visibilidade completa de eventos com impacto nos seus recursos, a Resource Health também surge em eventos publicados no dashboard Service Health.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Preciso ativar a Saúde de Recursos para cada recurso?
Não, informações de saúde estão disponíveis para todos os tipos de recursos disponíveis através da Saúde de Recursos. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Precisamos de ativar a Saúde dos Recursos para a minha organização?
Não.  A Azure Resource Health está acessível dentro do portal Azure sem quaisquer requisitos de configuração.

## <a name="is-resource-health-available-free-of-charge"></a>A Saúde de Recursos está disponível gratuitamente?
Sim.  A Azure Resource Health é gratuita.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Quais são as recomendações que a Saúde dos Recursos fornece?
Com base no estado de saúde, a Resource Health fornece-lhe recomendações com o objetivo de reduzir o tempo que passou a resolução de problemas. Para os recursos disponíveis, as recomendações focam-se na forma de resolver os problemas mais comuns que os clientes encontram. Se o recurso não estiver disponível devido a um evento não planeado do Azure, o foco será ajudá-lo durante e após o processo de recuperação. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Saúde dos Recursos:
-  [Visão geral da Saúde dos Recursos Azure](Resource-health-overview.md)
-  [Os tipos de recursos e verificações do estado de funcionamento disponíveis através do Azure Resource Health](resource-health-checks-resource-types.md)
