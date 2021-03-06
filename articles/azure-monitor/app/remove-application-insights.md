---
title: Remover insights de aplicação em Estúdio Visual - Monitor Azure
description: Como remover o Application Insights SDK para ASP.NET e ASP.NET Core no Estúdio Visual.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80805108"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Como remover insights de aplicação em estúdio visual

Este artigo irá mostrar-lhe como remover a ASP.NET e ASP.NET Core Application Insights SDK no Estúdio Visual.

Para remover os Insights da Aplicação, terá de remover os pacotes NuGet e referências da API na sua aplicação. Pode desinstalar os pacotes NuGet utilizando a Consola de Gestão de Pacotes ou gerir a Solução NuGet em Estúdio Visual. As seguintes secções mostrarão duas formas de remover os Pacotes NuGet e o que foi adicionado automaticamente no seu projeto. Certifique-se de confirmar que os ficheiros adicionados e áreas com o seu próprio código em que fez chamadas para a API são removidos.

## <a name="uninstall-using-the-package-management-console"></a>Desinstalar usando a consola de gestão de pacotes

# <a name="net"></a>[.NET](#tab/net)

1. Para abrir a Consola de Gestão de Pacotes, no menu superior selecione Ferramentas > NuGet Package Manager > Consola de Gestor de Pacotes.
     
    ![No menu superior clique em Ferramentas > NuGet Package Manager > Consola de Gestor de Pacotes](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Se a recolha de vestígios estiver ativada, terá primeiro de desinstalar a Microsoft.ApplicationInsights.TraceListener. Introduza `Uninstall-package Microsoft.ApplicationInsights.TraceListener` e siga o passo abaixo para remover Microsoft.ApplicationInsights.Web.

1. Introduza o seguinte comando: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Após a entrada no comando, o pacote Application Insights e todas as suas dependências serão desinstalados no projeto.
    
    ![Insira o comando na consola](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Para abrir a Consola de Gestão de Pacotes, no menu superior selecione Ferramentas > NuGet Package Manager > Consola de Gestor de Pacotes.

    ![No menu superior clique em Ferramentas > NuGet Package Manager > Consola de Gestor de Pacotes](./media/remove-application-insights/package-manager.png)

1. Introduza o seguinte comando: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Após a entrada no comando, o pacote Application Insights e todas as suas dependências serão desinstalados no projeto.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Desinstalar usando o Estúdio Visual NuGet UI

# <a name="net"></a>[.NET](#tab/net)

1. No *Solution Explorer* à direita, clique à direita na **Solução** e selecione **Gerir pacotes NuGet para solução**.

    Em seguida, verá um ecrã que lhe permite editar todos os pacotes NuGet que fazem parte do projeto.
    
     ![Clique certo Solução, no Solution Explorer, em seguida, selecione Gerir pacotes NuGet para solução](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Se a recolha de vestígios estiver ativada, é necessário desinstalar primeiro a Microsoft.ApplicationInsights.TraceListener sem remover as dependências selecionadas e, em seguida, seguir os passos abaixo para desinstalar microsoft.ApplicationInsights.Web com dependências removidas selecionadas.
    
1. Clique no pacote "Microsoft.ApplicationInsights.Web".À direita, consulte a caixa de verificação ao lado do *Project* para selecionar todos os projetos.
    
1. Para remover todas as dependências ao desinstalar, selecione **o** botão de dropdown options abaixo da secção onde selecionou o projeto.

    Em *Opções de Desinstalação,* selecione a caixa de verificação ao lado de *Remover dependências*.

1. Selecione **Desinstalar**.
    
    ![Verifique remover dependências e, em seguida, desinstalar](./media/remove-application-insights/uninstall-framework.png)

    Será apresentada uma caixa de diálogo que mostra todas as dependências a remover da aplicação.Selecione **ok** para desinstalar.
    
    ![Verifique remover dependências e, em seguida, desinstalar](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Depois de tudo estar desinstalado, ainda pode ver "ApplicationInsights.config" e "AiHandleErrorAttribute.cs" no *Solution Explorer*.Pode eliminar os dois ficheiros manualmente.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. No *Solution Explorer* à direita, clique à direita na **Solução** e selecione **Gerir pacotes NuGet para solução**.

    Em seguida, verá um ecrã que lhe permite editar todos os pacotes NuGet que fazem parte do projeto.

    ![Clique certo Solução, no Solution Explorer, em seguida, selecione Gerir pacotes NuGet para solução](./media/remove-application-insights/manage-nuget-core.png)

1. Clique no pacote "Microsoft.ApplicationInsights.AspNetCore". À direita, verifique a caixa de verificação ao lado do *Project* para selecionar todos os projetos e, em seguida, selecione **Desinstalar**.

    ![Verifique remover dependências e, em seguida, desinstalar](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>O que é criado quando adiciona Insights de Aplicação

Quando adiciona informações de aplicação ao seu projeto, cria ficheiros e adiciona código a alguns dos seus ficheiros. Desinstalar exclusivamente os Pacotes NuGet nem sempre descartará os ficheiros e o código. Para remover totalmente os Insights da Aplicação, deve verificar e eliminar manualmente o código ou ficheiros adicionados, juntamente com quaisquer chamadas API adicionadas no seu projeto.

# <a name="net"></a>[.NET](#tab/net)

Quando adiciona sonímetria de Insights de Aplicação a um ASP.NET projeto do Estúdio Visual, adiciona os seguintes ficheiros:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

São adicionados os seguintes códigos:

- [O nome do seu projeto].csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Pacotes.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout.cshtml

    Se o seu projeto tiver um ficheiro Layout.cshtml, o código abaixo é adicionado.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Quando adiciona supor a Telemetria de Insights de Aplicação a um projeto de modelo de modelo ASP.NET Core do Estúdio Visual, adiciona o seguinte código:

- [O nome do seu projeto].csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appssettings.json:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Passos seguintes

- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)