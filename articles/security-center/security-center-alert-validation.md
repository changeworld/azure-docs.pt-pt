---
title: Validação de alerta (arquivo de teste EICAR) no Centro de Segurança Do Azure Microsoft Docs
description: Este documento ajuda-o a validar os alertas de segurança no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140002"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Validação de alertas (ficheiro de teste EICAR) no Centro de Segurança Azure
Este documento ajuda-o a aprender como verificar se o sistema está corretamente configurado para os alertas do Centro de Segurança do Azure.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
Os alertas são as notificações que o Centro de Segurança gera quando deteta ameaças nos seus recursos. Prioriza e enumera os alertas juntamente com as informações necessárias para investigar rapidamente o problema. O Centro de Segurança também fornece recomendações sobre como pode remediar um ataque.
Para mais informações, consulte [alertas](security-center-alerts-overview.md) de Segurança no Centro de Segurança e [Gestão e resposta a alertas](security-center-managing-and-responding-alerts.md) de segurança

## <a name="alert-validation"></a>Validação de alertas

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Valide alertas em VMs do Windows<a name="validate-windows"></a>

Depois de instalado o agente do Security Center no seu computador, siga estes passos do computador onde pretende ser o recurso atacado do alerta:

1. Copie um executável (por exemplo **calc.exe)** para o ambiente de trabalho do computador, ou outro diretório da sua conveniência, e mude o nome como **ASC_AlertTest_662jfi039N.exe**.
1. Abra o pedido de comando e execute este ficheiro com um argumento (apenas um nome de argumento falso), tais como:```ASC_AlertTest_662jfi039N.exe -foo```
1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Deve ser apresentado um alerta semelhante ao [exemplo](#alert-validate) abaixo:

> [!NOTE]
> Ao rever este alerta de teste para windows, certifique-se de que o campo **Desativação** de Argumentos é **verdadeiro**. Se for **falso,** então tem de ativar a auditoria de argumentos de linha de comando. Para o ativar, utilize o seguinte comando:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Validar alertas em VMs Linux<a name="validate-linux"></a>

Depois de instalado o agente do Security Center no seu computador, siga estes passos do computador onde pretende ser o recurso atacado do alerta:
1. Copie um executável para um local conveniente e mude o nome para **./asc_alerttest_662jfi039n**, por exemplo:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Abra o pedido de comando e execute este ficheiro:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Deve ser apresentado um alerta semelhante ao [exemplo](#alert-validate) abaixo:

### <a name="alert-example"></a>Exemplo de alerta<a name="alert-validate"></a>

![Exemplo de validação de alerta](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Validar alertas em Kubernetes<a name="validate-kubernetes"></a>

Se estiver a utilizar a função de pré-visualização do Centro de Segurança de integrar o Serviço Azure Kubernetes, execute o seguinte comando kubectl para testar que os seus alertas estão a funcionar:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Para mais informações sobre a integração do Serviço Azure Kubernetes e do Centro de Segurança Azure, consulte [este artigo](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Passos seguintes
Este artigo apresentou-lhe o processo de validação de alertas. Agora que está familiarizado com a validação, experimente os seguintes artigos:

* [Validação da deteção de ameaças do cofre de chaves azure no Centro de Segurança Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Gestão e resposta a alertas](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) de segurança no Azure Security Center - Saiba como gerir os alertas e responda a incidentes de segurança no Centro de Segurança.
* [Monitorização da saúde de segurança no Azure Security Center](security-center-monitoring.md) - Saiba como monitorizar a saúde dos seus recursos Azure.
* [Compreender alertas de segurança no Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) - Conheça os diferentes tipos de alertas de segurança.
