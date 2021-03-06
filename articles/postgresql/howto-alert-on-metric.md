---
title: Configure alertas - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como configurar e aceder a alertas métricos para a Base de Dados Azure para PostgreSQL - Servidor Único do portal Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: fe099dcb49d176d27466c08749a5873904d1ae2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74766842"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>Utilize o portal Azure para configurar alertas sobre métricas para base de dados Azure para PostgreSQL - Servidor Único

Este artigo mostra-lhe como configurar a Base de Dados Azure para alertas PostgreSQL utilizando o portal Azure. Pode receber um alerta com base em métricas de monitorização dos seus serviços Azure.

O alerta dispara quando o valor de uma métrica especificada atravessa um limiar que atribui. O alerta dispara tanto quando a condição é satisfeita pela primeira vez, e depois quando essa condição já não está a ser satisfeita. 

Pode configurar um alerta para fazer as seguintes ações quando dispara:
* Envie notificações por e-mail ao administrador de serviço e aos coadministradores.
* Envie e-mail para e-mails adicionais que especifice.
* Chame um gancho de teia.

Pode configurar e obter informações sobre as regras de alerta usando:
* [Portal Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta sobre uma métrica do portal Azure
1. No [portal Azure,](https://portal.azure.com/)selecione a Base de Dados Azure para o servidor PostgreSQL que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Alertas** conforme mostrado:

   ![Selecione Regras de Alerta](./media/howto-alert-on-metric/2-alert-rules.png)

3. **Selecione Adicionar alerta métrico** (ícone+).

4. A página de **regras Create** abre como mostrado abaixo. Preencha as informações necessárias:

   ![Adicionar forma de alerta métrico](./media/howto-alert-on-metric/4-add-rule-form.png)

5. Dentro da secção **Condição,** **selecione Adicionar condição**.

6. Selecione uma métrica da lista de sinais a alertar. Neste exemplo, selecione "Storage percent".
   
   ![Selecionar métrica](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. Configure a lógica de alerta, incluindo a **Condição** (ex. "Maior do que"), **Threshold** (ex. 85 por cento), **Agregação**do Tempo , **Período** de tempo a regra métrica deve ser satisfeita antes do alerta disparar (ex. "Nos últimos 30 minutos"), e **Frequência.**
   
   Selecione **Feito** quando estiver completo.

   ![Selecionar métrica](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. Dentro da secção **Grupos de Ação,** selecione **Create New** para criar um novo grupo para receber notificações no alerta.

9. Preencha o formulário "Adicionar grupo de ação" com um nome, nome curto, subscrição e grupo de recursos.

10. Configure um tipo de ação de **e-mail/SMS/Push/Voice.**
    
    Escolha "Email Azure Resource Manager Role" para selecionar proprietários de subscrição, colaboradores e leitores para receber notificações.
   
    Opcionalmente, forneça um URI válido no campo **Webhook** se quiser que seja chamado quando o alerta disparar.

    Selecione **OK** quando estiver concluído.

    ![Grupo de ações](./media/howto-alert-on-metric/10-action-group-type.png)

11. Especifique um nome de regra de alerta, descrição e gravidade.

    ![Grupo de ações](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. Selecione **Criar a regra** de alerta para criar o alerta.

    Dentro de poucos minutos, o alerta está ativo e dispara como descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerir os alertas
Uma vez criado um alerta, pode selecioná-lo e fazer as seguintes ações:

* Veja um gráfico que mostre o limiar métrico e os valores reais do dia anterior relevantes para este alerta.
* **Editar** ou **Eliminar** a regra de alerta.
* **Desative** ou **Ative** o alerta, caso pretenda parar ou retomar temporariamente a receção de notificações.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [configurar webhooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
* Obtenha uma [visão geral da recolha de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para garantir que o seu serviço está disponível e reativo.
