---
title: Instale a Atualização 1.0 no StorSimple Virtual Array [ StorSimple Virtual Array ] Microsoft Docs
description: Descreve como usar o StorSimple Virtual Array web UI para aplicar atualizações usando o portal Azure e método de hotfix
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fa53213e577028628d48db91704578e23888f2a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254511"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Instale a Atualização 1.0 na sua Matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar o Update 1.0 no seu StorSimple Virtual Array através da UI web local e através do portal Azure.

Aplica as atualizações de software ou os hotfixes para manter o storSimple Virtual Array atualizado. Antes de aplicar uma atualização, recomendamos que leve os volumes ou partilhas offline no anfitrião primeiro e depois o dispositivo. Isto minimiza qualquer possibilidade de danos em dados. Depois de os volumes ou ações estarem offline, deve também retirar uma cópia de segurança manual do dispositivo.

> [!IMPORTANT]
> - A atualização 1.0 corresponde a **10.0.10296.0** versão de software no seu dispositivo. Para obter informações sobre o que é novidade nesta atualização, vá a notas de [Lançamento para Atualização 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Tenha em mente que instalar uma atualização ou um hotfix reinicia o seu dispositivo. Dado que o StorSimple Virtual Array é um único dispositivo de nó, qualquer I/O em curso é interrompido e o seu dispositivo experimenta tempo de inatividade.
>
> - A atualização 1 só está disponível no portal Azure se a matriz virtual estiver a executar o Update 0.6. Para as matrizes virtuais que executam as versões pré-Actualização 0.6, tem de instalar o Update 0.6 primeiro e, em seguida, instalar o Update 1.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar o Update 0.2 e mais tarde, recomendamos que instale atualizações através do portal Azure. O procedimento do portal requer que o utilizador faça o download, descarregue e instale as atualizações. Dependendo da versão do software, a sua matriz virtual está a funcionar, aplicar a atualização através do portal Azure é diferente.

 - Se a sua matriz virtual estiver a funcionar Update 0.6, o portal Azure instala diretamente o Update 1 (10.0.10296.0) no seu dispositivo. Este procedimento leva cerca de 7 minutos para ser concluído.
 - Se a sua matriz virtual estiver a executar uma versão antes do Update 0.6, a atualização é feita em duas fases. O portal Azure instala primeiro o Update 0.6 (10.0.10293.0) no seu dispositivo. A matriz virtual reinicia e o portal instala a Atualização 1 (10.0.10296.0) no seu dispositivo. Este procedimento leva cerca de 15 minutos para ser concluído.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Depois de concluída a instalação, vá ao serviço StorSimple Device Manager. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que acaba de atualizar. Vá para **as Definições > Gerir > atualizações**de dispositivos . A versão de software exibida deve ser **10.0.10296.0**.

![Versão de software após atualização](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Use a UI web local

Existem dois passos ao utilizar a UI web local:

* Descarregue a atualização ou o hotfix
* Instale a atualização ou o hotfix

> [!IMPORTANT] 
> **Proceda aesta atualização apenas se estiver a executar a Atualização 0.6 (10.0.10293.0). Se estiver a executar uma versão anterior, instale primeiro o [Update 0.6](storsimple-virtual-array-install-update-06.md) no seu dispositivo e, em seguida, aplique o Update 1.**

### <a name="download-the-update-or-the-hotfix"></a>Descarregue a atualização ou o hotfix

Se a sua matriz virtual estiver a executar o Update 0.6, execute os seguintes passos para descarregar o Update 1 do Catálogo de Atualizações da Microsoft.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para descarregar a atualização ou o hotfix

1. Inicie o Internet [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Explorer e navegue para .

2. Se estiver a utilizar o Catálogo de Atualizações da Microsoft pela primeira vez neste computador, clique em **Instalar** quando for solicitado para instalar o add-on do Microsoft Update Catalog.

3. Na caixa de pesquisa do Catálogo de Atualizações da Microsoft, introduza o número da Base de Conhecimento (KB) do hotfix que pretende descarregar. Introduza **4047203** para atualizar 1.0 e, em seguida, clique em **Procurar**.
   
    A listagem de hotfix aparece, por exemplo, **storSimple Virtual Array Update 1.0**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Clique em **Baixar**.

5. Descarregue os dois ficheiros para uma pasta. Também pode copiar a pasta para uma partilha de rede que é acessível a partir do dispositivo.

6. Abra a pasta onde os ficheiros estão localizados.

    ![Ficheiros no pacote](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Vê dois ficheiros:
    -  Um ficheiro `WindowsTH-KB3011067-x64`de pacote autónomo da Microsoft Update . Este ficheiro é utilizado para atualizar o software do dispositivo.
    - Um ficheiro que contém atualizações cumulativas para agosto. `windows8.1-kb4034681-x64` Para mais informações sobre o que está incluído neste rollup, vá para o rollup mensal de [segurança de agosto.](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810)

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da instalação de atualização ou de fixação, certifique-se de que:

 - Tem a atualização ou o hotfix descarregado localmente no seu anfitrião ou acessível através de uma partilha de rede.
 - A sua matriz virtual está a executar a Atualização 0.6 (10.0.10293.0). Se estiver a executar uma versão antes do Update 0.6, instale primeiro a [Atualização 0.6](storsimple-virtual-array-install-update-06.md) e, em seguida, instale o Update 1.

Este procedimento leva cerca de 4 minutos para ser concluído. Execute os seguintes passos para instalar a atualização ou o hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou o hotfix

1. Na Web UI local, vá a**Atualização**de Software **de Manutenção** > . Tome nota da versão de software que está a executar. **Proceda aesta atualização apenas se estiver a executar a Atualização 0.6 (10.0.10293.0). Se estiver a executar uma versão anterior, instale primeiro o [Update 0.6](storsimple-virtual-array-install-update-06.md) no seu dispositivo e, em seguida, aplique o Update 1.**
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. No caminho do **ficheiro Atualizar,** introduza o nome do ficheiro para a atualização ou para o hotfix. Também pode navegar no ficheiro de instalação de atualização ou hotfix se for colocado numa partilha de rede. Clique em **Aplicar**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. É apresentado um aviso. Dado que a matriz virtual é um único dispositivo de nó, após a atualização ser aplicada, o dispositivo reinicia e há tempo de inatividade. Clique no ícone de verificação.
   
   ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. A atualização começa. Depois de o dispositivo ser atualizado com sucesso, reinicia. A UI local não é acessível nesta duração.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Após o reinício, é levado para a página **do Sinal.** Para verificar se o software do dispositivo foi atualizado, na Web UI local, vá para **a Atualização** > de**Software**de Manutenção . A versão de software exibida deve ser **10.0.0.0.0.10296** para a Atualização 1.0.
   
   > [!NOTE]
   > Relatamos as versões de software de uma forma ligeiramente diferente na UI web local e no portal Azure. Por exemplo, a UI web local reporta **10.0.0.0.10296** e o portal Azure reporta **10.0.10296.0** para a mesma versão.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Repita os passos 2-4 para `windows8.1-kb4012213-x64`instalar a correção de segurança do Windows utilizando o ficheiro . A matriz virtual reinicia após a instalação e precisa de assinar na UI web local.

> [!NOTE]
> Se aplicou diretamente o Update 1 a um dispositivo que executa uma versão antes do Update 0.6, está a perder algumas atualizações. Contacte o Microsoft Support para os próximos passos.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a administração do seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
