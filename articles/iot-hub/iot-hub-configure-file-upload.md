---
title: Utilize o portal Azure para configurar o upload de ficheiros [ Microsoft Docs
description: Como utilizar o portal Azure para configurar o seu hub IoT para permitir uploads de ficheiros a partir de dispositivos conectados. Inclui informações sobre a configuração da conta de armazenamento do destino Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: bd7cc37b8fc81fc9d4109826743f2243913d0604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60735056"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configurar os carregamentos de ficheiros do Hub IoT com o portal do Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Upload de ficheiros

Para utilizar a funcionalidade de upload de [ficheiros no IoT Hub,](iot-hub-devguide-file-upload.md)tem primeiro de associar uma conta De Armazenamento Azure ao seu hub. Selecione **upload de ficheiro** para mostrar uma lista de propriedades de upload de ficheiros para o hub IoT que está a ser modificado.

![Ver definições de upload de ficheiros IoT Hub no portal](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Recipiente de armazenamento**: Utilize o portal Azure para selecionar um recipiente de blob numa conta de Armazenamento Azure na sua assinatura Azure atual para se associar ao seu Hub IoT. Se necessário, pode criar uma conta de Armazenamento Azure na lâmina das contas de **armazenamento** e no recipiente de bolhas na lâmina dos **contentores.** O IoT Hub gera automaticamente URIs SAS com permissões de escrita a este recipiente blob para dispositivos a utilizar quando fazem o upload de ficheiros.

   ![Ver recipientes de armazenamento para upload de ficheiros no portal](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Receba notificações para ficheiros enviados**: Ativar ou desativar notificações de upload de ficheiros através do alternância.

* **SAS TTL**: Esta definição é o tempo de viver dos URIs SAS devolvidos ao dispositivo pelo IoT Hub. Configurado para uma hora por padrão, mas pode ser personalizado para outros valores usando o slider.

* Definições de notificação de **ficheiros padrão TTL**: O tempo de vida de uma notificação de upload de ficheiro antes de expirar. Definido para um dia por padrão, mas pode ser personalizado para outros valores usando o slider.

* Contagem máxima de entrega de notificação de **ficheiros**: O número de vezes que o IoT Hub tenta entregar uma notificação de upload de ficheiro. Configurado para 10 por padrão, mas pode ser personalizado para outros valores usando o slider.

   ![Configure o upload de ficheiro sinuoso do IoT Hub no portal](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as capacidades de upload de ficheiros do IoT Hub, consulte [ficheiros Upload de um dispositivo](iot-hub-devguide-file-upload.md) no guia de desenvolvimento do IoT Hub.

Siga estes links para saber mais sobre a gestão do Azure IoT Hub:

* [Gerir dispositivos IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do Hub IoT](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Proteja a sua solução IoT do zero](../iot-fundamentals/iot-security-ground-up.md)
