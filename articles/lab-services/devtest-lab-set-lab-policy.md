---
title: Gerir as políticas de laboratório em Azure DevTest Labs [ Laboratórios DevTest] Microsoft Docs
description: Aprenda a definir políticas de laboratório como tamanhos vm, VMs máximos por utilizador e automação de encerramento.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: aa0ffbd69e73ddbef72e0eabf79f2736079c3d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270722"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Gerir todas as políticas para um laboratório em Azure DevTest Labs

A Azure DevTest Labs permite controlar os custos e minimiza o desperdício nos seus laboratórios gerindo políticas (configurações) para cada laboratório. Este artigo explica em pormenor como definir cada política.  

## <a name="set-allowed-virtual-machine-sizes"></a>Conjunto permitido tamanhos de máquina virtual
A política de definição dos tamanhos de VM permitidos ajuda a minimizar os resíduos de laboratório, permitindo-lhe especificar quais os tamanhos de VM permitidos no laboratório. Se esta política for ativada, apenas os tamanhos vm desta lista podem ser usados para criar VMs.

1. No [portal Azure,](https://go.microsoft.com/fwlink/p/?LinkID=525040)selecione um laboratório e, em seguida, selecione **Configuração e políticas**.

    ![Aceda à configuração e políticas do laboratório](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. No painel de **configuração e políticas** do laboratório, selecione **tamanhos permitidos**de máquinas virtuais .
   
    ![Tamanhos permitidos de máquinas virtuais](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Selecione **On** para ativar esta política e **Desligue** para desativá-la.

1. Se ativar esta política, selecione um ou mais tamanhos vm que podem ser criados no seu laboratório.

1. Selecione **Guardar**.

## <a name="set-virtual-machines-per-user"></a>Definir máquinas virtuais por utilizador
A política para **máquinas Virtuais por utilizador** permite especificar o número de VMs que podem ser criados por um utilizador individual. Se um utilizador tentar criar ou reclamar um VM quando o limite de utilizador tiver sido cumprido, uma mensagem de erro indica que o VM não pode ser criado/reclamado. 

1. No painel de **configuração e políticas** do laboratório, selecione **máquinas virtuais por utilizador**.
   
    ![Máquinas virtuais por utilizador](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selecione **Sim** para limitar o número de VMs por utilizador. Se não quiser limitar o número de VMs por utilizador, selecione **No**. Se selecionar **Sim,** introduza um valor numérico indicando o número de VMs que podem ser criados ou reclamados por um utilizador. 

1. Selecione **Sim** para limitar o número de VMs que podem usar SSD (disco de estado sólido). Se não quiser limitar o número de VMs que podem utilizar SSD, selecione **No**. Se selecionar **Sim,** introduza um valor indicando o número de VMs que podem ser criados usando SSD. 

1. Selecione **Guardar**.

## <a name="set-virtual-machines-per-lab"></a>Definir máquinas virtuais por laboratório
A política para **máquinas virtuais por laboratório** permite especificar o número de VMs que podem ser criados para o laboratório atual. Se um utilizador tentar criar um VM quando o limite do laboratório tiver sido cumprido, uma mensagem de erro indica que o VM não pode ser criado. 

1. No painel de **configuração e políticas** do laboratório, selecione **máquinas virtuais por laboratório**.
   
    ![Máquinas virtuais por laboratório](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Selecione **Sim** para limitar o número de VMs por laboratório. Se não quiser limitar o número de VMs por laboratório, selecione **No**. Se selecionar **Sim,** introduza um valor numérico indicando o número de VMs que podem ser criados ou reclamados por um utilizador. 

1. Selecione **Sim** para limitar o número de VMs que podem usar SSD (disco de estado sólido). Se não quiser limitar o número de VMs que podem utilizar SSD, selecione **No**. Se selecionar **Sim,** introduza um valor indicando o número de VMs que podem ser criados usando SSD. 

1. Selecione **Guardar**.

## <a name="set-auto-shutdown"></a>Definir a paragem automática
A política de paragem automática ajuda a minimizar os resíduos de laboratório, deixando-o especificar a hora em que os VMs deste laboratório desligam.

1. No painel de **configuração e políticas** do laboratório, selecione a paragem **automática**.
   
    ![Paragem automática](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selecione **On** para ativar esta política e **Desligue** para desativá-la.

1. Se ativar esta política, especifique a hora (e o fuso horário) para desligar todos os VMs do laboratório atual.

1. Especifique **Sim** ou **Não** para a opção de enviar uma notificação 15 minutos antes do tempo de paragem automática especificado. Se escolher **Sim**, introduza um ponto final de URL de webhook ou um endereço de e-mail especificando onde pretende que a notificação seja publicada ou enviada. O utilizador recebe a notificação e tem a opção de adiar a paralisação.

   Para mais informações sobre webhooks, consulte [Criar um webhook ou função API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Selecione **Guardar**.

Por predefinição, uma vez ativada, esta política aplica-se a todos os VMs do laboratório atual. Para remover esta definição de um VM específico, abra o painel de gestão da VM e altere a sua definição de **paragem automática.**

## <a name="set-auto-shutdown-policy"></a>Definir a política de encerramento automático
Como dono de laboratório, pode configurar um horário de encerramento para todos os VMs do seu laboratório. Ao fazê-lo, pode poupar custos de máquinas de funcionamento que não estão a ser usadas (ociosa). Você pode impor uma política de encerramento em todos os seus VMs de laboratório centralmente, mas também poupar os seus utilizadores de laboratório o esforço de configurar um horário para as suas máquinas individuais. Esta funcionalidade permite definir a política na sua programação de laboratório a partir de não oferecer controlo total aos utilizadores do laboratório. Como dono de laboratório, pode configurar esta política tomando os seguintes passos:

1. Na página inicial do seu laboratório, selecione **Configuração e políticas**.
2. Selecione a política de **paragem automática** na secção **Horários** do menu esquerdo.
3. Selecione uma das opções. As seguintes secções dão-lhe mais detalhes sobre estas opções: A política definida aplica-se apenas aos novos VMs criados em laboratório e não aos VMs já existentes. 

    ![Opções de política de encerramento automático](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>O utilizador define um horário e pode optar por não o fazer
Se definir o seu laboratório para esta política, os utilizadores do laboratório podem anular ou optar por não participar do horário do laboratório. Esta opção confere aos utilizadores de laboratório o controlo total sobre o calendário de paragem automática dos seus VMs. Os utilizadores do laboratório não vêem nenhuma alteração na sua página de horário de paragem automática VM.

![Opção de política de encerramento automático - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>O utilizador define um horário e não pode optar por não optar
Se definir o seu laboratório para esta política, os utilizadores do laboratório podem anular o horário do laboratório. No entanto, não podem optar por não aceitar a política de encerramento automático. Esta opção garante que todas as máquinas do seu laboratório estão sob um horário de paragem automática. Os utilizadores do laboratório podem atualizar o horário de paragem automática dos seus VMs e configurar notificações de encerramento.

![Opção de política de encerramento automático - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>O utilizador não tem controlo sobre o horário definido pela administração do laboratório
Se definir o seu laboratório para esta política, os utilizadores do laboratório não podem anular ou optar por não cumprir o horário do laboratório. Esta opção oferece ao laboratório o controlo completo do horário de todas as máquinas do laboratório. Os utilizadores de laboratório só podem configurar notificações de paragem automática para os seus VMs.

![Opção de política de encerramento automático - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Definir arranque automático
A política de arranque automático permite especificar quando os VMs no laboratório atual devem ser iniciados.  

1. No painel de **configuração e políticas** do laboratório, selecione **Autostart**.
   
    ![Arranque automático](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selecione **On** para ativar esta política e **Desligue** para desativá-la.

3. Se ativar esta política, especifique a hora de início programada, o fuso horário e os dias da semana para os quais o tempo se aplica. 

4. Selecione **Guardar**.

Uma vez ativada, esta política não é aplicada automaticamente a quaisquer VMs no laboratório atual. Para aplicar esta definição a um VM específico, abra o painel de gestão da VM e altere a sua definição **de Arranque Automático.**

## <a name="set-expiration-date"></a>Definir data de validade
Pode definir uma data de validade quando [criar o VM](devtest-lab-add-vm.md). Em **definições avançadas,** escolha o ícone do calendário para especificar uma data em que o VM é automaticamente eliminado. Por defeito, o VM nunca expira.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de definir e aplicar as várias definições políticas de VM para o seu laboratório, aqui estão algumas coisas para tentar a seguir:

* [Compreender endereços IP partilhados](devtest-lab-shared-ip.md) - Explica como os endereços IP partilhados são usados em Laboratórios DevTest para minimizar o número de endereços IP públicos necessários para se conectarem aos seus VMs de laboratório.
* [Configure gestão](devtest-lab-configure-cost-management.md) de custos - Ilustra como usar o gráfico mensal de tendência de **custos estimado**  
  para ver o custo estimado do mês em curso e o custo previsto para o final do mês.
* [Criar imagem personalizada](devtest-lab-create-template.md) - Quando criar um VM, especifice uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada a partir de um ficheiro VHD.
* [Configure Imagens do Marketplace](devtest-lab-configure-marketplace-images.md) - Azure DevTest Labs suporta a criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais, se houver, imagens do Azure Marketplace podem ser usadas na criação de VMs num laboratório.
* [Crie um VM num laboratório](devtest-lab-add-vm.md) - Ilustra como criar um VM a partir de uma imagem base (personalizada ou marketplace), e como trabalhar com artefactos no seu VM.

