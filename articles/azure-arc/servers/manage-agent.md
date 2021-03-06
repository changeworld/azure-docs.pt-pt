---
title: Gestão do Azure Arc para servidores (pré-visualização) agente
description: Este artigo descreve as diferentes tarefas de gestão que irá executar normalmente durante o ciclo de vida do Azure Arc para servidores Connected Machine agent.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308961"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Gestão e manutenção do agente máquina conectada

Após a implementação inicial do Azure Arc para servidores (pré-visualização) Agente de máquina conectada para Windows ou Linux, poderá ser necessário reconfigurar o agente, atualizá-lo ou removê-lo do computador se tiver atingido a fase de reforma no seu ciclo de vida. Pode gerir facilmente estas tarefas de manutenção de rotina manualmente ou através da automatização, o que reduz tanto o erro operacional como as despesas.

## <a name="upgrading-agent"></a>Agente de upgrade

O agente Azure Connected Machine para Windows e Linux pode ser atualizado para a mais recente versão manual ou automaticamente, dependendo dos seus requisitos. A tabela seguinte descreve os métodos suportados para realizar a atualização do agente.

| Sistema operativo | Método de atualização |
|------------------|----------------|
| Windows | Manualmente<br> Windows Update |
| Ubuntu | [Apto](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zigpper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Agente do Windows

Para atualizar o agente numa máquina do Windows para a versão mais recente, o agente está disponível a partir do Microsoft Update e pode ser implementado utilizando o processo de gestão de atualizações de software existente. Também pode ser executado manualmente a partir do Comando Solicitativo, a partir de `AzureConnectedMachine.msi`um script ou outra solução de automatização, ou do assistente ui executando . 

> [!NOTE]
> * Para atualizar o agente, tem de ter permissões do *Administrador.*
> * Para atualizar manualmente, tem primeiro de descarregar e copiar o pacote Installer para uma pasta no servidor alvo ou a partir de uma pasta de rede partilhada. 

Se não estiver familiarizado com as opções de linha de comando para pacotes de instaladores do Windows, reveja as [opções padrão de linha de comando msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) e [opções de linha de comando Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Para atualizar usando o Assistente de Configuração

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Execute **AzureConnectedMachineAgent.msi** para iniciar o Assistente de Configuração.

O Assistente de Configuração descobre se existe uma versão anterior e, em seguida, executa automaticamente uma atualização do agente. Quando a atualização estiver concluída, o Assistente de Configuração fecha automaticamente.

#### <a name="to-upgrade-from-the-command-line"></a>Para atualizar a partir da linha de comando

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Para atualizar o agente silenciosamente e criar `C:\Support\Logs` um ficheiro de registo de configuração na pasta, execute o seguinte comando.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agente Linux

Para atualizar o agente numa máquina Linux para a versão mais recente, envolve dois comandos. Um comando para atualizar o índice de pacotelocal com a lista dos pacotes mais recentes disponíveis dos repositórios, e um comando para atualizar o pacote local. 

> [!NOTE]
> Para atualizar o agente, deve ter permissões de acesso *à raiz* ou com uma conta que tenha direitos elevados usando o Sudo.

#### <a name="upgrade-ubuntu"></a>Upgrade Ubuntu

1. Para atualizar o índice de pacotelocal com as últimas alterações feitas nos repositórios, executar o seguinte comando:

    ```bash
    apt update
    ```

2. Para atualizar o seu sistema, execute o seguinte comando:

    ```bash
    apt upgrade
    ```

As ações do comando [apto,](https://help.ubuntu.com/lts/serverguide/apt.html) tais como a instalação `/var/log/dpkg.log` e remoção de pacotes, são registadas no ficheiro de registo.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Upgrade Red Hat/CentOS/Amazon Linux

1. Para atualizar o índice de pacotelocal com as últimas alterações feitas nos repositórios, executar o seguinte comando:

    ```bash
    yum check-update
    ```

2. Para atualizar o seu sistema, execute o seguinte comando:

    ```bash
    yum update
    ```

As ações do comando [yum,](https://access.redhat.com/articles/yum-cheat-sheet) tais como a instalação `/var/log/yum.log` e remoção de pacotes, são registadas no ficheiro de registo. 

#### <a name="upgrade-suse-linux-enterprise"></a>Upgrade SUSE Linux Enterprise

1. Para atualizar o índice de pacotelocal com as últimas alterações feitas nos repositórios, executar o seguinte comando:

    ```bash
    zypper refresh
    ```

2. Para atualizar o seu sistema, execute o seguinte comando:

    ```bash
    zypper update
    ```

As ações do comando [zypper,](https://en.opensuse.org/Portal:Zypper) tais como a instalação `/var/log/zypper.log` e remoção de pacotes, são registadas no ficheiro de registo. 

## <a name="about-the-azcmagent-tool"></a>Sobre a ferramenta Azcmagent

A ferramenta Azcmagent (Azcmagent.exe) é utilizada para configurar o Arco Azure para servidores (pré-visualização) Agente da Máquina Conectada durante a instalação ou modificar a configuração inicial do agente após a instalação. Azcmagent.exe fornece parâmetros de linha de comando para personalizar o agente e ver o seu estado:

* **Ligar** - Ligar a máquina ao Arco Azure

* **Desligar** - Para desligar a máquina do Arco Azure

* **Reconectar** - Para voltar a ligar uma máquina desligada ao Arco Azure

* **Mostrar** - Ver o estado do agente e as suas propriedades de configuração (nome do Grupo de Recursos, ID de Subscrição, versão, etc.), que podem ajudar a resolver um problema com o agente.

* **-h ou --ajuda** - Mostra parâmetros de linha de comando disponíveis

    Por exemplo, para ver ajuda detalhada para `azcmagent reconnect -h`o parâmetro **de religação,** escreva . 

* **-v ou -verbosa** - Ativar a exploração madeireira verbosa

Pode executar um **Connect**, **Disconnect**, e **Reconectar** manualmente durante o login interativa, ou automatizar utilizando o mesmo diretor de serviço que usou para embarcar vários agentes ou com um [token](../../active-directory/develop/access-tokens.md)de acesso à plataforma de identidade microsoft . Se não usou um diretor de serviço para registar a máquina com o Azure Arc para servidores (pré-visualização), consulte o [seguinte artigo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) para criar um diretor de serviço.

### <a name="connect"></a>Ligar

Este parâmetro especifica um recurso no Gestor de Recursos Azure que representa a máquina é criado em Azure. O recurso está no grupo de subscrição e recursos especificado, e os dados `--location` sobre a máquina são armazenados na região de Azure especificado pela definição. O nome de recurso predefinido é o nome de anfitrião desta máquina, se não especificado.

Um certificado correspondente à identidade atribuída pelo sistema da máquina é então descarregado e armazenado localmente. Uma vez concluído este passo, o Serviço de Metadados de Máquinas Conectadas Azure e o Agente de Configuração do Hóspede começam a sincronizar com o Azure Arc para servidores (pré-visualização).

Para ligar utilizando um diretor de serviço, execute o seguinte comando:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Para ligar usando um sinal de acesso, executar o seguinte comando:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Para se conectar com as suas credenciais elevadas (interativas), execute o seguinte comando:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Desligar

Este parâmetro especifica que um recurso no Gestor de Recursos Azure que representa a máquina é eliminado em Azure. Não elimina o agente da máquina, isto deve ser feito como um passo separado. Depois de a máquina estar desligada, se pretender reregistrá-la com o `azcmagent connect` Azure Arc para servidores (pré-visualização), utilize para que seja criado um novo recurso para o mesmo em Azure.

Para desligar utilizando um diretor de serviço, execute o seguinte comando:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Para desligar utilizando um sinal de acesso, execute o seguinte comando:

`azcmagent disconnect --access-token <accessToken>`

Para se desligar com as suas credenciais elevadas (interativas), execute o seguinte comando:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Restabelecer ligação

Este parâmetro reconecta a máquina já registada ou conectada com o Azure Arc para servidores (pré-visualização). Isto pode ser necessário se a máquina tiver sido desligada, pelo menos 45 dias, para que o seu certificado expire. Este parâmetro utiliza as opções de autenticação fornecidas para recuperar novas credenciais correspondentes ao recurso Do Gestor de Recursos Azure que representa esta máquina.

Este comando requer privilégios mais elevados do que a função de embarque da [Máquina Conectada Azure.](overview.md#required-permissions)

Para voltar a ligar utilizando um diretor de serviço, execute o seguinte comando:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Para voltar a ligar-se utilizando um sinal de acesso, execute o seguinte comando:

`azcmagent reconnect --access-token <accessToken>`

Para voltar a ligar-se às suas credenciais elevadas (interativas), execute o seguinte comando:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Remova o agente

Execute um dos seguintes métodos para desinstalar o windows ou o agente Máquina Conectada Linux da máquina. A remoção do agente não desregistra a máquina com Arc para servidores (pré-visualização), este é um processo separado que executa quando já não precisa de gerir a máquina em Azure.

### <a name="windows-agent"></a>Agente do Windows

Ambos os métodos seguintes removem o agente, mas não removem a pasta *C:\Program Files\AzureConnectedMachineAgent* na máquina.

#### <a name="uninstall-from-control-panel"></a>Desinstalar do Painel de Controlo

1. Para desinstalar o agente windows da máquina, faça o seguinte:

    a. Inscreva-se no computador com uma conta que tenha permissões de administrador.  
    b. No **Painel de Controlo,** selecione **Programas e Funcionalidades.**  
    c. Em **Programas e Funcionalidades,** selecione **Agente de Máquina Seletiva,** selecione **Desinstalar**, e, em seguida, selecione **Sim**.  

    >[!NOTE]
    > Também pode executar o assistente de configuração do agente clicando duas vezes no pacote de instalação **AzureConnectedMachineAgent.msi.**

#### <a name="uninstall-from-the-command-line"></a>Desinstalar a partir da linha de comando

Para desinstalar o agente manualmente a partir do Pedido de Comando ou utilizar um método automatizado, como um script, pode utilizar o seguinte exemplo. Primeiro é necessário recuperar o código do produto, que é um GUID que é o principal identificador do pacote de aplicação, do sistema operativo. A desinstalação é realizada utilizando a linha de `msiexec /x {Product Code}`comando Msiexec.exe - .
    
1. Abra o Editor de Registo.

2. Sob a `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`chave de registo, procure e copie o código do produto GUID.

3. Em seguida, pode desinstalar o agente utilizando a Msiexec utilizando os seguintes exemplos:

   * Do tipo de linha de comando:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Pode executar os mesmos passos utilizando o PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Agente Linux

> [!NOTE]
> Para desinstalar o agente, deve ter permissões de acesso *à raiz* ou com uma conta que tenha direitos elevados usando o Sudo.

Para desinstalar o agente Linux, o comando a utilizar depende do sistema operativo Linux.

- Para Ubuntu, executar o seguinte comando:

    ```bash
    sudo apt purge azcmagent
    ```

- Para rhel, CentOS e Amazon Linux, executar o seguinte comando:

    ```bash
    sudo yum remove azcmagent
    ```

- Para as SLES, executar o seguinte comando:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Máquina de não registar

Se planeia parar de gerir a máquina com serviços de suporte no Azure, execute os seguintes passos para desregistar a máquina com o Arc para servidores (pré-visualização). Pode executar estes passos antes ou depois de ter removido o agente máquina conectada da máquina.

1. Abra o Arco Azure para servidores (pré-visualização) indo para o [portal Azure](https://aka.ms/hybridmachineportal).

2. Selecione a máquina na lista, selecione a elipse (**...**) e, em seguida, selecione **Delete**.
