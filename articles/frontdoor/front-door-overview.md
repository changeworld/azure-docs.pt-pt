---
title: Porta da frente azure [ Microsoft Docs
description: Este artigo apresenta uma descrição geral do Azure Front Door. Descubra se é a escolha certa para equilibrar o tráfego de utilizadores para a sua aplicação.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: sharadag
ms.openlocfilehash: b2ee41324cfaefa4d5aec3aa02b2d0d8c75da78f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879127"
---
# <a name="what-is-azure-front-door"></a>O que é o Azure Front Door?
A Porta Frontal Azure permite-lhe definir, gerir e monitorizar o encaminhamento global para o seu tráfego web, otimizando para melhor desempenho e falha global instantânea para alta disponibilidade. Com a Porta da Frente, você pode transformar suas aplicações globais (multi-regiões) de consumo e empresa em aplicações modernas robustas e personalizadas de alto desempenho, APIs e conteúdo que atinge um público global com Azure.

O Front Door funciona na Camada 7 ou na camada HTTP/HTTPS e utiliza o protocolo anycast com TCP dividido e a rede global da Microsoft para melhorar a conectividade global. Assim, em função do método de encaminhamento que selecionar na configuração, pode garantir que o Front Door está a encaminhar os pedidos dos seus clientes para o back-end de aplicação mais rápido e disponível. Os back-ends de aplicação são serviços com acesso à Internet alojados dentro ou fora do Azure. O Front Door proporciona vários [métodos de encaminhamento de tráfego](front-door-routing-methods.md) e [opções de monitorização de estado de funcionamento de back-end](front-door-health-probes.md) para satisfazer diferentes necessidades das aplicações e modelos de ativação pós-falha automática. Tal como o [Gestor de Tráfego](../traffic-manager/traffic-manager-overview.md), o Front Door é resiliente a falhas, incluindo a falhas numa região do Azure inteira.

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. Se estiver à procura de um encaminhamento global baseado em DNS e **não** tiver requisitos em termos de terminação de protocolo TLS (Transport Layer Security) ("descarga de SSL") ou de processamento de camada de aplicação por pedido HTTP/HTTPS, reveja [Gestor de Tráfego](../traffic-manager/traffic-manager-overview.md). Se estiver à procura de balanceamento de carga entre os seus servidores numa região para camada de aplicação, veja [Gateway de Aplicação](../application-gateway/application-gateway-introduction.md) e, para o balanceamento de carga da camada de rede, veja [Balanceador de Carga](../load-balancer/load-balancer-overview.md). Combinar estas soluções conforme necessário poderá trazer benefícios aos seus cenários completos.
>
> Para uma comparação de opções de equilíbrio de carga Azure, consulte [a visão geral das opções de equilíbrio de carga em Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

As seguintes funcionalidades estão incluídas com o Front Door:

## <a name="accelerate-application-performance"></a>Acelerar o desempenho das aplicações
Ao utilizar o protocolo anycast baseado em TCP dividido, o Front Door assegura que os seus utilizadores finais estabelecem uma ligação imediata ao POP (Point of Presence – ponto de presença) do Front Door mais próximo. A utilização da rede global da Microsoft para estabelecer ligação com os seus back-ends de aplicação a partir de POPs do Front Doors permite assegurar uma fiabilidade e disponibilidade mais elevadas sem comprometer o desempenho. Esta conectividade ao seu back-end também se baseia numa menor latência de rede. Saiba mais sobre as técnicas de encaminhamento do Front Door, tais como o [TCP Dividido](front-door-routing-architecture.md#splittcp) e o [Protocolo Anycast](front-door-routing-architecture.md#anycast).

## <a name="increase-application-availability-with-smart-health-probes"></a>Aumentar a disponibilidade das aplicações com sondas inteligentes de estado de funcionamento

O Front Door faz com que as suas aplicações críticas tenham uma elevada disponibilidade graças às respetivas sondas inteligentes de estado de funcionamento que monitorizam os back-ends em termos de latência e disponibilidade e fornecem ativação pós-falha automática e instantânea quando um back-end fica inativo. Desta forma, pode executar operações de manutenção planeada nas suas aplicações sem tempo de inatividade. Enquanto a manutenção está a decorrer, o Front Door direciona o tráfego para back-ends alternativos.

## <a name="url-based-routing"></a>Encaminhamento com base no URL
O Encaminhamento Baseado no Caminho do URL permite-lhe encaminhar o tráfego para agrupamentos de back-end com base nos caminhos de URL do pedido. Um dos cenários consiste em encaminhar pedidos de diferentes tipos de conteúdos para diversos agrupamentos de back-end.

Por exemplo, os pedidos de `http://www.contoso.com/users/*` são encaminhados para UserProfilePool e os pedidos de `http://www.contoso.com/products/*` são encaminhados para ProductInventoryPool.  O Front Door possibilita cenários de correspondência de rotas ainda mais complexos com recurso ao algoritmo de melhor correspondência, pelo que se nenhum dos padrões de caminho corresponder, a sua regra de encaminhamento predefinida para `http://www.contoso.com/*` é selecionada e o tráfego é direcionado para a regra de encaminhamento catch-all predefinida. Saiba mais em [Correspondência de Rotas](front-door-route-matching.md).

## <a name="multiple-site-hosting"></a>Alojamento de vários sites
O alojamento de múltiplos sites permite-lhe configurar mais do que um site na mesma configuração do Front Door. Esta funcionalidade permite-lhe configurar uma topologia mais eficiente para as suas implementações ao adicionar diferentes sites a uma única configuração do Front Door. Com base na arquitetura da sua aplicação, pode configurar a Porta frontal do Azure para direcionar cada web site para a sua própria piscina de backend ou ter vários web sites direcionados para a mesma piscina de backend. Por exemplo, o Front Door pode servir o tráfego de `images.contoso.com` e `videos.contoso.com` a partir de dois conjuntos de back-end chamados ImagePool e VideoPool. Em alternativa, pode configurar os dois anfitriões de front-end de forma a direcionar o tráfego para um único conjunto de back-end chamado MediaPool.

Da mesma forma, pode ter dois domínios diferentes, `www.contoso.com` e `www.fabrikam.com`, configurados no mesmo Front Door.

## <a name="session-affinity"></a>Afinidade de sessão
A funcionalidade de afinidade de sessão com base em cookies é útil quando quiser manter uma sessão de utilizador no mesmo back-end de aplicação. Ao utilizar cookies geridos pelo Front Door, o tráfego subsequente de uma sessão de utilizador é direcionado para o mesmo back-end de aplicação para efeitos de processamento. Esta funcionalidade é importante quando o estado da sessão é guardado localmente no back-end para uma sessão de utilizador.

## <a name="tls-termination"></a>TLS rescisão
A Porta Frontal suporta a rescisão de TLS na borda, ou seja, os utilizadores individuais podem configurar uma ligação TLS com ambientes frontdoor em vez de a estabelecer em ligações de longo curso com o backend da aplicação. Além disso, o Front Door suporta conectividade HTTP e HTTPS entre ambientes do Front Door e os seus back-ends. Assim, também pode configurar encriptação TLS de ponta a ponta. Por exemplo, se o Front Door da carga de trabalho da sua aplicação receber mais de 5000 pedidos num minuto devido à reutilização de ligações recentes para os serviços ativos, só estabelecerá, por exemplo, cerca de 500 ligações com o seu back-end de aplicação, o que resultará na redução significativa da carga dos seus back-ends.

## <a name="custom-domains-and-certificate-management"></a>Domínios personalizados e gestão de certificados
Se utilizar o Front Door para entregar conteúdos e se quiser que o seu próprio nome de domínio seja visível no URL do Front Door, é necessário um domínio personalizado. Ter um nome de domínio visível pode ser conveniente para os seus clientes e útil para fins de imagem corporativa.
O Front Door também suporta HTTPS para nomes de domínio personalizados. Utilize esta funcionalidade escolhendo certificados geridos pela Porta Frontal para o seu tráfego ou carregando o seu próprio certificado Personalizado TLS/SSL.

## <a name="application-layer-security"></a>Segurança de camada de aplicação
A Porta Frontal Azure permite-lhe autor de regras personalizadas de firewall de aplicação web (WAF) para controlo de acesso para proteger a sua carga de trabalho HTTP/HTTPS da exploração com base em endereços IP do cliente, código de país e parâmetros http. Além disso, o Front Door também permite criar regras de limitação de velocidade para evitar o tráfego de bots maliciosos. Para mais informações sobre o Firewall da Aplicação Web, consulte o que é o Firewall de [aplicação web do Azure?](../web-application-firewall/overview.md)

A plataforma do Front Door encontra-se protegida pela proteção básica do [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md). Para aumentar a proteção, é possível ativar a proteção padrão do Azure DDoS Protection nas suas VNETs e recursos de salvaguarda contra ataques de camada de rede (TCP/UDP) através de ajuste automático e mitigação. O Front Door é um proxy inverso de camada 7; só permite que o tráfego Web passe para os back-ends e bloqueia os outros tipos de tráfego por predefinição.

## <a name="url-redirection"></a>Redirecionamento de URL
Com o forte impulso da indústria no apoio apenas a uma comunicação segura, espera-se que as aplicações web redirecionem automaticamente qualquer tráfego HTTP para HTTPS. Isto garante que toda a comunicação entre os utilizadores e a aplicação ocorre através de um caminho encriptado. 

Tradicionalmente, os proprietários de aplicações têm tratado desta exigência através da criação de um serviço dedicado, cujo único objetivo era redirecionar os pedidos que recebe em HTTP para HTTPS. A Porta Frontal Azure suporta a capacidade de redirecionar o tráfego de HTTP para HTTPS. Isto simplifica a configuração da aplicação, otimiza a utilização de recursos e suporta novos cenários de redirecionamento, incluindo o redirecionamento global e com base no caminho. A reorientação do URL da Porta Frontal azure não se limita apenas a http para a redirecionamento HTTPS, mas também para redirecionar para um nome de anfitrião diferente, redirecionando para um caminho diferente, ou mesmo redirecionando para uma nova cadeia de consulta no URL.

Para mais informações, consulte [redirecionar o tráfego](front-door-url-redirect.md) com a Porta da Frente Azure.

## <a name="url-rewrite"></a>Reescrever URL
O Front Door suporta [reescrita de URL](front-door-url-rewrite.md) ao permitir-lhe configurar um Caminho de Reencaminhamento Personalizado opcional que poderá utilizar quando criar o pedido para reencaminhar para o back-end. O Front Door também permite configurar o cabeçalho do Anfitrião que será enviado durante o reencaminhamento do pedido para o back-end.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Suporte de protocolo – tráfego IPv6 e HTTP/2
O Azure Front Door suporta conectividade IPv6 ponto a ponto, bem como o protocolo HTTP/2, de forma nativa. 

O protocolo e HTTP/2 ativa a comunicação duplex completa entre back-ends de aplicação e um cliente através de uma ligação TCP de execução longa. O HTTP/2 permite uma comunicação mais interativa entre o back-end e o cliente, que pode ser bidirecional sem necessidade de consulta, que é necessária em implementações com base em HTTP. O protocolo HTTP/2 tem uma sobrecarga reduzida, ao contrário do HTTP, e pode reutilizar a mesma ligação TCP para múltiplos pedidos/respostas, o que resulta numa utilização mais eficiente dos recursos. Saiba mais sobre o [suporte http/2 na Porta da Frente Azure.](front-door-http2.md)

## <a name="pricing"></a>Preços

Para obter informações sobre preços, veja [Preços do Front Door](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
