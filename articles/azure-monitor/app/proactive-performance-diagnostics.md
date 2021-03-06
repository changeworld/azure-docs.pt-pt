---
title: Deteção Inteligente - anomalias de desempenho [ anomalias de desempenho ] Microsoft Docs
description: Application Insights realiza uma análise inteligente da sua telemetria de aplicações e alerta-o para potenciais problemas. Esta funcionalidade não necessita de configuração.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: 3d8de08605d3dd693eb74a84a29c2efa6cad669a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671737"
---
# <a name="smart-detection---performance-anomalies"></a>Deteção Inteligente - Anomalias de Desempenho

[Application Insights](../../azure-monitor/app/app-insights-overview.md) analisa automaticamente o desempenho da sua aplicação web, e pode avisá-lo sobre potenciais problemas. Pode estar a ler isto porque recebeu uma das nossas notificações inteligentes de deteção.

Esta funcionalidade não requer configuração especial, a não ser configurar a sua aplicação para Insights de Aplicação (em [ASP.NET,](../../azure-monitor/app/asp-net.md) [Java](../../azure-monitor/app/java-get-started.md), ou [Node.js](../../azure-monitor/app/nodejs.md), e no código da [página web).](../../azure-monitor/app/javascript.md) Está ativo quando a sua aplicação gera telemetria suficiente.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Quando é que recebia uma notificação de deteção inteligente?

A Application Insights detetou que o desempenho da sua aplicação se degradou de uma destas formas:

* **Degradação do tempo** de resposta - A sua aplicação começou a responder a pedidos mais lentamente do que costumava. A mudança pode ter sido rápida, por exemplo, porque houve uma regressão na sua última implantação. Ou pode ter sido gradual, talvez causado por uma fuga de memória. 
* **Degradação** da duração da dependência - A sua aplicação faz chamadas para uma API REST, base de dados ou outra dependência. A dependência está a responder mais lentamente do que antes.
* **Padrão** de desempenho lento - A sua aplicação parece ter um problema de desempenho que está a afetar apenas alguns pedidos. Por exemplo, as páginas estão a carregar mais lentamente num tipo de navegador do que noutros; ou pedidos estão sendo servidos mais lentamente a partir de um servidor em particular. Atualmente, os nossos algoritmos olham para os tempos de carregamento de página, tempos de resposta de pedidos e tempos de resposta à dependência.  

A Deteção Inteligente requer pelo menos 8 dias de telemetria num volume viável, a fim de estabelecer uma linha de base de desempenho normal. Assim, depois de a sua candidatura estar em execução durante esse período, qualquer problema significativo resultará numa notificação.


## <a name="does-my-app-definitely-have-a-problem"></a>A minha aplicação tem um problema?

Não, uma notificação não significa que a sua aplicação definitivamente tenha um problema. É simplesmente uma sugestão sobre algo que pode querer examinar mais detalhadamente.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?

As notificações incluem informações de diagnóstico. Segue-se um exemplo:


![Aqui está um exemplo de deteção de degradação do tempo de resposta do servidor](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Triagem.** A notificação mostra quantos utilizadores ou quantas operações são afetadas. Isto pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Âmbito**. O problema está a afetar todo o tráfego, ou apenas algumas páginas? Está restrito a navegadores ou locais específicos? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar**. Muitas vezes, as informações de diagnóstico na notificação sugerem a natureza do problema. Por exemplo, se o tempo de resposta abrandar quando a taxa de pedido é elevada, isso sugere que o seu servidor ou dependências estão sobrecarregados. 

    Caso contrário, abra a lâmina de desempenho em Insights de Aplicação. Aí, encontrará dados do [Profiler.](profiler.md) Se forem lançadas exceções, também pode experimentar o [desfoque](../../azure-monitor/app/snapshot-debugger.md)instantâneo.



## <a name="configure-email-notifications"></a>Configure notificações de e-mail

As notificações de Deteção Inteligente são ativadas por padrão e enviadas para aqueles que têm acesso ao Leitor de [Monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) e [ao Colaborador de Monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) à subscrição em que reside o recurso Application Insights. Para alterar isto, clique em **Configurar** na notificação de e-mail ou abra as definições de Deteção Inteligente em Insights de Aplicação. 
  
  ![Definições de deteção inteligente](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Pode utilizar o link de cancelamento de **subscrição** no e-mail de Deteção Inteligente para deixar de receber as notificações de e-mail.

Os e-mails sobre anomalias de desempenho de Deteções Inteligentes estão limitados a um e-mail por dia por recurso Application Insights. O e-mail só será enviado se houver pelo menos uma nova emissão que tenha sido detetada nesse dia. Não vai saques de nenhuma mensagem. 

## <a name="faq"></a>FAQ

* *Então, os funcionários da Microsoft olham para os meus dados?*
  * Não. O serviço é totalmente automático. Só recebeas as notificações. Os seus dados são [privados.](../../azure-monitor/app/data-retention-privacy.md)
* *Analisa todos os dados recolhidos pela Application Insights?*
  * No momento, não. Atualmente, analisamos o tempo de resposta do pedido, o tempo de resposta da dependência e o tempo de carregamento da página. A análise de métricas adicionais está no nosso atraso olhando para a frente.

* Para que tipo de aplicação isto funciona?
  * Estas degradações são detetadas em qualquer aplicação que gere a telemetria apropriada. Se instalou Insights de Aplicação na sua aplicação web, então os pedidos e dependências são automaticamente rastreados. Mas em serviços de backend ou outras aplicações, se inseriu chamadas para [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) ou [TrackDependency,](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)então a Deteção Inteligente funcionará da mesma forma.

* *Posso criar as minhas próprias regras de deteção de anomalias ou personalizar as regras existentes?*

  * Ainda não, mas pode:
    * [Instale alertas](../../azure-monitor/app/alerts.md) que lhe digam quando uma métrica atravessa um limiar.
    * [Exportar telemetria](../../azure-monitor/app/export-telemetry.md) para uma [base de dados](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) ou para o [PowerBI,](../../azure-monitor/app/export-power-bi.md )onde pode analisá-la por si mesma.
* *Com que frequência é feita a análise?*

  * Executamos a análise diariamente sobre a telemetria do dia anterior (dia inteiro no fuso horário UTC).
* *Então isto substitui [os alertas métricos?](../../azure-monitor/app/alerts.md)*
  * Não.  Não nos comprometemos a detetar todos os comportamentos que considere anormais.


* *Se eu não fizer nada em resposta a uma notificação, vou receber um lembrete?*
  * Não, só recebes uma mensagem sobre cada assunto uma vez. Se o problema persistir, será atualizado na lâmina de alimentação smart Detection.
* *Perdi o e-mail. Onde posso encontrar as notificações no portal?*
  * Na visão geral da aplicação Insights da sua aplicação, clique no azulejo **smart Detection.** Lá poderá encontrar todas as notificações até 90 dias atrás.

## <a name="how-can-i-improve-performance"></a>Como posso melhorar o desempenho?
Respostas lentas e falhadas são uma das maiores frustrações para os utilizadores do site, como sabe pela sua própria experiência. Então, é importante abordar as questões.

### <a name="triage"></a>Triagem
Primeiro, importa? Se uma página é sempre lenta a carregar, mas apenas 1% dos utilizadores do seu site alguma vez têm de olhar para ela, talvez tenha coisas mais importantes para pensar. Por outro lado, se apenas 1% dos utilizadores a abrirem, mas abram sempre exceções, isso pode valer a pena investigar.

Utilize a declaração de impacto (utilizadores afetados ou % do tráfego) como guia geral, mas esteja ciente de que não é toda a história. Reúna outras provas para confirmar.

Considere os parâmetros da questão. Se for dependente da geografia, estabeleça testes de [disponibilidade,](../../azure-monitor/app/monitor-web-app-availability.md) incluindo aquela região: pode simplesmente haver problemas de rede nessa área.

### <a name="diagnose-slow-page-loads"></a>Diagnosticar cargas de página lenta
Onde está o problema? O servidor é lento para responder, a página é muito longa, ou o navegador tem que fazer muito trabalho para exibi-lo?

Abra a lâmina métrica do Navegador. O ecrã segmentado do tempo de carregamento da página do navegador mostra para onde vai a hora. 

* Se o tempo de **envio** for elevado, ou o servidor está a responder lentamente, ou o pedido é uma publicação com muitos dados. Olhe para as métricas de desempenho para investigar os [tempos](../../azure-monitor/app/web-monitor-performance.md#metrics) de resposta.
* Instale o [rastreio da dependência](../../azure-monitor/app/asp-net-dependencies.md) para ver se a lentidão se deve a serviços externos ou à sua base de dados.
* Se **a Resposta de Receção** for predominante, a sua página e as suas partes dependentes - JavaScript, CSS, imagens e assim por diante (mas não dados assincronicamente carregados) são longos. Configurar um [teste de disponibilidade,](../../azure-monitor/app/monitor-web-app-availability.md)e certifique-se de definir a opção de carregar peças dependentes. Quando obtém alguns resultados, abra o detalhe de um resultado e expanda-o para ver os tempos de carga de diferentes ficheiros.
* O elevado tempo de processamento de **clientes** sugere que os scripts estão a funcionar lentamente. Se a razão não for óbvia, considere adicionar algum código de tempo e enviar os tempos em chamadas trackMetric.

### <a name="improve-slow-pages"></a>Melhorar as páginas lentas
Há uma web cheia de conselhos sobre melhorar as respostas do servidor e os tempos de carregamento de página, por isso não tentaremos repetir tudo aqui. Aqui estão algumas dicas que provavelmente já conhece, só para te fazer pensar:

* Carga lenta por causa de ficheiros grandes: Carregue os scripts e outras partes assincronicamente. Use a gregação de scripts. Parta a página principal em widgets que carregam os seus dados separadamente. Não envie HTML simples para longas tabelas: use um script para solicitar os dados como JSON ou outro formato compacto e, em seguida, encha a tabela no lugar. Há grandes quadros para ajudar com tudo isto. (Também implicam grandes scripts, claro.)
* Dependências lentas do servidor: Considere as localizações geográficas dos seus componentes. Por exemplo, se estiver a utilizar o Azure, certifique-se de que o servidor web e a base de dados estão na mesma região. As consultas recuperam mais informação do que precisam? O cache ou o lote ajudariam?
* Problemas de capacidade: Veja as métricas do servidor dos tempos de resposta e o pedido conta. Se os tempos de resposta atingirem um pico desproporcionalmente com picos de pedidos, é provável que os seus servidores estejam esticados.


## <a name="server-response-time-degradation"></a>Degradação do tempo de resposta do servidor

A notificação de degradação do tempo de resposta diz-lhe:

* O tempo de resposta comparado com o tempo normal de resposta para esta operação.
* Quantos utilizadores são afetados.
* Tempo médio de resposta e 90º tempo de resposta por cento para esta operação no dia da deteção e 7 dias antes. 
* Contagem desta operação solicita no dia da deteção e 7 dias antes.
* Correlação entre a degradação desta operação e as degradações em dependências relacionadas. 
* Ligações para ajudá-lo a diagnosticar o problema.
  * Os rastreios do profiler para ajudá-lo a ver onde o tempo de funcionamento é gasto (o link está disponível se os exemplos de perfis foram recolhidos para esta operação durante o período de deteção). 
  * Relatórios de desempenho no Metric Explorer, onde pode cortar e picar intervalo/filtros para esta operação.
  * Procure por esta chamada para ver propriedades de chamada específicas.
  * Relatórios de falhas - Se a contagem > 1 isto significa que houve falhas nesta operação que poderiam ter contribuído para a degradação do desempenho.

## <a name="dependency-duration-degradation"></a>Degradação da duração da dependência

Aplicações modernas adotam cada vez mais a abordagem de conceção de micro serviços, o que, em muitos casos, leva a uma forte fiabilidade nos serviços externos. Por exemplo, se a sua aplicação depender de alguma plataforma de dados ou mesmo se construir o seu próprio serviço de bots provavelmente transmitirá em algum fornecedor de serviços cognitivos para permitir que os seus bots interajam de formas mais humanas e algum serviço de loja de dados para o bot puxar as respostas De.  

Notificação de degradação da dependência de exemplo:

![Aqui está um exemplo de deteção de degradação da duração da dependência](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Reparem que lhe diz:

* A duração comparada com o tempo normal de resposta para esta operação
* Quantos utilizadores são afetados
* Duração média e 90º percentil para esta dependência no dia da deteção e 7 dias antes
* Número de chamadas de dependência no dia da deteção e 7 dias antes
* Links para ajudá-lo a diagnosticar o problema
  * Relatórios de desempenho no Metric Explorer para esta dependência
  * Procure por esta dependência chamadas para ver propriedades de chamadas
  * Relatórios de falha - Se a contagem > 1 isto significa que houve chamadas de dependência falhadas durante o período de deteção que poderiam ter contribuído para a degradação da duração. 
  * Open Analytics com consultas que calculam esta duração da dependência e contam  

## <a name="smart-detection-of-slow-performing-patterns"></a>Deteção inteligente de padrões de desempenho lento 

O Application Insights encontra problemas de desempenho que podem afetar apenas alguma parte dos seus utilizadores, ou apenas afetar os utilizadores em alguns casos. Por exemplo, a notificação sobre a carga de páginas é mais lenta num tipo de navegador do que em outros tipos de navegadores, ou se os pedidos são servidos mais lentamente a partir de um determinado servidor. Também pode descobrir problemas associados a combinações de propriedades, tais como cargas de página lenta numa área geográfica para clientes que usam determinado sistema operativo.  

Anomalias como estas são muito difíceis de detetar apenas inspecionando os dados, mas são mais comuns do que se possa pensar. Muitas vezes só surgem quando os seus clientes se queixam. Nessa altura, já é tarde demais: os utilizadores afetados já estão a mudar para os seus concorrentes!

Atualmente, os nossos algoritmos olham para os tempos de carregamento da página, tempos de resposta de pedidos no servidor e tempos de resposta à dependência.  

Não é preciso estabelecer limites ou definir regras. Os algoritmos de aprendizagem automática e de mineração de dados são usados para detetar padrões anormais.

![A partir do alerta de e-mail, clique no link para abrir o relatório de diagnóstico em Azure](./media/proactive-performance-diagnostics/03.png)

* **Quando** mostra a hora em que o problema foi detetado.
* **O que** descreve:

  * O problema que foi detetado;
  * As características do conjunto de eventos que encontramos mostraram o comportamento do problema.
* A tabela compara o conjunto de mau desempenho com o comportamento médio de todos os outros eventos.

Clique nos links para abrir o Metric Explorer e procurar em relatórios relevantes, filtrados na hora e propriedades do conjunto de desempenho lento.

Modifique a gama de tempo e os filtros para explorar a telemetria.

## <a name="next-steps"></a>Passos seguintes
Estas ferramentas de diagnóstico ajudam-no a inspecionar a telemetria da sua aplicação:

* [Gerador de perfis](profiler.md) 
* [Depurador de instantâneos](../../azure-monitor/app/snapshot-debugger.md)
* [Análise](../../azure-monitor/log-query/get-started-portal.md)
* [Diagnósticos inteligentes de análise](../../azure-monitor/app/analytics.md)

As deteções inteligentes são completamente automáticas. Mas talvez queira criar mais alguns alertas?

* [Alertas métricos configurados manualmente](../../azure-monitor/app/alerts.md)
* [Testes Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
