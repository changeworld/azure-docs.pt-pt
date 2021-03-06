---
title: Aceder a um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Neste tutorial, vai aceder a máquinas virtuais num laboratório de sala de aula configurado por um professor.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 27d79e28a986e929fb71dd77fc50b3c2cd32618f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77134045"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Tutorial: Aceder a um laboratório de sala de aula no Azure Lab Services
Neste tutorial, como estudante vai ligar-se a uma máquina virtual (VM) num laboratório de sala de aula. 

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Registe-se no laboratório
> * Iniciar a VM
> * Ligar à VM

## <a name="register-to-the-lab"></a>Registe-se no laboratório

1. Navegue para o **URL de registo** que recebeu do professor/educador. Não precisa de usar o URL de registo depois de completar o registo. Em vez disso, [https://labs.azure.com](https://labs.azure.com)utilize o URL: . O Internet Explorer 11 ainda não tem suporte. 
1. Inicie sessão no serviço com a sua conta escolar para concluir o registo. 

    > [!NOTE]
    > É necessária uma conta Microsoft para a utilização de Serviços De Laboratório Azure. Se estiver a tentar utilizar a sua conta não Microsoft, como contas Yahoo ou Google para iniciar sessão no portal, siga as instruções para criar uma conta Microsoft que estará ligada à sua conta não Microsoft. Em seguida, siga os passos para completar o processo de registo. 
1. Depois de se registar, confirme se vê a máquina virtual do laboratório a que tem acesso. 
1. Espere até a máquina virtual estar pronta. No azulejo VM, repare nos seguintes campos:
    1. No topo do azulejo, vê-se o **nome do laboratório.**
    1. À sua direita, vê-se o ícone que representa o **sistema operativo (OS)** do VM. Neste exemplo, é o Windows OS. 
    1. A barra de progresso no azulejo mostra o número de horas utilizadas contra o número de horas de quota que lhe são [atribuídas.](how-to-configure-student-usage.md#set-quotas-for-users) Desta vez é o tempo adicional atribuído para além da hora marcada para o laboratório. 
    1. Vê ícones/botões na parte inferior do azulejo para iniciar/parar o VM e ligar-se ao VM. 
    1. À direita dos botões, vê-se o estado do VM. Confirme que vê o estado do VM **parado**. 

        ![VM em estado de parada](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Iniciar a VM
1. **Inicie** o VM selecionando o primeiro botão como mostrado na imagem seguinte. Este processo leva algum tempo.  

    ![Iniciar a VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Confirme que o estado do VM está definido para **executar**. 

    ![VM em estado de execução](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Note que o ícone do primeiro botão mudou para representar uma operação de **paragem.** Pode selecionar este botão para parar o VM. 

## <a name="connect-to-the-vm"></a>Ligar à VM

1. Selecione o segundo botão como mostrado na imagem seguinte para **ligar** ao VM do laboratório. 

    ![Ligar à VM](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Faça um dos seguintes passos: 
    1. Para máquinas virtuais **Windows,** guarde o ficheiro **RDP** para o disco rígido. Abra o ficheiro RDP para ligar à máquina virtual. Utilize o nome de **utilizador** e **a palavra-passe** que obtém do seu educador/professor para iniciar sessão na máquina. 
    3. Para máquinas virtuais **Linux,** pode utilizar **SSH** ou **RDP** (se estiver ativado) para se ligar a elas. Para mais informações, consulte [Ativar a ligação remota para as máquinas Linux](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, acedeu a um laboratório de sala de aulas através da ligação de registo que recebeu do seu professor/educador.

Como dono de laboratório, quer ver quem se registou no seu laboratório e rastrear o uso de VMs. Avançar para o próximo tutorial para aprender a rastrear o uso do laboratório:

> [!div class="nextstepaction"]
> [Track usage of a lab](tutorial-track-usage.md) (Acompanhar a utilização de um laboratório) 
