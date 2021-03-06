---
title: Gerir políticas de encerramento automático em Azure DevTest Labs [ Microsoft Docs
description: Aprenda a definir a política de encerramento automático para um laboratório para que as máquinas virtuais sejam desligadas automaticamente quando não estão a ser utilizadas.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 7cdc9f9a4503c786065b6d514f61fe17eae4ce5e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270915"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Configure o encerramento automático para laboratório e computação de máquinas virtuais em Azure DevTest Labs

Este artigo explica como configurar as definições de encerramento automático para VMs de laboratório em DevTest Labs e computações vMs. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Configure o encerramento automático para VMs de laboratório (DevTest Labs)
A Azure DevTest Labs permite-lhe controlar os custos e minimizar os resíduos nos seus laboratórios, gerindo políticas (configurações) para cada laboratório. Este artigo mostra-lhe como configurar a política de encerramento automático para uma conta de laboratório e configurar as definições de encerramento automático para um laboratório na conta do laboratório. Para ver como definir todas as políticas de laboratório, consulte as políticas de [laboratório define em Laboratórios Azure DevTest](devtest-lab-set-lab-policy.md).  

### <a name="set-auto-shutdown-policy-for-a-lab"></a>Definir a política de encerramento automático para um laboratório
Como dono de laboratório, pode configurar um horário de encerramento para todos os VMs do seu laboratório. Ao fazê-lo, pode poupar custos de máquinas de funcionamento que não estão a ser usadas (ociosa). Você pode impor uma política de encerramento em todos os seus VMs de laboratório centralmente, mas também poupar os seus utilizadores de laboratório o esforço de configurar um horário para as suas máquinas individuais. Esta funcionalidade permite definir a política na sua programação de laboratório a partir de não oferecer controlo total aos utilizadores do laboratório. Como dono de laboratório, pode configurar esta política tomando os seguintes passos:

1. Na página inicial do seu laboratório, selecione **Configuração e políticas**.
2. Selecione a política de **paragem automática** na secção **Horários** do menu esquerdo.
3. Selecione uma das opções. As seguintes secções dão-lhe mais detalhes sobre estas opções: A política definida aplica-se apenas aos novos VMs criados em laboratório e não aos VMs já existentes. 

    ![Desligue automaticamente as opções políticas](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Configure as definições de paragem automática
A política de encerramento automático ajuda a minimizar os resíduos de laboratório, permitindo-lhe especificar a hora em que os VMs deste laboratório desligam.

Para ver (e alterar) as políticas para um laboratório, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório desejado.   
4. **Selecione Configuração e políticas**.

    ![Painel de definições de políticas](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. No painel de **configuração e políticas** do laboratório, selecione a paragem **automática** ao abrigo dos **Horários**.
   
    ![Encerramento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selecione **On** para ativar esta política e **Desligue** para desativá-la.
7. Se ativar esta política, especifique a hora (e o fuso horário) para desligar todos os VMs do laboratório atual.
8. Especifique **Sim** ou **Não** para a opção de enviar uma notificação 30 minutos antes do tempo especificado de encerramento automático. Se escolher **Sim**, introduza um ponto final de URL de webhook ou endereço de e-mail especificando onde pretende que a notificação seja publicada ou enviada. O utilizador recebe a notificação e é-lhe dada a opção de atrasar a paralisação. Para mais informações, consulte a secção [Notificações.](#notifications) 
9. Selecione **Guardar**.

    Por predefinição, uma vez ativada, esta política aplica-se a todos os VMs do laboratório atual. Para remover esta definição de um VM específico, abra o painel de gestão da VM e altere a sua definição de **encerramento automático.**
    
> [!NOTE]
> Se atualizar o horário de encerramento automático do seu laboratório ou uma máquina virtual específica dentro de 30 minutos da hora programada, o tempo de paragem atualizado aplicar-se-á para o horário do dia seguinte. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>O utilizador define um horário e pode optar por não o fazer
Se definir o seu laboratório para esta política, os utilizadores do laboratório podem anular ou optar por não participar do horário do laboratório. Esta opção confere aos utilizadores de laboratório o controlo total sobre o calendário de paragem automática dos seus VMs. Os utilizadores do laboratório não vêem nenhuma alteração na sua página de horário de paragem automática VM.

![Opção política de encerramento automático - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>O utilizador define um horário e não pode optar por não optar
Se definir o seu laboratório para esta política, os utilizadores do laboratório podem anular o horário do laboratório. No entanto, não podem optar por não aceitar a política de encerramento automático. Esta opção garante que todas as máquinas do seu laboratório estão sob um horário de paragem automática. Os utilizadores do laboratório podem atualizar o horário de paragem automática dos seus VMs e configurar notificações de encerramento.

![Opção política de encerramento automático - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>O utilizador não tem controlo sobre o horário definido pela administração do laboratório
Se definir o seu laboratório para esta política, os utilizadores do laboratório não podem anular ou optar por não cumprir o horário do laboratório. Esta opção oferece ao laboratório o controlo completo do horário de todas as máquinas do laboratório. Os utilizadores de laboratório só podem configurar notificações de paragem automática para os seus VMs.

![Opção política de encerramento automático - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Notificações
Assim que o encerramento automático configurado pelo dono do laboratório, as notificações serão enviadas aos utilizadores do laboratório 30 minutos antes do encerramento automático desencadeado se algum dos seus VMs for afetado. Esta opção dá aos utilizadores do laboratório a oportunidade de salvar o seu trabalho antes do encerramento. A notificação também fornece links para cada VM para as seguintes ações:

- Ignore o encerramento automático por este tempo
- Soneca a paragem automática por uma ou duas horas, para que possam continuar a trabalhar no VM.

A notificação é enviada através do ponto final do gancho web configurado ou de um endereço de e-mail especificado pelos proprietários do laboratório nas definições de encerramento automático. Os webhooks permitem-lhe construir ou configurar integrações que subscrevam determinados eventos. Quando um desses eventos for desencadeado, o DevTest Labs enviará uma carga de correio http para o URL configurado do webhook. Para mais informações sobre webhooks, consulte [Criar um webhook ou função API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Recomendamos que utilize web hooks porque são amplamente suportados por várias aplicações (por exemplo, Slack, Azure Logic Apps, e assim por diante.) e permite implementar a sua própria maneira de enviar notificações. Como exemplo, este artigo explica como obter notificação de encerramento automático de e-mails usando apps da Lógica Azure. Primeiro, vamos rapidamente analisar os passos básicos para permitir a notificação de encerramento automático no seu laboratório.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Criar uma aplicação lógica que receba notificações por e-mail
[A Azure Logic Apps](../logic-apps/logic-apps-overview.md) fornece muitos conectores fora da caixa que facilitam a integração de um serviço com outros clientes, como o Office 365 e o twitter. Ao alto nível, os passos para a criação de uma App Lógica para notificação de e-mail podem ser divididos em quatro fases: 

- Criar uma aplicação lógica. 
- Configure o modelo incorporado.
- Integre com o seu cliente de e-mail
- Obtenha o URL webhook.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
Para começar, crie uma aplicação lógica na subscrição do Azure utilizando os seguintes passos:

1. Selecione **+ Crie um recurso** no menu esquerdo, selecione **Integração,** e selecione **Logic App**. 

    ![Novo menu de aplicativológico lógico](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na **Aplicação Lógica - Criar** página, siga estes passos: 
    1. Introduza um **nome** para a aplicação lógica.
    2. Selecione a sua **subscrição** do Azure.
    3. Crie um novo grupo de **recursos** ou selecione um grupo de recursos existente. 
    4. Selecione um **local** para a aplicação lógica. 

        ![Nova aplicação lógica - configurações](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Nas **Notificações,** selecione **Recorrer à** notificação. 

    ![Ir para recurso](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selecione designer de **aplicativos Logic** na categoria **Ferramentas de Implantação.**

    ![Selecione PEDIDO/Resposta HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na página **HTTP Request-Response,** selecione **Use este modelo**. 

    ![Selecione Use esta opção de modelo](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copie o seguinte JSON na secção **JSON Schema** do Órgão de Pedido: 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![Solicitar corpo JSON Schema](./media/devtest-lab-auto-shutdown/request-json.png)
7. Selecione **+ Novo passo** no designer e siga estes passos:
    1. Pesquisar pelo **Office 365 Outlook - Envie um e-mail**. 
    2. Selecione **Enviar um e-mail** de **Ações**. 
    
        ![Enviar opção de e-mail](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selecione **iniciar sessão** para iniciar sessão na sua conta de e-mail. 
    4. Selecione campo **TO** e escolha proprietário.
    5. Selecione **ASSUNTO**, e insere um tema da notificação por e-mail. Por exemplo: "Encerramento da máquina vmName para laboratório: nome de laboratório."
    6. Selecione **BODY**, e defina o conteúdo do corpo para notificação por e-mail. Por exemplo: "O vmName está programado para desligar em 15 minutos. Ignore esta paragem clicando: URL. Atraso de paragem por uma hora: atrasoUrl60. Atraso de paragem por 2 horas: atrasoUrl120."

        ![Solicitar corpo JSON Schema](./media/devtest-lab-auto-shutdown/email-options.png)
1. Selecione **Guardar** na barra de ferramentas. Agora, pode copiar o **URL HTTP POST**. Selecione o botão de cópia para copiar o URL para a área de redação. 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Configure o encerramento automático para VMs computacionais

1. Na página da **máquina Virtual,** selecione **a paragem automática** no menu esquerdo na secção **Operações.** 
2. Na página de encerramento automático, selecione **On** para ativar esta política e **Desligue** para desativá-la. **Auto-shutdown**
3. Se ativar esta política, especifique a **hora** (e o **fuso horário)** em que o VM deve ser desligado.
4. Especifique **Sim** ou **Não** para a opção de enviar uma notificação 30 minutos antes do tempo especificado de encerramento automático. Se escolher **Sim**, introduza um ponto final de URL de webhook ou endereço de e-mail especificando onde pretende que a notificação seja publicada ou enviada. O utilizador recebe a notificação e é-lhe dada a opção de atrasar a paralisação. Para mais informações, consulte a secção [Notificações.](#notifications) 
9. Selecione **Guardar**.

    ![Configure o encerramento automático para um VM computacional](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Ver registos de atividade para atualizações de encerramento automático
Quando atualizar a definição de desativação automática, verá a atividade registada no registo de atividade para o VM. 

1. No [portal Azure,](https://portal.azure.com)navegue para a página inicial do seu VM.
2. Selecione **registo de atividade** a partir do menu esquerdo. 
3. Remover **Recurso: mycomputevm** dos filtros.
3. Confirme que vê o funcionamento do **Add ou modifique** o funcionamento dos horários no registo de atividade. Se não o vir, espere um dia e refresque o registo de atividade.

    ![Entrada de registo de atividade](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
4. Selecione o funcionamento **de Adicionar ou modificar os horários** para ver as seguintes informações na página **Resumo:**

    - Nome de operação (Adicionar ou modificar horários)
    - A data e a hora em que a definição de encerramento automático foi atualizada.
    - O endereço de e-mail do utilizador que atualizou a definição. 

        ![Resumo de entrada de registo de atividade](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
5. Mude para o separador **'Alterar'** o histórico na página Adicionar ou modificar os **horários,** consulte o histórico de alterações para a definição. No exemplo seguinte, o tempo de paragem foi alterado das 19:00 às 18:00 do dia 10 de abril de 2020 às 15:18:47 EST. E o cenário foi desativado às 15:25:09 EST. 

    ![Registo de atividade - alterar histórico](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
6. Para ver mais detalhes sobre a operação, mude para o separador **JSON** na página Adicionar ou modifique os **horários.**

## <a name="next-steps"></a>Passos seguintes
Para aprender a definir todas as políticas, consulte as políticas de [laboratório define em Azure DevTest Labs](devtest-lab-set-lab-policy.md).

