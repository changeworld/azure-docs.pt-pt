---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060576"
---
## <a name="scenario"></a>Cenário

Para melhor ilustrar como configurar um endereço IP estático para um VM, este documento utiliza este cenário:

![Cenário de rede virtual: Subnets frontais e traseiras, com um endereço IP estático para a sub-rede frontal](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

Neste cenário, cria-se um VM denominado *DNS01* na subnet *FrontEnd* e, em seguida, decidiu-o para utilizar um endereço IP estático de *192.168.1.101*.
