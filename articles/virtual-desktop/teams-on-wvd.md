---
title: Equipas da Microsoft no Windows Virtual Desktop - Azure
description: Como utilizar as equipas da Microsoft no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 15a4c9b16b102310fd02f8db3a4fb93cff84882b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314058"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Utilize as equipas da Microsoft no ambiente de trabalho do Windows Virtual

> Aplica-se a: Windows 10 e Windows 10 IoT Enterprise

Ambientes virtualizados apresentam um conjunto único de desafios para apps de colaboração como microsoft Teams, incluindo o aumento da latência, alta utilização do CPU anfitrião e fraco desempenho geral de áudio e vídeo. Para saber mais sobre a utilização de Equipas Microsoft em ambientes VDI, consulte [as Equipas para infraestruturas de ambiente de trabalho virtualizadas](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar as Equipas microsoft no Windows Virtual Desktop, terá de fazer estas coisas:

- Instale o [cliente Do Windows Desktop](connect-windows-7-and-10.md) num dispositivo Windows 10 que satisfaça os requisitos de [hardware](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)da Microsoft Teams.
- Ligue-se a uma máquina virtual Do Windows 10 Multi-sessão ou Windows 10 Enterprise (VM).
- [Prepare a sua rede](https://docs.microsoft.com/microsoftteams/prepare-network) para as Equipas Microsoft.

## <a name="use-unoptimized-microsoft-teams"></a>Utilize equipas microsoft não otimizadas

Pode utilizar equipas Microsoft não otimizadas nos ambientes do Windows Virtual Desktop para aproveitar as funcionalidades completas de chat e colaboração das Microsoft Teams, bem como chamadas áudio. A qualidade do áudio nas chamadas variará em função da configuração do anfitrião, uma vez que as chamadas não otimizadas utilizam mais do CPU do anfitrião.

### <a name="prepare-your-image-for-teams"></a>Prepare a sua imagem para as equipas

Para permitir a instalação das equipas por máquina, detete a seguinte tecla de registo no hospedeiro:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Instalar equipas da Microsoft

Pode implementar a aplicação de ambiente de trabalho Teams utilizando uma instalação por máquina. Para instalar equipas da Microsoft no seu ambiente de ambiente de trabalho virtual windows:

1. Descarregue o [pacote MSI](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) das equipas que corresponda ao seu ambiente. Recomendamos a utilização do instalador de 64 bits num sistema operativo de 64 bits.
2. Execute este comando para instalar o MSI no VM hospedeiro.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Isto irá instalar equipas para ficheiros de programas ou ficheiros de programa (x86). Da próxima vez que iniciar o insessão e iniciar as Equipas, a aplicação irá pedir as suas credenciais.

      > [!NOTE]
      > Os utilizadores e administradores não podem desativar o lançamento automático para equipas durante o início de sessão neste momento.

      Para desinstalar o MSI do VM anfitrião, execute este comando:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Se instalar equipas com a definição de MSI ALLUSER=1, as atualizações automáticas serão desativadas. Recomendamos que atualize as Equipas pelo menos uma vez por mês.
      
### <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalize propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões
Personalizar as propriedades do Protocolo de Ambiente de Trabalho Remoto (RDP) de um pool anfitrião, como experiência de multimonitor, permitindo a reorientação do Microfone e do áudio, permite-lhe oferecer uma experiência ideal para os seus utilizadores com base nas suas necessidades. Pode personalizar propriedades RDP no Windows Virtual Desktop utilizando o parâmetro **-CustomRdpProperty** no **set-RdsHostPool** cmdlet.
Consulte as definições de [ficheiroRD Suportadas](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) para obter uma lista completa de propriedades suportadas e os seus valores predefinidos.
