---
title: Compreender métricas para nuvem de primavera azure
description: Saiba como rever as métricas em Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256760"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Compreenda as métricas para a Nuvem de primavera Azure

O explorador de Métricas Azure é um componente do portal Microsoft Azure que permite traçar gráficos, correlacionar visualmente tendências e investigar picos e mergulhos nas métricas. Use o explorador de métricas para investigar a saúde e utilização dos seus recursos. 

Na Nuvem de primavera de Azure, existem dois pontos de vista para métricas.
* Gráficos em cada página de visão geral de cada aplicação
* Página de métricas comuns

 ![Gráficos de Métricas](media/metrics/metrics-1.png)

Os gráficos na **visão geral** da aplicação fornecem verificações rápidas de estado para cada aplicação. A **página** métrica comum contém todas as métricas disponíveis para referência. Pode construir os seus próprios gráficos na página de métricas comuns e fixá-los no Dashboard.

## <a name="application-overview-page"></a>Página de visão geral da aplicação
Selecione uma aplicação em Gestão de **Aplicações** para encontrar gráficos na página geral.  

 ![Gestão de Métricas de Aplicação](media/metrics/metrics-2.png)

A página de visão geral de **cada aplicação** apresenta um gráfico de métricas que lhe permite realizar uma verificação rápida do estado da sua aplicação.  

 ![Visão geral das métricas de aplicação](media/metrics/metrics-3.png)

A Nuvem de primavera Azure fornece estes cinco gráficos com métricas que são atualizadas a cada minuto:

* **Erros do Servidor http**: Contagem de erros para pedidos HTTP para a sua aplicação
* **Data In**: Bytes recebidos pela sua app
* **Data out**: Bytes enviados pela sua app
* **Pedidos**: Pedidos recebidos pela sua app
* **Tempo médio de resposta**: Tempo médio de resposta da sua aplicação

Para a tabela, pode selecionar um intervalo de tempo de uma hora a sete dias.

## <a name="common-metrics-page"></a>Página de métricas comuns

As **Métricas** no painel de navegação esquerdo ligam-se à página de métricas comuns.

Primeiro, selecione métricas para ver:

![Selecione A Visão Métrica](media/metrics/metrics-4.png)

Detalhes de todas as opções métricas podem ser encontrados na [secção](#user-metrics-options) abaixo.

Em seguida, selecione o tipo de agregação para cada métrica:

![Agregação Métrica](media/metrics/metrics-5.png)

O tipo de agregação indica como agregar pontos métricos no gráfico pelo tempo. Há um ponto métrico bruto a cada minuto, e o tipo de pré-agregação em um minuto é pré-definido pelo tipo de métricas.
* Soma: Soma todos os valores como saída de alvo.
* Média: Utilize o valor médio no período como saída-alvo.
* Max/Min: Utilize o valor Max/Min no período como saída de destino.

O intervalo de tempo para mostrar também pode ser modificado. O intervalo de tempo pode ser escolhido de 30 minutos para durar 30 dias, ou um intervalo de tempo personalizado.

![Modificação Métrica](media/metrics/metrics-6.png)

A vista padrão inclui todas as métricas da aplicação do serviço Azure Spring Cloud em conjunto. As métricas de uma aplicação ou instância podem ser filtradas no visor.  Clique em **Adicionar filtro,** definir a propriedade para **App**, e selecione a aplicação-alvo que pretende monitorizar na caixa de texto **Values.** 

Pode utilizar dois tipos de filtros (propriedades):
* App: filtro por nome de app
* Caso: filtrar por instância de aplicação

![Filtros Métricos](media/metrics/metrics-7.png)

Também pode utilizar a opção **de divisão Apply,** que irá desenhar várias linhas para uma aplicação:

![Divisão Métrica](media/metrics/metrics-8.png)

>[!TIP]
> Pode construir os seus próprios gráficos na página das métricas e fixá-los no seu **Dashboard**. Comece por nomear a sua ficha.  Em seguida, selecione **Pin para painel no canto superior direito**. Agora pode verificar a sua candidatura no portal **dashboard**.

## <a name="user-metrics-options"></a>Opções de métricas de utilizador

As tabelas que se seguem mostram as métricas e detalhes disponíveis.

### <a name="error"></a>Erro
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| Erro Global tomcat | tomcat.global.error | Contagem | Número de erros ocorre de pedidos processados |

### <a name="performance"></a>Desempenho
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>|Percentagem de utilização do CPU do sistema | sistema.cpu.usage | Percentagem | Utilização recente do CPU para todo o sistema. Este valor é um duplo no intervalo [0.0,1.0]. Um valor de 0,0 significa que todos os CPUs ficaram inativos durante o período de tempo recente observado, enquanto um valor de 1.0 significa que todos os CPUs estavam ativamente a funcionar 100% do tempo durante o período recente a ser observado.|
>| Percentagem de utilização do CPU da aplicação | Percentagem de utilização do CPU da aplicação | Percentagem | Utilização recente do CPU para o processo da Máquina Virtual Java. Este valor é um duplo no intervalo [0.0,1.0]. Um valor de 0.0 significa que nenhum dos CPUs estava a executar fios do processo JVM durante o período de tempo recente observado, enquanto um valor de 1.0 significa que todos os CPUs estavam a executar ativamente fios a partir do JVM 100% do tempo durante o período recente a ser observado. Os fios do JVM incluem os fios de aplicação, bem como os fios internos JVM.|
>| Memória de aplicativo atribuída | jvm.memory.comprometido | Bytes | Representa a quantidade de memória que é garantida para ser utilizada pelo JVM. O JVM pode libertar memória para o sistema e comprometido pode ser menor do que o init. comprometido será sempre maior ou igual a usado. |
>| Memória de aplicativo usada | jvm.memory.used | Bytes | Representa a quantidade de memória atualmente utilizada em bytes. |
>| Memória de aplicativo Max | jvm.memory.max | Bytes | Representa a quantidade máxima de memória que pode ser usada para a gestão da memória. A quantidade de memória usada e comprometida será sempre inferior ou igual a máx se for definida no máximo. Uma alocação de memória pode falhar se tentar aumentar a memória usada de tal forma que a utilizada > cometida mesmo que seja usada <= max ainda seria verdade (por exemplo, quando o sistema está baixo na memória virtual). |
>| Tamanho de dados de geração antiga disponível Max | jvm.gc.max.data.size | Bytes | O uso máximo da memória da antiga geração de memória desde que a máquina virtual java foi iniciada. |
>| Tamanho dos dados da geração antiga | jvm.gc.live.data.size | Bytes | Tamanho da piscina de memória de geração antiga depois de um GC completo. |
>| Promover o tamanho dos dados da geração antiga | jvm.gc.memory.promovido | Bytes | Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes de GC para depois de GC. |
>| Promover para o tamanho de dados de geração jovem | jvm.gc.memory.allocated | Bytes | Incrementado para um aumento do tamanho do pool de memória de geração jovem após um GC para antes do próximo. |
>| Contagem de pausas GC | jvm.gc.pause (contagem total) | Contagem | Contagem total de GC após este JMV começou, incluindo Young e Old GC. |
>| GC Pausa Tempo Total | jvm.gc.pause (tempo total) | Milissegundos | O tempo total de GC consumido após este JMV começou, incluindo Young e Old GC. |

### <a name="request"></a>Pedir
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| Tomcat Total Bytes Enviados | tomcat.global.enviado | Bytes | Quantidade de dados Enviados servidor web Tomcat |
>| Tomcat Total Recebido Bytes | tomcat.global.received | Bytes | Quantidade de dados Que o servidor web tomcat recebeu |
>| Tomcat Request Total Time | tomcat.global.request (tempo total) | Milissegundos | Tempo total do servidor web tomcat para processar os pedidos |
>| Tomcat Request Contagem Total | tomcat.global.request (contagem total) | Contagem | Contagem total de pedidos processados do servidor web tomcat |
>| Tomcat Request Max Time | tomcat.global.request.max | Milissegundos | Tempo máximo do servidor web tomcat para processar um pedido |

### <a name="session"></a>Sessão
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| Tomcat Session Max Ative Count | tomcat.sessions.ative.max | Contagem | Número máximo de sessões que estiveram ativas ao mesmo tempo |
>| Tomcat Session Max Alive Time | tomcat.sessions.alive.max | Milissegundos | Mais tempo (em segundos) que uma sessão expirada tinha sido viva |
>| Tomcat Session Criou a Contagem | tomcat.sessions.created | Contagem | Número de sessões que foram criadas |
>| Contagem expirada da sessão de Tomcat | tomcat.sessions.expirou | Contagem | Número de sessões que expiraram |
>| Tomcat Session Rejeitou contagem | tomcat.sessions.rejeitado | Contagem | Número de sessões que não foram criadas porque o número máximo de sessões ativas chegou. |

## <a name="see-also"></a>Consulte também
* [Getting started with Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) (Introdução ao Explorador de Métricas do Azure)

* [Analisar registos e métricas com definições de diagnóstico](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Passos seguintes
* [Tutorial: Monitor ize os recursos da Nuvem de primavera utilizando alertas e grupos de ação](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Quotas e Planos de Serviço para nuvem de primavera azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

