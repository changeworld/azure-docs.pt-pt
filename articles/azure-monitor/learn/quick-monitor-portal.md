---
title: Monitorizar uma aplicação Web ASP.NET com o Azure Application Insights | Microsoft Docs
description: Fornece instruções para configurar rapidamente uma ASP.NET Web App para monitorização com Insights de Aplicação
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: 3775070320b8c6e96dd06031bd87ea53f37046c5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78894861"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Iniciar a Monitorização de uma Aplicação Web ASP.NET

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização.  Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique.  Com as informações que recolher com o Application Insights sobre o desempenho e a eficácia da sua aplicação, pode tomar decisões informadas para manter e melhorar a sua aplicação.

Este início rápido mostra como adicionar o Application Insights a uma aplicação Web ASP.NET existente e iniciar a análise das estatísticas em direto, um dos vários métodos que pode utilizar para analisar a sua aplicação. Se não tiver uma aplicação web ASP.NET, pode criar uma seguindo o [ASP.NET Web App .](../../app-service/app-service-web-get-started-dotnet-framework.md)

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de início rápido:

- Instale o [Estúdio Visual 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure


Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="enable-application-insights"></a>Ativar o Application Insights

1. Abra o seu projeto no Visual Studio 2019.
2. Selecione **Configurar o Application Insights** no menu Projeto. O Visual Studio adiciona o Application Insights SDK à sua aplicação.

    > [!IMPORTANT]
    > O processo de adicionar o Application Insights varia consoante o tipo de modelo do ASP.NET. Se estiver a utilizar o modelo **Vazio** ou **Aplicação Móvel do Azure**, selecione **Projeto** > **Adicionar Telemetria do Application Insights**. Relativamente a todos os outros modelos ASP.NET, consulte as instruções no passo acima. 

3. Clique em **Introdução** (as versões anteriores do Visual Studio apresentam um botão **Iniciar a Versão Gratuita**).

    ![Adicionar o Application Insights ao Visual Studio](./media/quick-monitor-portal/add-application-insights-b.png)

4. Selecione a sua subscrição e clique em **Registo**.

5. Selecione **Project** > **Manage NuGet Packages** > **Package source: nuget.org** > **Update** the Application Insights SDK packages to the latest stable release.

6. Execute a sua aplicação selecionando **Iniciar Depuração** no menu **Depurar** ou premindo a tecla F5.

## <a name="confirm-app-configuration"></a>Confirmar a configuração da aplicação

O Application Insights recolhe dados de telemetria da sua aplicação, independentemente de estar ou não em execução. Utilize os passos seguintes para começar a ver estes dados.

1. Abra insights de aplicação clicando em **Ver** -> **Outras** -> Pesquisas de Insights de**Aplicação**do Windows .  É apresentada a telemetria da sua sessão atual.<BR><br>![Telemetria no Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Clique no primeiro pedido na lista (GET Home/Index neste exemplo) para ver os detalhes do pedido. Tenha em atenção que o código de estado e o tempo de resposta são incluídos juntamente com outras informações importantes sobre o pedido.<br><br>![Detalhes de resposta no Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

Agora, pode abrir o Application Insights no portal do Azure para ver os vários detalhes sobre a sua aplicação em execução.

1. Expanda a pasta **Serviços Conectados** (ícone de nuvem e ficha) no Solution Explorer e, em seguida, clique à direita na pasta Insights de **Aplicação** e clique no Portal de Insights de **Aplicação Aberta**.  São apresentadas algumas informações sobre a sua aplicação e uma variedade de opções.

    ![Mapeamento de Aplicações](media/quick-monitor-portal/04-overview.png)

2. Clique em **Mapa da Aplicação** para obter um esquema visual das relações de dependência entre os componentes da aplicação.  Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

    ![Mapeamento de Aplicações](media/quick-monitor-portal/05-appmap.png)

3. Clique no **App Analytics** mapa ![do](media/quick-monitor-portal/app-viewinlogs-icon.png) mapa do ícone app Analytics **em Registos (Analytics)** num dos componentes da aplicação. Isto abre **Registos (Analytics),** que fornece uma linguagem de consulta rica para analisar todos os dados recolhidos pela Application Insights. Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico. Pode escrever as suas próprias consultas para analisar outros dados.

    ![Análise](media/quick-monitor-portal/6viewanalytics.png)

4. Clique no **Live Metrics Stream** à esquerda sob investigação. Isto mostra estatísticas em direto sobre a sua aplicação em execução. Inclui informações como o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram. Também pode inspecionar métricas críticas de desempenho, tais como processador e memória.

    ![Live Stream](media/quick-monitor-portal/7livemetrics.png)

    Se está pronto para alojar a sua aplicação no Azure, pode publicá-la agora. Siga os passos descritos em [Início Rápido para Criar uma Aplicação Web ASP.NET](../../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy).

5. Se utilizar o Visual Studio para adicionar a monitorização do Application Insights, pode adicionar automaticamente a monitorização do lado do cliente. Para adicionar monitorização manualmente do lado do cliente a uma aplicação, adicione o seguinte JavaScript à sua aplicação:

```html
<!-- 
To collect user behavior analytics about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });

window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Para saber mais, visite o repositório do GitHub para o nosso [SDK de JavaScript de open source](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="video"></a>Vídeo

* Vídeo externo passo a passo sobre [configurar Insights de Aplicação com uma aplicação .NET do zero](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="clean-up-resources"></a>Limpar recursos
Quando terminar os testes, pode eliminar o grupo de recursos e todos os recursos relacionados. Para isso siga os passos abaixo.
1. No menu do lado esquerdo no portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myResourceGroup**.
2. Na página do grupo de recursos, clique em **Eliminar**, escreva **myResourceGroup** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, permitiu a sua aplicação de monitorização pela Azure Application Insights.  Continue para os tutoriais para saber como utilizá-lo para monitorizar as estatísticas e detetar problemas na sua aplicação.

> [!div class="nextstepaction"]
> [Azure Application Insights](tutorial-runtime-exceptions.md)
