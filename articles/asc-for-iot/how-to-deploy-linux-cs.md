---
title: Instale & implementar o agente Linux C#
description: Aprenda a instalar o Centro de Segurança Azure para agente IoT em Linux de 32 bits e 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: 40c6ea91fd84a0f088ed770cd7c4c3ea7b8b1c91
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311134"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Implementar um agente de segurança baseado em C# do Centro de Segurança do Azure para IoT para o Linux

Este guia explica como instalar e implantar o Centro de Segurança Azure para o agente de segurança baseado em IoT C#no Linux.

Neste guia, ficará a saber como:

> [!div class="checklist"]
> * Instalar
> * Verificar a implementação
> * Desinstalar o agente
> * Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e sabores de agente, consulte [Escolha o agente de segurança certo](how-to-deploy-agent.md).

1. Para implantar o agente de segurança, são necessários direitos de administração locais na máquina que pretende instalar.

1. [Crie um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação

Para implantar o agente de segurança, utilize os seguintes passos:

1. Descarregue a versão mais recente para a sua máquina a partir do [GitHub.](https://aka.ms/iot-security-github-cs)

1. Extrair o conteúdo da embalagem e navegar para a pasta _/Instalar._

1. Adicione permissões de execução ao **script InstallSecurityAgent** executando`chmod +x InstallSecurityAgent.sh`

1. Em seguida, executar o seguinte comando com **privilégios de raiz:**

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   para mais informações sobre parâmetros de autenticação, consulte [Como configurar a autenticação](concept-security-agent-authentication-methods.md).

Este script executa as seguintes ações:

- Instala pré-requisitos.

- Adiciona um utilizador de serviço (com sinal interativo em desativado).

- Instala o agente como **Daemon** - assume que o dispositivo utiliza **o sistema para** modelo de implementação clássico.

- Configures **sudoers** para permitir que o agente faça certas tarefas como raiz.

- Configura o agente com os parâmetros de autenticação fornecidos.

Para obter ajuda adicional, execute o script com o parâmetro de ajuda:`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o `./InstallSecurityAgent.sh -u`script com o parâmetro -u: .

> [!NOTE]
> A desinstalação não remove quaisquer pré-requisitos em falta que tenham sido instalados durante a instalação.

## <a name="troubleshooting"></a>Resolução de problemas

1. Verifique o estado de implantação executando:

    `systemctl status ASCIoTAgent.service`

1. Ativar a exploração madeireira.
   Se o agente não começar, ligue a exploração de madeira para obter mais informações.

   Ligue a exploração madeireira por:

   1. Abra o ficheiro de configuração para edição em qualquer editor do Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Editar os seguintes valores:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       O valor **do logFilePath** é configurável.

       > [!NOTE]
       > Recomendamos que desligue **o** registo após a resolução de problemas. Deixar o registo **em** aumentos do tamanho do ficheiro de registo e utilização de dados.

   1. Reiniciar o agente executando:

       `systemctl restart ASCIoTAgent.service`

   1. Consulte o ficheiro de registo para obter mais informações sobre a falha.

       A localização do ficheiro de registo é:`/var/ASCIoTAgent/IotAgentLog.log`

       Altere o caminho de localização do ficheiro de acordo com o nome que escolheu para o **logFilePath** no passo 2.

## <a name="next-steps"></a>Passos seguintes

- Leia o Centro de Segurança Azure para a [visão geral](overview.md) do serviço IoT
- Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
- Ativar o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [FAQ](resources-frequently-asked-questions.md)
- Compreender [alertas](concept-security-alerts.md)
