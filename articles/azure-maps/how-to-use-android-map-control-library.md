---
title: Começar com o controlo de mapas Android / Microsoft Azure Maps
description: Neste artigo você vai aprender, como começar com o controle de mapa Android usando o Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6e0f0f311b7ec8adae6ddb25e01046141adadfa4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548545"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Começando com Azure Maps Android SDK

O Azure Maps Android SDK é uma biblioteca de mapas vetoriais para Android. Este artigo guia-o através dos processos de instalação do Azure Maps Android SDK e do carregamento de um mapa.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Para completar os procedimentos neste artigo, é necessário criar [primeiro uma conta Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps) no nível de preços S1 e obter a chave [principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para a sua conta.

Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Baixar Android Studio

Baixe o Android Studio e crie um projeto com uma atividade vazia antes de instalar o Azure Maps Android SDK. Você pode [baixar Android Studio](https://developer.android.com/studio/) gratuitamente a partir do Google. 

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Primeiro, crie um novo projeto com uma atividade vazia. Complete estes passos para criar um projeto Android Studio:

1. Em **'Escolha o seu projeto**' selecione Telefone e **Tablet**. A sua aplicação será executada neste fator de formulário.
2. No separador **Telefone e Tablet,** selecione **Atividade Vazia**, e, em seguida, selecione **Seguinte**.
3. Em **Configurar o seu projeto,** selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo. Esta é a versão mais antiga suportada pelo Azure Maps Android SDK.
4. Aceite o `Activity Name` `Layout Name` padrão e selecione **Terminar**.

Consulte a [documentação](https://developer.android.com/studio/intro/) do Android Studio para obter mais ajuda na instalação do Android Studio e na criação de um novo projeto.

![Criar um projeto em estúdio Android ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurar um dispositivo virtual

O Android Studio permite-lhe configurar um dispositivo Android virtual no seu computador. Fazê-lo pode ajudá-lo a testar a sua aplicação durante o desenvolvimento. Para configurar um dispositivo virtual, selecione o ícone do Gestor de Gestor de Dispositivo Virtual Android (AVD) no canto superior direito do ecrã do seu projeto e, em seguida, selecione **Create Virtual Device**. Também pode chegar ao Gestor AVD selecionando **Ferramentas** > **Android** > **AVD Manager** a partir da barra de ferramentas. Na categoria **Telefones,** selecione **Nexus 5X**, e, em seguida, selecione **Next**.

Você pode saber mais sobre a configuração de um AVD na [documentação](https://developer.android.com/studio/run/managing-avds)do Android Studio .

![Emulador Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instale o Azure Maps Android SDK

O próximo passo na construção da sua aplicação é instalar o Azure Maps Android SDK. Complete estes passos para instalar o SDK:

1. Abra o ficheiro **build.gradle** de nível superior e adicione o seguinte código a **todos os projetos,** secção de blocos de **repositórios:**

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualize a sua **app/build.gradle** e adicione-lhe o seguinte código:
    
    1. Certifique-se de que o **minSdkVersion** do seu projeto está na API 21 ou superior.

    2. Adicione o seguinte código à secção Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Atualize o bloco de dependências e adicione uma nova linha de dependência de implementação para o mais recente Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. Vá ao **Ficheiro** na barra de ferramentas e clique em **Sync Project com Ficheiros Gradle**.
3. Adicione um fragmento de mapa à \> \> atividade\_principal (atividade de layout res main.xml):
    
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
            />
    </FrameLayout>
    ```

4. No ficheiro **MainActivity.java** terá de:
    
    * adicionar importações para o Azure Maps SDK
    * definir as suas informações de autenticação do Azure Maps
    * obter a instância de controlo do mapa no método **onCreate**

    Definir as informações `AzureMaps` de autenticação `setSubscriptionKey` na `setAadProperties` classe globalmente utilizando os ou métodos, faz com que não tenha de adicionar as suas informações de autenticação em todas as vistas. 

    O controlo do mapa contém os seus próprios métodos de ciclo de vida para gerir o ciclo de vida OpenGL do Android. Estes métodos de ciclo de vida devem ser chamados diretamente da atividade contendo. Para que a sua aplicação ligue corretamente para os métodos de ciclo de vida do controlo do mapa, deve substituir os seguintes métodos de ciclo de vida na Atividade que contém o controlo do mapa. E, deve chamar o respetivo método de controlo de mapas. 

    * onCreate(Bundle) 
    * onIniciar() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Bundle) 
    * onLowMemory() 

    Editar o ficheiro **MainActivity.java** da seguinte forma:
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
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

## <a name="import-classes"></a>Classes de importação

Depois de completar os passos anteriores, provavelmente receberá avisos do Android Studio sobre alguns dos códigos. Para resolver estes avisos, importe as classes referenciadas em `MainActivity.java`.

Pode importar automaticamente estas classes selecionando Alt+Enter (Opção+Retorno num Mac).

Selecione o botão de execução, como mostrado no gráfico seguinte (ou prima Control+R num Mac), para construir a sua aplicação.

![Clique em Executar](./media/how-to-use-android-map-control-library/run-app.png)

O Android Studio levará alguns segundos para construir a aplicação. Depois de concluída a construção, pode testar a sua aplicação no dispositivo Android emulado. Devia ver um mapa como este:

<center>

![Mapas Azure na aplicação Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Localização do mapa

O Azure Maps Android SDK fornece três formas diferentes de definir o idioma e a vista regional do mapa. O código que se segue mostra como definir a língua para francês ("fr-FR") e a visão regional para "auto". 

A primeira opção é passar a língua `AzureMaps` e ver `setLanguage` `setView` a informação regional na classe usando a estática e os métodos a nível global. Isto definirá a linguagem padrão e a vista regional em todos os controlos do Azure Maps carregados na sua aplicação.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

A segunda opção é passar a linguagem e ver a informação no controlo do mapa XML.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A terceira opção é definir programáticamente a linguagem e `setStyle` a visão regional do mapa utilizando o método dos mapas. Isto pode ser feito a qualquer momento para alterar a linguagem e a visão regional do mapa.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Aqui está um exemplo de Azure Maps com a linguagem definida para "fr-FR" e vista regional definida para "auto".

<center>

![Azure Maps, imagem do mapa mostrando rótulos em francês](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Uma lista completa de línguas apoiadas e pontos de vista regionais é documentada [aqui.](supported-languages.md)

## <a name="navigating-the-map"></a>Navegar no mapa

Existem várias maneiras diferentes em que o mapa pode ser zoomed, panned, rodado e arremessado. Os seguintes detalhes são todas as diferentes formas de navegar no mapa.

**Zoom o mapa**

- Toque no mapa com dois dedos e belisque juntos para ampliar ou espalhar os dedos para ampliar.
- Toque duas vezes no mapa para ampliar num nível.
- Toque duas vezes com dois dedos para ampliar o mapa de um nível.
- Toque duas vezes; na segunda torneira, segure o dedo no mapa e arraste para o zoom, ou para baixo para fazer zoom para fora.

**Pan o mapa**

- Toque no mapa e arraste em qualquer direção.

**Rode o mapa**

- Toque no mapa com dois dedos e rode.

**Lançar o mapa**

- Toque no mapa com dois dedos e arraste-os juntos para cima ou para baixo.

## <a name="next-steps"></a>Passos seguintes

Saiba como adicionar dados sobrepostos no mapa:

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo a um mapa Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicione formas a um mapa Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Alterar estilos de mapa em mapas Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
