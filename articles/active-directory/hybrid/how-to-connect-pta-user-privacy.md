---
title: Privacidade do utilizador e autenticação de passe de diretório ativo Azure / Microsoft Docs
description: Este artigo trata da autenticação pass-through do Azure Ative Directory (Azure AD).
services: active-directory
keywords: Autenticação de passagem de ligação azure AD, RGPD, componentes necessários para Azure AD, SSO, Sign-on Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0af1c42e7e2c163e7f9e7407d0236e35bfacf8e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931012"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Privacidade do Utilizador e Autenticação Pass-through do Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Descrição geral

A Autenticação pass-through Azure AD cria o seguinte tipo de registo, que pode conter Dados Pessoais:

- Ficheiros de registo de rastreio de ligação adligações Azur.
- Autenticação Agente rastrear ficheiros de registo.
- Ficheiros de registo do Windows Event.

Melhorar a privacidade do utilizador para a autenticação pass-through de duas formas:

1.  Mediante solicitação, extrai os dados de uma pessoa e remova os dados dessa pessoa das instalações.
2.  Certifique-se de que nenhum dado é retido para além de 48 horas.

Recomendamos vivamente a segunda opção, uma vez que é mais fácil implementar e manter. Seguem-se as instruções para cada tipo de registo:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminar ficheiros de registo de rastreio de ligação ad ad azure

Verifique o conteúdo da pasta **%ProgramData\AADConnect** e elimine o conteúdo do registo de rastreios **(ficheiros de\*** rastreio- .log) desta pasta no prazo de 48 horas após a instalação ou atualização do Azure AD Connect ou modificando a configuração de autenticação pass-through, uma vez que esta ação pode criar dados cobertos pelo RGPD.

>[!IMPORTANT]
>Não elimine o ficheiro **PersistedState.xml** nesta pasta, uma vez que este ficheiro é utilizado para manter o estado da instalação anterior do Azure AD Connect e é utilizado quando uma instalação de atualização é feita. Este ficheiro nunca conterá quaisquer dados sobre uma pessoa e nunca deve ser eliminado.

Pode rever e eliminar estes ficheiros de registo de rastreio utilizando o Windows Explorer ou pode utilizar o seguinte script PowerShell para executar as ações necessárias:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Guarde o guião num ficheiro com ". Extensão PS1". Executa este guião conforme necessário.

Para saber mais sobre os requisitos relacionados do RGPD Azure AD Connect, consulte [este artigo](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Eliminar registos de eventos do Agente de Autenticação

Este produto também pode criar registos de **eventos do Windows.** Para saber mais, por favor leia as [condições](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx)de publicação.

Para visualizar registos relacionados com o Agente de Autenticação Pass-through, abra a aplicação do Espectador de **Eventos** no servidor e verifique em registos de **aplicação e serviço\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Eliminar ficheiros de registo do Agente de Autenticação

Deve verificar regularmente o conteúdo do **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace** e eliminar o conteúdo desta pasta a cada 48 horas. 

>[!IMPORTANT]
>Se o serviço do Agente de Autenticação estiver em funcionamento, não poderá eliminar o ficheiro de registo atual na pasta. Pare o serviço antes de tentar de novo. Para evitar falhas de inscrição no utilizador, já deve ter configurado a Autenticação Pass-through para [uma elevada disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Pode rever e eliminar estes ficheiros utilizando o Windows Explorer ou pode utilizar o seguinte script para executar as ações necessárias:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Para agendar este guião a cada 48 horas siga estes passos:

1.  Guarde o guião num ficheiro com ". Extensão PS1".
2.  Abra **o Painel** de Controlo e clique no Sistema e **Segurança.**
3.  Sob a rubrica **Ferramentas Administrativas,** clique em "**Tarefas**de Agenda ".
4.  No **Agendador**de Tarefas, clique à direita em "**Task Schedule Library**" e clique em " Criar tarefa**básica...**".
5.  Introduza o nome para a nova tarefa e clique em **Next**.
6.  Selecione "**Daily**" para o Gatilho de **Tarefa** e clique **em Seguinte**.
7.  Detete a recorrência para dois dias e clique **em Seguinte**.
8.  Selecione "**Iniciar um programa**" como ação e clicar em **Seguinte**.
9.  Digite "**PowerShell**" na caixa para o Programa/script, e na caixa com o rótulo "**Adicione argumentos (opcional)**", insira o caminho completo para o script que criou anteriormente, em seguida, clique em **Seguinte**.
10. O ecrã seguinte mostra um resumo da tarefa que está prestes a criar. Verifique os valores e clique **em Terminar** para criar a tarefa:
 
### <a name="note-about-domain-controller-logs"></a>Nota sobre registos de controladores de domínio

Se o registo de auditoria estiver ativado, este produto poderá gerar registos de segurança para os seus Controladores de Domínio. Para saber mais sobre a configuração das políticas de auditoria, leia este [artigo.](https://technet.microsoft.com/library/dd277403.aspx)

## <a name="next-steps"></a>Passos seguintes
* [Reveja a política de privacidade da Microsoft no Trust Center](https://www.microsoft.com/trustcenter)
* [**Troubleshoot**](tshoot-connect-pass-through-authentication.md) - Aprenda a resolver problemas comuns com a funcionalidade.
