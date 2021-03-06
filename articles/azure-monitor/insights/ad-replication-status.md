---
title: Monitor ativo estatuto de replicação do Diretório
description: O pacote de solução Ative Directory Replication Status monitoriza regularmente o seu ambiente de Diretório Ativo para eventuais falhas de replicação.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: 30b0c7c87f6d55586b931be1445b175ce58565d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055904"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Monitor ativo de replicação de diretório com monitor Azure

![Símbolo do estado de replicação do AD](./media/ad-replication-status/ad-replication-status-symbol.png)

O Diretório Ativo é um componente chave de um ambiente de TI empresarial. Para garantir uma elevada disponibilidade e alto desempenho, cada controlador de domínio tem a sua própria cópia da base de dados do Ative Directory. Os controladores de domínio replicam-se uns com os outros para propagar alterações em toda a empresa. Falhas neste processo de replicação podem causar uma variedade de problemas em toda a empresa.

A solução De Estatuto de Replicação AD monitoriza regularmente o seu ambiente de Diretório Ativo para eventuais falhas de replicação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

### <a name="prerequisites"></a>Pré-requisitos

* A solução De Estatuto de Replicação ad requer uma versão suportada do .NET Framework 4.6.2 ou superior instalado em cada computador que tenha o agente Log Analytics para Windows (também designado por Agente de Monitorização da Microsoft (MMA)) instalado.  O agente é utilizado pelo System Center 2016 - Diretor de Operações, Diretor de Operações 2012 R2 e Monitor Azure.
* A solução suporta controladores de domínio que executam o Windows Server 2008 e 2008 R2, o Windows Server 2012 e o 2012 R2 e o Windows Server 2016.
* Um espaço de trabalho log Analytics para adicionar a solução Ative Directory Health Check do mercado Azure no portal Azure. Não é necessária uma configuração adicional.


### <a name="install-agents-on-domain-controllers"></a>Instale agentes em controladores de domínio
Deve instalar agentes em controladores de domínio que sejam membros do domínio a avaliar. Ou, deve instalar agentes nos servidores dos membros e configurar os agentes para enviar dados de replicação aD para o Monitor Azure. Para entender como ligar os computadores Windows ao Monitor Azure, consulte [o Connect Windows computers para o Monitor Azure](../../azure-monitor/platform/agent-windows.md). Se o seu controlador de domínio já fizer parte de um ambiente existente do Gestor de Operações do System Center que pretende ligar ao Monitor Azure, consulte [o Connect Operations Manager para o Azure Monitor](../../azure-monitor/platform/om-agents.md).

### <a name="enable-non-domain-controller"></a>Ativar controlador não-domínio
Se não quiser ligar nenhum dos seus controladores de domínio diretamente ao Monitor Azure, pode utilizar qualquer outro computador do seu domínio ligado ao Monitor Azure para recolher dados para o pacote de solução DeEstatuto de Replicação AD e enviá-lo para enviar os dados.

1. Verifique se o computador é um membro do domínio que pretende monitorizar utilizando a solução DeEstatuto de Replicação AD.
2. [Ligue o computador Windows ao Monitor Azure](../../azure-monitor/platform/om-agents.md) ou [conecte-o utilizando o ambiente do Gestor de Operações existente ao Monitor Azure](../../azure-monitor/platform/om-agents.md), caso não esteja já ligado.
3. Nesse computador, detete a seguinte chave de registo:<br>Chave: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parâmetros\Management\<GroupName>\Solutions\ADReplication**<br>Valor: **IsTarget**<br>Dados de Valor: **verdadeiro**

   > [!NOTE]
   > Estas alterações não entram em vigor até reiniciar o serviço do Microsoft Monitoring Agent (HealthService.exe).
   > ### <a name="install-solution"></a>Instalar solução
   > Siga o processo descrito na [Instalação de uma solução de monitorização](solutions.md#install-a-monitoring-solution) para adicionar a solução de Estado de Replicação de **Diretório Ativo** ao seu espaço de trabalho Log Analytics. Não há nenhuma configuração adicional.


## <a name="ad-replication-status-data-collection-details"></a>Dados da recolha de dados do Estado de Replicação aD
O quadro seguinte mostra métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o Estado de Replicação de AD.

| 
    da Microsoft
   | Agente Direto | Agente SCOM | Storage do Azure | SCOM necessário? | Dados do agente SCOM enviados através de grupo de gestão | frequência de recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |a cada cinco dias |



## <a name="understanding-replication-errors"></a>Compreender erros de replicação

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

O azulejo do Estado de Replicação AD mostra quantos erros de replicação tem atualmente. **Erros de Replicação Crítica** são erros que estão a 75% da vida útil da [lápide](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) para a sua floresta de Diretório Ativo.

![Azulejo do Estado de Replicação AD](./media/ad-replication-status/oms-ad-replication-tile.png)

Ao clicar no azulejo, pode ver mais informações sobre os erros.
![Painel de instrumentos de estado de replicação ad](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Estado do servidor de destino e estado do servidor de origem
Estas colunas mostram o estado dos servidores de destino e servidores de origem que estão a experimentar erros de replicação. O número após cada nome do controlador de domínio indica o número de erros de replicação nesse controlador de domínio.

Os erros tanto para servidores de destino como para servidores de origem são mostrados porque alguns problemas são mais fáceis de resolver a partir da perspetiva do servidor de origem e outros do ponto de vista do servidor de destino.

Neste exemplo, pode ver que muitos servidores de destino têm aproximadamente o mesmo número de erros, mas há um servidor de origem (ADDC35) que tem muito mais erros do que todos os outros. É provável que haja algum problema no ADDC35 que o esteja a fazer não enviar dados aos seus parceiros de replicação. A correção dos problemas no ADDC35 pode resolver muitos dos erros que aparecem na área do servidor de destino.

### <a name="replication-error-types"></a>Tipos de erro de replicação
Esta área dá-lhe informações sobre os tipos de erros detetados em toda a sua empresa. Cada erro tem um código numérico único e uma mensagem que pode ajudá-lo a determinar a causa principal do erro.

O donut no topo dá-lhe uma ideia de quais erros aparecem cada vez mais frequentemente no seu ambiente.

Mostra-lhe quando vários controladores de domínio experimentam o mesmo erro de replicação. Neste caso, poderá ser capaz de descobrir ou identificar uma solução num controlador de domínio e, em seguida, repeti-la noutros controladores de domínio afetados pelo mesmo erro.

### <a name="tombstone-lifetime"></a>Vida útil de Lápide
A vida útil da lápide determina quanto tempo um objeto apagado, referido como lápide, é mantido na base de dados do Diretório Ativo. Quando um objeto apagado passa a vida útil da lápide, um processo de recolha de lixo remove-o automaticamente da base de dados do Diretório Ativo.

A vida útil da lápide padrão é de 180 dias para as versões mais recentes do Windows, mas foram 60 dias em versões mais antigas, e pode ser alterada explicitamente por um administrador de Diretório Ativo.

É importante saber se está sem erros de replicação que se aproximam ou já passaram da vida útil da lápide. Se dois controladores de domínio experimentarem um erro de replicação que persiste após o tempo de vida da lápide, a replicação é desativada entre esses dois controladores de domínio, mesmo que o erro de replicação subjacente seja corrigido.

A área de Tombstone Lifetime ajuda-o a identificar locais onde a replicação desativada está em perigo de acontecer. Cada erro na categoria Mais de **100% De TSL** representa uma partição que não se replica entre a sua fonte e o servidor de destino durante pelo menos a vida útil da lápide para a floresta.

Nesta situação, a simples correção do erro de replicação não será suficiente. No mínimo, é necessário investigar manualmente para identificar e limpar objetos persistentes antes de poder reiniciar a replicação. Pode até precisar de desmantelar um controlador de domínio.

Além de identificar quaisquer erros de replicação que tenham permanecido para além da vida útil da lápide, você também quer estar atento a quaisquer erros que caiam nas categorias **TSL de 50-75%** ou **75-100% TSL.**

Estes são erros que são claramente persistentes, não transitórios, por isso provavelmente precisam da sua intervenção para resolver. A boa notícia é que ainda não chegaram à vida de lápide. Se resolver estes problemas rapidamente e *antes* de atingirem a vida útil da lápide, a replicação pode reiniciar com uma intervenção manual mínima.

Como notado anteriormente, o azulejo do painel de instrumentos para a solução AD Replication Status mostra o número *de* erros críticos de replicação no seu ambiente, que é definido como erros que são mais de 75% da vida útil da lápide (incluindo erros que são superiores a 100% da TSL). Esforce-se para manter este número em 0.

> [!NOTE]
> Todos os cálculos percentuais de vida de lápide são baseados na vida real da lápide para a sua floresta de Diretório Ativo, para que possa confiar que essas percentagens são precisas, mesmo que tenha um conjunto de valor de vida de lápide personalizado.
>
>

### <a name="ad-replication-status-details"></a>Detalhes do estado da replicação do AD
Quando clica em qualquer item numa das listas, vê detalhes adicionais sobre o mesmo utilizando uma consulta de registo. Os resultados são filtrados para mostrar apenas os erros relacionados com esse item. Por exemplo, se clicar no primeiro controlador de domínio listado no Status do Servidor de **Destino (ADDC02),** vê os resultados da consulta filtrados para mostrar erros com esse controlador de domínio listado como servidor de destino:

![Erros de estado de replicação aD nos resultados da consulta](./media/ad-replication-status/oms-ad-replication-search-details.png)

A partir daqui, pode filtrar ainda mais, modificar a consulta de registo, e assim por diante. Para obter mais informações sobre a utilização das consultas de registo no Monitor Azure, consulte [analisar os dados de log no Monitor Azure](../../azure-monitor/log-query/log-query-overview.md).

O campo **HelpLink** mostra o URL de uma página TechNet com detalhes adicionais sobre esse erro específico. Pode copiar e colar este link na janela do seu navegador para ver informações sobre resolução de problemas e corrigir o erro.

Também pode clicar em **Exportar** para exportar os resultados para excel. Exportar os dados pode ajudá-lo a visualizar os dados de erro de replicação da forma que quiser.

![erros de estado de replicação da AD exportados em Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Estado de replicação de Anúncios FAQ
**P: Com que frequência os dados do estado de replicação da AD são atualizados?**
R: A informação é atualizada de cinco em cinco dias.

**P: Existe uma forma de configurar com que frequência estes dados são atualizados?**
A: Não neste momento.

**P: Preciso adicionar todos os meus controladores de domínio ao meu espaço de trabalho log Analytics para ver o estado de replicação?**
R: Não, apenas deve ser adicionado um único controlador de domínio. Se tiver vários controladores de domínio no seu espaço de trabalho Log Analytics, os dados de todos eles são enviados para o Monitor Azure.

**P: Não quero adicionar controladores de domínio ao meu espaço de trabalho Log Analytics. Ainda posso usar a solução de Estado de Replicação AD?**

R: Sim. Pode definir o valor de uma chave de registo para a ativar. Ver [Ativar o controlador não-domínio](#enable-non-domain-controller).

**P: Qual é o nome do processo que faz a recolha de dados?**
A: AdvisorAssessment.exe

**P: Quanto tempo leva para que os dados sejam recolhidos?**
R: O tempo de recolha de dados depende do tamanho do ambiente do Diretório Ativo, mas geralmente demora menos de 15 minutos.

**P: Que tipo de dados são recolhidos?**
R: As informações de replicação são recolhidas via LDAP.

**P: Existe uma forma de configurar quando os dados são recolhidos?**
A: Não neste momento.

**P: Que permissões preciso para recolher dados?**
R: As permissões normais do utilizador ao Diretório Ativo são suficientes.

## <a name="troubleshoot-data-collection-problems"></a>Problemas de recolha de dados de resolução de problemas
Para recolher dados, o pacote de solução AD Replication Status requer pelo menos um controlador de domínio para ser ligado ao seu espaço de trabalho Log Analytics. Até ligar um controlador de domínio, aparece uma mensagem indicando que **os dados ainda estão a ser recolhidos**.

Se necessitar de assistência para ligar um dos seus controladores de domínio, pode ver a documentação nos [computadores Connect Windows ao Monitor Azure](../../azure-monitor/platform/om-agents.md). Alternativamente, se o seu controlador de domínio já estiver ligado a um ambiente de Gestor de Operações do System Center existente, pode ver documentação no [Connect System Center Operations Manager para o Azure Monitor](../../azure-monitor/platform/om-agents.md).

Se não quiser ligar nenhum dos seus controladores de domínio diretamente ao Monitor Azure ou ao System Center Operations Manager, consulte [o controlador de não domínio Enable](#enable-non-domain-controller).

## <a name="next-steps"></a>Passos seguintes
* Utilize consultas de [registo no Monitor Azure](../../azure-monitor/log-query/log-query-overview.md) para visualizar dados detalhados do estado de replicação do diretório ativo.
