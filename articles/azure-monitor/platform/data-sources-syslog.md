---
title: Recolher e analisar mensagens Syslog no Monitor Azure Microsoft Docs
description: Syslog é um protocolo de registo de eventos que é comum ao Linux. Este artigo descreve como configurar a recolha de mensagens Syslog no Log Analytics e detalhes dos registos que criam.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/22/2019
ms.openlocfilehash: 8d68a8d6d28d79c50a92cd2d18df2abab26c30ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274726"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Syslog data sources in Azure Monitor (Origens de dados de Syslog no Azure Monitor)
Syslog é um protocolo de registo de eventos que é comum ao Linux. As aplicações enviarão mensagens que podem ser armazenadas na máquina local ou entregues a um colecionador syslog. Quando o agente Log Analytics para o Linux é instalado, ele confunde o daemon syslog local para encaminhar mensagens para o agente. O agente envia então a mensagem para o Monitor Azure onde é criado um registo correspondente.  

> [!NOTE]
> O Azure Monitor suporta a recolha de mensagens enviadas por rsyslog ou syslog-ng, onde o rsyslog é o daemon padrão. O daemon padrão de slog na versão 5 da versão Red Hat Enterprise Linux, CentOS e Oracle Linux (sysklog) não é suportado para a coleção de eventos syslog. Para recolher dados syslog desta versão destas distribuições, o [daemon rsyslog](http://rsyslog.com) deve ser instalado e configurado para substituir o sysklog.
>
>

![Coleção Syslog](media/data-sources-syslog/overview.png)

As seguintes instalações são suportadas com o colecionador Syslog:

* núcleo
* utilizador
* correio
* daemon
* auth
* syslog
* Rio Lpr
* news
* uucp
* cron
* authpriv
* ftp
* local0-local7

Para qualquer outra instalação, configure uma fonte de [dados de Registos Personalizados](data-sources-custom-logs.md) no Monitor Azure.
 
## <a name="configuring-syslog"></a>Configurar syslog
O agente Log Analytics para o Linux apenas irá recolher eventos com as instalações e severidades especificadas na sua configuração. Pode configurar o Syslog através do portal Azure ou gerindo ficheiros de configuração dos seus agentes Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Configure syslog no portal Azure
Configure o Syslog a partir do [menu Dados em Definições Avançadas](agent-data-sources.md#configuring-data-sources). Esta configuração é entregue no ficheiro de configuração de cada agente Linux.

Pode adicionar uma nova facilidade selecionando primeiro a opção Aplicar abaixo a **configuração às minhas máquinas** e, em seguida, digitar em seu nome e clicar **+**. Para cada instalação, apenas serão recolhidas mensagens com as severidades selecionadas.  Verifique as severidades das instalações específicas que pretende recolher. Não é possível fornecer quaisquer critérios adicionais para filtrar mensagens.

![Configure Syslog](media/data-sources-syslog/configure.png)

Por predefinição, todas as alterações de configuração são automaticamente empurradas para todos os agentes. Se pretender configurar o Syslog manualmente em cada agente Linux, então desverifique a caixa Aplique abaixo a *configuração para as minhas máquinas*.

### <a name="configure-syslog-on-linux-agent"></a>Configure Syslog no agente Linux
Quando o [agente Log Analytics é instalado num cliente Linux,](../../azure-monitor/learn/quick-collect-linux-computer.md)instala um ficheiro de configuração de sislog predefinido que define a instalação e a gravidade das mensagens que são recolhidas. Pode modificar este ficheiro para alterar a configuração. O ficheiro de configuração é diferente dependendo do daemon Syslog que o cliente instalou.

> [!NOTE]
> Se editar a configuração do sislog, tem de reiniciar o daemon syslog para que as alterações entrem em vigor.
>
>

#### <a name="rsyslog"></a>rsyslog
O ficheiro de configuração para rsyslog está localizado em **/etc/rsyslog.d/95-omsagent.conf**. O seu conteúdo predefinido é mostrado abaixo. Isto recolhe mensagens syslog enviadas do agente local para todas as instalações com um nível de aviso ou superior.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

Pode remover uma instalação removendo a secção do ficheiro de configuração. Pode limitar as severidades que são recolhidas para uma determinada instalação modificando a entrada dessa instalação. Por exemplo, para limitar a facilidade do utilizador a mensagens com uma gravidade de erro ou superior, modificaria essa linha do ficheiro de configuração para o seguinte:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
O ficheiro de configuração para syslog-ng é localização em **/etc/syslog-ng/syslog-ng.conf**.  O seu conteúdo predefinido é mostrado abaixo. Isto recolhe mensagens syslog enviadas do agente local para todas as instalações e todas as severidades.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Pode remover uma instalação removendo a secção do ficheiro de configuração. Pode limitar as severidades que são recolhidas para uma determinada instalação, retirando-as da sua lista.  Por exemplo, para limitar a facilidade do utilizador apenas a alertar e a mensagens críticas, modificaessa essa secção do ficheiro de configuração para o seguinte:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Recolha de dados de portas syslog adicionais
O agente Log Analytics ouve mensagens Syslog no cliente local no porto 25224.  Quando o agente é instalado, é aplicada uma configuração de sislog predefinido e encontrada no seguinte local:

* Rsyslog:`/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng:`/etc/syslog-ng/syslog-ng.conf`

Pode alterar o número da porta criando dois ficheiros de configuração: um ficheiro config FluentD e um ficheiro rsyslog-or-syslog-ng dependendo do daemon Syslog que instalou.  

* O ficheiro config FluentD deve ser `/etc/opt/microsoft/omsagent/conf/omsagent.d` um novo ficheiro localizado em: e substituir o valor na entrada da **porta** pelo seu número de porta personalizado.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* Para rsyslog, deve criar um novo `/etc/rsyslog.d/` ficheiro de configuração localizado em: e substituir o valor %SYSLOG_PORT% com o seu número de porta personalizado.  

    > [!NOTE]
    > Se modificar este valor no `95-omsagent.conf`ficheiro de configuração, será substituído quando o agente aplicar uma configuração predefinida.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* A config syslog-ng deve ser modificada copiando a configuração do exemplo abaixo e adicionando as definições modificadas `/etc/syslog-ng/`personalizadas à extremidade do ficheiro de configuração syslog-ng.conf localizado em . **Não** utilize a etiqueta predefinida **%WORKSPACE_ID%_oms** ou **%WORKSPACE_ID_OMS,** defina uma etiqueta personalizada para ajudar a distinguir as suas alterações.  

    > [!NOTE]
    > Se modificar os valores predefinidos no ficheiro de configuração, serão substituídos quando o agente aplica uma configuração predefinida.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Após a conclusão das alterações, o serviço syslog e o serviço de agente Log Analytics precisam de ser reiniciados para garantir que as alterações de configuração tenham efeito.   

## <a name="syslog-record-properties"></a>Propriedades de registo syslog
Os registos syslog têm um tipo de **Syslog** e têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador de que o evento foi recolhido. |
| Instalação |Define a parte do sistema que gerou a mensagem. |
| HostIP |Endereço IP do sistema enviando a mensagem. |
| NomedeAnfitrião |Nome do sistema enviando a mensagem. |
| Nível de gravidade |Nível de gravidade do evento. |
| Mensagem syslog |Texto da mensagem. |
| ProcessID |Identificação do processo que gerou a mensagem. |
| Horário de eventos |Data e hora que o evento foi gerado. |

## <a name="log-queries-with-syslog-records"></a>Consultas de log com registos syslog
A tabela seguinte fornece diferentes exemplos de consultas de log que recuperam registos syslog.

| Consulta | Descrição |
|:--- |:--- |
| Syslog |Todos os Syslogs. |
| Syslog &#124; onde SeverityLevel == "erro" |Todos os registos do Syslog com gravidade de erro. |
| Syslog &#124; resumir AgregadValue = contagem() por Computador |Contagem de registos syslog por computador. |
| Syslog &#124; resumir AgregadValue = contagem() por Facilidade |Contagem de registos syslog por instalações. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre consultas de [registo](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados.
* Utilize [campos personalizados](../../azure-monitor/platform/custom-fields.md) para analisar dados de registos syslog em campos individuais.
* [Configure os agentes Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) para recolher outros tipos de dados.
