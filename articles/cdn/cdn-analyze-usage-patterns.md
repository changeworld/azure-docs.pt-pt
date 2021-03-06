---
title: Relatórios Do Núcleo de Verizon Microsoft Docs
description: 'Pode visualizar padrões de utilização para o seu CDN utilizando os seguintes relatórios: Largura de Banda, Dados Transferidos, Acessos, Estados de Cache, Relação de Impacto de Cache, IPV4/IPV6 Dados transferidos.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d48ddafdc1ec30ae1533b3a3101582f33e7f4b5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594162"
---
# <a name="core-reports-from-verizon"></a>Relatórios Do Núcleo de Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Ao utilizar os Relatórios Verizon Core através do portal Manage para perfis Verizon, pode ver padrões de utilização para o seu CDN com os seguintes relatórios:

* Largura de banda
* Dados Transferidos
* Acertos
* Estados de Cache
* Rácio de impacto de cache
* Dados IPV4/IPV6 transferidos

## <a name="accessing-verizon-core-reports"></a>Aceder a Relatórios Do Núcleo de Verizon
1. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Perfil CDN Gerir botão](./media/cdn-reports/cdn-manage-btn.png)
   
    Abre o portal de gestão da CDN.
2. Paire sobre o separador **Analytics** e, em seguida, paire sobre o voo core **reports.** Clique num relatório no menu.
   
    ![Portal de gestão cdN - Menu Core Reports](./media/cdn-reports/cdn-core-reports.png)

3. Para cada relatório, selecione uma gama de datas da lista de **Data Range.** Pode selecionar uma gama de datas pré-definida, como **hoje** ou **esta semana,** ou pode selecionar **O Costume** e introduzir manualmente uma gama de datas clicando nos ícones do calendário. 

4. Depois de ter selecionado um intervalo de datas, clique em **Ir** para gerar o relatório. 

4. Se quiser exportar os dados em formato Excel, clique no ícone Excel acima do botão **Go.**

## <a name="bandwidth"></a>Largura de banda
O relatório de largura de banda consiste num gráfico e numa tabela de dados que indica o uso da largura de banda CDN para HTTP e HTTPS durante um determinado período de tempo, em Mbps. Pode visualizar o uso da largura de banda em todos os POPs ou para um POP em particular. Este relatório permite-lhe visualizar os picos de tráfego e distribuição de POPs.

Na lista de **Nódeos Edge,** selecione **All Edge Nodes** para ver o tráfego de todos os nós ou selecione uma região específica.

O relatório é atualizado a cada cinco minutos.

![Relatório de largura de banda](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Dados transferidos
Este relatório consiste num gráfico e numa tabela de dados que indica o uso do tráfego de CDN para HTTP e HTTPS durante um determinado período de tempo, em GB. Pode visualizar o uso do tráfego em todos os POPs ou para um POP em particular. Este relatório permite-lhe visualizar os picos de tráfego e distribuição através dos POPs.

Na lista de **Nódeos Edge,** selecione **All Edge Nodes** para ver o tráfego de todos os nós ou selecione uma região específica.

O relatório é atualizado a cada cinco minutos.

![Relatório transferido de dados](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Acessos (códigos de estado)
Este relatório descreve a distribuição dos códigos de estado do pedido para o seu conteúdo. Cada pedido de conteúdo gera um código de estado HTTP. O código de estado descreve como os POPs de borda lidaram com o pedido. Por exemplo, um código de estado 2xx indica que o pedido foi servido com sucesso a um cliente, enquanto um código de estado 4xx indica que ocorreu um erro. Para obter mais informações sobre os códigos de estado http, consulte [a Lista de Códigos de Estado HTTP](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Relatório de acessos](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Estados de cache
Este relatório descreve a distribuição de cache hits e cache faltas para pedidos de clientes. Como o desempenho mais rápido resulta de impactos de cache, pode otimizar as velocidades de entrega de dados minimizando falhas de cache e acessos de cache expirados. 

Para reduzir as falhas de cache, configure o servidor de origem para minimizar a utilização do seguinte: 
 * `no-cache`cabeçalhos de resposta
 * Caching de corda de consulta, a menos que estritamente necessário  
 * Códigos de resposta não cacheable

Para reduzir os acessos de cache `max-age` expirados, detete tede um ativo para um longo período para minimizar o número de pedidos ao servidor de origem.

![Relatório de estados de cache](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Os principais estados de cache incluem:
* TCP_HIT: Servido a partir de um servidor de borda. O objeto estava na cache e não excedeu a sua idade máxima.
* TCP_MISS: Servido do servidor de origem. O objeto não estava na cache e a resposta voltou à origem.
* TCP_EXPIRED _MISS: Servido a partir do servidor de origem após revalidação com origem. O objeto estava na cache, mas tinha excedido a sua idade máxima. Uma revalidação com origem resultou na substituído pelo objeto cache por uma nova resposta de origem.
* TCP_EXPIRED _HIT: Servido de Edge após revalidação com origem. O objeto estava em cache, mas tinha excedido a sua idade máxima. Uma revalidação com o servidor de origem resultou na não modificação do objeto de cache.

### <a name="full-list-of-cache-statuses"></a>Lista completa de estados de cache
* TCP_HIT - Este estado é reportado quando um pedido é servido diretamente do POP ao cliente. Um ativo é imediatamente servido a partir de um POP quando é colocado em cache no POP mais próximo do cliente e tem um tempo de vida válido (TTL). A TTL é determinada pelos seguintes cabeçalhos de resposta:
  
  * Cache-Control: s-maxage
  * Cache-Control: idade máxima
  * Validade
* TCP_MISS: Este estado indica que não foi encontrada uma versão em cache do ativo solicitado no POP mais próximo do cliente. O ativo é solicitado a partir de um servidor de origem ou de um servidor de escudo de origem. Se o servidor de origem ou o servidor do escudo de origem devolverum ativo, este é servido ao cliente e cached tanto no cliente como no servidor de borda. Caso contrário, é devolvido um código de estado não-200 (por exemplo, 403 Proibido ou 404 Não Encontrados).
* TCP_EXPIRED_HIT: Este estado é reportado quando um pedido que visa um ativo com um TTL expirado foi servido diretamente do POP para o cliente. Por exemplo, quando a idade máxima do ativo expirou. 
  
   Um pedido expirado normalmente resulta num pedido de revalidação ao servidor de origem. Para que ocorra um estado TCP_EXPIRED_HIT, o servidor de origem deve indicar que uma versão mais recente do ativo não existe. Esta situação normalmente resulta numa atualização dos cabeçalhos de Cache-Control e Expirado do ativo.
* TCP_EXPIRED_MISS: Este estado é relatado quando uma versão mais recente de um ativo em cache expirado é servida do POP ao cliente. Este estado ocorre quando o TTL para um ativo em cache é expirado (por exemplo, expirada a idade máxima) e o servidor de origem devolve uma versão mais recente desse ativo. Esta nova versão do ativo é servida ao cliente em vez da versão em cache. Além disso, está em cache no servidor edge e no cliente.
* CONFIG_NOCACHE: Este estado indica que uma configuração específica do cliente o POP de borda impediu que o ativo fosse protegido.
* NENHUM - Este estado indica que não foi efetuada uma verificação de frescura do conteúdo da cache.
* TCP_CLIENT_REFRESH_MISS: Este estado é relatado quando um cliente HTTP, como um navegador, obriga um POP de borda a recuperar uma nova versão de um ativo velho do servidor de origem. Por padrão, os servidores impedem um cliente HTTP de forçar os servidores de borda a recuperar uma nova versão do ativo a partir do servidor de origem.
* TCP_PARTIAL_HIT: Este estado é relatado quando um pedido de alcance de byte resulta num golpe para um ativo parcialmente cached. A gama de byte supérpede é imediatamente servida do POP ao cliente.
* ESTE estado é relatado quando um ativo `Cache-Control` `Expires` e os cabeçalhos indicam que não deve ser colocado em cache num POP ou pelo cliente HTTP. Este tipo de pedidos são servidos a partir do servidor de origem.

## <a name="cache-hit-ratio"></a>Rácio de impacto de cache
Este relatório indica a percentagem de pedidos em cache que foram servidos diretamente a partir de cache.

O relatório fornece os seguintes detalhes:

* O conteúdo solicitado foi colocado em cache no POP mais próximo do solicitador.
* O pedido foi servido diretamente a partir da borda da nossa rede.
* O pedido não exigia revalidação com o servidor de origem.

O relatório não inclui:

* Pedidos que são negados devido a opções de filtragem país/região.
* Pedidos de ativos cujos cabeçalhos indicam que não devem ser cache. Por `Cache-Control: private`exemplo, `Cache-Control: no-cache`ou `Pragma: no-cache` cabeçalhos impedem que um ativo seja cache.
* Byte range solicita conteúdo parcialmente emcache.

A fórmula é: (TCP_ HIT/(TCP_ HIT+TCP_MISS)*100

![Relatório de rácio de impacto de cache](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPV4/IPV6 Dados transferidos
Este relatório mostra a distribuição do uso do tráfego no IPV4 vs IPV6.

![IPV4/IPV6 Dados transferidos](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Considerações
Os relatórios só podem ser gerados nos últimos 18 meses.

