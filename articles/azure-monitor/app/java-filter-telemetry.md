---
title: Filter Azure Application Insights telemetria na sua aplicação web Java
description: Reduza o tráfego de telemetria filtrando os eventos que não precisa de monitorizar.
ms.topic: conceptual
ms.date: 3/14/2019
ms.openlocfilehash: 020e54132e0ca0a9f9ccf0236f94515877015637
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659922"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrar telemetria na sua aplicação web Java

Os filtros fornecem uma forma de selecionar a telemetria que a sua [aplicação web Java envia para Application Insights](java-get-started.md). Existem alguns filtros fora da caixa que pode usar, e também pode escrever os seus próprios filtros personalizados.

Os filtros fora da caixa incluem:

* Nível de gravidade dos vestígios
* URLs específicos, palavras-chave ou códigos de resposta
* Respostas rápidas - isto é, pedidos aos quais a sua app respondeu rapidamente
* Nomes específicos de eventos

> [!NOTE]
> Os filtros distorcem as métricas da sua aplicação. Por exemplo, pode decidir que, para diagnosticar respostas lentas, irá definir um filtro para descartar tempos de resposta rápidos. Mas deve estar ciente de que os tempos médios de resposta reportados pela Application Insights serão então mais lentos do que a velocidade real, e a contagem de pedidos será menor do que a contagem real.
> Se isto for uma preocupação, utilize a [Amostragem.](../../azure-monitor/app/sampling.md)

## <a name="setting-filters"></a>Definição de filtros

Em ApplicationInsights.xml, `TelemetryProcessors` adicione uma secção como este exemplo:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Inspecione todo o conjunto de processadores incorporados](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Filtros embutidos

### <a name="metric-telemetry-filter"></a>Filtro de Telemetria Métrica

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded`- Lista separada da vírposta de nomes métricos personalizados.


### <a name="page-view-telemetry-filter"></a>Filtro de telemetria de vista de página

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS`- A duração refere-se ao tempo de carga da página. Se isto for definido, as páginas que carregaram mais rápido do que desta vez não são reportadas.
* `NotNeededNames`- Lista separada de nomes de páginas.
* `NotNeededUrls`- Lista separada da vírposta de fragmentos de URL. Por exemplo, `"home"` filtra todas as páginas que têm "casa" no URL.


### <a name="request-telemetry-filter"></a>Solicitar filtro de telemetria


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filtro Fonte Sintética

Filtra toda a telemetria que tem valores na propriedade SyntheticSource. Estes incluem pedidos de bots, aranhas e testes de disponibilidade.

Filtrar a telemetria para todos os pedidos sintéticos:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtrar a telemetria para fontes sintéticas específicas:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded`- Lista separada da vírposta de nomes de origem sintética.

### <a name="telemetry-event-filter"></a>Filtro de evento de telemetria

Filtra eventos personalizados (registados utilizando [trackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames`- Lista separada de nomes de eventos.


### <a name="trace-telemetry-filter"></a>Trace Telemettry filtro

Filtra os vestígios de registo (registados utilizando [trackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) ou um [coletor](java-trace-logs.md)de quadros de registo).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel`os valores válidos são:
  *  OFF - Filtrar todos os vestígios
  *  TRACE - Sem filtragem. igual ao nível de traço
  *  INFO - Filtrar o nível TRACE
  *  WARN - Filtrar o TRACE e a INFO
  *  ERROR - Filter out WARN, INFO, TRACE
  *  CRITICAL - filtrar tudo menos CRÍTICO


## <a name="custom-filters"></a>Filtros personalizados

### <a name="1-code-your-filter"></a>1. Código o seu filtro

No seu código, crie `TelemetryProcessor`uma classe que implemente:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Invoque o seu filtro no ficheiro de configuração

Em ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. Invoque o seu filtro (Java Spring)

Para aplicações baseadas no quadro da primavera, os processadores de telemetria personalizados devem ser registados na sua classe de aplicação principal como feijão. Serão então autoligados quando a aplicação começar.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Terá de criar os seus próprios parâmetros de filtro `application.properties` e alavancar a estrutura de configuração externa da Spring Boot para passar esses parâmetros no seu filtro personalizado. 


## <a name="troubleshooting"></a>Resolução de problemas

*O meu filtro não está a funcionar.*

* Verifique se forneceu valores de parâmetroválidoválidos válidos. Por exemplo, as durações devem ser inteiros. Valores inválidos farão com que o filtro seja ignorado. Se o seu filtro personalizado lançar uma exceção a partir de um método de construção ou conjunto, será ignorado.

## <a name="next-steps"></a>Passos seguintes

* [Amostragem](../../azure-monitor/app/sampling.md) - Considere a amostragem como uma alternativa que não distorça as suas métricas.
