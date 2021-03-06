---
title: Compare o Hub Azure IoT com os Hubs de Eventos Azure [ Hubs de Eventos Azure] Microsoft Docs
description: Uma comparação dos serviços IoT Hub e Event Hubs Azure destacando diferenças funcionais e casos de utilização. A comparação inclui protocolos suportados, gestão de dispositivos, monitorização e uploads de ficheiros.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60735528"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Ligação de dispositivos IoT ao Azure: Hub IoT e Centros de Eventos

O Azure fornece serviços especificamente desenvolvidos para diversos tipos de conectividade e comunicação para ajudá-lo a ligar os seus dados à potência da nuvem. Tanto o Azure IoT Hub como o Azure Event Hubs são serviços na nuvem que podem ingerir grandes quantidades de dados e processar ou armazenar esses dados para insights de negócio. Os dois serviços são semelhantes na medida em que ambos suportam a ingestão de dados com baixa latência e elevada fiabilidade, mas são concebidos para diferentes finalidades. O IoT Hub foi desenvolvido para responder aos requisitos únicos de ligação de dispositivos IoT à nuvem Azure, enquanto o Event Hubs foi projetado para o streaming de big data. Microsoft recomenda a utilização do Hub Azure IoT para ligar dispositivos IoT ao Azure

O Azure IoT Hub é o portal de cloud que liga dispositivos IoT para recolher dados e impulsionar insights de negócio sonorizadores e automação. Além disso, o IoT Hub inclui funcionalidades que enriquecem a relação entre os seus dispositivos e os seus sistemas de backend. Capacidades de comunicação bidirecionais significam que, enquanto recebe dados de dispositivos, também pode enviar comandos e políticas de volta para os dispositivos. Por exemplo, utilize mensagens cloud-to-device para atualizar propriedades ou invocar ações de gestão de dispositivos. A comunicação Cloud-to-device também permite enviar inteligência em nuvem para os seus dispositivos de borda com O Edge Azure IoT Edge. A identidade única ao nível do dispositivo fornecida pelo IoT Hub ajuda a proteger melhor a sua solução IoT de potenciais ataques. 

[O Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) é o grande serviço de streaming de dados do Azure. Foi concebido para cenários de transmissão de dados em fluxo com elevado débito, em que os clientes poderão enviar milhares de milhões de pedidos por dia. O Hubs de Eventos utiliza um modelo de consumidor de partição para ampliar o seu fluxo e está integrado nos serviços de análise e macrodados do Azure, incluindo o Databricks, Stream Analytics, ADLS e HDInsight. Com funcionalidades como A Captura de Hubs de Eventos e Auto-Inflate, este serviço foi concebido para suportar as suas aplicações e soluções de big data. Além disso, o IoT Hub utiliza Hubs de Eventos para o seu caminho de fluxo de telemetria, pelo que a sua solução IoT também beneficia do tremendo poder dos Centros de Eventos.

Resumindo, ambas as soluções são concebidas para a ingestão de dados em larga escala. Apenas o IoT Hub fornece as ricas capacidades específicas do IoT que são projetadas para maximizar o valor de negócio de ligar os seus dispositivos IoT à nuvem Azure.  Se a sua viagem ioT está apenas a começar, a começar pelo IoT Hub para apoiar os seus cenários de ingestão de dados, irá garantir que tem acesso instantâneo às capacidades ioT em destaque uma vez que o seu negócio e necessidades técnicas as exijam.

A tabela seguinte fornece detalhes sobre como os dois níveis do IoT Hub se comparam aos Hubs de Eventos quando você está avaliando-os para as capacidades de IoT. Para obter mais informações sobre os níveis padrão e básicos do IoT Hub, consulte [Como escolher o nível ioT hub certo](iot-hub-scaling.md).

| Capacidade ioT | Nível padrão IoT Hub | IoT Hub básico | Event Hubs |
| --- | --- | --- | --- |
| Mensagens dispositivo-a-nuvem | ![Marcar][checkmark] | ![Marcar][checkmark] | ![Marcar][checkmark] |
| Protocolos: HTTPS, AMQP, AMQP sobre webSockets | ![Marcar][checkmark] | ![Marcar][checkmark] | ![Marcar][checkmark] |
| Protocolos: MQTT, MQTT sobre webSockets | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Identidade por dispositivo | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Upload de ficheiros a partir de dispositivos | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Serviço de Provisionamento de Dispositivos | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Mensagens cloud-to-device | ![Marcar][checkmark] |  |  |
| Gestão de dispositivos twin e dispositivos | ![Marcar][checkmark] |  |  |
| Fluxos de dispositivos (pré-visualização) | ![Marcar][checkmark] |  |  |
| IoT Edge | ![Marcar][checkmark] |  |  |

Mesmo que o único caso de utilização seja a ingestão de dados dispositivo-nuvem, recomendamos vivamente a utilização do IoT Hub, uma vez que fornece um serviço que é projetado para a conectividade do dispositivo IoT. 

### <a name="next-steps"></a>Passos seguintes

Para explorar ainda mais as capacidades do IoT Hub, consulte o guia de [desenvolvimento do IoT Hub.](iot-hub-devguide.md)

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
