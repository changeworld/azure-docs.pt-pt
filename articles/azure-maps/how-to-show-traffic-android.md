---
title: Mostrar dados de tráfego no mapa android / Microsoft Azure Maps
description: Neste artigo você vai aprender, como exibir dados de tráfego em um mapa usando o Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e5611eeb08ac370e12cf452d57a87e449fbd80da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335382"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Mostre dados de tráfego no mapa usando Azure Maps Android SDK

Dados de fluxo e dados de incidentes são os dois tipos de dados de tráfego que podem ser exibidos no mapa. Este guia mostra-lhe como exibir ambos os tipos de dados de tráfego. Os dados relativos aos incidentes consistem em dados de pontos e de linha para coisas como construções, encerramentos de estradas e acidentes. Os dados de fluxo mostram métricas sobre o fluxo de tráfego na estrada.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder mostrar o tráfego no mapa, tem de [fazer uma Conta Azure](quick-demo-map-app.md#create-an-account-with-azure-maps)e obter uma chave de [subscrição](quick-demo-map-app.md#get-the-primary-key-for-your-account). Depois, é necessário instalar o [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) e carregar um mapa.

## <a name="incidents-traffic-data"></a>Incidentes dados de tráfego 

Terá de importar as seguintes `setTraffic` bibliotecas para ligar e: `incidents`

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 O seguinte código de corte mostra-lhe como exibir dados de tráfego no mapa. Passamos um valor booleano ao `incidents` método, `setTraffic` e passamos isso para o método. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Dados de tráfego de fluxo

Primeiro terá de importar as seguintes `setTraffic` `flow`bibliotecas para ligar e:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Utilize o seguinte código para definir os dados de fluxo de tráfego. Semelhante ao código na secção anterior, passamos `flow` o valor `setTraffic` de retorno do método ao método. Há quatro valores que `flow`podem ser passados `flow` para, e cada valor desencadearia a devolução do respetivo valor. O valor `flow` de retorno será então `setTraffic`passado como argumento para . Consulte a tabela abaixo para estes quatro valores:

| | |
| :-- | :-- |
| Fluxo de Tráfego.NENHUM | Não exibe dados de tráfego no mapa |
| Fluxo de Tráfego.RELATIVE | Mostra dados de tráfego relativos à velocidade de fluxo livre da estrada |
| TrafficFlow.RELATIVE_DELAY | Exibe áreas mais lentas do que o atraso esperado médio |
| Fluxo de Tráfego.ABSOLUTE | Mostra a velocidade absoluta de todos os veículos na estrada |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Mostre dados de tráfego de incidentes clicando numa funcionalidade

Para obter os incidentes para uma característica específica, pode utilizar o código abaixo. Quando uma funcionalidade é clicada, a lógica do código verifica incidentes e constrói uma mensagem sobre o incidente. Uma mensagem aparece na parte inferior do ecrã com os detalhes.

1. Primeiro, é necessário editar **res > layout > activity_main.xml,** para que se pareça com o que está por baixo. Pode substituir `mapcontrol_centerLat`o, `mapcontrol_centerLng` `mapcontrol_zoom` e os valores desejados. Lembre-se, o nível de zoom é um valor entre 0 e 22. No nível de zoom 0, o mundo inteiro encaixa-se num único azulejo.

   ```XML
   <?xml version="1.0" encoding="utf-8"?>
   <FrameLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       >
    
       <com.microsoft.azure.maps.mapcontrol.MapControl
           android:id="@+id/mapcontrol"
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Adicione o seguinte código ao seu ficheiro **MainActivity.java.** O pacote está incluído por defeito, por isso certifique-se de manter o seu pacote no topo.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
           });
       }

       @Override
       public void onResume() {
           super.onResume();
           mapControl.onResume();
       }

       @Override
       protected void onStart(){
           super.onStart();
           mapControl.onStart();
       }

       @Override
       public void onPause() {
           super.onPause();
           mapControl.onPause();
       }

       @Override
       public void onStop() {
           super.onStop();
           mapControl.onStop();
       }

       @Override
       public void onLowMemory() {
           super.onLowMemory();
           mapControl.onLowMemory();
       }

       @Override
       protected void onDestroy() {
           super.onDestroy();
           mapControl.onDestroy();
       }

       @Override
       protected void onSaveInstanceState(Bundle outState) {
           super.onSaveInstanceState(outState);
           mapControl.onSaveInstanceState(outState);
       }
   }
   ```

3. Assim que incorporar o código acima na sua aplicação, poderá clicar numa funcionalidade e ver os detalhes dos incidentes de trânsito. Dependendo da latitude, longitude e dos valores de nível de zoom que usou no seu ficheiro **activity_main.xml,** verá resultados semelhantes à seguinte imagem:

   <center>

   ![Incidente-tráfego no mapa](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes guias para saber como adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de mosaico](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Adicione formas ao mapa android](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Apresentar informações da funcionalidade](display-feature-information-android.md)
