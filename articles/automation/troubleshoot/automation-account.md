---
title: Resolução de problemas da conta de automação
description: Aprenda a resolver problemas e a resolver problemas com uma conta Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c66b1728144b8517f6ac444059b3a8def956c6e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301008"
---
# <a name="automation-account-troubleshooting"></a>Resolução de problemas da conta de automação

Este artigo discute soluções para questões que pode encontrar quando utiliza uma conta de Automação. As seguintes secções destacam mensagens de erro específicas e possíveis resoluções para cada um. Para obter informações gerais sobre as contas da Automação, consulte [Criar uma conta Azure](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: Incapaz de registar Fornecedor de Recursos Automation para subscrições

### <a name="issue"></a>Problema

Quando trabalha com soluções de gestão na sua conta Automation, encontra o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

O Fornecedor de Recursos Automation não está registado na subscrição.

### <a name="resolution"></a>Resolução

Para registar o Fornecedor de Recursos Automation, siga estes passos no portal Azure:

1. Do seu navegador, vá ao [portal Azure.](https://portal.azure.com)

2. Navegue para **Subscrições** e selecione a sua subscrição na página De Assinaturas.   

3. Em **Definições,** selecione **Fornecedores de Recursos**.

4. A partir da lista de fornecedores de recursos, verifique se o fornecedor de recursos **da Microsoft.Automation** está registado.

5. Se não estiver listado, registe o fornecedor **Microsoft.Automation** seguindo os passos no [Resolve erros para o registo](/azure/azure-resource-manager/resource-manager-register-provider-errors)do fornecedor de recursos .

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport)a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.