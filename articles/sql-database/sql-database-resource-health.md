---
title: Use a Azure Resource Health para monitorizar a saúde da base de dados
description: Use a Azure Resource Health para monitorizar a saúde da Base de Dados SQL, ajuda-o a diagnosticar e obter suporte quando um problema azure afeta os seus recursos SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 9e19e904b47d69444b491dd88ffe49ff812aafc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208877"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Use Resource Health to troubleshoot connectivity for Azure SQL Database (Utilizar o Resource Health para resolver problemas de conectividade da Base de Dados SQL do Azure)

## <a name="overview"></a>Descrição geral

[A Saúde de Recursos](../service-health/resource-health-overview.md#get-started) para a Base de Dados SQL ajuda-o a diagnosticar e a obter suporte quando um problema do Azure afeta os seus recursos SQL. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.

![Descrição geral](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Verificação de saúde

A Resource Health determina a saúde do seu recurso SQL examinando o sucesso e a falha dos logins no recurso. Atualmente, a Saúde de Recursos para o seu recurso SQL DB apenas examina falhas de login devido a erro do sistema e não erro do utilizador. O estado da Saúde dos Recursos é atualizado a cada 1-2 minutos.

## <a name="health-states"></a>Estados da Saúde

### <a name="available"></a>Disponível

Um estado de **Disponível** significa que a Saúde dos Recursos não detetou falhas de login devido a erros do sistema no seu recurso SQL.

![Disponível](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degradado

O estado **Degradado** significa que o Resource Health detetou maioritariamente inícios de sessão com êxito, mas também algumas falhas. Estes são provavelmente erros transitórios de login. Para reduzir o impacto dos problemas de ligação causados por erros transitórios de login, por favor implemente a lógica de [retry](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) no seu código.

![Degradado](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Indisponível

Um estado de **Indisponíveis** significa que a Saúde dos Recursos detetou falhas de login consistentes no seu recurso SQL. Se o seu recurso permanecer neste estado por um longo período de tempo, contacte o suporte.

![Indisponível](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Desconhecido

O estado de saúde de **Unknown** indica que a Saúde de Recursos não recebe informações sobre este recurso há mais de 10 minutos. Embora este estado não seja uma indicação definitiva do estado do recurso, é um importante ponto de dados no processo de resolução de problemas. Se o recurso estiver a funcionar como esperado, o estado do recurso mudará para Disponível após alguns minutos. Se estiver com problemas com o recurso, o estado de saúde desconhecido pode sugerir que um evento na plataforma está a afetar o recurso.

![Desconhecido](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informação histórica

Pode aceder até 14 dias de história da saúde na secção de História da Saúde da Saúde. A secção também conterá a razão de inatividade (quando disponível) para os tempos de inatividade reportados pela Resource Health. Atualmente, o Azure mostra o período de indisponibilidade do recurso da base de dados SQL com uma granularidade de dois minutos. O período de indisponibilidade real é provavelmente inferior a um minuto; a média são oito segundos.

### <a name="downtime-reasons"></a>Razões de inatividade

Quando a sua Base de Dados SQL experimenta tempo de inatividade, a análise é realizada para determinar uma razão. Quando disponível, a razão de inatividade é reportada na secção de História da Saúde da Saúde dos Recursos. Geralmente, os motivos para os tempos de inatividade são publicados 30 minutos após os eventos.

#### <a name="planned-maintenance"></a>Manutenção planeada

A infraestrutura Azure realiza periodicamente a manutenção planeada – a tualização de componentes de hardware ou software no datacenter. Enquanto a base de dados é submetida a manutenção, a SQL pode encerrar algumas ligações existentes e recusar novas. As falhas de login experimentadas durante a manutenção planeada são tipicamente transitórias e a lógica de [retry](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) ajuda a reduzir o impacto. Se continuar a sentir erros de login, contacte o suporte.

#### <a name="reconfiguration"></a>Reconfiguração

As reconfigurações são consideradas condições transitórias, e são esperadas de vez em quando. Estes eventos podem ser desencadeados por falhas de equilíbrio de carga ou de software/hardware. Qualquer aplicação de produção de clientes que se conectem a uma base de dados em nuvem deve implementar uma lógica robusta de [retry](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)de ligação, uma vez que ajudaria a mitigar estas situações e geralmente tornaria os erros transparentes para o utilizador final.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a lógica de [retry para erros transitórios](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Troubleshoot, diagnose, and prevent SQL connection errors](./sql-database-connectivity-issues.md) (Resolver problemas, diagnosticar e evitar erros de ligação do SQL)
- Saiba mais sobre [configurar alertas](../service-health/resource-health-alert-arm-template-guide.md) de saúde de recursos
- Obtenha uma visão geral da [Saúde](../service-health/resource-health-overview.md) dos Recursos
- [FAQ do Resource Health](../service-health/resource-health-faq.md)
