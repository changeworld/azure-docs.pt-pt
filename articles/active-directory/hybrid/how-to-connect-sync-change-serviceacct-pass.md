---
title: 'Sincronização Azure AD Connect: Alterar a conta de serviço ADSync Microsoft Docs'
description: Este documento tópico descreve a chave de encriptação e como abandoná-la após a mudança da palavra-passe.
services: active-directory
keywords: Conta de serviço de sincronização Azure AD, senha
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b2a0d0b77b6db481b13785907a1359d2bbe3e9b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984509"
---
# <a name="changing-the-adsync-service-account-password"></a>Alterar a senha da conta de serviço ADSync
Se alterar a palavra-passe da conta de serviço ADSync, o Serviço de Sincronização não poderá começar corretamente até ter abandonado a chave de encriptação e reinicializado a palavra-passe da conta de serviço ADSync. 

O Azure AD Connect, como parte dos Serviços de Sincronização, utiliza uma chave de encriptação para armazenar as palavras-passe da conta DeConector AD DS e da conta de serviço ADSync.  Estas contas são encriptadas antes de serem armazenadas na base de dados. 

A chave de encriptação utilizada é protegida utilizando a Proteção de [Dados do Windows (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). O DPAPI protege a chave de encriptação utilizando a conta de **serviço ADSync**. 

Se precisar de alterar a palavra-passe da conta de serviço, pode utilizar os procedimentos no Abandono da chave de encriptação da conta de [serviço ADSync](#abandoning-the-adsync-service-account-encryption-key) para o conseguir.  Estes procedimentos também devem ser utilizados se precisar de abandonar a chave de encriptação por qualquer motivo.

## <a name="issues-that-arise-from-changing-the-password"></a>Questões que surgem da alteração da senha
Há duas coisas que precisam de ser feitas quando mudas a senha da conta de serviço.

Primeiro, tem de alterar a palavra-passe no Controlo de Serviços do Windows.  Até que este problema seja resolvido, verá os seguintes erros:


- Se tentar iniciar o Serviço de Sincronização no Windows Service Control Manager, recebe o erro " O**Windows não poderia iniciar o serviço Microsoft Azure AD Sync no Computador Local**". **Erro 1069: O serviço não começou devido a uma falha de logon.**"
- No Windows Event Viewer, o registo do evento do sistema contém um erro com o **Id 7038** do evento e a mensagem "**O serviço ADSync não foi capaz de iniciar sessão como com a senha configurada atualmente devido ao seguinte erro: O nome de utilizador ou palavra-passe está incorreto"**

Em segundo lugar, em condições específicas, se a palavra-passe for atualizada, o Serviço de Sincronização já não pode recuperar a chave de encriptação através do DPAPI. Sem a chave de encriptação, o Serviço de Sincronização não pode desencriptar as palavras-passe necessárias para sincronizar de/para a AD no local.
Verá erros como:

- No Controlo de Serviços do Windows, se tentar ligar o Serviço de Sincronização e não conseguir recuperar a chave de encriptação, falha com erro "<strong>O Windows não conseguiu iniciar o Microsoft Azure AD Sync no Computador Local. Para mais informações, reveja o registo do Evento do Sistema. Se este for um serviço não Microsoft, contacte o fornecedor de serviços e consulte o código de erro específico do serviço -21451857952</strong>."
- No Windows Event Viewer, o registo do evento da aplicação contém um erro com o **Id do evento 6028** e a mensagem de erro "A chave de encriptação do *servidor não pode ser acedida."*

Para garantir que não recebe estes erros, siga os procedimentos no Abandono da chave de [encriptação da conta de serviço ADSync](#abandoning-the-adsync-service-account-encryption-key) ao alterar a palavra-passe.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Abandonar a chave de encriptação da conta de serviço ADSync
>[!IMPORTANT]
>Os seguintes procedimentos aplicam-se apenas à construção Azure AD Connect 1.1.443.0 ou mais antiga.

Utilize os seguintes procedimentos para abandonar a chave de encriptação.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>O que fazer se precisar abandonar a chave de encriptação

Se precisar de abandonar a chave de encriptação, utilize os seguintes procedimentos para o conseguir.

1. [Parar o Serviço de Sincronização](#stop-the-synchronization-service)

1. [Abandonar a chave de encriptação existente](#abandon-the-existing-encryption-key)

2. [Fornecer a palavra-passe da conta AD DS Connector](#provide-the-password-of-the-ad-ds-connector-account)

3. [Reinicializar a palavra-passe da conta de serviço ADSync](#reinitialize-the-password-of-the-adsync-service-account)

4. [Inicie o Serviço de Sincronização](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Parar o Serviço de Sincronização
Primeiro pode parar o serviço no Gestor de Controlo de Serviços windows.  Certifique-se de que o serviço não está a funcionar quando tentar detê-lo.  Se for, espere até que complete e, em seguida, pare.


1. Vá ao Gestor de Controlo de Serviços Windows (START → Serviços).
2. Selecione **Microsoft Azure AD Sync** e clique em Stop.

#### <a name="abandon-the-existing-encryption-key"></a>Abandonar a chave de encriptação existente
Abandone a chave de encriptação existente para que possa ser criada uma nova chave de encriptação:

1. Inscreva-se no seu Servidor de Ligação AD Azure como administrador.

2. Inicie uma nova sessão powerShell.

3. Navegar para pasta:`'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Executar o comando:`./miiskmu.exe /a`

![Utilitário de chave de encriptação de sincronização de ligação a ad.a do Azure](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Fornecer a palavra-passe da conta AD DS Connector
Como as senhas existentes armazenadas dentro da base de dados já não podem ser desencriptadas, é necessário fornecer ao Serviço de Sincronização a palavra-passe da conta AD DS Connector. O Serviço de Sincronização encripta as palavras-passe utilizando a nova chave de encriptação:

1. Inicie o Gestor de Serviços de Sincronização (START → Serviço de Sincronização).
</br>![Gestor de Serviços de Sincronização](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Vá ao separador **Conectores.**
3. Selecione o **Conector AD** que corresponde ao seu D.A. no local. Se tiver mais de um conector AD, repita os seguintes passos para cada um deles.
4. Em **Ações,** selecione **Propriedades**.
5. No diálogo pop-up, selecione **Connect to Ative Directory Forest:**
6. Introduza a palavra-passe da conta AD DS na caixa de texto **password.** Se não sabe a sua senha, deve defini-la para um valor conhecido antes de realizar este passo.
7. Clique **em OK** para guardar a nova senha e fechar o diálogo pop-up.
![Utilitário de chave de encriptação de sincronização de ligação a ad.a do Azure](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Reinicializar a palavra-passe da conta de serviço ADSync
Não é possível fornecer diretamente a palavra-passe da conta de serviço Azure AD ao Serviço de Sincronização. Em vez disso, tem de utilizar a conta de serviço **Add-ADSyncAADServiceAccount** para reinicializar a conta de serviço Azure AD. O cmdlet repõe a palavra-passe da conta e coloca-a à disposição do Serviço de Sincronização:

1. Inicie uma nova sessão powerShell no servidor Azure AD Connect.
2. Executar cmdlet `Add-ADSyncAADServiceAccount`.
3. No diálogo pop-up, forneça as credenciais de administração da Azure AD Global para o seu inquilino Azure AD.
![Utilitário de chave de encriptação de sincronização de ligação a ad.a do Azure](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Se for bem sucedido, verá o pedido de comando PowerShell.

#### <a name="start-the-synchronization-service"></a>Inicie o Serviço de Sincronização
Agora que o Serviço de Sincronização tem acesso à chave de encriptação e a todas as palavras-passe de que necessita, pode reiniciar o serviço no Windows Service Control Manager:


1. Vá ao Gestor de Controlo de Serviços Windows (START → Serviços).
2. Selecione **Microsoft Azure AD Sync** e clique em Reiniciar.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
