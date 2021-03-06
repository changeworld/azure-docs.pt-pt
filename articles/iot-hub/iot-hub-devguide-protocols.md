---
title: Protocolos de comunicação azure IoT Hub e portos / Microsoft Docs
description: Guia de desenvolvimento - descreve os protocolos de comunicação suportados para comunicações dispositivo-nuvem e nuvem-a-dispositivo e os números de porta que devem estar abertos.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 6d1ab50e471c9c603c7886130375dc74e9b2a755
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284632"
---
# <a name="reference---choose-a-communication-protocol"></a>Referência - escolha um protocolo de comunicação

o Hub IoT permite que os dispositivos utilizem os seguintes protocolos para comunicações do lado do dispositivo –

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT através de WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP através de WebSockets
* HTTPS

Para obter informações sobre como estes protocolos suportam funcionalidades específicas do IoT Hub, consulte a orientação de [comunicações Dispositivo-a-nuvem](iot-hub-devguide-d2c-guidance.md) e a orientação de [comunicações Cloud-to-device](iot-hub-devguide-c2d-guidance.md).

A tabela que se segue fornece as recomendações de alto nível para a sua escolha do protocolo:

| Protocolo | Quando deve escolher este protocolo |
| --- | --- |
| MQTT <br> MQTT sobre WebSocket |Utilize em todos os dispositivos que não necessitem de ligar vários dispositivos (cada um com as suas próprias credenciais por dispositivo) sobre a mesma ligação TLS. |
| AMQP <br> AMQP sobre WebSocket |Utilize em portais de campo e nuvem para aproveitar a ligação multiplexing através de dispositivos. |
| HTTPS |Utilizar para dispositivos que não possam suportar outros protocolos. |

Considere os seguintes pontos quando escolher o seu protocolo para comunicações do lado do dispositivo:

* **Padrão nuvem-a-dispositivo**. HTTPS não tem uma forma eficiente de implementar o impulso do servidor. Como tal, quando estiver a utilizar HTTPS, os dispositivos pesquisam IoT Hub para mensagens cloud-to-device. Esta abordagem é ineficiente tanto para o dispositivo como para o IoT Hub. De acordo com as atuais diretrizes HTTPS, cada dispositivo deve fazer uma sondagem para mensagens a cada 25 minutos ou mais. O servidor de suporte MQTT e AMQP pressiona ao receber mensagens cloud-to-device. Permitem empurrões imediatos de mensagens do IoT Hub para o dispositivo. Se a latência de entrega for uma preocupação, MQTT ou AMQP são os melhores protocolos a utilizar. Para dispositivos raramente ligados, https também funciona.

* **Portais de campo.** Ao utilizar mQTT e HTTPS, não é possível ligar vários dispositivos (cada um com as suas próprias credenciais por dispositivo) utilizando a mesma ligação TLS. Para [cenários](iot-hub-devguide-endpoints.md#field-gateways) de gateway de campo que requerem uma ligação TLS entre o gateway de campo e o IoT Hub para cada dispositivo conectado, estes protocolos são sub-ideais.

* **Dispositivos de baixo recurso.** As bibliotecas MQTT e HTTPS têm uma pegada menor do que as bibliotecas AMQP. Como tal, se o dispositivo tiver recursos limitados (por exemplo, menos de 1-MB RAM), estes protocolos podem ser a única implementação de protocolo disponível.

* **Transversal à rede.** O protocolo PADRÃO AMQP utiliza a porta 5671 e o MQTT ouve na porta 8883. A USe destes portos pode causar problemas em redes que estão fechadas a protocolos não HTTPS. Utilize mQTT sobre WebSockets, AMQP sobre WebSockets ou HTTPS neste cenário.

* **Tamanho da carga útil.** MQTT e AMQP são protocolos binários, que resultam em cargas mais compactas do que HTTPS.

> [!WARNING]
> Ao utilizar https, cada dispositivo deve fazer uma pesquisa para mensagens cloud-to-device não mais do que uma vez a cada 25 minutos. Em desenvolvimento, cada dispositivo pode fazer sondagens com mais frequência, se desejar.

## <a name="port-numbers"></a>Números de porta

Os dispositivos podem comunicar com o IoT Hub em Azure utilizando vários protocolos. Tipicamente, a escolha do protocolo é impulsionada pelos requisitos específicos da solução. A tabela que se segue lista as portas de saída que devem estar abertas para que um dispositivo possa utilizar um protocolo específico:

| Protocolo | Porta |
| --- | --- |
| MQTT |8883 |
| MQTT através de WebSockets |443 |
| AMQP |5671 |
| AMQP através de WebSockets |443 |
| HTTPS |443 |

Depois de ter criado um hub IoT numa região de Azure, o hub IoT mantém o mesmo endereço IP para a vida útil desse centro ioT. No entanto, se a Microsoft mover o hub IoT para uma unidade de escala diferente para manter a qualidade do serviço, então é-lhe atribuído um novo endereço IP.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como o IoT Hub implementa o protocolo MQTT, consulte [Comunicar com o seu hub IoT utilizando o protocolo MQTT](iot-hub-mqtt-support.md).
