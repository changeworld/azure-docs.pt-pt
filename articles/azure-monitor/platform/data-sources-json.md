---
title: Recolha de dados personalizados da JSON no Monitor Azure [ Monitor] Microsoft Docs
description: As fontes de dados personalizadas da JSON podem ser recolhidas no Monitor Azure utilizando o Agente de Análise de Registo para o Linux.  Estas fontes de dados personalizadas podem ser scripts simples que devolvem a JSON, como o curl ou um dos mais de 300 plugins da FluentD. Este artigo descreve a configuração necessária para esta recolha de dados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 49eb3fa22bc9afffb9e93f3152cdc00323b76d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662166"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Recolha de fontes de dados personalizadas da JSON com o agente Log Analytics para o Linux no Monitor Azure
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

As fontes de dados personalizadas da JSON podem ser recolhidas no [Monitor Azure](data-platform.md) utilizando o agente Log Analytics para o Linux.  Estas fontes de dados personalizadas podem ser scripts simples que devolvem jSON, como [o caracol](https://curl.haxx.se/) ou um dos [mais de 300 plugins da FluentD.](https://www.fluentd.org/plugins/all) Este artigo descreve a configuração necessária para esta recolha de dados.


> [!NOTE]
> O agente Delog Analytics para Linux v1.1.0-217+ é necessário para dados personalizados da JSON

## <a name="configuration"></a>Configuração

### <a name="configure-input-plugin"></a>Configure plugin de entrada

Para recolher dados da JSON `oms.api.` no Monitor Azure, adicione ao início de uma etiqueta FluentD num plugin de entrada.

Por exemplo, segue-se `exec-json.conf` um `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`ficheiro de configuração separado em .  Isto utiliza o plugin `exec` FluentD para executar um comando de caracóis a cada 30 segundos.  A saída deste comando é recolhida pelo plugin de saída JSON.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
O ficheiro de `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` configuração adicionado sob exigirá que a sua propriedade seja alterada com o seguinte comando.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Configure plugin de saída 
Adicione a seguinte configuração de plugin de saída à configuração principal dentro `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` ou como um ficheiro de configuração separado colocado em`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Reiniciar o agente Delog Analytics para Linux
Reinicie o agente Log Analytics para o serviço Linux com o seguinte comando.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Saída
Os dados serão recolhidos no Monitor `<FLUENTD_TAG>_CL`Azure com um tipo de registo de .

Por exemplo, a `tag oms.api.tomcat` etiqueta personalizada no Monitor `tomcat_CL`Azure com um tipo de registo de .  Pode recuperar todos os registos deste tipo com a seguinte consulta de registo.

    Type=tomcat_CL

As fontes de dados da JSON são suportadas, mas são indexadas fora do campo dos pais. Por exemplo, os seguintes dados da JSON `tag_s : "[{ "a":"1", "b":"2" }]`são devolvidos de uma consulta de registo como .

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados. 
