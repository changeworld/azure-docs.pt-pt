---
title: Aceda a bolhas de armazenamento utilizando um domínio personalizado Azure CDN sobre HTTPS
description: Aprenda a adicionar um domínio personalizado Azure CDN e ative HTTPS nesse domínio para o seu ponto final de armazenamento blob personalizado.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 5b6fe2b2704f101a7775b7eb700375105b0a9eca
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259889"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Tutorial: aceder aos blobs de armazenamento com um domínio personalizado da CDN do Azure através de HTTPS

Depois de ter integrado a conta de armazenamento do Azure na rede de entrega de conteúdos (CDN) do Azure, pode adicionar um domínio personalizado e ativar o HTTPS nesse domínio para o ponto final de armazenamento de blobs personalizado. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de concluir os passos neste tutorial, primeiro tem de integrar a sua conta de armazenamento do Azure na CDN do Azure. Para obter mais informações, veja [Início Rápido: integrar uma conta de armazenamento do Azure na CDN do Azure](cdn-create-a-storage-account-with-cdn.md).

## <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado
Ao criar um ponto final da CDN no seu perfil, o nome do mesmo, que é um subdomínio de azureedge.net, é incluído no URL da entrega de conteúdos da CDN por predefinição. Também tem a opção de associar um domínio personalizado a um ponto final da CDN. Com esta opção, os seus conteúdos são entregues com um domínio personalizado no seu URL em vez de um nome de ponto final. Para adicionar um domínio personalizado ao ponto final, siga as instruções neste tutorial: [Adicionar um domínio personalizado ao ponto final da CDN do Azure](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>Configurar HTTPS
Ao utilizar o protocolo HTTPS no domínio personalizado, está a garantir que os dados são entregues de forma segura na Internet através da encriptação TLS/SSL. Quando o browser está ligado a um site por HTTPS, valida o certificado de segurança do site e verifica que este é emitido por uma autoridade de certificação legítima. Para configurar HTTPS no domínio personalizado, siga as instruções deste tutorial: [Configurar HTTPS num domínio personalizado da CDN do Azure](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Assinaturas de Acesso Partilhado
Se o ponto final de armazenamento de blobs estiver configurado para não permitir o acesso de leitura anónimo, deve fornecer um token [SAS (Assinatura de Acesso Partilhado)](cdn-sas-storage-support.md) em cada pedido que fizer ao domínio personalizado. Por predefinição, os pontos finais de armazenamento de blobs não permitem o acesso de leitura anónimo. Para obter mais informações sobre SAS, veja [Gerir o acesso de leitura anónimo a contentores e blobs](../storage/blobs/storage-manage-access-to-resources.md).

A CDN do Azure ignora quaisquer restrições adicionadas ao token SAS. Por exemplo, todos os tokens SAS têm um prazo de expiração, o que significa que o conteúdo ainda pode ser acedido com uma SAS expirada até que esse conteúdo seja removido dos servidores de ponto de presença (POP) da CDN. Pode controlar o tempo durante o qual os dados são colocados em cache na CDN do Azure, ao definir o cabeçalho de resposta da cache. Para obter mais informações, veja [Gerir a expiração de blobs de Armazenamento do Azure na CDN do Azure](cdn-manage-expiration-of-blob-content.md).

Se criar vários URLs de SAS para o mesmo ponto final do blob, considere ativar a colocação em cache de cadeias de consulta. Isto garante que cada URL é tratado como uma entidade exclusiva. Para obter mais informações, veja [Controlar o comportamento de colocação em cache da CDN do Azure com cadeias de consulta](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Redirecionamento de HTTP para HTTPS
Pode optar por redirecionar o tráfego HTTP para HTTPS criando uma regra de redirecionamento de URL com o [motor de regras Standard](cdn-standard-rules-engine.md) ou o motor de regras Verizon [Premium](cdn-verizon-premium-rules-engine.md). O motor Standard Rules está disponível apenas para O CDN Azure a partir de perfis da Microsoft, enquanto o motor de regras premium Verizon está disponível apenas a partir de Azure CDN Premium a partir dos perfis verizon.

![Regra de redirecionamento da Microsoft](./media/cdn-storage-custom-domain-https/cdn-standard-redirect-rule.png)

Na regra acima, deixar o nome de anfitrião, path, corda de consulta e Fragmento resultará na entrada dos valores utilizados no redirecionamento. 

![Regra de redirecionamento de Verizon](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

Na regra acima, o nome final de *Cdn refere-se* ao nome que configurapara o seu ponto final cdN, que pode selecionar a partir da lista de lançamentos. O valor de *origin-path* refere-se ao caminho na sua conta de armazenamento de origem onde reside o seu conteúdo estático. Se estiver a alojar todo o conteúdo estático num único contentor, substitua *origin-path* pelo nome desse contentor.

## <a name="pricing-and-billing"></a>Preços e faturação
Ao aceder a blobs através da CDN do Azure, paga os [Preços de armazenamento de blobs](https://azure.microsoft.com/pricing/details/storage/blobs/) para o tráfego entre os servidores POP e a origem (Armazenamento de blobs) e os [Preços da CDN do Azure](https://azure.microsoft.com/pricing/details/cdn/) para os dados acedidos a partir dos servidores POP.

Se, por exemplo, tiver uma conta de armazenamento nos Estados Unidos que está a ser acedida através da CDN do Azure e alguém na Europa tentar aceder a um dos blobs nessa conta de armazenamento através da CDN do Azure, a CDN do Azure verifica primeiro o POP mais próximo na Europa para esse blob. Se o encontrar, a CDN do Azure acede a essa cópia do blob e utiliza os preços da CDN, pois está a ser acedida na CDN do Azure. Se não o encontrar, a CDN do Azure copia o blob para o servidor POP, o que incorre em custos de saída e transação, conforme especificado nos Preços de armazenamento de blobs e, em seguida, acede ao ficheiro no servidor POP, o que resulta numa faturação da CDN do Azure.

## <a name="next-steps"></a>Passos seguintes
[Tutorial: Definir regras de colocação em cache da CDN do Azure](cdn-caching-rules-tutorial.md)




