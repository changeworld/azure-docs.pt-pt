---
title: (DEPRECIADO) Monitorize um cluster de serviço de contentores Azure com Sysdig
description: Monitorizar um cluster do Azure Container Service com o Sysdig.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a22d48554573e2517b318f6172b759864bf46612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277738"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-sysdig"></a>(DEPRECIADO) Monitorize um cluster de serviço de contentores Azure com Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, vamos implementar agentes do Sysdig em todos os nós de agentes no seu cluster do Azure Container Service. Para esta configuração, é necessária uma conta do Sysdig. 

## <a name="prerequisites"></a>Pré-requisitos
[Implemente](container-service-deployment.md) e [ligue](../container-service-connect.md) um cluster configurado pelo Azure Container Service. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Vá [https://app.sysdigcloud.com](https://app.sysdigcloud.com) para criar uma conta na nuvem sysdig. 

## <a name="sysdig"></a>Sysdig
O Sysdig é um serviço de monitorização que lhe permite monitorizar os seus contentores no cluster. O Sysdig é conhecido por ajudá-lo em resolução de problemas, mas também tem as suas métricas de monitorização básicas para CPU, Redes, Memória e I/O. O Sysdig torna mais fácil ver que contentores se esforçam mais ou utilizam mais memória e CPU. Esta vista encontra-se na secção "Descrição Geral", que atualmente se encontra em fase beta. 

![IU do Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Configurar uma implementação do Sysdig com o Marathon
Estes passos mostram-lhe como configurar e implementar aplicações do Sysdig no seu cluster com o Marathon. 

Aceda ao seu DC/OS UI via [http://localhost:80/](http://localhost:80/) Once in the DC/OS UI navegar para o "Universo", que fica no canto inferior esquerdo e depois procure por "Sysdig".

![Sysdig no DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig1.png)

Agora, para completar a configuração, precisa de uma conta na nuvem do Sysdig ou de uma conta de avaliação gratuita. Após ter iniciado sessão no site na nuvem do Sysdig, clique no seu nome de utilizador e deverá ver a sua "Chave de Acesso" na página. 

![Chave de API do Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Em seguida, introduza a sua Chave de Acesso à configuração do Sysdig no DC/OS Universe. 

![Configuração do Sysdig no DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig3.png)

Agora, defina as ocorrências para 10000000. Assim, sempre que um novo nó for adicionado ao cluster, o Sysdig irá automaticamente implementar um agente nesse novo nó. Trata-se de uma solução provisória para garantir que o Sysdig é implementado para todos os novos agentes no cluster. 

![Configuração do Sysdig em ocorrências do DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig4.png)

Após instalar o pacote, regresse à IU do Sysdig e poderá explorar as diferentes métricas de utilização para contentores no seu cluster. 

Também pode instalar dasboards específicos do Mesos e do Marathon através do [assistente de dashboard novo](https://app.sysdigcloud.com/#/dashboards/new).
