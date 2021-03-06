---
title: Introdução aos Hubs de Notificação do Azure utilizando o Baidu | Microsoft Docs
description: Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para emitir notificações via push para dispositivos Android que utilizam o Baidu.
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: mobile-baidu
ms.workload: mobile
ms.date: 03/18/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 06/19/2019
ms.openlocfilehash: b9ca241b5deebc4d23e0ff648eddee0782617e0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536994"
---
# <a name="get-started-with-notification-hubs-using-baidu"></a>Introdução aos Hubs de Notificação utilizando o Baidu

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

O Baidu Cloud Push é um serviço em nuvem chinês que pode utilizar para enviar notificações push para dispositivos móveis.

Como o Google Play e o FCM (Firebase Cloud Messaging) não estão disponíveis na China, é necessário utilizar várias lojas de aplicações e serviços push. O Baidu é um deles e o utilizado atualmente pelo Hub de Notificação.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer:

* SDK Android (partimos do princípio que utiliza o Android Studio), que pode transferir a partir do [site do Android](https://go.microsoft.com/fwlink/?LinkId=389797)
* [Android SDK do Baidu Push]

> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).

Para começar, faça o seguinte:

1. Crie uma conta Baidu.
2. Crie um projeto de empurrar a nuvem baidu e tome nota da chave API e chave secreta.

## <a name="configure-a-new-notification-hub"></a>Configurar um novo Notification Hub

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. No hub de notificação, selecione **Serviços de Notificação** e, em seguida, **Baidu (Android China)**.

&emsp;&emsp;&emsp;&emsp;![Hubs de Notificação do Azure – Baidu](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

&emsp;&emsp;7. Desloque-se para baixo até à secção de definições de notificação do Baidu. Introduza a chave de API e a chave secreta que obteve na consola do Baidu, no projeto Baidu Cloud Push. Em seguida, clique em Guardar.

&emsp;&emsp;&emsp;&emsp;![Hubs de Notificação do Azure – Segredos do Baidu](./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png)

O seu hub de notificação está agora configurado para funcionar com o Baidu. Dispõe também de **cadeias de ligação** para registar a sua aplicação para enviar e receber notificações push.

Tome nota da `DefaultListenSharedAccessSignature` e da `DefaultFullSharedAccessSignature` na janela de informações de ligação de acesso.

## <a name="connect-your-app-to-the-notification-hub"></a>Ligar a aplicação ao Notification Hub

1. No Android Studio, crie um novo projeto Android (Ficheiro > Novo > Novo Projeto).

    ![Hubs de Notificação do Azure – Novo Projeto Baidu](./media/notification-hubs-baidu-get-started/AndroidNewProject.png)

2. Introduza um Nome da Aplicação e certifique-se de que a versão do SDK Mínimo Necessário está definida como API 16: Android 4.1. **Certifique-se também de que o seu nome do pacote (应用包名) é igual ao do Portal Push da Cloud do Baidu**

    ![Hubs de notificação Azure -](./media/notification-hubs-baidu-get-started/AndroidMinSDK.png) ![Baidu Min SDK1 Azure Centros de Notificação - Baidu Min SDK2](./media/notification-hubs-baidu-get-started/AndroidMinSDK2.png)

3. Clique em Seguinte e continue a seguir o assistente até aparecer a janela Criar Atividade. Certifique-se de que a opção Atividade Vazia está selecionada e, por fim, selecione Concluir para criar uma nova Aplicação Android.

    ![Hubs de Notificação do Azure – Adicionar Atividade do Baidu](./media/notification-hubs-baidu-get-started/AndroidAddActivity.png)

4. Certifique-se de que a Compilação do Projeto de Destino está definida corretamente.

5. Em seguida, adicione bibliotecas dos Hubs de Notificação do Azure. No ficheiro `Build.Gradle` da aplicação, adicione as linhas seguintes na secção de dependências.

    ```javascript
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

    Adicione o repositório seguinte depois da secção de dependências.

    ```javascript
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

    Para evitar o conflito da Lista, adicione `Manifest.xml` o seguinte código no ficheiro do projeto:

    ```xml
    <manifest package="YOUR.PACKAGE.NAME"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:android="http://schemas.android.com/apk/res/android">
    ```

    e na etiqueta `<application/>`:

    ```xml
    <application
        tools:replace="android:allowBackup,icon,theme,label">
    ```

6. Transfira e descomprima o [SDK Android do Baidu Push](https://push.baidu.com/doc/android/api). Copie o ficheiro `pushservice-x.y.z jar` na pasta de bibliotecas. Em seguida, copie os ficheiros `.so` nas pastas `src/main/jniLibs` (criar uma nova pasta) da sua aplicação Android.

    ![Hubs de Notificação do Azure – Bibliotecas do SDK do Baidu](./media/notification-hubs-baidu-get-started/BaiduSDKLib.png)

7. Na pasta do `libs` projeto, clique `pushervice-x.y.z.jar` no ficheiro; selecione **Adicionar como Biblioteca** a incluir esta biblioteca no projeto.

    ![Hubs de Notificação do Azure – Adicionar Como Biblioteca do Baidu](./media/notification-hubs-baidu-get-started/BaiduAddAsALib.jpg)

8. Abra o ficheiro `AndroidManifest.xml` do projeto Android e adicione as permissões exigidas pelo Baidu SDK. **Substitua `YOURPACKAGENAME` pelo nome do pacote**.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
    <uses-permission android:name="android.permission.VIBRATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
    <uses-permission android:name="android.permission.EXPAND_STATUS_BAR" />
    !! <uses-permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME" />
    !!<permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME"android:protectionLevel="normal" />

    ```

9. Adicione a seguinte configuração no elemento da aplicação depois do elemento de atividade `.MainActivity` ao substituir *nomedoprojeto* (por exemplo, `com.example.BaiduTest`):

    ```xml
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaViewActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaViewActivity" />
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaListActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaListActivity"
        android:launchMode="singleTask" />

    <!-- Push application definition message -->
    <receiver android:name=".MyPushMessageReceiver">
        <intent-filter>

            <!-- receive push message-->
            <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
            <!-- receive bind,unbind,fetch,delete.. message-->
            <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
            <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.PushServiceReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
            <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
            <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            <action android:name="com.baidu.android.pushservice.action.media.CLICK" />
            <action android:name="android.intent.action.MEDIA_MOUNTED" />
            <action android:name="android.intent.action.USER_PRESENT" />
            <action android:name="android.intent.action.ACTION_POWER_CONNECTED" />
            <action android:name="android.intent.action.ACTION_POWER_DISCONNECTED" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.RegistrationReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.METHOD" />
            <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
        </intent-filter>
        <intent-filter>
            <action android:name="android.intent.action.PACKAGE_REMOVED" />

            <data android:scheme="package" />
        </intent-filter>
    </receiver>

    <service
        android:name="com.baidu.android.pushservice.PushService"
        android:exported="true"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
        </intent-filter>
    </service>

    <service
        android:name="com.baidu.android.pushservice.CommandService"
        android:exported="true" />

    <!-- Adapt the ContentProvider declaration required for the Android N system, and the write permissions include the application package name-->
    <provider
        android:name="com.baidu.android.pushservice.PushInfoProvider"
        android:authorities="com.baidu.push.example.bdpush"
        android:exported="true"
        android:protectionLevel="signature"
        android:writePermission="baidu.push.permission.WRITE_PUSHINFOPROVIDER. yourprojectname  " />

    <!-- API Key of the Baidu application -->
    <meta-data
        android:name="api_key"
        !!   android:value="api_key" />
    </application>
    ```

10. Adicione uma nova classe denominada `ConfigurationSettings.java` ao projeto.

    ```java
    public class ConfigurationSettings {
        public static String API_KEY = "...";
        public static String NotificationHubName = "...";
        public static String NotificationHubConnectionString = "...";
    }
    ```

    Defina o valor da cadeia `API_KEY` com a API_KEY do projeto Baidu Cloud.

    Defina o valor da cadeia `NotificationHubName` com o nome do hub de notificação a partir do [portal do Azure] e, em seguida, `NotificationHubConnectionString` com `DefaultListenSharedAccessSignature` a partir do [portal do Azure].

11. Abra MainActivity.java e adicione o seguinte ao método onCreate:

    ```java
    PushManager.startWork(this, PushConstants.LOGIN_TYPE_API_KEY,  API_KEY );
    ```

12. Adicione uma nova classe denominada `MyPushMessageReceiver.java` e adicione o código seguinte. Esta é a classe que processa as notificações push que são recebidas do servidor push do Baidu.

    ```java
    package your.package.name;

    import android.content.Context;
    import android.content.Intent;
    import android.os.AsyncTask;
    import android.text.TextUtils;
    import android.util.Log;

    import com.baidu.android.pushservice.PushMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import org.json.JSONException;
    import org.json.JSONObject;

    import java.util.List;

    public class MyPushMessageReceiver extends PushMessageReceiver {

        public static final String TAG = MyPushMessageReceiver.class
                .getSimpleName();
        public static NotificationHub hub = null;
        public static String mChannelId, mUserId;

        @Override
        public void onBind(Context context, int errorCode, String appid,
                        String userId, String channelId, String requestId) {
            String responseString = "onBind errorCode=" + errorCode + " appid="
                    + appid + " userId=" + userId + " channelId=" + channelId
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

            if (errorCode == 0) {
                // Binding successful
                Log.d(TAG, " Binding successful");
            }
            try {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName,
                            ConfigurationSettings.NotificationHubConnectionString,
                            context);
                    Log.i(TAG, "Notification hub initialized");
                }
            } catch (Exception e) {
                Log.e(TAG, e.getMessage());
            }
            mChannelId = channelId;
            mUserId = userId;

            registerWithNotificationHubs();
        }
        private void registerWithNotificationHubs() {

            new AsyncTask<Void, Void, Void>() {
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        hub.registerBaidu(mUserId, mChannelId);
                        Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                    } catch (Exception e) {
                        Log.e(TAG, e.getMessage());
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

        @Override
        public void onMessage(Context context, String message,
                            String customContentString) {
            String messageString = " onMessage=\"" + message
                    + "\" customContentString=" + customContentString;
            Log.d(TAG, messageString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }

        }

        @Override
        public void onNotificationArrived(Context context, String title, String description, String customContentString) {
            String notifyString = " Notice Arrives onNotificationArrived  title=\"" + title
                    + "\" description=\"" + description + "\" customContent="
                    + customContentString;
            Log.d(TAG, notifyString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
        }

        @Override
        public void onNotificationClicked(Context context, String title, String description, String customContentString) {
            String notifyString = " onNotificationClicked title=\"" + title + "\" description=\""
                    + description + "\" customContent=" + customContentString;
            Log.d(TAG, notifyString);
            Intent intent = new Intent(context.getApplicationContext(),MainActivity.class);
            intent.putExtra("title",title);
            intent.putExtra("description",description);
            intent.putExtra("isFromNotify",true);
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            context.getApplicationContext().startActivity(intent);

        }

        @Override
        public void onSetTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onSetTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

        }

        @Override
        public void onDelTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onDelTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

        }

        @Override
        public void onListTags(Context context, int errorCode, List<String> tags,
                            String requestId) {
            String responseString = "onListTags errorCode=" + errorCode + " tags="
                    + tags;
            Log.d(TAG, responseString);

        }

        @Override
        public void onUnbind(Context context, int errorCode, String requestId) {
            String responseString = "onUnbind errorCode=" + errorCode
                    + " requestId = " + requestId;
            Log.d(TAG, responseString);

            if (errorCode == 0) {
                // Unbinding is successful
                Log.d(TAG, " Unbinding is successful ");
            }
        }
    }
    ```

## <a name="send-notifications-to-your-app"></a>Enviar notificações para a sua aplicação

Pode testar rapidamente a receção das notificações no [portal do Azure]: utilize o botão **Enviar** no ecrã de configuração do hub de notificação, conforme mostrado nos seguintes ecrãs:

![](./media/notification-hubs-baidu-get-started/BaiduTestSendButton.png)
![](./media/notification-hubs-baidu-get-started/BaiduTestSend.png)

Normalmente, as notificações push são enviadas num serviço de back-end como os Mobile Services ou ASP.NET utilizando uma biblioteca compatível. Caso não esteja disponível uma biblioteca para o back-end, pode utilizar a API REST diretamente para enviar mensagens de notificação.

Para ser mais simples, este tutorial utiliza uma aplicação de consola como demonstração sobre como enviar uma notificação com o SDK .NET. No entanto, recomendamos o tutorial [Utilizar Hubs de Notificação para notificações push a utilizadores](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) como passo seguinte para enviar notificações de um back-end do ASP.NET. 

Seguem-se diferentes abordagens para enviar notificações:

* **Interface REST**: pode suportar notificações em qualquer plataforma de back-end utilizando a [Interface REST](https://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **SDK .NET dos Notification Hubs do Microsoft Azure**: no Gestor de Pacotes Nuget para Visual Studio, execute [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* **Node.js**: [como utilizar os Hubs de Notificação do Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* **Aplicações Móveis**: para obter um exemplo de como enviar notificações de um back-end das Aplicações Móveis do Serviço de Aplicações do Azure integrado aos Hubs de Notificação, veja [Add push notifications to your mobile app (Adicionar notificações push à sua aplicação móvel)](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).
* **Java/PHP**: para obter um exemplo de como enviar notificações com as APIs REST, consulte “Como utilizar os Hubs de Notificação de Java/PHP” ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="optional-send-notifications-from-a-net-console-app"></a>(Opcional) Envie notificações a partir de uma aplicação de consola do .NET.

Nesta secção, mostramos como enviar uma notificação com uma aplicação de consola do .NET.

1. Crie uma nova aplicação de consola do Visual c#:

    ![](./media/notification-hubs-baidu-get-started/ConsoleProject.png)

2. Na janela Consola do Gestor de Pacotes, defina o **Projeto predefinido** como o novo projeto da aplicação de consola e, em seguida, execute o seguinte comando na janela da consola:

    ```shell
    Install-Package Microsoft.Azure.NotificationHubs
    ```

    Esta instrução adiciona uma referência ao SDK dos Hubs de Notificação do Azure através do [Pacote NuGet Microsoft.Azure.Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Abra o ficheiro `Program.cs` e adicione a seguinte instrução:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

4. Na classe `Program`, adicione o seguinte método e substitua `DefaultFullSharedAccessSignatureSASConnectionString` e `NotificationHubName` pelos valores que tem.

    ```csharp
    private static async void SendNotificationAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
        string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
        var result = await hub.SendBaiduNativeNotificationAsync(message);
    }
    ```

5. Adicione as seguintes linhas no método `Main`:

    ```csharp
    SendNotificationAsync();
    Console.ReadLine();
    ```

## <a name="test-your-app"></a>Testar a aplicação

Para testar esta aplicação com um telefone real, basta ligar o telefone ao computador utilizando um cabo USB. Esta ação carrega a aplicação para o telefone anexado.

Para testar esta aplicação com o emulador, na barra de ferramentas superior do Android Studio, clique em **Executar** e selecione a aplicação: inicia o emulador, carrega e executa a aplicação.

A aplicação obtém o `userId` e o `channelId` a partir do serviço de notificações do Baidu Push e regista-os no hub de notificação.

Para enviar uma notificação de teste, pode utilizar o separador de depuração do [portal do Azure]. Se criou a aplicação de consola do .NET para Visual Studio, basta premir a tecla F5 no Visual Studio para executar a aplicação. A aplicação envia uma notificação apresentada na área de notificação superior do dispositivo ou emulador.

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Android SDK do Baidu Push]: https://push.baidu.com/sdk/push_client_sdk_for_android
[Portal Azure]: https://portal.azure.com/
[Baidu portal]: https://www.baidu.com/
