---
title: Ações no motor standard regras para Azure CDN [ Microsoft Docs
description: Documentação de referência para ações no motor de regras Standard para a Rede de Entrega de Conteúdos Azure (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 29138b4fc6716ae5361cc4d7f97ceba41b90c2da
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259957"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Ações no motor standard regras para Azure CDN

No [motor standard regras](cdn-standard-rules-engine.md) para a Rede de Entrega de Conteúdos Azure (Azure CDN), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das ações que pode utilizar no motor standard regras para O CDN Azure.

A segunda parte de uma regra é uma ação. Uma ação define o comportamento que é aplicado ao tipo de pedido que uma condição de correspondência ou conjunto de condições de correspondência identifica.

## <a name="actions"></a>Ações

As seguintes ações estão disponíveis para utilização no motor standard regras para o Azure CDN. 

### <a name="cache-expiration"></a>Expiração da cache

Utilize esta ação para substituir o valor de tempo de vida (TTL) do ponto final para pedidos que as regras correspondem às condições especificadas.

#### <a name="required-fields"></a>Campos necessários

Comportamento de cache |  Descrição              
---------------|----------------
Cache de bypass | Quando esta opção é selecionada e a regra corresponde, o conteúdo não é colocado em cache.
Substituição | Quando esta opção é selecionada e a regra corresponde, o valor TTL devolvido da sua origem é substituído com o valor especificado na ação.
Definir se faltar | Quando esta opção é selecionada e a regra corresponde, se nenhum valor TTL foi devolvido da sua origem, a regra define o TTL ao valor especificado na ação.

#### <a name="additional-fields"></a>Campos adicionais

Dias | Horas | Minutos | Segundos
-----|-------|---------|--------
int | int | int | int 

### <a name="cache-key-query-string"></a>Cadeia de consulta de chave cache

Utilize esta ação para modificar a tecla cache com base em cordas de consulta.

#### <a name="required-fields"></a>Campos necessários

Comportamento | Descrição
---------|------------
Incluir | Quando esta opção é selecionada e a regra corresponde, as cordas de consulta especificadas nos parâmetros são incluídas quando a chave cache é gerada. 
Colocar em cache todos os URL exclusivos | Quando esta opção é selecionada e a regra corresponde, cada URL único tem a sua própria chave cache. 
Excluir | Quando esta opção é selecionada e a regra corresponde, as cordas de consulta especificadas nos parâmetros são excluídas quando a chave cache é gerada.
Ignorar cadeias de consulta | Quando esta opção é selecionada e a regra corresponde, as cordas de consulta não são consideradas quando a chave cache é gerada. 

### <a name="modify-request-header"></a>Modificar cabeçalho de pedido

Utilize esta ação para modificar os cabeçalhos presentes nos pedidos enviados para a sua origem.

#### <a name="required-fields"></a>Campos necessários

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome header** é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor está anexado ao valor existente. | String
Overwrite | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome header** é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado substitui o valor existente. | String
Eliminar | Quando esta opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é apagado do pedido. | String

### <a name="modify-response-header"></a>Modificar cabeçalho de resposta

Utilize esta ação para modificar os cabeçalhos presentes nas respostas devolvidas aos seus clientes.

#### <a name="required-fields"></a>Campos necessários

Ação | Http Nome cabeçalho | Valor
-------|------------------|------
Acrescentar | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome header** é adicionado à resposta utilizando o **valor**especificado . Se o cabeçalho já estiver presente, o **Valor** está anexado ao valor existente. | String
Overwrite | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome header** é adicionado à resposta utilizando o **valor**especificado . Se o cabeçalho já estiver presente, o **Valor** substitui o valor existente. | String
Eliminar | Quando esta opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é eliminado da resposta. | String

### <a name="url-redirect"></a>Redirecionamento de URL

Use esta ação para redirecionar os clientes para um novo URL. 

#### <a name="required-fields"></a>Campos necessários

Campo | Descrição 
------|------------
Tipo | Selecione o tipo de resposta para devolver ao solicitador: Found (302), Moved (301), Redirectição Temporária (307) e Redirectivo Permanente (308).
Protocolo | Pedido de Jogo, HTTP, HTTPS.
Nome de anfitrião | Selecione o nome de anfitrião para o quais pretende que o pedido seja redirecionado. Deixe em branco para preservar o hospedeiro que está a chegar.
Caminho | Defina o caminho a utilizar no redirecionamento. Deixe em branco preservar o caminho de entrada.  
Cadeias de consulta | Defina a corda de consulta utilizada no redirecionamento. Deixe em branco para preservar a corda de consulta de entrada. 
Fragment | Defina o fragmento a utilizar no redirecionamento. Deixe em branco preservar o fragmento de entrada. 

Recomendamos vivamente que use um URL absoluto. A utilização de um URL relativo pode redirecionar os URLs Azure CDN para um caminho inválido. 

### <a name="url-rewrite"></a>Reescrever URL

Use esta ação para reescrever o caminho de um pedido que está a caminho da sua origem.

#### <a name="required-fields"></a>Campos necessários

Campo | Descrição 
------|------------
Padrão de origem | Defina o padrão de origem no caminho do URL para substituir. Atualmente, o padrão de origem usa uma correspondência baseada em prefixo. Para combinar com todos os caminhos**/** de URL, use um corte para a frente ( ) como o valor do padrão de origem.
Destino | Defina o caminho de destino a utilizar na reescrita. O caminho de destino substitui o padrão de origem.
Preservar caminho inigualável | Se definido para **Sim,** o caminho restante após o padrão de origem é anexado ao novo caminho de destino. 

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do CDN azure](cdn-overview.md)
- [Referência do Motor de regras standard](cdn-standard-rules-engine-reference.md)
- [Condições de jogo no motor de regras Standard](cdn-standard-rules-engine-match-conditions.md)
- [Impor HTTPS com o Motor de regras standard](cdn-standard-rules-engine.md)
