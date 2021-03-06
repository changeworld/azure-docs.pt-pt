---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 261ae22348cd82b129727261c619727917e19c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73832060"
---
### <a name="record-names"></a>Nomes de registo

No DNS do Azure, os registos são especificados com nomes relativos. Um nome de domínio *totalmente qualificado* (FQDN) inclui o nome da zona, enquanto um nome *relativo* não. Por exemplo, o `www` nome relativo `contoso.com` do registo na `www.contoso.com`zona dá o nome de registo totalmente qualificado .

Um registo *apex* é um registo DNS na raiz (ou *apex*) de uma zona DNS. Por exemplo, na zona `contoso.com`DNS , um registo ápice também tem o nome `contoso.com` totalmente qualificado (este é por vezes chamado de domínio *nu).*  Por convenção, o\@nome relativo ' é usado para representar registos ápice.

### <a name="record-types"></a>Tipos de registo

Cada registo DNS tem um nome e um tipo. Os registos são organizados em vários tipos de acordo com os dados que contêm. O tipo mais comum é um registo “A”, que mapeia um nome para um endereço IPv4. Outro tipo comum é um registo “MX”, que mapeia um nome para um servidor de correio.

O Azure DNS suporta todos os tipos comuns de discos DNS: A, AAAA, CAA, CNAME, MX, NS, PTR, SOA, SRV e TXT. Tenha em atenção que [os registos SPF são representados utilizando registos TXT](../articles/dns/dns-zones-records.md#spf-records).

### <a name="record-sets"></a>Conjuntos de registos

Por vezes, precisa de criar mais do que um registo DNS com um determinado nome e tipo. Por exemplo, suponha que o site “www.contoso.com” está alojado em dois endereços IP diferentes. O site necessita de dois registos A diferentes, um para cada endereço IP. Aqui está um exemplo de um conjunto de registos:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

O DNS do Azure gere todos os registos DNS com *conjuntos de registos*. Um conjunto de registos (também conhecido como conjunto de registos de *recurso*) é uma coleção de registos DNS numa zona com o mesmo nome e do mesmo tipo. A maioria dos conjuntos de registos contêm um único registo. No entanto, os exemplos como o apresentado acima, no qual um conjunto um registos contém mais de um registo, não são invulgares.

Por exemplo, imagine que já criou um registo "www" A na zona "contoso.com", a apontar para o endereço IP "134.170.185.46" (o primeiro registo acima).  Para criar o segundo registo, deverá adicionar esse registo para o conjunto de registos existente, em vez de criar um conjunto de registos adicional.

Os tipos de registos SOA e CNAME são exceções. As normas DNS não permitirem vários registos com o mesmo nome para estes tipos, por conseguinte, estes conjuntos de registos só podem conter um único registo.