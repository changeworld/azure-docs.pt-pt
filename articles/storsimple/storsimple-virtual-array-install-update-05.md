---
title: Instale a atualização 0.5 no StorSimple Virtual Array [ StorSimple Virtual Array ] Microsoft Docs
description: Descreve como usar o StorSimple Virtual Array web UI para aplicar atualizações usando o portal Azure e método de hotfix
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: e09ff4bcbc141b1a1f80bc278918a291639c1885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61445425"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Instale a atualização 0.5 na sua Matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar o Update 0.5 no seu StorSimple Virtual Array através da UI web local e através do portal Azure. Tem de aplicar atualizações de software ou hotfixes para manter o storSimple Virtual Array atualizado.

Antes de aplicar uma atualização, recomendamos que leve os volumes ou partilhas offline no anfitrião primeiro e depois o dispositivo. Isto minimiza qualquer possibilidade de danos em dados. Depois de os volumes ou ações estarem offline, deve também retirar uma cópia de segurança manual do dispositivo.

> [!IMPORTANT]
> - A atualização 0.5 corresponde à versão **de software 10.0.10290.0** no seu dispositivo. Para obter informações sobre o que é novidade nesta atualização, vá a notas de [Lançamento para Atualização 0.5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Se estiver a executar o Update 0.2 ou mais tarde, recomendamos que instale as atualizações através do portal Azure. Se estiver a executar versões de software Update 0.1 ou GA, tem de utilizar o método de correção de hotéis através da UI web local para instalar o Update 0.5.
>
> - Tenha em mente que instalar uma atualização ou um hotfix reinicia o seu dispositivo. Dado que o StorSimple Virtual Array é um único dispositivo de nó, qualquer I/O em curso é interrompido e o seu dispositivo experimenta tempo de inatividade.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar o Update 0.2 e mais tarde, recomendamos que instale atualizações através do portal Azure. O procedimento do portal requer que o utilizador faça o download, descarregue e instale as atualizações. Este procedimento leva cerca de 7 minutos para ser concluído. Execute os seguintes passos para instalar a atualização ou o hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Depois de concluída a instalação, vá ao serviço StorSimple Device Manager. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que acaba de atualizar. Vá para **as Definições > Gerir > atualizações**de dispositivos . A versão de software exibida deve ser **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>Use a UI web local

Existem dois passos ao utilizar a UI web local:

* Descarregue a atualização ou o hotfix
* Instale a atualização ou o hotfix

### <a name="download-the-update-or-the-hotfix"></a>Descarregue a atualização ou o hotfix

Execute os seguintes passos para transferir a atualização de software a partir do Catálogo Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para descarregar a atualização ou o hotfix

1. Inicie o Internet [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Explorer e navegue para .

2. Se esta for a primeira vez que utiliza o Catálogo Microsoft Update neste computador, clique em **Instalar** quando lhe for pedido para instalar o suplemento do Catálogo Microsoft Update.

3. Na caixa de pesquisa do Catálogo de Atualizações da Microsoft, introduza o número da Base de Conhecimento (KB) do hotfix que pretende descarregar. Introduza **4021576** para Atualizar 0.5 e, em seguida, clique em **Procurar**.
   
    A listagem de hotfix aparece, por exemplo, **storSimple Virtual Array Update 0.5**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Clique em **Baixar**. 

5. Devias ver dois ficheiros para descarregar, um *.msu* e um ficheiro *.cab.* Descarregue cada um desses ficheiros para uma pasta. A pasta também pode ser copiada para uma partilha de rede que é acessível a partir do dispositivo.

6. Abra a pasta onde os ficheiros estão localizados.
    ![Ficheiros no pacote](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Verá:
    -  Um ficheiro `WindowsTH-KB3011067-x64`de pacote autónomo da Microsoft Update . Este ficheiro é utilizado para atualizar o software do dispositivo.
    - Um ficheiro `GenevaMonitoringAgentPackageInstaller`pacote de pacote de agente de monitorização de Genebra. Este ficheiro é utilizado para atualizar o agente do serviço de Monitorização e Diagnóstico (MDS). Clique duas vezes no ficheiro do táxi. Um .msi é exibido. Selecione o ficheiro, clique à direita e, em seguida, **extrai** o ficheiro. Utilizará o ficheiro _.msi_ para atualizar o agente.

        ![Extrair ficheiro de atualização do agente MDS](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da atualização ou instalação hotfix, certifique-se de que tem a atualização ou o hotfix descarregado localmente no seu anfitrião ou acessível através de uma partilha de rede.

Utilize este método para instalar atualizações num dispositivo que executa as versões de software GA ou Update 0.1. Este procedimento leva menos de 2 minutos para ser concluído. Execute os seguintes passos para instalar a atualização ou o hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou o hotfix

1. Na Web UI local, vá a**Atualização**de Software **de Manutenção** > .
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. No caminho do **ficheiro Atualizar,** introduza o nome do ficheiro para a atualização ou para o hotfix. Também pode navegar no ficheiro de instalação de atualização ou hotfix se for colocado numa partilha de rede. Clique em **Aplicar**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. É apresentado um aviso. Dado que se trata de um único dispositivo de nó, após a atualização ser aplicada, o dispositivo reinicia e há tempo de inatividade. Clique no ícone de verificação.
   
   ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. A atualização começa. Depois de o dispositivo ser atualizado com sucesso, reinicia. A UI local não é acessível nesta duração.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Após o reinício, é levado para a página **do Sinal.** Para verificar se o software do dispositivo foi atualizado, na Web UI local, vá para **a Atualização** > de**Software**de Manutenção . A versão de software exibida deve ser **10.0.0.0.0.10290.0** para Atualização 0.5.
   
   > [!NOTE]
   > Relatamos as versões de software de uma forma ligeiramente diferente na UI web local e no portal Azure. Por exemplo, a UI web local reporta **10.0.0.0.10290** e o portal Azure reporta **10.0.10290.0** para a mesma versão.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. O próximo passo é atualizar o agente da MDS. Na página de Atualização de **Software,** vá ao `GenevaMonitoringAgentPackageInstaller.msi` caminho do ficheiro **Update** e navegue para o ficheiro. Repita os passos 2-4. Depois do reinício da matriz virtual, assine na UI web local.

A atualização está agora completa.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a administração do seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

