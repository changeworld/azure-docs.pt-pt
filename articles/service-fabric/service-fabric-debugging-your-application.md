---
title: Desinvejo a sua aplicação no Estúdio Visual
description: Melhore a fiabilidade e desempenho dos seus serviços desenvolvendo-os e depurando-os no Estúdio Visual num cluster de desenvolvimento local.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: fff8a19d5643f7ce866c9eb9c57486340b6f8a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624144"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Depurar a sua aplicação do Service Fabric com o Visual Studio
> [!div class="op_single_selector"]
> * [Estúdio Visual/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Depurar uma aplicação de Tecido de Serviço local
Pode economizar tempo e dinheiro implementando e depurando a sua aplicação Azure Service Fabric num cluster de desenvolvimento informático local. O Visual Studio 2019 ou 2015 pode implementar a aplicação para o cluster local e ligar automaticamente o debugger a todas as instâncias da sua aplicação. O Estúdio Visual tem de ser executado como Administrador para ligar o debugger.

1. Inicie um cluster de desenvolvimento local seguindo os passos na configuração do seu ambiente de desenvolvimento de [Tecido de Serviço.](service-fabric-get-started.md)
2. Prima **F5** ou clique em **Depurar** > **Depuração**.
   
    ![Comece a depurar uma aplicação][startdebugging]
3. Desloque os pontos de rutura no seu código e passe pela aplicação clicando em comandos no menu **Debug.**
   
   > [!NOTE]
   > O Visual Studio liga-se a todas as instâncias da sua aplicação. Enquanto está a passar pelo código, os breakpoints podem ser atingidos por vários processos que resultam em sessões simultâneas. Tente desativar os pontos de rutura depois de serem atingidos, condicionando cada ponto de rutura à identificação da linha ou utilizando eventos de diagnóstico.
   > 
   > 
4. A janela **Eventos de Diagnóstico** será aberta automaticamente para que possa ver eventos de diagnóstico em tempo real.
   
    ![Ver eventos de diagnóstico em tempo real][diagnosticevents]
5. Também pode abrir a janela **de Eventos de Diagnóstico** no Cloud Explorer.  Em **Tecido de Serviço,** clique à direita em qualquer nó e escolha **Ver Rastreios de Streaming**.
   
    ![Abra a janela de eventos de diagnóstico][viewdiagnosticevents]
   
    Se pretender filtrar os seus vestígios para um serviço ou aplicação específico, ative rastreios de streaming nesse serviço ou aplicação específicos.
6. Os eventos de diagnóstico podem ser vistos no ficheiro **ServiceEventSource.cs** gerado automaticamente e são chamados a partir do código de aplicação.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. A janela **Eventos** de Diagnóstico suporta filtragem, pausa e inspeção de eventos em tempo real.  O filtro é uma simples pesquisa de cordas da mensagem do evento, incluindo o seu conteúdo.
   
    ![Filtrar, fazer pausas e retomar, ou inspecionar eventos em tempo real][diagnosticeventsactions]
8. Depuração de serviços é como depurar qualquer outra aplicação. Normalmente, irá definir Breakpoints através do Estúdio Visual para uma depuração fácil. Apesar de as Coleções Fiáveis se replicarem em vários nós, ainda implementam o IEnumerable. Esta implementação significa que pode utilizar a Visualização de Resultados no Estúdio Visual enquanto depura para ver o que guardou no seu interior. Para tal, detete um ponto de rutura em qualquer lugar do seu código.
   
    ![Comece a depurar uma aplicação][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Executando um guião como parte da depuração
Em certos cenários, poderá ser necessário executar um guião como parte de iniciar uma sessão de depuração (por exemplo, quando não utilizar os Serviços Predevidos).

No Estúdio Visual, pode adicionar um ficheiro chamado **Start-Service.ps1** na pasta **Scripts** do projeto Aplicação de Tecido de Serviço (.sfproj). Este script será invocado após a criação da aplicação no cluster local.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Depurar uma aplicação de tecido de serviço remoto
Se as suas aplicações Service Fabric estiverem a funcionar num cluster de Tecido de Serviço em Azure, pode desinviar remotamente estas aplicações, diretamente do Visual Studio.

> [!NOTE]
> A funcionalidade requer o [Serviço Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [o Azure SDK para .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> A depuração remota destina-se a cenários de dev/teste e não deve ser utilizado em ambientes de produção, devido ao impacto nas aplicações em funcionamento.

1. Navegue para o seu cluster no **Cloud Explorer.** Clique à direita e escolha **Ativar Debugging**
   
    ![Ativar depuração remota][enableremotedebugging]
   
    Esta ação iniciará o processo de permitir a extensão de depuração remota nos seus nós de cluster e configurações de rede necessárias.
2. Clique no nó de cluster no **Cloud Explorer**e escolha **Attach Debugger**
   
    ![Anexar o debugger][attachdebugger]
3. No **Attach para processar o** diálogo, escolha o processo que pretende depurar e clique **em Anexar**
   
    ![Escolha o processo][chooseprocess]
   
    O nome do processo a que pretende anexar, equivale ao nome do seu nome de montagem do projeto de serviço.
   
    O debugger vai ligar-se a todos os nós que executam o processo.
   
   * No caso de estar a depurar um serviço apátrida, todos os casos do serviço em todos os nós fazem parte da sessão de depuração.
   * Se estiver a depurar um serviço imponente, apenas a réplica primária de qualquer partição estará ativa e, portanto, apanhada pelo desbugger. Se a réplica primária se mover durante a sessão de depuração, o processamento dessa réplica continuará a fazer parte da sessão de depuração.
   * Para capturar apenas divisórias ou instâncias relevantes de um determinado serviço, pode utilizar pontos de rutura condicional para quebrar apenas uma partição ou instância específica.
     
     ![Ponto de rutura condicional][conditionalbreakpoint]
     
     > [!NOTE]
     > Atualmente não apoiamos a depuração de um cluster de Tecido de Serviço com múltiplas instâncias do mesmo nome executável de serviço.
     > 
     > 
4. Assim que terminar de depurar a sua aplicação, pode desativar a extensão de depuração remota clicando no cluster no **Cloud Explorer** e escolher Depuração de **Depuração**
   
    ![Desativar a depuração remota][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Rastreios de streaming de um nó de cluster remoto
Você também é capaz de transmitir vestígios diretamente de um nó de cluster remoto para Visual Studio. Esta funcionalidade permite-lhe transmitir eventos de vestígios eTW, produzidos num nó de cluster de tecido de serviço.

> [!NOTE]
> Esta função requer tecido de [serviço SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK para .NET 2.9](https://azure.microsoft.com/downloads/).
> Esta funcionalidade apenas suporta clusters em funcionamento em Azure.
> 
> 

<!-- -->
> [!WARNING]
> O streaming de vestígios destina-se a cenários de dev/teste e não deve ser utilizado em ambientes de produção, devido ao impacto nas aplicações em funcionamento.
> Num cenário de produção, deve contar com o reencaminhamento de eventos utilizando o Azure Diagnostics.

1. Navegue para o seu cluster no **Cloud Explorer.** Clique à direita e escolha Localizar rastreios de **streaming de ativação**
   
    ![Ativar vestígios de streaming remoto][enablestreamingtraces]
   
    Esta ação iniciará o processo de permitir a extensão de vestígios de streaming nos seus nós de cluster, bem como as configurações de rede necessárias.
2. Expanda o elemento **Nós** no **Cloud Explorer,** clique à direita no nó de que deseja transmitir vestígios e escolha **Rastreios de Streaming** de Visualização
   
    ![Ver vestígios de streaming remoto][viewremotestreamingtraces]
   
    Repita o passo 2 para o número de nódosos que quiser ver vestígios. Cada fluxo de nós aparecerá numa janela dedicada.
   
    Agora pode ver os vestígios emitidos pela Service Fabric e os seus serviços. Se pretender filtrar os eventos para mostrar apenas uma aplicação específica, basta digitar o nome da aplicação no filtro.
   
    ![Visualização de rastreios de streaming][viewingstreamingtraces]
3. Uma vez feito o streaming de vestígios do seu cluster, pode desativar os traços de streaming remotos, clicando corretamente no cluster no **Cloud Explorer** e escolher **Desativar os Rastreios** de Streaming
   
    ![Desativar vestígios de streaming remoto][disablestreamingtraces]

## <a name="next-steps"></a>Passos seguintes
* [Teste um serviço de tecido de serviço](service-fabric-testability-overview.md).
* [Gerencie as suas aplicações de Tecido de Serviço no Estúdio Visual.](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
