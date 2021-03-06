---
title: Desenvolver para android things plataforma usando SDKs Azure IoT [ Microsoft Docs
description: Guia de desenvolvedores - Saiba como desenvolver-se em Android Things usando SDKs Azure IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: a06583e9aab4b082517d47c1022f7bec5184b9bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673389"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Desenvolver para android things plataforma usando SDKs Azure IoT

[Os SDKs Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) fornecem suporte de primeira linha para plataformas populares como Windows, Linux, OSX, MBED e plataformas móveis como Android e iOS.  Como parte do nosso compromisso de permitir uma maior escolha e flexibilidade nas implementações ioT, o Java SDK também suporta a plataforma [Android Things.](https://developer.android.com/things/)  Os desenvolvedores podem aproveitar os benefícios do sistema operativo Android Things no lado do dispositivo, ao mesmo tempo que usam o [Azure IoT Hub](about-iot-hub.md) como o centro de mensagens central que escala para milhões de dispositivos conectados simultaneamente.

Este tutorial descreve os passos para construir uma aplicação lateral do dispositivo no Android Things usando o Azure IoT Java SDK.

## <a name="prerequisites"></a>Pré-requisitos

* Um Android Things suportava hardware com o Android Things OS em execução.  Pode seguir a [documentação do Android Things](https://developer.android.com/things/get-started/kits#flash-at) sobre como mostrar o Android Things OS.  Certifique-se de que o seu dispositivo Android Things está ligado à internet com periféricos essenciais, tais como teclado, exibição e rato ligados.  Este tutorial usa Raspberry Pi 3.

* Versão mais recente do [Android Studio](https://developer.android.com/studio/)

* Versão mais recente de [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o marcador de posição abaixo pelo nome que escolher para o seu hub IoT.

   **MyAndroidThingsDevice** : Este é o nome dado para o dispositivo registado. Utilize o MyAndroidThingsDevice como mostrado. Se escolher um nome diferente para o seu dispositivo, também irá precisar de utilizar esse nome através deste artigo, e atualize o nome do dispositivo em aplicações de exemplo antes de as executar.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Execute os seguintes comandos em Azure Cloud Shell para obter a *cadeia de ligação* do dispositivo para o dispositivo que acabou de registar. Substitua `YourIoTHubName` abaixo o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="building-an-android-things-application"></a>Construção de uma aplicação Android Things

1. O primeiro passo para a construção de uma aplicação Android Things é a ligação aos seus dispositivos Android Things. Ligue o seu dispositivo Android Things a um ecrã e conecte-o à internet. O Android Things fornece [documentação](https://developer.android.com/things/get-started/kits) sobre como se conectar ao Wi-Fi. Depois de ter ligado à internet, tome nota do endereço IP listado em Redes.

2. Utilize a ferramenta [adb](https://developer.android.com/studio/command-line/adb) para se ligar ao seu dispositivo Android Things com o endereço IP acima indicado. Verifique duas vezes a ligação utilizando este comando a partir do seu terminal. Deve ver os seus dispositivos listados como "conectados".

   ```
   adb devices
   ```

3. Descarregue a nossa amostra para Android/Android Things a partir deste [repositório](https://github.com/Azure-Samples/azure-iot-samples-java) ou use Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. No Android Studio, abra o Projeto Android em "\azure-iot-samples-java\iot-hub\Samples\Device\AndroidSample".

5. Abra o ficheiro gradle.properties e substitua o "Device_connection_string" pela corda de ligação do dispositivo anteriormente anotado.
 
6. Clique em Executar - Debug e selecione o seu dispositivo para implementar este código para os seus dispositivos Android Things.

7. Quando a aplicação é iniciada com sucesso, pode ver uma aplicação a funcionar no seu dispositivo Android Things. Esta aplicação de amostra envia leituras de temperatura geradas aleatoriamente.

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Pode ver os dados através do seu hub IoT à medida que são recebidos. A extensão da CLI do Hub IoT pode ligar ao ponto final de **Eventos** do lado do serviço no seu Hub IoT. A extensão recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação back-end do Hub IoT é normalmente executada na cloud para receber e processar mensagens do dispositivo para a cloud.

Execute os seguintes comandos no Azure Cloud Shell, ao substituir `YourIoTHubName` pelo nome do hub IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

* Saiba [como gerir a conectividade e mensagens fiáveis](iot-hub-reliability-features-in-sdks.md) utilizando os SDKs IoT Hub.
* Saiba como [se desenvolver para plataformas móveis](iot-hub-how-to-develop-for-mobile-devices.md) como iOS e Android.
* [Suporte à plataforma Azure IoT SDK](iot-hub-device-sdk-platform-support.md)
