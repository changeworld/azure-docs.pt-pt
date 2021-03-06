---
title: Suporte protocolar para cabeçalhos HTTP na Porta da Frente Azure [ Microsoft Docs
description: Este artigo descreve protocolos de cabeçalho HTTP que a Porta Da Frente suporta.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471681"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Suporte protocolar para cabeçalhos HTTP na Porta da Frente Azure
Este artigo descreve o protocolo que a Porta Da Frente suporta com partes do caminho de chamada (ver imagem). As seguintes secções fornecem mais informações sobre os cabeçalhos HTTP suportados pela Porta da Frente.

![Protocolo de cabeçalhos da porta da frente azure HTTP][1]

>[!IMPORTANT]
>A Porta da Frente não certifica nenhum cabeçalho http que não esteja documentado aqui.

## <a name="client-to-front-door"></a>Cliente para porta da frente
A Porta da Frente aceita a maioria dos cabeçalhos do pedido de entrada sem modificá-los. Alguns cabeçalhos reservados são removidos do pedido de entrada se enviados, incluindo cabeçalhos com o prefixo X-FD-*.

## <a name="front-door-to-backend"></a>Porta da frente para apoiar

A Porta da Frente inclui cabeçalhos de um pedido de entrada a menos que seja removido devido a restrições. A Porta da Frente também adiciona os seguintes cabeçalhos:

| Cabeçalho  | Exemplo e descrição |
| ------------- | ------------- |
| Via |  Via: 1.1 Azure </br> A Porta Frontal adiciona a versão HTTP do cliente seguida pelo *Azure* como o valor para o cabeçalho Via. Este cabeçalho indica a versão HTTP do cliente e que a Porta Frontal foi um destinatário intermédio para o pedido entre o cliente e o backend.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Representa o endereço IP do cliente associado ao pedido que está a ser processado. Por exemplo, um pedido proveniente de um proxy pode adicionar o cabeçalho X-Forward-For para indicar o endereço IP do chamador original. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Representa o endereço IP da tomada associado à ligação TCP de que o pedido atual teve origem. O endereço IP do cliente de um pedido pode não ser igual ao seu endereço IP da tomada, uma vez que pode ser arbitrariamente substituído por um utilizador.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYWYS00YTU0LTk0YzMnwZmNzA3NjQ3Nzgz </br> Uma cadeia de referência única que identifica um pedido servido pela Porta da Frente. É usado para pesquisar registos de acesso e crítico para resolução de problemas.|
| Cadeia de pedidos X-Azure |  X-Azure-RequestChain: lúpulo=1 </br> Um cabeçalho que a Porta Da Frente usa para detetar loops de pedido, e os utilizadores não devem assumir uma dependência dele. |
| X-Forward-For | X-Forward-For: 127.0.0.1 </br> O campo de cabeçalho S-Forwarded-For (XFF) HTTP identifica frequentemente o endereço IP originário de um cliente que se conecta a um servidor web através de um proxy ou um balancer de carga HTTP. Se houver um cabeçalho XFF existente, então a Porta frontal anexa o IP da tomada do cliente ou adiciona o cabeçalho XFF com o IP da tomada do cliente. |
| X-Forward-Host | X-Forwardhost: contoso.azurefd.net </br> O campo de cabeçalho si-forward-host http é um método comum usado para identificar o anfitrião original solicitado pelo cliente no cabeçalho de pedido do Anfitrião HTTP. Isto porque o nome do anfitrião da Porta Frontal pode diferir para o servidor de backend que manuseia o pedido. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> O campo de cabeçalho X-Forwarded-Proto HTTP é frequentemente utilizado para identificar o protocolo de origem de um pedido HTTP porque a Porta Frontal, com base na configuração, pode comunicar com o backend utilizando HTTPS. Isto é verdade mesmo que o pedido ao representante inverso seja HTTP. |
| Sonda X-FD-Health | O campo de cabeçalho Saúde Http X-FD-HealthProbe HTTP é utilizado para identificar a sonda de saúde da Porta da Frente. Se este cabeçalho definido para 1, o pedido é sonda de saúde. Pode utilizar quando quiser aceder estritamente a partir de uma porta particular com um campo de cabeçalho x-forward-host. |

## <a name="front-door-to-client"></a>Porta da frente para o cliente

Quaisquer cabeçalhos enviados para a Porta da Frente a partir do backend também são passados para o cliente. Seguem-se os cabeçalhos enviados da Porta da Frente para os clientes.

| Cabeçalho  | Exemplo |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYWYS00YTU0LTk0YzMnwZmNzA3NjQ3Nzgz* </br> Esta é uma cadeia de referência única que identifica um pedido servido pela Porta da Frente. Isto é fundamental para a resolução de problemas, uma vez que é usado para pesquisar registos de acesso.|

## <a name="next-steps"></a>Passos seguintes

- [Criar uma Porta de Entrada](quickstart-create-front-door.md)
- [Como funciona a Porta da Frente](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
