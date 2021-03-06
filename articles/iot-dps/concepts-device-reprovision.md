---
title: Serviço de Provisionamento de Dispositivos Azure IoT Hub - Conceitos de dispositivo
description: Descreve conceitos de reprovisionamento de dispositivos para o Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2bf369b784cddf307abc59d2b8766fc8a87e0985
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975351"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT Hub Dispositivo reprovisionando conceitos

Durante o ciclo de vida de uma solução IoT, é comum mover dispositivos entre centros IoT. As razões para esta mudança podem incluir os seguintes cenários:

* **Geolocalização / GeoLatency**: À medida que um dispositivo se move entre locais, a latência da rede é melhorada por ter o dispositivo migrado para um centro IoT mais próximo.

* **Multi-arrendamento**: Um dispositivo pode ser utilizado dentro da mesma solução IoT e reatribuído a um novo cliente, ou site do cliente. Este novo cliente pode ser servido usando um hub IoT diferente.

* **Mudança de solução**: Um dispositivo poderia ser movido para uma nova ou atualizada solução IoT. Esta reatribuição pode exigir que o dispositivo se comunique com um novo hub IoT que está ligado a outros componentes traseiros.

* **Quarentena**: Semelhante a uma mudança de solução. Um dispositivo que esteja avariado, comprometido ou desatualizado pode ser transferido para um hub IoT que só pode atualizar e voltar a estar em conformidade. Uma vez que o dispositivo está a funcionar corretamente, é então migrado de volta para o seu centro principal.

O reprovisionamento do suporte no âmbito do Serviço de Provisionamento de Dispositivos aborda estas necessidades. Os dispositivos podem ser automaticamente transferidos para novos centros IoT com base na política de reprovisionamento configurada na entrada de inscrição do dispositivo.

## <a name="device-state-data"></a>Dados do estado do dispositivo

Os dados do estado do dispositivo são compostos pelas capacidades gémeas e do dispositivo do [dispositivo.](../iot-hub/iot-hub-devguide-device-twins.md) Estes dados são armazenados na instância do Serviço de Provisionamento de Dispositivos e no hub IoT a que um dispositivo é atribuído.

![Provisionamento com o Serviço de Provisionamento de Dispositivos](./media/concepts-device-reprovisioning/dps-provisioning.png)

Quando um dispositivo é inicialmente aprovisionado com uma instância do Serviço de Provisionamento de Dispositivos, são feitas as seguintes etapas:

1. O dispositivo envia um pedido de fornecimento para uma instância do Serviço de Provisionamento de Dispositivos. A instância de serviço autentica a identidade do dispositivo com base numa entrada de inscrição e cria a configuração inicial dos dados do estado do dispositivo. A instância de serviço atribui o dispositivo a um hub IoT com base na configuração de inscrição e devolve a atribuição do hub IoT ao dispositivo.

2. A instância de serviço de provisionamento fornece uma cópia de qualquer dado do estado do dispositivo inicial para o hub IoT atribuído. O dispositivo liga-se ao centro IoT designado e inicia operações.

Com o tempo, os dados estatais do dispositivo no hub IoT podem ser atualizados através de [operações](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) do dispositivo e [operações de back-end](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). As informações estatais do dispositivo inicial armazenadas na instância do Serviço de Provisionamento de Dispositivos permanecem intocadas. Este dispositivo intocado é a configuração inicial.

![Provisionamento com o Serviço de Provisionamento de Dispositivos](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Dependendo do cenário, à medida que um dispositivo se move entre os hubs IoT, também pode ser necessário migrar o estado do dispositivo atualizado no anterior hub IoT para o novo hub IoT. Esta migração é apoiada através da reprovisionamento de políticas no Serviço de Provisionamento de Dispositivos.

## <a name="reprovisioning-policies"></a>Políticas de reprovisionamento

Dependendo do cenário, um dispositivo geralmente envia um pedido para uma instância de serviço de provisionamento no reboot. Também apoia um método para desencadear manualmente o fornecimento a pedido. A política de reprovisionamento de uma entrada de matrícula determina a forma como a instância de serviço de fornecimento de dispositivos lida com estes pedidos de provisionamento. A política também determina se os dados do estado do dispositivo devem ser migrados durante o reprovisionamento. As mesmas políticas estão disponíveis para as matrículas individuais e grupos de matrículas:

* **Reprovisionamento e migração de dados**: Esta política é o padrão para novas inscrições. Esta política toma medidas quando os dispositivos associados à inscrição apresentam um novo pedido (1). Dependendo da configuração de inscrição, o dispositivo pode ser transferido para outro hub IoT. Se o dispositivo estiver a mudar os cubos IoT, o registo do dispositivo com o hub ioT inicial será removido. As informações atualizadas do dispositivo a partir desse centro ioT inicial serão migradas para o novo hub IoT (2). Durante a migração, o estado do dispositivo será reportado como **Asigning**.

    ![Provisionamento com o Serviço de Provisionamento de Dispositivos](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Reprovisionamento e reset para a config inicial**: Esta política toma medidas quando os dispositivos associados à inscrição apresentam um novo pedido de provisionamento (1). Dependendo da configuração de inscrição, o dispositivo pode ser transferido para outro hub IoT. Se o dispositivo estiver a mudar os cubos IoT, o registo do dispositivo com o hub ioT inicial será removido. Os dados iniciais de configuração que a instância de serviço de provisionamento recebeu quando o dispositivo foi provisionado é fornecido ao novo hub IoT (2). Durante a migração, o estado do dispositivo será reportado como **Asigning**.

    Esta política é frequentemente utilizada para uma reinstalação de fábrica sem alterar os centros ioT.

    ![Provisionamento com o Serviço de Provisionamento de Dispositivos](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nunca reprovisionamento**: O dispositivo nunca é transferido para um centro diferente. Esta política está prevista para gerir a retrocompatibilidade.

### <a name="managing-backwards-compatibility"></a>Gerir a compatibilidade para trás

Antes de setembro de 2018, as atribuições de dispositivos aos centros IoT tinham um comportamento pegajoso. Quando um dispositivo voltou ao processo de provisionamento, só seria atribuído de volta ao mesmo centro IoT.

Para soluções que tenham tomado uma dependência deste comportamento, o serviço de provisionamento inclui retrocompatibilidade. Este comportamento é atualmente mantido para dispositivos de acordo com os seguintes critérios:

1. Os dispositivos ligam-se a uma versão API antes da disponibilidade de suporte de reprovisionamento nativo no Serviço de Provisionamento de Dispositivos. Consulte a tabela API abaixo.

2. A inscrição para os dispositivos não tem uma política de reprovisionamento definida neles.

Esta compatibilidade garante que os dispositivos previamente implantados experimentem o mesmo comportamento que está presente durante os testes iniciais. Para preservar o comportamento anterior, não guarde uma política de reprovisionamento a estas matrículas. Se for definida uma política de reprovisionamento, a política de reprovisionamento tem precedência sobre o comportamento. Ao permitir que a política de reprovisionamento tenha precedência, os clientes podem atualizar o comportamento do dispositivo sem terem de reimagem do dispositivo.

O seguinte gráfico de fluxo ajuda a mostrar quando o comportamento está presente:

![retro-retro compatibilidade fluxo gráfico](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

O quadro seguinte mostra as versões API antes da disponibilidade de suporte de reprovisionamento nativo no Serviço de Provisionamento de Dispositivos:

| API REST | C SDK | SDK Python |  SDK de Node | SDK Java | SDK .NET |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 e mais cedo](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 e mais cedo](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 e mais cedo](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 ou mais cedo](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 ou mais cedo](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 ou mais cedo](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Estes valores e ligações são suscetíveis de mudar. Esta é apenas uma tentativa de espaço reservado para determinar onde as versões podem ser determinadas por um cliente e quais serão as versões esperadas.

## <a name="next-steps"></a>Passos seguintes

* [Como reabastecer dispositivos](how-to-reprovision.md)
