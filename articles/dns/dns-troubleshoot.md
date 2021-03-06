---
title: Guia de resolução de problemas - Azure DNS
description: Neste caminho de aprendizagem, começar a resolver problemas comuns com o Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: b5e1624bf852256f6e8fb0b616258f932c5a8998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939037"
---
# <a name="azure-dns-troubleshooting-guide"></a>Guia de resolução de problemas azure DNS

Este artigo fornece informações de resolução de problemas para questões comuns do DNS do Azure.

Se estes passos não resolverem o seu problema, também pode pesquisar ou publicar o seu problema no nosso fórum de [apoio à comunidade na MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). Ou pode abrir um pedido de apoio azure.


## <a name="i-cant-create-a-dns-zone"></a>Não posso criar uma zona DNS.

Para resolver problemas comuns, experimente um ou mais dos métodos seguintes:

1.  Reveja os registos de auditoria do Azure DNS para determinar o motivo da falha.
2.  Cada nome da zona DNS tem de ser exclusivo dentro do respetivo grupo de recursos. Ou seja, duas zonas de DNS com o mesmo nome não podem partilhar um grupo de recursos. Tente utilizar um nome de zona diferente ou um grupo de recursos diferente.
3.  Pode ver um erro "Atingiu ou excedeu o número máximo de zonas na subscrição {id da subscrição}." Utilize uma subscrição do Azure diferente, elimine algumas zonas ou contacte o suporte do Azure para aumentar o limite de sua subscrição.
4.  Pode ver um erro "A zona "{nome da zona}" não está disponível." Este erro significa que DNS do Azure não conseguiu alocar servidores de nomes para esta zona de DNS. Tente utilizar um nome de zona diferente. Ou, se for o proprietário do nome de domínio, pode contactar o suporte do Azure para atribuir servidores de nomes para si.


### <a name="recommended-articles"></a>Artigos recomendados

* [Zonas e registos DNS](dns-zones-records.md)
* [Criar uma zona DNS](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Não consigo criar um registo DNS

Para resolver problemas comuns, experimente um ou mais dos métodos seguintes:

1.  Reveja os registos de auditoria do Azure DNS para determinar o motivo da falha.
2.  O conjunto de registos já existe?  O DNS do Azure gere registos com *conjuntos* de registos, que são a recolha de registos do mesmo nome e do mesmo tipo. Se já existir um registo com o mesmo nome e tipo, para adicionar outro registo deve editar o conjunto de registos existente.
3.  Está a tentar criar um registo no apex de zona DNS (a “raiz” da zona)? Se for o caso, a convenção DNS consiste em utilizar o caráter “@” como o nome do registo. Note também que as normas DNS não permitem registos CNAME no ápice da zona.
4.  Tem um conflito com o CNAME?  Os padrões DNS não permitem um registo CNAME com o mesmo nome que um registo de qualquer outro tipo. Se tiver um CNAME existente, a criação de um registo com o mesmo nome de um tipo diferente falha.  Da mesma forma, a criação de um CNAME falha se o nome corresponder a um registo existente de um tipo diferente. Remova o conflito ao remover o outro registo ou ao escolher um nome de registo diferente.
5.  Atingiu o limite do número de conjuntos de registos permitido numa zona DNS? O número atual de conjuntos de registos e o número máximo de conjuntos de registos são apresentados no portal do Azure, em “Propriedades” para a zona. Se atingiu este limite, ou apague alguns conjuntos de discos ou contacte o Suporte Azure para elevar o limite de recorde para esta zona, tente novamente. 


### <a name="recommended-articles"></a>Artigos recomendados

* [Zonas e registos DNS](dns-zones-records.md)
* [Criar uma zona DNS](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Não consigo resolver o meu registo DNS

A resolução de nomes DNS é um processo com vários passos que pode falhar por muitos motivos. Os passos seguintes ajudam a investigar por que motivo a resolução de DNS está a falhar para um registo DNS numa zona alojada no DNS do Azure.

1.  Confirme que os registos DNS foram configurados corretamente no DNS do Azure. Reveja os registos DNS no portal do Azure, verificando que o nome da zona, o nome do registo e o tipo de registo estão corretos.
2.  Confirme que os registos DNS resolvem corretamente nos servidores de nome do DNS do Azure.
    - Se fizer consultas de DNS a partir do seu PC local, poderá ver os resultados em cache que não refletem o estado atual dos servidores de nome.  Além disso, as redes empresariais utilizam frequentemente servidores de proxy do DNS que impedem que as consultas de DNS sejam direcionadas para servidores de nomes específicos.  Para evitar estes problemas, utilize um serviço de resolução de nome baseado na Web, tal como [digwebinterface](https://digwebinterface.com).
    - Confirme que especifica os servidores de nome corretos para a zona DNS — estes são apresentados no Portal do Azure.
    - Verifique se o nome DNS está correto (tem de especificar o nome totalmente qualificado, incluindo o nome da zona) e se o tipo de registo está correto
3.  Confirme que o nome de domínio DNS foi corretamente [delegado para os servidores de nomes DNS do Azure](dns-domain-delegation.md). Existem [muitos sites de terceiros que oferecem a validação de delegação DNS](https://www.bing.com/search?q=dns+check+tool). Este é um teste de delegação de *zona*, pelo que deve introduzir apenas o nome da zona DNS e não o nome de registo completamente qualificado.
4.  Tendo concluído o procedimento acima, o registo DNS deverá agora resolver corretamente. Para verificar, pode utilizar novamente [digwebinterface](https://digwebinterface.com), desta vez com as predefinições de servidor de nome.


### <a name="recommended-articles"></a>Artigos recomendados

* [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Como posso especificar o “service” e o “protocol” para um registo SRV?

O DNS do Azure gere registos DNS como conjuntos de registos. A recolha de registos com o mesmo nome e o mesmo tipo. Para um conjunto de registos SRV, o “service” e o “protocol” têm de ser especificados como parte do nome do conjunto de registos. Os outros parâmetros SRV (“priority”, “weight”, “port” e “target”) são especificados em separado para cada registo no conjunto de registos.

Exemplos de nomes de registos SRV (“sip” do nome do serviço, “tcp” do protocolo):

- \_sip.\_tcp (cria um conjunto de registos no apex da zona)
- \_sip.\_tcp.sipservice (cria um conjunto de registos com o nome “sipservice”)

### <a name="recommended-articles"></a>Artigos recomendados

* [Zonas e registos DNS](dns-zones-records.md)
* [Criar registos e conjuntos de registos DNS com o portal do Azure](dns-getstarted-create-recordset-portal.md)
* [Tipo de registo SRV (Wikipédia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Passos seguintes

* Conheça [zonas e registos De DNS Azure](dns-zones-records.md)
* Para começar a utilizar o Azure DNS, aprenda a [criar uma zona DNS](dns-getstarted-create-dnszone-portal.md) e [crie registos DNS](dns-getstarted-create-recordset-portal.md).
* Para migrar uma zona DNS existente, aprenda a importar e exportar um ficheiro de [zona DNS](dns-import-export.md).

