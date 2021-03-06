---
title: Conecte-se e comunique com serviços em Tecido de Serviço Azure
description: Aprenda a resolver, conectar e comunicar com serviços em Tecido de Serviço.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: e57d169decf482f8b8be1e3b31a07690bc222c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458237"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Conecte-se e comunique com serviços em Tecido de Serviço
No Service Fabric, um serviço funciona algures num cluster de Tecido de Serviço, tipicamente distribuído por vários VMs. Pode ser movido de um lugar para outro, quer pelo proprietário do serviço, quer automaticamente pela Fabric de Serviço. Os serviços não estão estáticos ligados a uma determinada máquina ou endereço.

Uma aplicação De Tecido de Serviço é geralmente composta por muitos serviços diferentes, onde cada serviço realiza uma tarefa especializada. Estes serviços podem comunicar entre si para formar uma função completa, como a renderização de diferentes partes de uma aplicação web. Existem também aplicações de clientes que se ligam e comunicam com os serviços. Este documento discute como estabelecer comunicação com e entre os seus serviços em Tecido de Serviço.

## <a name="bring-your-own-protocol"></a>Traga o seu próprio protocolo
O Service Fabric ajuda a gerir o ciclo de vida dos seus serviços, mas não tome decisões sobre o que os seus serviços fazem. Isto inclui comunicação. Quando o seu serviço é aberto pela Service Fabric, essa é a oportunidade do seu serviço de configurar um ponto final para pedidos de entrada, utilizando qualquer protocolo ou pilha de comunicação que queira. O seu serviço ouvirá um endereço **IP:porta** normal utilizando qualquer esquema de endereçamento, como um URI. Várias instâncias de serviço ou réplicas podem partilhar um processo de hospedagem, caso em que terão de utilizar portas diferentes ou utilizar um mecanismo de partilha de portas, como o condutor do kernel http.sys no Windows. Em qualquer dos casos, cada instância de serviço ou réplica num processo de acolhimento deve ser exclusivamente endereçada.

![pontos finais de serviço][1]

## <a name="service-discovery-and-resolution"></a>Descoberta e resolução de serviços
Num sistema distribuído, os serviços podem passar de uma máquina para outra ao longo do tempo. Isto pode acontecer por várias razões, incluindo o equilíbrio de recursos, upgrades, failovers ou scale-out. Isto significa que os endereços finais do serviço mudam à medida que o serviço se move para nós com diferentes endereços IP, e pode abrir em diferentes portas se o serviço utilizar uma porta dinamicamente selecionada.

![Distribuição de serviços][7]

A Service Fabric fornece um serviço de descoberta e resolução chamado Serviço de Nomeação. O Serviço de Naming mantém uma tabela que mapeia as instâncias de serviço para os endereços finais que ouvem. Todas as instâncias de serviço nomeadas no Service Fabric `"fabric:/MyApplication/MyService"`têm nomes únicos representados como URIs, por exemplo, . O nome do serviço não muda ao longo da vida útil do serviço, são apenas os endereços finais que podem mudar quando os serviços se movem. Isto é análogo a websites que têm URLs constantes mas onde o endereço IP pode mudar. E semelhante ao DNS na web, que resolve urLs do website para endereços IP, o Service Fabric tem um registrador que mapeia nomes de serviço para o seu endereço de ponto final.

![pontos finais de serviço][2]

A resolução e a ligação aos serviços envolve os seguintes passos executados em loop:

* **Resolver**: Obtenha o ponto final que um serviço publicou no Serviço de Nomeação.
* **Ligar**: Ligue-se ao serviço sobre qualquer protocolo que utilize nesse ponto final.
* **Retry**: Uma tentativa de ligação pode falhar por várias razões, por exemplo, se o serviço tiver mudado desde a última vez que o endereço final foi resolvido. Nesse caso, os passos de determinação e de ligação anteriores têm de ser novamente experimentados, e este ciclo é repetido até que a ligação tenha sucesso.

## <a name="connecting-to-other-services"></a>Ligação a outros serviços
Os serviços que se ligam entre si dentro de um cluster geralmente podem aceder diretamente aos pontos finais de outros serviços porque os nós de um cluster estão na mesma rede local. Para facilitar a ligação entre serviços, o Service Fabric presta serviços adicionais que utilizam o Serviço de Nomeação. Um serviço DNS e um serviço de procuração inversa.


### <a name="dns-service"></a>Serviço DNS
Uma vez que muitos serviços, especialmente serviços contentorizados, podem ter um nome URL existente, poder resolvê-los usando o protocolo Padrão DNS (em vez do protocolo de Serviço de Nomeação) é muito conveniente, especialmente em cenários de aplicação "levantar e mudar". Isto é exatamente o que o serviço DNS faz. Permite-lhe mapear os nomes dNS para um nome de serviço e, portanto, resolver endereços IP do ponto final. 

Como mostra o seguinte diagrama, o serviço DNS, em execução no cluster Service Fabric, mapeia os nomes de DNS para nomes de serviço que são depois resolvidos pelo Serviço de Nomeação para devolver os endereços de ponto final para ligar. O nome DNS para o serviço é fornecido no momento da criação. 

![pontos finais de serviço][9]

Para mais detalhes sobre como utilizar o serviço DNS consulte o serviço DNS no artigo [Azure Service Fabric.](service-fabric-dnsservice.md)

### <a name="reverse-proxy-service"></a>Serviço de procuração inversa
O proxy inverso aborda os serviços no cluster que expõe os pontos finais http, incluindo HTTPS. O representante inverso simplifica consideravelmente a chamada de outros serviços e os seus métodos através de um formato URI específico e lida com as etapas de determinação, ligação, retry necessárias para que um serviço comunique com outro usando o Serviço de Nomeação. Por outras palavras, esconde o Serviço de Nomeação de si ao ligar para outros serviços, tornando isto tão simples como chamar um URL.

![pontos finais de serviço][10]

Para obter mais detalhes sobre como usar o serviço de procuração inversa consulte O proxy inverso no artigo [Azure Service Fabric.](service-fabric-reverseproxy.md)

## <a name="connections-from-external-clients"></a>Ligações de clientes externos
Os serviços que se ligam entre si dentro de um cluster geralmente podem aceder diretamente aos pontos finais de outros serviços porque os nós de um cluster estão na mesma rede local. Em alguns ambientes, no entanto, um cluster pode estar por trás de um equilibrador de carga que encaminha o tráfego externo através de um conjunto limitado de portas. Nestes casos, os serviços ainda podem comunicar entre si e resolver endereços usando o Serviço de Nomeação, mas devem ser tomadas medidas adicionais para permitir que clientes externos se conectem aos serviços.

## <a name="service-fabric-in-azure"></a>Tecido de serviço em Azure
Um cluster de tecido de serviço em Azure é colocado atrás de um Equilíbrio de Carga Azure. Todo o tráfego externo para o cluster deve passar pelo equilibrador de carga. O equilibrador de carga encaminhará automaticamente o tráfego para uma dada porta para um *nó* aleatório que tem a mesma porta aberta. O Azure Load Balancer só sabe sobre portas abertas nos *nós,* não sabe sobre portas abertas por *serviços*individuais.

![Topologia azure Load Balancer e Tecido de Serviço][3]

Por exemplo, para aceitar o tráfego externo no porto **80,** devem ser configuradas as seguintes coisas:

1. Escreva um serviço que ouça no porto 80. Configure a porta 80 no ServiceManifest.xml do serviço e abra um ouvinte no serviço, por exemplo, um servidor web auto-hospedado.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Crie um Cluster de Tecido de Serviço em Azure e especifique a porta **80** como uma porta de ponto final personalizada para o tipo de nó que irá acolher o serviço. Se tiver mais do que um nó, pode configurar uma restrição de *colocação* no serviço para garantir que funciona apenas no tipo de nó que tem a porta de ponto final personalizada aberta.

    ![Abra uma porta em um tipo de nó][4]
3. Uma vez criado o cluster, configure o Balancer de Carga Azure no Grupo de Recursos do cluster para encaminhar o tráfego na porta 80. Ao criar um cluster através do portal Azure, este é configurado automaticamente para cada porta de ponto final personalizado que foi configurada.

    ![Tráfego avançado no Equilíbrio de Carga Azure][5]
4. O Balancer de Carga Azure utiliza uma sonda para determinar se deve ou não enviar tráfego para um determinado nó. A sonda verifica periodicamente um ponto final em cada nó para determinar se o nó está ou não a responder. Se a sonda não receber uma resposta após um número configurado de vezes, o equilibrador de carga para de enviar tráfego para esse nó. Ao criar um cluster através do portal Azure, uma sonda é automaticamente configurada para cada porta de ponto final personalizada que foi configurada.

    ![Tráfego avançado no Equilíbrio de Carga Azure][8]

É importante lembrar que o Azure Load Balancer e a sonda só sabem dos *nós*, e não dos *serviços* que estão a funcionar nos nós. O Azure Load Balancer enviará sempre tráfego para nós que respondam à sonda, pelo que deve ser tomado cuidado para garantir que os serviços estão disponíveis nos nós que são capazes de responder à sonda.

## <a name="reliable-services-built-in-communication-api-options"></a>Serviços fiáveis: Opções api de comunicação incorporadas
Os navios-quadro de Serviços Fiáveis com várias opções de comunicação pré-construídas. A decisão sobre qual deles irá funcionar melhor para si depende da escolha do modelo de programação, do quadro de comunicação e da linguagem de programação em que os seus serviços estão escritos.

* **Nenhum protocolo específico:**  Se você não tem uma escolha particular de estrutura de comunicação, mas você quer obter algo em funcionamento rapidamente, então a opção ideal para você é [remoing](service-fabric-reliable-services-communication-remoting.md)de serviço , que permite procedimentos remotos fortemente digitados para Serviços Fiáveis e Atores Fiáveis. Esta é a maneira mais fácil e rápida de começar com a comunicação de serviço. O remoing de serviço trata da resolução de endereços de serviço, ligação, retentativa e manuseamento de erros. Isto está disponível tanto para aplicações C# como Java.
* **HTTP**: Para comunicação agnóstica de idiomas, http fornece uma escolha padrão da indústria com ferramentas e servidores HTTP disponíveis em muitos idiomas diferentes, todos suportados pelo Service Fabric. Os serviços podem utilizar qualquer stack HTTP disponível, incluindo [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) para aplicações C#. Os clientes escritos em C# podem alavancar as `ICommunicationClient` aulas e e classes, `ServicePartitionClient` enquanto para Java, usar `CommunicationClient` as aulas e e classes, `FabricServicePartitionClient` para resolução de [serviços, conexões HTTP e voltas](service-fabric-reliable-services-communication.md)de retry .
* **WCF**: Se tiver um código existente que utiliza o WCF `WcfCommunicationListener` como sua `WcfCommunicationClient` estrutura `ServicePartitionClient` de comunicação, então pode usar o lado do servidor e classes para o cliente. No entanto, isto só está disponível para aplicações C# em clusters baseados no Windows. Para mais detalhes, consulte este artigo sobre [a implementação baseada no WCF da pilha de comunicações](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Utilização de protocolos personalizados e outros quadros de comunicação
Os serviços podem usar qualquer protocolo ou enquadramento para comunicação, seja um protocolo binário personalizado sobre tomadas TCP, ou eventos de streaming através de Hubs de [Eventos Azure](https://azure.microsoft.com/services/event-hubs/) ou [Azure IoT Hub.](https://azure.microsoft.com/services/iot-hub/) O Service Fabric fornece APIs de comunicação em que pode ligar a sua pilha de comunicação, enquanto todo o trabalho para descobrir e conectar é abstrato de si. Consulte este artigo sobre o modelo de [comunicação De Serviço Fiável](service-fabric-reliable-services-communication.md) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre os conceitos e APIs disponíveis no modelo de [comunicação De Serviços Fiáveis,](service-fabric-reliable-services-communication.md)em seguida, começar rapidamente com [o serviço remoting](service-fabric-reliable-services-communication-remoting.md) ou ir em profundidade para aprender a escrever um ouvinte de comunicação usando [Web API com self-host OWIN](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
