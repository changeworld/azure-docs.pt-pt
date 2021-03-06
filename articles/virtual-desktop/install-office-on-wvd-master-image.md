---
title: Instalar escritório em uma imagem master VHD - Azure
description: Como instalar e personalizar o Office numa imagem master do Windows Virtual Desktop para o Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127846"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalar o Office numa imagem principal de VHD

Este artigo diz-lhe como instalar o Office 365 ProPlus, OneDrive e outras aplicações comuns numa imagem de disco rígido virtual master (VHD) para upload para O Azure. Se os seus utilizadores precisarem de aceder a determinadas aplicações de linha de negócio (LOB), recomendamos que as instale depois de completar as instruções neste artigo.

Este artigo assume que já criou uma máquina virtual (VM). Caso contrário, consulte [Preparar e personalizar uma imagem VHD mestre](set-up-customize-master-image.md#create-a-vm)

Este artigo também assume que você tem acesso elevado no VM, seja ele provisionado em Azure ou Hyper-V Manager. Caso contrário, consulte o acesso elevado para gerir todos os grupos de [subscrição e gestão do Azure.](../role-based-access-control/elevate-access-global-admin.md)

>[!NOTE]
>Estas instruções são para uma configuração específica do Windows Virtual Desktop que pode ser usada com os processos existentes da sua organização.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instalar o Office no modo de ativação do computador partilhado

A ativação partilhada do computador permite-lhe implementar o Office 365 ProPlus para um computador da sua organização que é acedido por vários utilizadores. Para obter mais informações sobre a ativação de computador partilhado, consulte [a visão geral da ativação do computador partilhado para o Office 365 ProPlus](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/).

Utilize a ferramenta de [implantação](https://www.microsoft.com/download/details.aspx?id=49117) do escritório para instalar o Office. A multi-sessão do Windows 10 Enterprise suporta apenas as seguintes versões do Office:
- Office 365 ProPlus
- Office 365 Business que vem com uma subscrição microsoft 365 Business

A Ferramenta de Implantação do Office requer um ficheiro XML de configuração. Para personalizar a seguinte amostra, consulte as Opções de [Configuração para a Ferramenta](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)de Implementação do Escritório .

Esta configuração de amostra XML que fornecemos fará as seguintes coisas:

- Instale o Office a partir do canal mensal e entregue atualizações a partir do canal mensal quando forem executados.
- Use a arquitetura x64.
- Desative as atualizações automáticas.
- Remova quaisquer instalações existentes do Office e emigra as suas definições.
- Ativar a ativação do computador partilhado.

>[!NOTE]
>A funcionalidade de pesquisa de stencil da Visio pode não funcionar como esperado no Windows Virtual Desktop.

Aqui está o que esta configuração de amostra XML não vai fazer:

- Instalar skype para negócios
- Instale o OneDrive no modo por utilizador. Para saber mais, consulte [Instalar o OneDrive no modo por máquina](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>A ativação partilhada do computador pode ser configurada através de objetos de política de grupo (GPOs) ou definições de registo. O GPO está localizado em **\\Modelos\\Administrativos\\de Configuração\\de ComputadorEs Microsoft Office 2016 (Máquina) Definições de licenciamento**

A ferramenta de implantação do escritório contém configuração.exe. Para instalar o Office, execute o seguinte comando numa linha de comando:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Configuração da amostra.xml

A amostra XML seguinte instalará o lançamento mensal.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>A equipa do Office recomenda a utilização de uma instalação de 64 bits para o parâmetro **OfficeClientEdition.**

Depois de instalar o Office, pode atualizar o comportamento padrão do Office. Executar os seguintes comandos individualmente ou num ficheiro de lote para atualizar o comportamento.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Instale o OneDrive no modo por máquina

O OneDrive é normalmente instalado por utilizador. Neste ambiente, deve ser instalado por máquina.

Eis como instalar o OneDrive no modo por máquina:

1. Primeiro, crie um local para encenar o instalador OneDrive. Uma pasta de\\\\disco local ou a localização [unc] (file://unc) está bem.

2. Baixe OneDriveSetup.exe para a sua localização faseada com este link:<https://aka.ms/OneDriveWVD-Installer>

3. Se instalou o escritório com o OneDrive omitindo ** \<o Exclusapp\>ID="OneDrive" /**, desinstale quaisquer instalações oneDrive por utilizador existentes a partir de um pedido de comando elevado executando o seguinte comando:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Executar este comando a partir de um pedido de comando elevado para definir o valor de registo **AllUsersInstall:**

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Executar este comando para instalar o OneDrive no modo por máquina:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Executar este comando para configurar o OneDrive para iniciar o início do início do início do início do início do início do início do início do início do sessão para todos os utilizadores:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Ative **silenciosamente a conta** de utilizador executando o seguinte comando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Redirecione e mova as pastas conhecidas do Windows para o OneDrive executando o seguinte comando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Equipas e Skype

O Windows Virtual Desktop não suporta o Skype para negócios e equipas.

## <a name="next-steps"></a>Passos seguintes

Agora que adicionou o Office à imagem, pode continuar a personalizar a sua imagem de Mestre VHD. Consulte [preparar e personalizar uma imagem VHD mestre](set-up-customize-master-image.md).
