---
title: Insights de aplicação Azure para aplicações web JavaScript
description: Obtenha a visualização da página e as contagens de sessão, dados do cliente web, Aplicações de página única (SPA) e rastreie os padrões de utilização. Detete exceções e problemas de desempenho em páginas Web de JavaScript.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 5414a70180a82be8253dace7d800c90c1ae6a9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276078"
---
# <a name="application-insights-for-web-pages"></a>Application Insights para páginas Web

Saiba mais sobre o desempenho e a utilização da sua aplicação ou página Web. Se adicionar Insights de [Aplicação](app-insights-overview.md) ao script da sua página, obtém os timings das cargas da página e das chamadas DOAjax, conta e detalhes das exceções do navegador e falhas do AJAX, bem como dos utilizadores e contagens de sessões. Todas estas podem ser segmentadas por página, SO de cliente e versão do browser, geolocalização e outras dimensões. Pode definir alertas em contagens de falhas ou carregamento lento de página. E, ao inserir chamadas de rastreio no seu código JavaScript, pode controlar a utilização das diferentes funcionalidades da sua aplicação da página Web.

O Application Insights pode ser utilizado com quaisquer páginas Web - basta adicionar um pequeno pedaço de JavaScript. Se o seu serviço web for [Java](java-get-started.md) ou [ASP.NET,](asp-net.md)pode utilizar os SDKs do lado do servidor em conjunto com o JavaScript SDK do lado do cliente para obter uma compreensão final do desempenho da sua aplicação.

## <a name="adding-the-javascript-sdk"></a>Adicionando o JavaScript SDK

1. Primeiro precisa de um recurso Application Insights. Se ainda não tiver uma chave de recursos e instrumentação, siga a [criação de novas instruções](create-new-resource.md)de recurso .
2. Copie a chave de instrumentação do recurso onde pretende que a sua telemetria JavaScript seja enviada.
3. Adicione os Insights de Aplicação JavaScript SDK à sua página web ou aplicação através de uma das duas opções seguintes:
    * [configuração npm](#npm-based-setup)
    * [JavaScript Snippet](#snippet-based-setup)

> [!IMPORTANT]
> Utilize apenas um método para adicionar o JavaScript SDK à sua aplicação. Se utilizar a configuração NPM, não utilize o Snippet e vice-versa.

> [!NOTE]
> A Configuração NPM instala o JavaScript SDK como uma dependência do seu projeto, permitindo ao IntelliSense, enquanto o Snippet vai buscar o SDK em tempo de execução. Ambos suportam as mesmas características. No entanto, os desenvolvedores que desejam eventos mais personalizados e configuração geralmente optam pela Configuração NPM, enquanto os utilizadores que procuram uma facilitação rápida de análises web fora da caixa optam pelo Snippet.

### <a name="npm-based-setup"></a>configuração baseada npm

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Configuração baseada em snippet

Se a sua aplicação não utilizar o npm, pode instrumentar diretamente as suas páginas web com Insights de Aplicação, colando este corte no topo de cada uma das suas páginas. De preferência, deve ser o `<head>` primeiro script na sua secção para que possa monitorizar quaisquer problemas potenciais com todas as suas dependências. Se estiver a utilizar a App Blazor Server, adicione `_Host.cshtml` o `<head>` corte na parte superior do ficheiro na secção.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Envio de telemetria para o portal Azure

Por padrão, o JavaScript SDK insights de aplicação recolhe uma série de itens de telemetria que são úteis para determinar a saúde da sua aplicação e a experiência subjacente ao utilizador. Estas incluem:

- **Exceções não apanhadas** na sua app, incluindo informações sobre
    - Vestígios de pilha
    - Detalhes de exceção e mensagem que acompanha o erro
    - Linha & número de erro da coluna
    - URL onde o erro foi levantado
- **Pedidos** de Dependência de Rede feitos pela sua app **XHR** e **Fetch** (recolha de fetch is disabled by default) solicitações, incluem informações sobre
    - Url de fonte de dependência
    - Método de comando & usado para solicitar a dependência
    - Duração do pedido
    - Código de resultados e estado de sucesso do pedido
    - ID (se houver) do utilizador que efaz o pedido
    - Contexto de correlação (se houver) onde o pedido é feito
- **Informações dos utilizadores** (por exemplo, Localização, rede, IP)
- **Informações sobre dispositivos** (por exemplo, Navegador, OS, versão, idioma, resolução, modelo)
- **Informações de sessão**

### <a name="telemetry-initializers"></a>Inicializadores de telemetria
Os iniciais de telemetria são usados para modificar o conteúdo da telemetria recolhida antes de serem enviados do navegador do utilizador. Podem também ser utilizados para impedir que determinada `false`telemetria seja enviada, regressando. Vários iniciantes de telemetria podem ser adicionados à sua instância de Insights de Aplicação, e são executados por ordem de adicioná-los.

O argumento de `addTelemetryInitializer` entrada para um [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) callback que toma `boolean` `void`um argumento e devolve um ou . Se `false`regressar, o item de telemetria não é enviado, então segue para o próximo inseritente de telemetria, se houver, ou é enviado para o ponto final da recolha de telemetria.

Um exemplo de utilização de iniciais de telemetria:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Configuração
A maioria dos campos de configuração são nomeados de tal forma que podem ser indefinidos a falsos. Todos os campos `instrumentationKey`são opcionais, exceto para .

| Nome | Predefinição | Descrição |
|------|---------|-------------|
| instrumentaçãoChave | nulo | **Necessário**<br>Chave de instrumentação que obteve do portal Azure. |
| accountId | nulo | Um ID de conta opcional, se a sua aplicação agrupa os utilizadores em contas. Sem espaços, vírgulas, pontos evígonos, iguais ou barras verticais |
| sessõesRenovam | 1800000 | Uma sessão é registada se o utilizador estiver inativo durante este período de tempo em milissegundos. Padrão é de 30 minutos |
| sessõesValidadeM | 86400000 | Uma sessão é registada se tiver continuado por este período de tempo em milissegundos. Padrão é de 24 horas |
| maxBatchSizeInBytes | 10000 | Tamanho máximo do lote de telemetria. Se um lote exceder este limite, é imediatamente enviado e um novo lote é iniciado |
| maxBatchInterval | 15 000 | Quanto tempo para a telemetria de lote antes de enviar (milissegundos) |
| disableExceptionTracking | false | Se for verdade, as exceções não são recolhidas automaticamente. A predefinição é falso. |
| desativarTelemetria | false | Se for verdade, a telemetria não é recolhida ou enviada. A predefinição é falso. |
| enableDebug | false | Se forem verdadeiros, os dados **internos** de depuração são lançados como uma exceção **em vez** de serem registados, independentemente das definições de registo de SDK. A predefinição é falso. <br>***Nota:*** Permitir esta definição resultará numa telemetria abandonada sempre que ocorrer um erro interno. Isto pode ser útil para identificar rapidamente problemas com a sua configuração ou utilização do SDK. Se não quiser perder a telemetria durante a `consoleLoggingLevel` `telemetryLoggingLevel` depuração, considere usar ou em vez de `enableDebug`. |
| logloggingLevelConsole | 0 | Regista erros **internos** de Insights de aplicação para consolar. <br>0: fora, <br>1: Apenas erros críticos, <br>2: Tudo (erros & avisos) |
| loggingLevelTelemettry | 1 | Envia erros **internos** de insights de aplicação como telemetria. <br>0: fora, <br>1: Apenas erros críticos, <br>2: Tudo (erros & avisos) |
| diagnósticoLogInterval | 10000 | (interno) Intervalo de votação (em ms) para fila de exploração interna |
| amostragemPercentage | 100 | Percentagem de eventos que serão enviados. Padrão é 100, o que significa que todos os eventos são enviados. Detete isto se pretender preservar a sua tampa de dados para aplicações em larga escala. |
| visita de autoTrackPage | false | Se for verdade, numa visão de página, o tempo de visualização da página anterior é rastreado e enviado como telemetria e um novo temporizador é iniciado para a visão de página atual. A predefinição é falso. |
| desativarAjaxTracking | false | Se for verdade, as chamadas do Ajax não são recolhidas automaticamente. A predefinição é falso. |
| desativarFetchTracking | true | Se for verdade, os pedidos de fetch não são recolhidos automaticamente. Padrão é verdade |
| sobrerideçãoPageViewDura | false | Se for verdade, o comportamento padrão do trackPageView é alterado para registar o intervalo de duração da visualização da página quando o trackPageView é chamado. Se for fornecida uma duração falsa e nenhuma duração personalizada para rastrear o PageView, o desempenho da visualização da página é calculado utilizando o tempo de navegação API. A predefinição é falso. |
| maxAjaxCallsPerView | 500 | Padrão 500 - controla quantas chamadas do Ajax serão monitorizadas por visualização de página. Definido para -1 para monitorizar todas as chamadas (ilimitadas) do Ajax na página. |
| desativarDataLossAnalysis | true | Se os amortecedores de para-choques de telemetria internos falsos forem verificados no arranque dos itens ainda não enviados. |
| desativar Corações de Coração | false | Se for falso, o SDK adicionará dois cabeçalhos ('Request-Id' e 'Request-Context') a todos os pedidos de dependência para os correlacionar com os pedidos correspondentes no lado do servidor. A predefinição é falso. |
| correlaçãoHeaderExcluídosDomínios |  | Desativar cabeçalhos de correlação para domínios específicos |
| correlaçãoHeaderDomínios |  | Ativar cabeçalhos de correlação para domínios específicos |
| desativarFlushOn Antes de Descarregar | false | Padrão falso. Se for verdade, o método de descarga não será chamado quando o evento Antes de descarregar dispara |
| enableSessionStorageBuffer | true | Padrão verdadeiro. Se for verdade, o tampão com toda a telemetria não enviada é armazenado no armazenamento da sessão. O tampão é restaurado na carga da página |
| isCookieUseDisabled | false | Padrão falso. Se for verdade, o SDK não armazenará nem lerá quaisquer dados dos cookies.|
| cookieDomínio | nulo | Domínio personalizado de cookies. Isto é útil se quiser partilhar cookies de Insights de Aplicação em subdomínios. |
| isRetryDisabled | false | Padrão falso. Se for falso, retente em 206 (sucesso parcial), 408 (timeout), 429 (pedidos a mais), 500 (erro interno do servidor), 503 (serviço indisponível) e 0 (offline, apenas se detetado) |
| isStorageUseDisabled | false | Se for verdade, o SDK não armazenará nem lerá quaisquer dados do armazenamento local e da sessão. A predefinição é falso. |
| isBeaconApiDisabled | true | Se for falso, o SDK enviará toda a telemetria usando a [API](https://www.w3.org/TR/beacon) do Farol |
| onunloadDisableBeacon | false | Padrão falso. quando o separador estiver fechado, o SDK enviará toda a telemetria restante usando a [API do Farol](https://www.w3.org/TR/beacon) |
| sdkExtension | nulo | Define o nome de extensão sdk. Só são permitidos caracteres alfabéticos. O nome da extensão é adicionado como prefixo à etiqueta 'ai.internal.sdkVersion' (por exemplo, 'ext_javascript:2.0.0'). O padrão é nulo. |
| isBrowserLinkTrackingEnabled | false | A predefinição é falso. Se for verdade, o SDK rastreará todos os pedidos de Link do [Navegador.](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) |
| appId | nulo | O AppId é utilizado para a correlação entre as dependências do AJAX que ocorram do lado do cliente com os pedidos do lado do servidor. Quando a API do Farol está ativada, não pode ser utilizada automaticamente, mas pode ser configurada manualmente na configuração. Padrão é nulo |
| enableCorsCor | false | Se for verdade, o SDK adicionará dois cabeçalhos ('Request-Id' e 'Request-Context') a todos os pedidos corsários para correlacionar as dependências do AJAX cessantes com os pedidos correspondentes no lado do servidor. Padrão é falso |
| nomePrefixo | indefinido | Um valor opcional que será usado como postfixo de nome para site localStorage e nome cookie.
| enableAutoRouteTracking | false | Acompanhe automaticamente as alterações de rota nas Aplicações de Página Única (SPA). Se for verdade, cada alteração de rota enviará uma nova Página para Insights de Aplicação. As mudanças na`example.com/foo#bar`rota hash () também são registadas como novas vistas de página.
| ativarRequestHeaderTracking | false | Se for verdade, os cabeçalhos de pedido do AJAX & Fetch são rastreados, o padrão é falso.
| enableResponseHeaderTracking | false | Se for verdade, os cabeçalhos de resposta do pedido & Do AJAX são rastreados, o padrão é falso.
| distribuídoModo de rastreio | `DistributedTracingModes.AI` | Define o modo de rastreio distribuído. Se AI_AND_W3C modo ou modo W3C estiver definido, os cabeçalhos de contexto do rastreio W3C (traceparent/tracestate) serão gerados e incluídos em todos os pedidos de saída. AI_AND_W3C está prevista para a cocompatibilidade com quaisquer serviços instrumentais de aplicação de aplicação.

## <a name="single-page-applications"></a>Aplicações de página única

Por padrão, este SDK **não** lidará com a mudança de rota baseada no Estado que ocorre em aplicações de página única. Para ativar o rastreio automático de alterações `enableAutoRouteTracking: true` de rota para a sua aplicação de uma única página, pode adicionar à configuração da configuração de configuração.

Atualmente, oferecemos um [plugin React](#react-extensions) separado que pode inicializar com este SDK. Também realizará o rastreio de mudança de rota para si, bem como [recolherá outra telemetria específica do React.](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)

## <a name="react-extensions"></a>Extensões de reação

| Extensões |
|---------------|
| [Reagir](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Reagir Nativo](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Explore os dados do lado do navegador/cliente

Os dados do lado do navegador/cliente podem ser vistos indo para **Métricas** e adicionando métricas individuais em que está interessado:

![](./media/javascript/page-view-load-time.png)

Também pode ver os seus dados a partir do JavaScript SDK através da experiência do Navegador no portal.

Selecione **Browser** e, em seguida, escolha **Falhas** ou **Desempenho**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Desempenho

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Dependências

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Análise

Para consultar a sua telemetria recolhida pelo JavaScript SDK, selecione o botão **'Ver em Registos ( Analytics).** Ao adicionar `where` uma `client_Type == "Browser"`declaração de , só verá dados do JavaScript SDK e qualquer telemetria do lado do servidor recolhida por outros SDKs será excluída.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Suporte do mapa de origem

A pilha de chamadas minificada da sua telemetria de exceção pode ser desminificada no portal Azure. Todas as integrações existentes no painel Dedetalhes de Exceção trabalharão com a nova pilha de chamadas não minizada.

#### <a name="link-to-blob-storage-account"></a>Link para a conta de armazenamento Blob

Pode ligar o seu recurso Application Insights ao seu próprio recipiente de armazenamento Azure Blob a pilhas de chamadas automaticamente inministíveis. Para começar, consulte o [suporte automático do mapa de origem.](./source-map-support.md)

### <a name="drag-and-drop"></a>Arrastar e largar

1. Selecione um item de Telemetria de Exceção no portal Azure para ver os seus "detalhes de transação de ponta a ponta"
2. Identifique quais mapas de origem correspondem a esta pilha de chamadas. O mapa de origem deve coincidir com o ficheiro fonte de uma pilha, mas sufixo com`.map`
3. Arraste e deixe cair os mapas de origem na pilha de chamadas no portal Azure![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Insights de aplicação Web Basic

Para uma experiência leve, pode, em vez disso, instalar a versão básica do Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Esta versão vem com o número mínimo de funcionalidades e funcionalidades e conta consigo para construí-la como entender. Por exemplo, não executa nenhuma recolha automática (exceções não capturadas, AJAX, etc.). As APIs para enviar certos tipos `trackTrace` `trackException`de telemetria, como, etc., não estão incluídas nesta versão, pelo que terá de fornecer o seu próprio invólucro. A única API disponível é. `track` Uma [amostra](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) está localizada aqui.

## <a name="examples"></a>Exemplos

Para exemplos runnáveis, consulte [Amostras JavaScript SDK](https://github.com/topics/applicationinsights-js-demo) de Aplicação Insights

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Upgrade a partir da versão antiga de Application Insights

Alterações de rutura na versão SDK V2:
- Para permitir melhores assinaturas de API, algumas das chamadas DaPI, como trackPageView e trackException, foram atualizadas. A execução no Internet Explorer 8 e versões anteriores do navegador não são suportadas.
- O envelope de telemetria tem nome de campo e alterações de estrutura devido a atualizações de esquemade dados.
- Mudou-se `context.operation` para. `context.telemetryTrace` Alguns campos também`operation.id` --> `telemetryTrace.traceID`foram alterados ( ).
  - Para refrescar manualmente o ID atual da página (por exemplo, em aplicações SPA), use `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`.
    > [!NOTE]
    > Para manter o trace ID único, `Util.newId()`onde `Util.generateW3CId()`já utilizou, agora use . Ambos acabam por ser a identidade da operação.

Se estiver a utilizar os insights de aplicação atuais PRODUCTION SDK (1.0.20) e quiser ver se o novo SDK funciona em tempo de funcionamento, atualize o URL dependendo do cenário atual de carregamento de SDK.

- Baixar através do cenário CDN: Atualize o código que utiliza atualmente para apontar para o seguinte URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- cenário npm: `downloadAndSetup` Ligue para descarregar o script completo applicationInsights da CDN e rubricar-o com chave de instrumentação:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

O teste em ambiente interno para verificar a telemetria de monitorização está a funcionar como esperado. Se tudo funcionar, atualize as suas assinaturas API adequadamente para a versão SDK V2 e implemente nos seus ambientes de produção.

## <a name="sdk-performanceoverhead"></a>Desempenho/sobrecarga sDK

Com apenas 25 KB gzipped, e levando apenas ~15 ms para inicializar, Application Insights adiciona uma quantidade insignificante de tempo de carga ao seu website. Utilizando o corte, os componentes mínimos da biblioteca são rapidamente carregados. Entretanto, o script completo é descarregado em segundo plano.

Enquanto o script está a ser descarregado a partir do CDN, todo o rastreio da sua página está na fila. Uma vez que o script descarregado termina assincronicamente inicializando, todos os eventos que foram em fila são rastreados. Como resultado, não perderá nenhuma telemetria durante todo o ciclo de vida da sua página. Este processo de configuração fornece à sua página um sistema de análise sem emenda, invisível para os seus utilizadores.

> Resumo:
> - **25 KB** gzipped
> - **15 ms** tempo de inicialização geral
> - **Rastreio zero** perdido durante ciclo de vida da página

## <a name="browser-support"></a>Browser support (Suporte do browser)

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Ópera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Última saque ✔ |  Última ✔ do Firefox | IE 9+ & Edge ✔ | Ópera Última ✔ | Última saque de ✔ do Safari |

## <a name="open-source-sdk"></a>SDK de código aberto

O Aplicativo Insights JavaScript SDK é de código aberto para ver o código fonte ou para contribuir para o projeto visitar o [repositório oficial gitHub](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next-steps"></a><a name="next"></a>Próximos passos
* [Controlar a utilização](usage-overview.md)
* [Métricas e eventos personalizados](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
