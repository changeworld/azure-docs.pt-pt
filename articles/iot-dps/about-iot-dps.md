---
title: Descrição Geral do Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs
description: Descreve o fornecimento de dispositivos em Azure com o Serviço de Provisionamento de Dispositivos (DPS) e IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: e564660b502a950021ba4e4754ff5c210dfd477f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241179"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Aprovisionar dispositivos com o Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure
O Microsoft Azure fornece um conjunto avançado de serviços de cloud pública integrada para todas as suas necessidades de solução IoT. O IoT Hub Device Provisioning Service (DPS) é um serviço de ajuda para o IoT Hub que permite o fornecimento de zero toques, just-in-time para o centro ioT direito sem necessitar de intervenção humana. O DPS permite o fornecimento de milhões de dispositivos de forma segura e escalável.

## <a name="when-to-use-device-provisioning-service"></a>Quando utilizar o Serviço de Aprovisionamento de Dispositivos
Existem muitos cenários de provisionamento em que o DPS é uma excelente escolha para ligar dispositivos e configurar o IoT Hub, tais como:

* Aprovisionamento sem toques numa solução de IoT única sem codificar informações de ligação do Hub IoT na fábrica de (configuração inicial)
* Dispositivos de equilíbrio de carga em vários centros
* Ligação de dispositivos à solução de IoT do respetivo proprietário com base em dados de transações de venda (arquitetura "multitenancy")
* Ligação de dispositivos a uma solução de IoT específica, consoante o caso de utilização (isolamento da solução)
* Ligação de um dispositivo ao hub IoT com a latência mais baixa (fragmentação geográfica)
* Reaprovisionamento com base numa alteração no dispositivo
* Implementar as chaves utilizadas pelo dispositivo para estabelecer ligação ao Hub IoT (quando não utilizar certificados x.509 para ligar)

## <a name="behind-the-scenes"></a>Nos bastidores
Todos os cenários listados na secção anterior podem ser feitos utilizando DPS para o fornecimento de toque zero com o mesmo fluxo. Muitas das etapas manuais tradicionalmente envolvidas no fornecimento são automatizadas com DPS para reduzir o tempo de implementação de dispositivos IoT e reduzir o risco de erro manual. A secção seguinte descreve o que acontece nos bastidores para que um dispositivo seja aprovisionado. O primeiro passo é manual e todos os passos seguintes são automatizados.

![Fluxo de aprovisionamento básico](./media/about-iot-dps/dps-provisioning-flow.png)

1. O fabricante do dispositivo adiciona as informações de registo do dispositivo à lista de inscrição no portal do Azure.
2. O dispositivo contacta o ponto final do DPS definido na fábrica. O dispositivo transmite a informação de identificação ao DPS para provar a sua identidade.
3. O DPS valida a identidade do dispositivo validando o ID de registo e a chave contra a entrada da lista de inscrições utilizando um desafio nonce ([Módulo de Plataforma Fidedigna)](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)ou verificação padrão X.509 (X.509).
4. A DPS regista o dispositivo com um hub IoT e povoa o [estado gémeo desejado](../iot-hub/iot-hub-devguide-device-twins.md)pelo dispositivo.
5. O hub IoT devolve informações de ID do dispositivo ao DPS.
6. O DPS devolve as informações de ligação do hub IoT ao dispositivo. O dispositivo pode agora começar a enviar dados diretamente para o hub IoT.
7. O dispositivo estabelece ligação ao hub IoT.
8. O dispositivo obtém o estado pretendido do respetivo dispositivo duplo no hub IoT.

## <a name="provisioning-process"></a>Processo de aprovisionamento
Existem dois passos distintos no processo de implantação de um dispositivo em que o DPS toma uma parte que pode ser feita de forma independente:

* O **passo de fabrico** no qual o dispositivo é criado e preparado na fábrica, e
* O **passo de configuração da cloud** no qual o Serviço de Aprovisionamento de Dispositivos é configurado para o aprovisionamento automatizado.

Ambos os passos enquadram-se de forma totalmente integrada com os processos de implementação e fabrico existentes. O DPS até simplifica alguns processos de implementação que envolvem trabalho manual para obter informações de ligação no dispositivo.

### <a name="manufacturing-step"></a>Passo de fabrico
Este passo envolve o que acontece na linha de fabrico. As funções envolvidas neste passo incluem o designer de silício, o fabricante de silício, o integrador e/ou o fabricante final do dispositivo. Este passo está relacionado com a criação do próprio hardware.

O DPS não introduz um novo passo no processo de fabrico; pelo contrário, liga-se ao passo existente que instala o software inicial e (idealmente) o HSM no dispositivo. Em vez de criar um ID de dispositivo neste passo, o dispositivo é programado com as informações do serviço de aprovisionamento, permitindo-lhe chamar o serviço de aprovisionamento para obter a respetiva informação de ligação/ atribuição de solução IoT quando é ligado.

Também neste passo, o fabricante fornece informações sobre a chave de identificação ao implementador/operador do dispositivo. O fornecimento dessas informações pode ser tão simples como confirmar que todos os dispositivos têm um certificado X.509 gerado a partir de um certificado de assinatura fornecido pelo implementador/operador do dispositivo ou tão complicado como extrair a parte pública de uma chave de endossamento TPM de cada dispositivo TPM. Estes serviços são oferecidos atualmente por muitos fabricantes de silício.

### <a name="cloud-setup-step"></a>Passo de configuração da cloud
Este passo diz respeito à configuração da cloud para o aprovisionamento automático adequado. Geralmente, existem dois tipos de utilizadores envolvidos no passo de configuração da cloud: alguém que saiba como os dispositivos têm de ser inicialmente configurados (um operador do dispositivo) e alguém que saiba como os dispositivos devem ser divididos entre os hubs IoT (um operador da solução).

Há uma configuração inicial única do aprovisionamento que tem de ocorrer, que normalmente é processada pelo operador da solução. Depois de o serviço de aprovisionamento ser configurado, não tem de ser modificado, a não ser que o caso de utilização mude.

Depois de o serviço ser configurado para o aprovisionamento automático, tem de ser preparado para inscrever dispositivos. Este passo é executado pelo operador do dispositivo, que conhece a configuração desejada dos dispositivos e é responsável por certificar-se de que o serviço de aprovisionamento pode atestar corretamente a identidade do dispositivo quando vai à procura do respetivo hub IoT. O operador do dispositivo utiliza as informações da chave de identificação do fabricante e adiciona-as à lista de inscrição. Podem existir atualizações subsequentes à lista de inscrição à medida que são adicionadas novas entradas ou que as entradas existentes são atualizadas com as informações mais recentes sobre os dispositivos.

## <a name="registration-and-provisioning"></a>Registo e aprovisionamento
*Aprovisionamento* tem significados diferentes consoante a indústria em que o termo é utilizado. No contexto do aprovisionamento de dispositivos IoT na respetiva solução de cloud, o aprovisionamento é um processo composto por duas partes:

1. A primeira parte consiste em estabelecer a ligação inicial entre o dispositivo e a solução de IoT ao registar o dispositivo.
2. A segunda parte consiste em aplicar a configuração adequada ao dispositivo com base nos requisitos específicos da solução na qual foi registado.

Após a conclusão desses dois passos, podemos afirmar que o dispositivo foi totalmente aprovisionado. Alguns serviços cloud fornecem apenas o primeiro passo do processo de aprovisionamento, ou seja, registam os dispositivos no ponto final da solução de IoT, mas não fornecem a configuração inicial. A DPS automatiza ambas as etapas para proporcionar uma experiência de provisionamento perfeita para o dispositivo.

## <a name="features-of-the-device-provisioning-service"></a>Funcionalidades do Serviço de Aprovisionamento de Dispositivos
O DPS tem muitas funcionalidades, tornando-o ideal para o fornecimento de dispositivos.

* Suporte de **atestado seguro** para identidades baseadas em X.509 e TPM.
* **Lista de inscrição** que contém o registo completo de dispositivos/grupos de dispositivos que podem ser registados em qualquer momento. A lista de inscrição contém informações sobre a configuração pretendida do dispositivo, depois de ser registado, e pode ser atualizada em qualquer altura.
* **Múltiplas políticas** de alocação para controlar como o DPS atribui dispositivos aos centros IoT em suporte aos seus cenários: Latência mais baixa, distribuição uniformemente ponderada (padrão) e configuração estática através da lista de inscrições. A latência é determinada usando o mesmo método que [o Gestor de Tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#performance).
* **Monitorização e registo de diagnóstico** para garantir que tudo está a funcionar corretamente.
* **O suporte multi-hub** permite que o DPS atribua dispositivos a mais de um hub IoT. O DPS pode falar com centros em várias subscrições do Azure.
* **O suporte transversal** permite que o DPS atribua dispositivos a centros IoT noutras regiões.
* **A encriptação para dados em repouso** permite que os dados em DPS sejam encriptados e desencriptados de forma transparente utilizando encriptação AES de 256 bits, uma das cifras de bloco mais fortes disponíveis, e é compatível com O FIPS 140-2.


Pode saber mais sobre os conceitos e as funcionalidades envolvidas no aprovisionamento de dispositivos em [conceitos de dispositivo](concepts-device.md), [conceitos de serviço](concepts-service.md) e [conceitos de segurança](concepts-security.md).

## <a name="cross-platform-support"></a>Suporte de várias plataformas
Tal como todos os serviços azure ioT, a DPS trabalha em plataforma transversal com uma variedade de sistemas operativos. O Azure disponibiliza SDKs open source em várias [linguagens](https://github.com/Azure/azure-iot-sdks) para facilitar a ligação dos dispositivos e a gestão do serviço. O DPS suporta os seguintes protocolos para dispositivos de ligação:

* HTTPS
* AMQP
* AMQP através de sockets web
* MQTT
* MQTT através de sockets web

O DPS apenas suporta ligações HTTPS para operações de serviço.

## <a name="regions"></a>Regiões
O DPS está disponível em muitas regiões. A lista atualizada das regiões existentes e recentemente anunciadas para todos os serviços encontra-se em [Regiões do Azure](https://azure.microsoft.com/regions/). Pode verificar a disponibilidade do Serviço de Aprovisionamento de Dispositivos na página [Estado do Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> O DPS é global e não está ligado a um local. No entanto, deve especificar uma região em que residem os metadados associados ao seu perfil DPS.

## <a name="availability"></a>Disponibilidade
Existe um Acordo de Nível de Serviço de 99,9% para dPS, e pode [ler-se o SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/). O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/) completo explica a disponibilidade garantida do Azure em termos globais.

## <a name="quotas"></a>Quotas
Cada subscrição do Azure possui limites de quota predefinidos que podem afetar o âmbito da sua solução de IoT. O limite atual por subscrição é de 10 Serviços de Aprovisionamento de Dispositivos por subscrição.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Para obter mais detalhes sobre os limites de quotas:
* [Limites do Serviço das Subscrições do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Componentes do Azure relacionados
DPS automatiza o fornecimento de dispositivos com Hub Azure IoT. Saiba mais sobre o [Hub IoT](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>Passos seguintes
Agora já tem uma descrição geral do aprovisionamento de dispositivos IoT no Azure. O passo seguinte é experimentar um cenário de IoT ponto a ponto.
> [!div class="nextstepaction"]
> [Crie](quick-setup-auto-provision.md)
> o Serviço de Provisionamento de Dispositivos IoT Hub com o portal Azure[Criar e fornecer um dispositivo](quick-create-simulated-device.md)
> simulado[Configurar dispositivo para o fornecimento](tutorial-set-up-device.md)
