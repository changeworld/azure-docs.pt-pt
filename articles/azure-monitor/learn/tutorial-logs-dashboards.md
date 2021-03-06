---
title: Criar e partilhar dashboards de dados do Log Analytics do Azure | Microsoft Docs
description: Este tutorial ajuda-o a entender como os dashboards do Log Analytics podem visualizar todas as suas consultas de registo guardadas, dando-lhe uma única lente para ver o seu ambiente.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 76ba79561df4a75004369d24c4c6af82de9b1cfc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77661537"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Criar e partilhar dashboards dos dados do Log Analytics

Os dashboards do Log Analytics podem visualizar todas as suas consultas de registo guardadas, dando-lhe a capacidade de encontrar, correlacionar e partilhar dados operacionais de TI na organização.  Este tutorial cobre a criação de uma consulta de log que será usada para suportar um dashboard partilhado que será acedido pela sua equipa de suporte de operações de TI.  Saiba como:

> [!div class="checklist"]
> * Criar um dashboard partilhado no portal do Azure
> * Visualizar uma consulta de registo de desempenho 
> * Adicione uma consulta de log a um dashboard partilhado 
> * Personalizar um mosaico num dashboard partilhado

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente [ligada à área de trabalho do Log Analytics](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em . 

## <a name="create-a-shared-dashboard"></a>Criar um dashboard partilhado
Selecione **Dashboard** para abrir o seu painel de [instrumentos](../../azure-portal/azure-portal-dashboards.md)predefinido . O seu painel de instrumentos será diferente do exemplo abaixo.

![Dashboard do portal do Azure](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Aqui, pode reunir os dados operacionais mais importantes para as TI em todos os recursos do Azure, incluindo telemetria do Log Analytics do Azure.  Antes de visualizarmos uma consulta de log, vamos primeiro criar um dashboard e partilhá-lo.  Podemos então focar-nos na nossa consulta de registo de desempenho, que será render como um gráfico de linha, e adicioná-lo ao painel de instrumentos.  

Para criar um dashboard, selecione o botão **Novo dashboard** junto ao nome do dashboard atual.

![Criar novo dashboard no portal do Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Esta ação cria um dashboard novo, vazio e privado, e coloca-o no modo de personalização onde pode nomear o seu dashboard e adicionar ou reorganizar os mosaicos. Editar o nome do painel de instrumentos e especificar *sample dashboard* para este tutorial e, em seguida, selecionar **Feito personalizar**.<br><br> ![Guardar dashboard do Azure personalizado](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Quando cria um dashboard, é privado por predefinição, o que significa que é o único utilizador que o pode ver. Para o tornar visível a outros utilizadores, utilize o botão **Partilhar** que se encontra junto aos outros comandos do dashboard.

![Partilhar um novo dashboard no portal do Azure](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

É-lhe pedido para escolher uma subscrição e um grupo de recursos para o seu dashboard ser publicado. Para sua comodidade, a experiência de publicação do portal encaminha-o para um padrão onde coloca dashboards num grupo de recursos designado **dashboards**.  Verifique a subscrição selecionada e, em seguida, clique em **Publicar**.  O acesso às informações apresentadas no dashboard é controlado com o [Controlo de Acesso Baseado no Recurso do Azure](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Visualizar uma consulta de registo
[Log Analytics](../log-query/get-started-portal.md) é um portal dedicado usado para trabalhar com consultas de registo e seus resultados. As funcionalidades incluem a capacidade de editar uma consulta em várias linhas, executar código seletivamente, contexto confidencial do Intellisense e Análise Inteligente. Neste tutorial, utilizará o Log Analytics para criar uma visão de desempenho em formagráfica, guarde-a para uma futura consulta e fixá-la-á no painel partilhado criado anteriormente.

Abra o Log Analytics selecionando **Registos** no menu Do Monitor Azure. Começa com uma nova consulta em branco.

![Página de boas-vindas](media/tutorial-logs-dashboards/homepage.png)

Introduza a seguinte consulta para devolver os registos de utilização do processador para computadores Windows e Linux, agrupados por Computer e TimeGenerated, e apresentados num gráfico visual. Clique em **Executar** para executar a consulta e ver o gráfico resultante.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Guarde a consulta selecionando o botão **Guardar** a partir do topo da página.

![Guardar consulta](media/tutorial-logs-dashboards/save-query.png)

No painel de controlo **Save Query,** forneça um nome como *VMs Azure - Utilização do Processador* e uma categoria como *Dashboards* e, em seguida, clique em **Guardar**.  Desta forma pode criar uma biblioteca de consultas comuns que pode usar e modificar.  Por fim, fixe-o ao painel partilhado criado anteriormente selecionando o botão Pin para o painel de **instrumentos** a partir do canto superior direito da página e, em seguida, selecionando o nome do painel de instrumentos.

Agora que temos uma consulta afixada ao dashboard, irá reparar que tem um título genérico e um comentário abaixo do mesmo.

![Exemplo de dashboard do Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Devemos alterar o nome para algo significativo que possa ser facilmente compreendido pelos utilizadores que o veem.  Clique no botão de edição para personalizar o título e a legenda para o azulejo e, em seguida, clique em **Atualizar**.  Será apresentada uma faixa a pedir-lhe para publicar ou eliminar as alterações.  Clique em **Guardar uma cópia**.  

![Configuração concluída do dashboard de exemplo](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar um dashboard no portal Azure e a adicionar-lhe uma consulta de log.  Avance para o próximo tutorial para saber as diferentes respostas que pode implementar com base nos resultados da consulta de log.  

> [!div class="nextstepaction"]
> [Responder a eventos com Alertas do Log Analytics](tutorial-response.md)
