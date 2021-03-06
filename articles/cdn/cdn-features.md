---
title: Comparar as funcionalidades do produto Rede de Entrega de Conteúdos (CDN) do Azure | Microsoft Docs
description: Saiba mais sobre as funcionalidades que cada produto de Rede de Entrega de Conteúdos (CDN) do Azure suporta.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 0e57ae691bf4b07b8161bc343929510d6be041a8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260518"
---
# <a name="compare-azure-cdn-product-features"></a>Comparar as funcionalidades do produto CDN do Azure

A Rede de Entrega de Conteúdos (CDN) do Azure inclui quatro produtos: **CDN do Azure Standard da Microsoft**, **CDN do Azure Standard da Akamai**, **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**. Para obter informações sobre como migrar um perfil do **CDN do Azure Standard da Verizon** para o **CDN do Azure Premium da Verizon**, veja [Migrar um perfil do CDN do Azure Standard da Verizon para Premium da Verizon](cdn-migrate.md). Note que, embora exista um caminho de upgrade da Standard Verizon para Premium Verizon, não existe nenhum mecanismo de conversão entre outros produtos neste momento.

A tabela seguinte compara as funcionalidades disponíveis com cada produto.

| **Funções e otimizações de desempenho** | **Standard da Microsoft** | **Standard da Akamai** | **Standard da Verizon** | **Premium da Verizon** |
| --- | --- | --- | --- | --- |
| [Aceleração dinâmica do site](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Oferecido via [Serviço de Porta Da Frente Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de site dinâmico - compressão de imagem adaptável](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de Site Dinâmico - Obtenção Prévia de Objeto](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Otimização geral da entrega web](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**, Selecione este tipo de otimização se o tamanho médio do ficheiro for inferior a 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Otimização da transmissão em fluxo de vídeo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | via Entrega Web Geral | **&#x2713;**  | via Entrega Web Geral |  via Entrega Web Geral |
| [Otimização de ficheiros grandes](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | via Entrega Web Geral | **&#x2713;**, Selecione este tipo de otimização se o tamanho médio do ficheiro for superior a 10 MB   | via Entrega Web Geral |  via Entrega Web Geral |
| Alterar tipo de otimização | |**&#x2713;** | | |
| Porto de Origem |Todas as portas TCP |[Portas de origem permitida](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Todas as portas TCP |Todas as portas TCP |
| [Balanceamento de carga de servidor global (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Remoção rápida](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, Purgue todos e a purga wildcard não é apoiada por Azure CDN da Akamai atualmente |**&#x2713;** |**&#x2713;** |
| [Pré-carregamento de recursos](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Definições de cache/cabeçalho (utilizando o [colocação de regras em cache](cdn-caching-rules.md))  |**&#x2713;** usando [o motor standard regras](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Personalizável, motor de entrega de conteúdo baseado em regras |**&#x2713;** usando [o motor standard regras](cdn-standard-rules-engine.md)  | | |**&#x2713;** usando [o motor de regras](cdn-rules-engine.md) |
| Definições de cache/cabeçalho  |**&#x2713;** usando [o motor standard regras](cdn-standard-rules-engine.md) | | |**&#x2713;** usando [motor de regras Premium](cdn-rules-engine.md) |
| REdirecionamento/reescrita de URL |**&#x2713;** usando [o motor standard regras](cdn-standard-rules-engine.md)  | | |**&#x2713;** usando [motor de regras Premium](cdn-rules-engine.md) |
| Regras do dispositivo móvel  |**&#x2713;** usando [o motor standard regras](cdn-standard-rules-engine.md) | | |**&#x2713;** usando [motor de regras Premium](cdn-rules-engine.md) |
| [Colocação em cache de cadeia de consulta](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Pilha dupla de IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Suporte HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Segurança** | **Standard da Microsoft** | **Standard da Akamai** | **Standard da Verizon** | **Premium da Verizon** | 
| Suporta HTTPS com ponto final CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS de domínio personalizado](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;, **requer CNAME direto para ativar |**&#x2713;** |**&#x2713;** |
| [Suporte de nomes de domínio personalizado](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtro geográfico](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticação de tokens](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Proteção DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Traga o seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Versões TLS suportadas | TLS 1.2, TLS 1.0/1.1 - [Configurável](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Análises e relatórios** | **Standard da Microsoft** | **Standard da Akamai** | **Standard da Verizon** | **Premium da Verizon** | 
| [Registos de diagnóstico do Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Relatórios de núcleos da Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Relatórios personalizados da Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Relatórios avançados do HTTP](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Estatísticas em tempo real](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Desempenho do nó edge](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Alertas em tempo real](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Facilidade de Utilização** | **Standard da Microsoft** | **Standard da Akamai** | **Standard da Verizon** | **Premium da Verizon** | 
| Fácil integração com os serviços do Azure, como, por exemplo, o [Armazenamento](cdn-create-a-storage-account-with-cdn.md), as [Aplicações Web](cdn-add-to-web-app.md) e os [Serviços de Multimédia](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gestão através da [API REST](/rest/api/cdn/), do [.NET](cdn-app-dev-net.md), do [Node.js](cdn-app-dev-node.md) ou do [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tipos mime de compressão](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Apenas por defeito |Configurável |Configurável  |Configurável  |
| Codificação de compressão  |gzip, brotli |gzip |gzip, esvaziar, bzip2, brotili  |gzip, esvaziar, bzip2, brotili  |






