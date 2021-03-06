---
title: Pacote de importação de solução de monitorização remota - Azure / Microsoft Docs
description: Este artigo descreve como importar um pacote automático de gestão de dispositivos para o acelerador de solução de monitorização remota
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61443450"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Importe um pacote automático de gestão de dispositivos no seu acelerador de solução de monitorização remota

Uma configuração automática de gestão do dispositivo define as alterações de configuração para implantar num grupo de dispositivos. Este artigo assume que um desenvolvedor na sua organização já criou uma configuração automática de gestão de dispositivos. Para saber como um desenvolvedor cria uma configuração, consulte um dos seguintes artigos ioT Hub how-to:

- [Configure e monitorize dispositivos IoT em escala utilizando o portal Azure](../iot-hub/iot-hub-auto-device-config.md)
- [Configure e monitorize dispositivos IoT em escala utilizando o Azure CLI](../iot-hub/iot-hub-auto-device-config-cli.md)

Um desenvolvedor cria e testa uma configuração automática de gestão de dispositivos num ambiente de desenvolvimento. Quando estiver pronto, pode importar a configuração para o seu acelerador de solução de monitorização remota.

## <a name="export-a-configuration"></a>Exportar uma configuração

Utilize o portal Azure para exportar a configuração automática de gestão de dispositivos a partir do seu ambiente de desenvolvimento:

1. No portal Azure, navegue para o hub IoT que está a usar para desenvolver e testar os seus dispositivos IoT. Clique na configuração do **dispositivo IoT:**

    [![Configuração do dispositivo IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Clique na configuração que pretende utilizar. A página de detalhes de **configuração** do dispositivo exibe:

    [![Detalhe de configuração do dispositivo IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Clique no **ficheiro de configuração de descarregamento:**

    [![Ficheiro de configuração de descarregamento](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Guarde o ficheiro JSON como um ficheiro local chamado **configuração.json**.

Agora tem um ficheiro que contém a configuração automática de gestão do dispositivo. Na secção seguinte, importa-se esta configuração como um pacote para a solução de Monitorização Remota.

## <a name="import-a-package"></a>Importar um pacote

Siga os passos abaixo para importar uma configuração automática de gestão do dispositivo como um pacote para a sua solução:

1. Navegue para a página **pacotes** na página ![web de monitorização remota UI: Página de pacotes](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Clique **+ Novo Pacote,** escolha **configuração** como tipo de pacote e clique **em Navegar** para selecionar o ficheiro **configuração.json** que guardou na secção anterior:

    ![Selecione configuração](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Clique em **Carregar** para adicionar o pacote à sua solução de monitorização remota:

    ![Pacote carregado](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Agora, fez o upload de uma configuração automática de gestão de dispositivos como pacote. Na página **de Implementações,** pode implantar este pacote nos seus dispositivos conectados.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar um pacote de configuração e a importá-lo a partir da solução de Monitorização Remota, o próximo passo é aprender a [gerir dispositivos ligados à Monitorização Remota a granel](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
