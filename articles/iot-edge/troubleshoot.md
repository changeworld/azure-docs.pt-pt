---
title: Troubleshoot - Azure IoT Edge [ Azure IoT Edge ] Microsoft Docs
description: Use este artigo para aprender habilidades de diagnóstico padrão para Azure IoT Edge, como recuperar o estado e os registos dos componentes, e resolver questões comuns
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 13eab175356ed1ec20caa3263ba00d0563384f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064382"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemas comuns e resoluções do Azure IoT Edge

Se ocorrerem problemas com o Azure IoT Edge no seu ambiente, utilize este artigo como guia para a resolução de problemas.

## <a name="run-the-iotedge-check-command"></a>Executar o comando iotedge 'check'

O seu primeiro passo ao resolver problemas `check` ioT Edge deve ser usar o comando, que executa uma coleção de testes de configuração e conectividade para problemas comuns. O `check` comando está disponível no [lançamento 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) e mais tarde.

Pode executar `check` o comando da seguinte `--help` forma, ou incluir a bandeira para ver uma lista completa de opções:

* Em Linux:

  ```bash
  sudo iotedge check
  ```

* No Windows:

  ```powershell
  iotedge check
  ```

Os tipos de controlos efetuados pela ferramenta podem ser classificados como:

* Verificações de configuração: Examina detalhes que podem impedir que os dispositivos Edge se conectem à nuvem, incluindo problemas com *o config.yaml* e o motor do recipiente.
* Verificações de ligação: Verifica-se que o tempo de execução do IoT Edge pode aceder às portas do dispositivo hospedeiro e todos os componentes do IoT Edge podem ligar-se ao Hub IoT.
* Verificações de prontidão de produção: Procura as melhores práticas recomendadas de produção, tais como os certificados de autoridade de certificados de dispositivo (CA) e a configuração de ficheiros de registo de módulos.

Para obter uma lista completa de verificações de diagnóstico, consulte a [funcionalidade de resolução de problemas incorporada](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Recolher informações sobre depuração com comando de "suporte-pacote" iotedge

Quando precisa de recolher registos de um dispositivo IoT Edge, `support-bundle` a forma mais conveniente é utilizar o comando. Por predefinição, este comando recolhe módulo, IoT Edge Security Manager e registos de motores de contentores, saída JSON 'iotedge check' e outras informações úteis de depuração. Comprime-os num único ficheiro para uma partilha fácil. O `support-bundle` comando está disponível no [lançamento 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) e mais tarde.

Mande `support-bundle` o `--since` comando com a bandeira para especificar quanto tempo do passado pretende obter registos. Por `6h` exemplo, receberá registos desde `6d` as últimas 6 `6m` horas, desde os últimos 6 dias, desde os últimos 6 minutos e assim por diante. Inclua `--help` a bandeira para ver uma lista completa de opções.


* Em Linux:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* No Windows:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> A saída `support-bundle` do comando pode conter nomes de hospedeiro, dispositivo e módulo, informações registadas pelos seus módulos, etc. Por favor, esteja ciente disso se partilhar a produção em um fórum público.

## <a name="standard-diagnostic-steps"></a>Passos de diagnóstico padrão

Se encontrar um problema, poderá saber mais sobre o estado do seu dispositivo IoT Edge, revendo os registos do contentor e as mensagens que passam de e para o dispositivo. Utilize as ferramentas e comandos nesta secção para recolher informações.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Verifique o estado do Gestor de Segurança IoT Edge e os seus registos

Em Linux:

* Para ver o estado do Gestor de Segurança IoT Edge:

   ```bash
   sudo systemctl status iotedge
   ```

* Para ver os registos do Gestor de Segurança IoT Edge:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Para ver registos mais detalhados do Gestor de Segurança IoT Edge:

  * Editar as definições de daemon iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Atualizar as seguintes linhas:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Reinicie o Daemon de Segurança IoT Edge:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

No Windows:

* Para ver o estado do Gestor de Segurança IoT Edge:

   ```powershell
   Get-Service iotedge
   ```

* Para ver os registos do Gestor de Segurança IoT Edge:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se o IoT Edge Security Manager não estiver a funcionar, verifique o seu ficheiro de configuração do yaml

> [!WARNING]
> Os ficheiros YAML não podem conter os separadores como recuo. Utilize 2 espaços em vez disso. Elementos de alto nível não devem ter espaços de liderança.

Em Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Verifique os registos de contentores para obter problemas

Assim que o Daemon de Segurança de Borda IoT estiver em funcionamento, veja os registos dos contentores para detetar problemas. Comece com os recipientes implantados e, em seguida, olhe para os recipientes que compõem o tempo de execução ioT Edge: edgeAgent e edgeHub. Os registos do agente IoT Edge normalmente fornecem informações sobre o ciclo de vida de cada recipiente. Os registos do hub IoT Edge fornecem informações sobre mensagens e encaminhamento.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Veja as mensagens que passam pelo centro de IoT Edge

Pode ver as mensagens que passam pelo centro ioT Edge e recolher informações dos registos verbosos dos recipientes de tempo de execução. Para ligar os registos verbosos `RuntimeLogLevel` nestes recipientes, coloque no seu ficheiro de configuração do yaml. Para abrir o ficheiro:

Em Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Por padrão, `agent` o elemento será o seguinte exemplo:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Substitua `env: {}` por:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Os ficheiros YAML não podem conter separadores como identificação. Utilize 2 espaços em vez disso. Itens de alto nível não podem ter espaço branco líder.

Guarde o ficheiro e reinicie o gestor de segurança IoT Edge.

Também pode verificar as mensagens que são enviadas entre o Hub IoT e os dispositivos do IoT Edge. Veja estas mensagens utilizando a [extensão Do Hub Azure IoT para O Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Para mais informações, consulte a [ferramenta Handy quando se desenvolver com o Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Reiniciar contentores

Depois de investigar os registos e mensagens para obter informações, pode tentar reiniciar os contentores:

```cmd
iotedge restart <container name>
```

Reiniciar os recipientes de tempo de execução IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Reinicie o gestor de segurança IoT Edge

Se o problema ainda persistir, pode tentar reiniciar o gestor de segurança IoT Edge.

Em Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

No Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agente ioT Edge para após cerca de um minuto

O módulo EdgeAgent começa e funciona com sucesso durante cerca de um minuto e para. Os registos indicam que o agente IoT Edge tenta ligar-se ao IoT Hub através da AMQP e, em seguida, tenta ligar-se usando amQP através do WebSocket. Quando isso falhar, o agente IoT Edge sai.

Registos de edgeAgent de exemplo:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Causa da raiz**

Uma configuração de rede na rede de hospedeiros está a impedir que o agente IoT Edge chegue à rede. O agente primeiro tenta ligar através de AMQP (porta 5671). Se a ligação falhar, tenta WebSockets (porta 443).

O runtime do IoT Edge configura uma rede para comunicar em cada um dos módulos. No Linux, esta rede é uma rede de ponte. No Windows, utiliza NAT. Este problema é mais comum nos dispositivos Windows com contentores do Windows que utilizam a rede NAT.

**Resolução**

Certifique-se de que existe uma rota para a Internet para os endereços IP atribuídos a esta rede de ponte/NAT. Por vezes, uma configuração de VPN no anfitrião sobrepõe-se à rede do IoT Edge.

## <a name="iot-edge-hub-fails-to-start"></a>Hub IoT Edge não arranca

O módulo EdgeHub não arranca e imprime a seguinte mensagem para os registos:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Causa da raiz**

Outro processo no computador anfitrião vinculou a porta 443. O hub IoT Edge mapeia portas 5671 e 443 para utilização em cenários de gateway. Este mapeamento de portas falha se outro processo já tiver vinculado esta porta.

**Resolução**

Encontre e pare o processo que está a utilizar a porta 443. Este processo é normalmente um servidor Web.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>O agente IoT Edge não consegue aceder à imagem de um módulo (403)

Um recipiente não é executado, e os registos edgeAgent mostram um erro de 403.

**Causa da raiz**

O agente IoT Edge não tem permissões para aceder à imagem de um módulo.

**Resolução**

Certifique-se de que as suas credenciais de registo estão corretamente especificadas no seu manifesto de implantação

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Daemon de segurança IoT Edge falha com um nome de anfitrião inválido

O `sudo journalctl -u iotedge` comando falha e imprime a seguinte mensagem:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Causa da raiz**

O tempo de execução do IoT Edge só pode suportar nomes de anfitriões que sejam inferiores a 64 caracteres. As máquinas físicas geralmente não têm nomes de anfitriões longos, mas o problema é mais comum numa máquina virtual. Os nomes de anfitriões gerados automaticamente para máquinas virtuais Windows alojadas no Azure, em particular, tendem a ser longos.

**Resolução**

Quando vir este erro, pode resolvê-lo configurando o nome DNS da sua máquina virtual e, em seguida, definindo o nome DNS como nome de anfitrião no comando de configuração.

1. No portal Azure, navegue para a página geral da sua máquina virtual.
2. **Selecione configurar** sob o nome DNS. Se a sua máquina virtual já tiver um nome DNS configurado, não precisa de configurar um novo.

   ![Configurar o nome DNS da máquina virtual](./media/troubleshoot/configure-dns.png)

3. Forneça um valor para a **etiqueta de nome DNS** e selecione **Guardar**.
4. Copie o novo nome DNS, que deve estar no formato ** \<\>DNSnamelabel\< . vmlocation\>.cloudapp.azure.com**.
5. No interior da máquina virtual, utilize o seguinte comando para configurar o tempo de execução do IoT Edge com o seu nome DNS:

   * Em Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * No Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemas de estabilidade em dispositivos limitados de recursos

Pode encontrar problemas de estabilidade em dispositivos limitados como o Raspberry Pi, especialmente quando usado como porta de entrada. Os sintomas incluem exceções de memória no módulo do hub de borda, os dispositivos a jusante não podem ligar ou o dispositivo para de enviar mensagens de telemetria após algumas horas.

**Causa da raiz**

O hub IoT Edge, que faz parte do tempo de execução do IoT Edge, está otimizado para o desempenho por padrão e tenta alocar grandes pedaços de memória. Esta otimização não é ideal para dispositivos de borda restrita e pode causar problemas de estabilidade.

**Resolução**

Para o hub IoT Edge, detete uma variável ambiental **OptimizeForPerformance** para **falso**. Existem duas formas de definir variáveis ambientais:

No portal do Azure:

No seu Hub IoT, selecione o seu dispositivo IoT Edge e a partir da página de detalhes do dispositivo e selecione**Definições**de tempo de execução de **módulos definidos** > . Crie uma variável ambiental para o módulo Edge Hub chamado *OptimizeForPerformance* que está definido para *falso*.

![OptimizeForPerformance definido para falso](./media/troubleshoot/optimizeforperformance-false.png)

**OU**

No manifesto de implantação:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Não consigo obter os registos de daemon IoT Edge no Windows

Se obtém uma Exceção `Get-WinEvent` do EventLogQuando estiver a utilizar no Windows, verifique as entradas de registo.

**Causa da raiz**

O `Get-WinEvent` comando PowerShell baseia-se numa entrada de registo para `ProviderName`estar presente para encontrar registos por um específico .

**Resolução**

Estabeleça uma entrada de registo para o daemon IoT Edge. Crie um ficheiro **iotedge.reg** com o seguinte conteúdo e importe para o `reg import iotedge.reg` Registo do Windows clicando-o duas vezes ou utilizando o comando:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Módulo IoT Edge falha em enviar uma mensagem para o edgeHub com erro de 404

Um módulo IoT Edge personalizado não envia uma mensagem para `Module not found` o edgeHub com um erro de 404. O daemon IoT Edge imprime a seguinte mensagem aos registos:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Causa da raiz**

O daemon IoT Edge impõe a identificação do processo para todos os módulos que se ligam ao edgeHub por razões de segurança. Verifica que todas as mensagens enviadas por um módulo provêm do id de processo principal do módulo. Se uma mensagem estiver a ser enviada por um módulo a partir de um ID de processo diferente do inicialmente estabelecido, rejeitará a mensagem com uma mensagem de erro 404.

**Resolução**

A partir da versão 1.0.7, todos os processos de módulo estão autorizados a ligar. Se não for possível atualizar para 1.0.7, complete os seguintes passos. Para mais informações, consulte o [changelog de lançamento 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Certifique-se de que o mesmo id de processo é sempre utilizado pelo módulo IoT Edge personalizado para enviar mensagens para o edgeHub. Por exemplo, `ENTRYPOINT` certifique-se `CMD` de que em `CMD` vez de comando no seu ficheiro Docker, uma vez que levará `ENTRYPOINT` a um processo de identificação para o módulo e outro id de processo para o comando de batida que executa o programa principal, enquanto que levará a um único processo DE IDENTIFICAção.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Regras de configuração de Firewall e Porta para implementação de IoT Edge

O Azure IoT Edge permite a comunicação de um servidor no local para a nuvem Azure utilizando protocolos IoT Hub suportados, ver escolher um protocolo de [comunicação](../iot-hub/iot-hub-devguide-protocols.md). Para uma maior segurança, os canais de comunicação entre o Azure IoT Edge e o Azure IoT Hub estão sempre configurados para serem Outbound. Esta configuração baseia-se no padrão de [Comunicação Assistida](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)de Serviços, que minimiza a superfície de ataque para uma entidade maliciosa explorar. A comunicação de entrada só é necessária para cenários específicos em que o Hub Azure IoT precisa de empurrar mensagens para o dispositivo Azure IoT Edge. As mensagens cloud-to-device estão protegidas utilizando canais TLS seguros e podem ser mais seguras utilizando certificados X.509 e módulos de dispositivoTPM. O Gestor de Segurança de Borda Sarna Azure IoT rege a forma como esta comunicação pode ser estabelecida, consulte [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Embora o IoT Edge forneça uma configuração melhorada para assegurar o tempo de funcionamento do Edge Azure IoT edge e os módulos implantados, ainda está dependente da configuração da máquina e da rede subjacentes. Por conseguinte, é imperativo garantir que sejam criadas regras adequadas de rede e firewall para uma borda segura para a comunicação em nuvem. A tabela a seguir pode ser utilizada como uma orientação quando as regras de firewall de configuração para os servidores subjacentes onde o tempo de execução do Edge Azure IoT edge é hospedado:

|Protocolo|Porta|Dados|Saída|Orientação|
|--|--|--|--|--|
|MQTT|8883|BLOQUEADO (Padrão)|BLOQUEADO (Padrão)|<ul> <li>Configure a saída (outbound) para estar aberta ao utilizar o MQTT como protocolo de comunicação.<li>1883 para MQTT não é suportado por IoT Edge. <li>As ligações de entrada (entrada) devem ser bloqueadas.</ul>|
|AMQP|5671|BLOQUEADO (Padrão)|ABERTO (Padrão)|<ul> <li>Protocolo de comunicação padrão para IoT Edge. <li> Deve ser configurado para estar aberto se o Azure IoT Edge não estiver configurado para outros protocolos suportados ou AMQP é o protocolo de comunicação pretendido.<li>5672 para AMQP não é suportado por IoT Edge.<li>Bloqueie esta porta quando o Azure IoT Edge utilizar um protocolo suportado pelo IoT Hub.<li>As ligações de entrada (entrada) devem ser bloqueadas.</ul></ul>|
|HTTPS|443|BLOQUEADO (Padrão)|ABERTO (Padrão)|<ul> <li>Configure Outgoing (Outbound) para ser aberto em 443 para o fornecimento de IoT Edge. Esta configuração é necessária quando se utilizam scripts manuais ou serviço de provisionamento de dispositivos Azure IoT (DPS). <li>A ligação de entrada (entrada) deve ser aberta apenas para cenários específicos: <ul> <li>  Se tiver um portal transparente com dispositivos de folhas que possam enviar pedidos de método. Neste caso, o Porto 443 não precisa de estar aberto a redes externas para ligar ao IoTHub ou fornecer serviços IoTHub através do Azure IoT Edge. Assim, a regra de entrada poderia limitar-se a abrir apenas a entrada (Entrada) da rede interna. <li> Para cenários de cliente para dispositivo (C2D).</ul><li>80 para HTTP não é suportado por IoT Edge.<li>Se os protocolos não HTTP (por exemplo, AMQP ou MQTT) não puderem ser configurados na empresa; as mensagens podem ser enviadas por WebSockets. A porta 443 será utilizada para a comunicação WebSocket neste caso.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Módulo Edge Agent relata continuamente 'ficheiro config vazio' e nenhum módulo começa no dispositivo

O dispositivo tem problemas de arranque de módulos definidos na implementação. Só o agente de borda está a funcionar, mas continuamente a reportar "ficheiro de config vazio...".

**Causa da raiz**

Por padrão, o IoT Edge inicia módulos na sua própria rede de contentores isolados. O dispositivo pode estar a ter problemas com a resolução de nomes DNS dentro desta rede privada.

**Resolução**

**Opção 1: Definir o servidor DNS nas definições do motor do recipiente**

Especifique o servidor DNS para o seu ambiente nas definições do motor do recipiente, que se aplicará a todos os módulos de contentores iniciados pelo motor. Crie um `daemon.json` ficheiro nomeado especificando o servidor DNS para usar. Por exemplo:

```json
{
    "dns": ["1.1.1.1"]
}
```

O exemplo acima define o servidor DNS para um serviço DNS acessível ao público. Se o dispositivo de borda não conseguir aceder a este IP a partir do seu ambiente, substitua-o por um endereço de servidor DNS acessível.

Coloque `daemon.json` no local certo para a sua plataforma:

| Plataforma | Localização |
| --------- | -------- |
| Linux | `/etc/docker` |
| Anfitrião de janelas com recipientes Windows | `C:\ProgramData\iotedge-moby\config` |

Se a localização `daemon.json` já contiver ficheiro, adicione a chave **dns** e guarde o ficheiro.

Reinicie o motor do contentor para que as atualizações entrem em vigor.

| Plataforma | Comando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Janelas (Admin Powershell) | `Restart-Service iotedge-moby -Force` |

**Opção 2: Definir servidor DNS na implementação IoT Edge por módulo**

Pode definir o servidor DNS para as *opções de criação* de cada módulo na implementação do IoT Edge. Por exemplo:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Certifique-se de definir esta configuração para os módulos *edgeAgent* e *edgeHub* também.

## <a name="next-steps"></a>Passos seguintes

Encontrou um erro na plataforma do IoT Edge? [Submeta um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar a melhorar.

Se tiver mais perguntas, crie um pedido de [apoio](https://portal.azure.com/#create/Microsoft.Support) para ajuda.
