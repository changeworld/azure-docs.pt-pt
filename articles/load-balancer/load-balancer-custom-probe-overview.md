---
title: Sondas de saúde à escala e fornecer HA para o seu serviço
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda a usar sondas de saúde para monitorizar casos por trás do Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 8e79f4c791d0252c719846da3aa8024b0e622dca
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477016"
---
# <a name="load-balancer-health-probes"></a>Sondas de estado de funcionamento do Balanceador de Carga

Ao utilizar regras de equilíbrio de carga com o Equilíbrio de Carga Azure, é necessário especificar as sondas de saúde para permitir que o Balancer de carga detete o estado do ponto final.  A configuração da sonda de saúde e as respostas da sonda determinam quais as instâncias de piscina de backend receberão novos fluxos. Pode utilizar sondas de saúde para detetar a falha de uma aplicação num ponto final. Também pode gerar uma resposta personalizada a uma sonda de saúde e usar a sonda de saúde para controlar o fluxo para gerir a carga ou o tempo de inatividade planeado. Quando uma sonda de saúde falhar, o Balancer load deixará de enviar novos fluxos para a respetiva instância pouco saudável. A conectividade de saída não é impactada, apenas a conectividade de entrada é impactada.

As sondas de saúde suportam vários protocolos. A disponibilidade de um protocolo específico de sonda de saúde varia de acordo com o Balancer SKU.  Adicionalmente, o comportamento do serviço varia de peso balancer SKU como mostrado nesta tabela:

| | SKU Standard | SKU Básico |
| --- | --- | --- |
| [Tipos de sonda](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Sondar o comportamento](#probedown) | Todas as sondas para baixo, todos os fluxos de TCP continuam. | Todas as sondas para baixo, todos os fluxos de TCP expiram. | 


>[!IMPORTANT]
>Reveja este documento na sua totalidade, incluindo [orientações](#design) importantes de design abaixo para criar um serviço fiável.

>[!IMPORTANT]
>As sondas de saúde load Balancer têm origem no endereço IP 168.63.129.16 e não devem ser bloqueadas para que as sondas marquem a sua instância.  Reveja o [endereço IP da fonte da sonda](#probesource) para obter detalhes.

>[!IMPORTANT]
>Independentemente do limiar de tempo limite configurado, as sondas de saúde HTTP(S) Load Balancer irão automaticamente sondar uma instância se o servidor devolver qualquer código de estado que não seja HTTP 200 OK ou se a ligação for terminada através do reset tCP.

## <a name="probe-configuration"></a><a name="probes"></a>Configuração da sonda

A configuração da sonda de saúde consiste nos seguintes elementos:

- Duração do intervalo entre sondas individuais
- Número de respostas de sonda que têm de ser observadas antes da transição da sonda para um estado diferente
- Protocolo da sonda
- Porto da sonda
- Caminho http para utilizar para HTTP GET ao utilizar sondas HTTP(S)

>[!NOTE]
>Uma definição de sonda não é obrigatória ou verificada quando se utiliza O Azure PowerShell, Azure CLI, Templates ou API. Os testes de validação da sonda só são feitos quando se utiliza o Portal Azure.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Compreender o sinal de aplicação, a deteção do sinal e a reação da plataforma

O número de respostas da sonda aplica-se a ambos

- o número de sondas bem sucedidas que permitem que uma instância seja marcada como até, e
- o número de sondas cronometradas que fazem com que uma instância seja marcada como para baixo.

Os valores de tempo limite e intervalo especificados determinam se uma instância será marcada como para cima ou para baixo.  A duração do intervalo multiplicada pelo número de respostas da sonda determina a duração durante a qual as respostas da sonda têm de ser detetadas.  E o serviço reagirá depois que as sondas necessárias foram alcançadas.

Podemos ilustrar o comportamento com um exemplo. Se tiver definido o número de respostas da sonda para 2 e o intervalo para 5 segundos, isto significa que 2 falhas de tempo de sonda devem ser observadas dentro de um intervalo de 10 segundos.  Como o momento em que uma sonda é enviada não é sincronizado quando a sua aplicação pode mudar de estado, podemos vincular o tempo para detetar por dois cenários:

1. Se a sua aplicação começar a produzir uma resposta de sonda de tempo antes da chegada da primeira sonda, a deteção destes eventos levará 10 segundos (2 x 5 segundos de intervalo) mais a duração da aplicação começando a sinalizar uma saída para quando a primeira sonda chegar.  Pode assumir que esta deteção demorará um pouco mais de 10 segundos.
2. Se a sua aplicação começar a produzir uma resposta de sonda time-out logo após a chegada da primeira sonda, a deteção destes eventos não começará até que a próxima sonda chegue (e sai) mais 10 segundos (intervalos de 2 x 5 segundos).  Pode assumir que esta deteção demorará pouco menos de 15 segundos.

Para este exemplo, uma vez que a deteção tenha ocorrido, a plataforma demorará um pouco de tempo a reagir a esta mudança.  Isto significa uma dependendo de 

1. quando a aplicação começa a mudar de estado e
2. quando esta alteração for detetada e satisfizesse os critérios exigidos (número de sondas enviadas ao intervalo especificado) e
3. quando a deteção foi comunicada em toda a plataforma 

pode assumir que a reação a uma resposta de sonda de tempo demorará entre um mínimo de pouco mais de 10 segundos e um máximo de pouco mais de 15 segundos para reagir a uma alteração do sinal da aplicação.  Este exemplo é dado para ilustrar o que está a acontecer, no entanto, não é possível prever uma duração exata para além das orientações ásperas acima referidas ilustradas neste exemplo.

>[!NOTE]
>A sonda de saúde vai sondar todos os casos de execução na piscina de backend. Se uma instância for interrompida, não será sondada até que seja reiniciada.

## <a name="probe-types"></a><a name="types"></a>Tipos de sonda

O protocolo utilizado pela sonda de saúde pode ser configurado para um dos seguintes:

- [Ouvintes do TCP](#tcpprobe)
- [Pontos finais HTTP](#httpprobe)
- [PONTOS finais HTTPS](#httpsprobe)

Os protocolos disponíveis dependem do Balancer SKU utilizado:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| SKU Standard |    &#9989; |   &#9989; |   &#9989; |
| SKU Básico |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a>Sonda TCP

As sondas TCP iniciam uma ligação executando um aperto de mão TCP aberto a três vias com a porta definida.  As sondas TCP terminam uma ligação com um aperto de mão de 4 vias.

O intervalo mínimo da sonda é de 5 segundos e o número mínimo de respostas não saudáveis é de 2.  A duração total de todos os intervalos não pode exceder 120 segundos.

Uma sonda TCP falha quando:
* O ouvinte da TCP na instância não responde de todo durante o período de tempo.  Uma sonda é marcada com base no número de pedidos de sonda cronometradas, que foram configurados para ficarem sem resposta antes de marcar a sonda.
* A sonda recebe um reset TCP da instância.

O seguinte ilustra como pode expressar este tipo de configuração de sonda num modelo de Gestor de Recursos:

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a> SONDA HTTP / HTTPS

>[!NOTE]
>A sonda HTTPS só está disponível para o [Standard Load Balancer](load-balancer-standard-overview.md).

As sondas HTTP e HTTPS baseiam-se na sonda TCP e emitem um HTTP GET com o caminho especificado. Ambas as sondas suportam caminhos relativos para o HTTP GET. As sondas HTTPS são as mesmas que as sondas HTTP com a adição de um invólucro de Segurança da Camada de Transporte (TLS, anteriormente conhecido como SSL). A sonda de saúde é marcada quando a instância responde com um status HTTP 200 no período de tempo.  A sonda de saúde tenta verificar a porta de sonda de saúde configurada a cada 15 segundos por padrão. O intervalo mínimo da sonda é de 5 segundos. A duração total de todos os intervalos não pode exceder 120 segundos.

As sondas HTTP / HTTPS também podem ser úteis para implementar a sua própria lógica para remover instâncias da rotação do equilíbrio de carga se a porta da sonda for também o ouvinte do próprio serviço. Por exemplo, pode decidir remover uma instância se estiver acima de 90% de CPU e devolver um estatuto de http não-200. 

> [!NOTE] 
> A Sonda HTTPS requer a utilização de certificados baseados que tenham um haxixe de assinatura mínima de SHA256 em toda a cadeia.

Se utilizar os Serviços cloud e tiver funções web que utilizem w3wp.exe, também obtém uma monitorização automática do seu website. Falhas no código do seu site devolvem um estatuto não-200 à sonda de equilíbrio de carga.

Uma sonda HTTP / HTTPS falha quando:
* O ponto final da sonda devolve um código de resposta HTTP diferente de 200 (por exemplo, 403, 404 ou 500). Isto marcará a sonda de saúde imediatamente. 
* O ponto final da sonda não responde durante o mínimo do intervalo da sonda e o período de 30 segundos. Vários pedidos de sonda podem ficar sem resposta antes que a sonda seja marcada como não funcionando e até que a soma de todos os intervalos de tempo for atingido.
* O ponto final da sonda fecha a ligação através de um reset TCP.

O seguinte ilustra como pode expressar este tipo de configuração de sonda num modelo de Gestor de Recursos:

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Sonda de agente convidado (apenas clássica)

As funções de serviço em nuvem (funções de trabalhador e funções web) utilizam um agente convidado para monitorização da sonda por padrão.  Uma sonda de agente convidado é uma configuração de último recurso.  Utilize sempre uma sonda de saúde explicitamente com uma sonda TCP ou HTTP. Uma sonda de agente convidado não é tão eficaz como sondas explicitamente definidas para a maioria dos cenários de aplicação.

Uma sonda de agente convidado é uma verificação do agente convidado dentro da VM. Em seguida, ouve e responde com uma resposta HTTP 200 OK apenas quando a instância está no estado Ready. (Outros estados são ocupados, reciclando ou parando.)

Para mais informações, consulte Configure o ficheiro de definição de [serviço (csdef) para sondas](https://msdn.microsoft.com/library/azure/ee758710.aspx) de saúde ou [Inicie a criação de um equilibrador](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services)de carga público para serviços na nuvem .

Se o agente convidado não responder com HTTP 200 OK, o equilibrante de carga marca a ocorrência como sem resposta. Em seguida, para de enviar fluxos para este caso. O equilibrador de carga continua a verificar a ocorrência. 

Se o agente convidado responder com um HTTP 200, o equilibrador de carga envia novos fluxos para essa instância novamente.

Quando se utiliza uma função web, o código do site normalmente funciona em w3wp.exe, que não é monitorizado pelo tecido Azure ou agente convidado. As falhas em w3wp.exe (por exemplo, respostas HTTP 500) não são reportadas ao agente convidado. Consequentemente, o equilibrista de carga não retira essa instância da rotação.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Sondar o comportamento

As sondas de saúde TCP, HTTP e HTTPS são consideradas saudáveis e marcam o ponto final como saudável quando:

* A sonda de saúde é bem sucedida uma vez depois das botas VM.
* O número especificado de sondas necessárias para marcar o ponto final como saudável foi alcançado.

Qualquer ponto final que tenha alcançado um estado saudável é elegível para receber novos fluxos.  

> [!NOTE]
> Se a sonda de saúde flutuar, o equilibrista de carga espera mais tempo antes de colocar o ponto final de volta no estado saudável. Este tempo extra de espera protege o utilizador e a infraestrutura e é uma política intencional.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Sondar o comportamento

### <a name="tcp-connections"></a>Ligações TCP

Novas ligações TCP conseguirão manter-se saudáveis no final.

Se a sonda de saúde de um ponto final falhar, as ligações tCP estabelecidas a este ponto final continuam.

Se todas as sondas, para todos os casos, numa piscina de backend falharem, nenhum novo fluxo será enviado para a piscina de backend. O Balancer de Carga Padrão permitirá que os fluxos de TCP estabelecidos continuem.  O Equilíbrio de Carga Básica terminará todos os fluxos de TCP existentes para a piscina de backend.
 
Load Balancer é um serviço de passagem (não termina ligações TCP) e o fluxo está sempre entre o cliente e o SISTEMA de hóspedes da VM e a aplicação. Uma piscina com todas as sondas para baixo fará com que uma extremidade frontal não responda às tentativas abertas de ligação TCP (SYN), uma vez que não existe um ponto final saudável para receber o fluxo e responder com um SYN-ACK.

### <a name="udp-datagrams"></a>Dados da UDP

Os dados da UDP serão entregues em pontos finais saudáveis.

A UDP não tem ligação e não há nenhum estado de fluxo rastreado para a UDP. Se alguma sonda de saúde do backend failing, os fluxos uDP existentes irão mover-se para outro caso saudável na piscina de backend.

Se todas as sondas, para todos os casos, numa piscina de backend falharem, os fluxos uDP existentes terminarão para os Equilíbrios básicos e standard de carga.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Endereço IP de fonte de sonda

O Load Balancer utiliza um serviço de sondagem distribuído para o seu modelo de saúde interna. O serviço de sondagem reside em cada anfitrião onde os VMs e podem ser programados a pedido para gerar sondas de saúde de acordo com a configuração do cliente. O tráfego de sonda de saúde está diretamente entre o serviço de sondagem que gera a sonda de saúde e o VM do cliente. Todas as sondas de saúde load Balancer têm origem no endereço IP 168.63.129.16 como fonte.  Pode utilizar o espaço de endereço IP dentro de um VNet que não é espaço RFC1918.  Utilizando um endereço IP reservado globalmente, o endereço IP da Microsoft reduz a possibilidade de um conflito de endereços IP com o espaço de endereço IP que utiliza dentro do VNet.  Este endereço IP é o mesmo em todas as regiões e não muda e não é um risco de segurança porque apenas o componente interno da plataforma Azure pode obter um pacote deste endereço IP. 

A etiqueta de serviço AzureLoadBalancer identifica este endereço IP de origem nos seus [grupos](../virtual-network/security-overview.md) de segurança de rede e permite o tráfego de sondade saúde por defeito.

Para além das sondas de saúde load Balancer, as [seguintes operações utilizam este endereço IP:](../virtual-network/what-is-ip-address-168-63-129-16.md)

- Permite ao Agente VM comunicar com a plataforma para sinalizar que está num estado "Pronto"
- Permite que a comunicação com o servidor virtual DNS forneça resolução de nome filtrada a clientes que não definam servidores DNS personalizados.  Esta filtragem garante que os clientes só podem resolver os nomes de hospedagem da sua implantação.
- Permite ao VM obter um endereço IP dinâmico do serviço DHCP em Azure.

## <a name="design-guidance"></a><a name="design"></a>Orientação de design

As sondas de saúde são usadas para tornar o seu serviço resiliente e permitir que ele se dimensione. Uma configuração errada ou um mau padrão de design podem afetar a disponibilidade e escalabilidade do seu serviço. Reveja todo este documento e considere qual é o impacto no seu cenário quando esta resposta da sonda é marcada para baixo ou marcada para baixo, e como impacta a disponibilidade do seu cenário de candidatura.

Ao conceber o modelo de saúde para a sua aplicação, deve sondar uma porta num ponto final que reflita a saúde desse caso __e__ o serviço de aplicação que está a prestar.  A porta de aplicação e a porta de sonda não são obrigadas a ser as mesmas.  Em alguns cenários, pode ser desejável que a porta da sonda seja diferente da porta em que a sua aplicação presta serviço.  

Por vezes, pode ser útil para a sua aplicação gerar uma resposta de sonda de saúde para não só detetar a sua saúde de aplicação, mas também sinalizar diretamente para load Balancer se a sua instância deve receber ou não novos fluxos.  Pode manipular a resposta da sonda para permitir que a sua aplicação crie pressão de fundo e entrega de novos fluxos para uma instância, falhando a sonda de saúde ou preparando-se para a manutenção da sua aplicação e iniciando a drenagem do seu cenário.  Ao utilizar o Balancer de Carga Padrão, um sinal de [sonorização](#probedown) permitirá sempre que os fluxos de TCP continuem até ao tempo de paragem ou ao fecho da ligação. 

Para o equilíbrio de carga uDP, deve gerar um sinal de sonda de saúde personalizado a partir do ponto final e utilizar uma sonda de saúde TCP, HTTP ou HTTPS direcionando o ouvinte correspondente para refletir a saúde da sua aplicação UDP.

Ao utilizar [regras de equilíbrio de carga seletivas](load-balancer-ha-ports-overview.md) ha portas com o Equilíbrio de Carga [Padrão,](load-balancer-standard-overview.md)todas as portas são equilibradas em carga e uma única resposta da sonda de saúde deve refletir o estado de toda a instância.

Não traduza ou proxy uma sonda de saúde através da instância que recebe a sonda de saúde para outro caso no seu VNet, uma vez que esta configuração pode levar a falhas em cascata no seu cenário.  Considere o seguinte cenário: um conjunto de aparelhos de terceiros é implantado na piscina de backend de um recurso Load Balancer para fornecer escala e redundância para os aparelhos e a sonda de saúde está configurada para sondar uma porta que o aparelho de terceiros proxies ou traduz para outras máquinas virtuais atrás do aparelho.  Se sondar a mesma porta que está a utilizar para traduzir ou proxy pedidos para as outras máquinas virtuais atrás do aparelho, qualquer resposta da sonda de uma única máquina virtual atrás do aparelho marcará o aparelho morto. Esta configuração pode levar a uma falha em cascata de todo o cenário de aplicação como resultado de um único ponto final traseiro atrás do aparelho.  O gatilho pode ser uma falha intermitente da sonda que fará com que o Balancer de Carga marque o destino original (a instância do aparelho) e, por sua vez, pode desativar todo o cenário de aplicação. Sondar a saúde do próprio aparelho. A seleção da sonda para determinar o sinal de saúde é uma consideração importante para os cenários de aparelhos virtuais de rede (NVA) e deve consultar o seu fornecedor de aplicações para saber qual é o sinal de saúde adequado para tais cenários.

Se não permitir o IP de [origem](#probesource) da sonda nas suas políticas de firewall, a sonda de saúde falhará, uma vez que não consegue chegar à sua instância.  Por sua vez, o Balancer load irá marcar o seu caso devido à falha da sonda de saúde.  Esta configuração errada pode fazer com que o seu cenário de aplicação equilibrado de carga falhe.

Para que a sonda de saúde load Balancer marque a sua instância, **deve** permitir este endereço IP em quaisquer [grupos](../virtual-network/security-overview.md) de segurança da rede Azure e políticas locais de firewall.  Por padrão, todos os grupos de segurança da rede incluem a etiqueta de [serviço](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer para permitir o tráfego de sondas de saúde.

Se desejar testar uma falha de sonda de saúde ou marcar uma instância individual, pode utilizar um grupo de segurança de [rede](../virtual-network/security-overview.md) para bloquear explicitamente a sonda de saúde (porta de destino ou IP de [origem)](#probesource)e simular a falha de uma sonda.

Não configure o seu VNet com a gama de endereços IP da Microsoft que contém 168.63.129.16.  Tais configurações colidirão com o endereço IP da sonda de saúde e podem fazer com que o seu cenário falhe.

Se tiver várias interfaces no seu VM, tem de garantir que responde à sonda na interface em que o recebeu.  Pode ser necessário obter o endereço de rede para traduzir este endereço no VM numa base de interface.

Não ative [os selos temporais da TCP](https://tools.ietf.org/html/rfc1323).  Permitir os selos temporais da TCP pode fazer com que as sondas de saúde falhem devido à queda dos pacotes de TCP pela pilha de Os TCP, que resulta na marcação do Balancer de Carga para baixo do respetivo ponto final.  Os selos temporais tCP são ativados rotineiramente por padrão em imagens VM endurecidas de segurança e devem ser desativados.

## <a name="monitoring"></a>Monitorização

Tanto o Equilíbrio de [Carga Padrão](load-balancer-standard-overview.md) público como interna expõe mato por ponto final e o estado da sonda de saúde endpoint endpoint como métricas multidimensionais através do Monitor Azure. Estas métricas podem ser consumidas por outros serviços Azure ou aplicações parceiras. 

O Balancer de Carga Pública básico expõe o estado da sonda de saúde resumido por piscina de backend através de registos do Monitor Azure.  Os registos do Monitor Azure não estão disponíveis para os equilibradores de carga básicos internos.  Pode utilizar [os registos do Monitor Azure](load-balancer-monitor-log.md) para verificar o estado de saúde da sonda do equilibrador de carga pública e a contagem de sondas. O registo pode ser utilizado com Power BI ou Azure Operational Insights para fornecer estatísticas sobre o estado de saúde do equilibrador de carga.

## <a name="limitations"></a>Limitações

- As sondas HTTPS não suportam a autenticação mútua com um certificado de cliente.
- Deve assumir que as sondas de saúde falharão quando os selos temporais do TCP estiverem ativados.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Balanceador de Carga Standard](load-balancer-standard-overview.md)
- [Começar a criar um equilibrador de carga pública em Gestor de Recursos usando powerShell](quickstart-create-standard-load-balancer-powershell.md)
- [API DE REPOUSO para sondas de saúde](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Solicite novas capacidades de sonda de saúde com [a fatura de utilizador do Balancer](https://aka.ms/lbuservoice) de Carga
