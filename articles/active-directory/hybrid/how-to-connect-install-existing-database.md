---
title: Instale o Azure AD Connect utilizando uma base de dados ADSync existente[ Microsoft Docs
description: Este tópico descreve como usar uma base de dados ADSync existente.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dc6993586063c9c99a287c51d799b44f921768d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245202"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Instalar o Azure AD Connect com uma base de dados ADSync existente
O Azure AD Connect requer uma base de dados do SQL Server para armazenar dados. Pode utilizar o SQL Server 2012 Express LocalDB instalado com o Azure AD Connect ou utilizar a sua própria versão completa do SQL. Anteriormente, quando instalou o Azure AD Connect, foi sempre criada uma nova base de dados chamada ADSync. Com o Azure AD Connect na versão 1.1.613.0 (ou depois), tem a opção de instalar o Azure AD Connect apontando-o para uma base de dados ADSync existente.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Benefícios da utilização de uma base de dados ADSync existente
Apontando para uma base de dados ADSync existente:

- Com exceção das informações sobre credenciais, a configuração de sincronização armazenada na base de dados ADSync (incluindo regras de sincronização personalizadas, conectores, filtragem e configuração de funcionalidades opcionais) é automaticamente recuperada e utilizada durante a instalação . As credenciais utilizadas pelo Azure AD Connect para sincronizar alterações com a AD e a AD Azure no local estão encriptadas e só podem ser acedidas pelo anterior servidor Azure AD Connect.
- Todos os dados de identidade (associados a espaços de conector e metaverso) e cookies de sincronização armazenados na base de dados ADSync também são recuperados. O servidor Azure AD Connect recentemente instalado pode continuar a sincronizar de onde o servidor Azure AD Connect anterior deixou de ser desligado, em vez de ter a necessidade de realizar uma sincronização completa.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Cenários em que a utilização de uma base de dados ADSync existente é benéfica
Estes benefícios são úteis nos seguintes cenários:


- Tem uma implantação Azure AD Connect existente. O servidor Azure AD Connect existente já não está a funcionar, mas o servidor SQL que contém a base de dados ADSync ainda está a funcionar. Pode instalar um novo servidor Azure AD Connect e apontá-lo para a base de dados ADSync existente. 
- Tem uma implantação Azure AD Connect existente. O seu servidor SQL que contém a base de dados ADSync já não funciona. No entanto, tem um reforço recente da base de dados. Pode restaurar a base de dados ADSync para um novo servidor SQL primeiro. Depois disso, pode instalar um novo servidor Azure AD Connect e apontá-lo para a base de dados ADSync restaurada.
- Tem uma implementação azure AD Connect existente que está a usar o LocalDB. Devido ao limite de 10 GB imposto pelo LocalDB, você gostaria de migrar para o SQL completo. Pode fazer o backup da base de dados ADSync a partir do LocalDB e restaurá-la para um servidor SQL. Depois disso, pode reinstalar um novo servidor Azure AD Connect e apontá-lo para a base de dados ADSync restaurada.
- Está a tentar configurar um servidor de encenação e quer certificar-se de que a sua configuração corresponde à do servidor ativo atual. Pode fazer o back up da base de dados ADSync e restaurá-la para outro servidor SQL. Depois disso, pode reinstalar um novo servidor Azure AD Connect e apontá-lo para a base de dados ADSync restaurada.

## <a name="prerequisite-information"></a>Informações de pré-requisitos

Notas importantes para tomar nota antes de prosseguir:

- Certifique-se de rever os pré-requisitos para instalar o Azure AD Connect no Hardware e pré-requisitos, e contas e permissões necessárias para a instalação do Azure AD Connect. As permissões necessárias para instalar o Azure AD Connect utilizando o modo "use base de dados existente" são as mesmas que a instalação "personalizada".
- A implementação do Azure AD Connect contra uma base de dados ADSync existente só é suportada com SQL completo. Não é suportado com o SQL Express LocalDB. Se tiver uma base de dados ADSync existente no LocalDB que deseja utilizar, tem primeiro de fazer backup na base de dados ADSync (LocalDB) e restaurá-la ao sQL completo. Depois disso, pode implantar o Azure AD Connect contra a base de dados restaurada utilizando este método.
- A versão do Azure AD Connect utilizado para a instalação deve satisfazer os seguintes critérios:
    - 1.1.613.0 ou acima, e
    - O mesmo ou superior à versão do Azure AD Connect usado pela última vez com a base de dados ADSync. Se a versão Azure AD Connect utilizada para a instalação for superior à versão utilizada pela última vez com a base de dados ADSync, poderá ser necessário um sincronização completo.  A sincronização completa é necessária se houver alterações de regras de esquema ou sincronização entre as duas versões. 
- A base de dados ADSync utilizada deve conter um estado de sincronização relativamente recente. A última atividade de sincronização com a base de dados ADSync existente deve ser nas últimas três semanas.
- Ao instalar o Azure AD Connect utilizando o método "use a base de dados existente", o método de início de sessão configurado no servidor Azure AD Connect anterior não é preservado. Além disso, não é possível configurar o método de entrada durante a instalação. Só é possível configurar o método de entrada após a instalação estar concluída.
- Não é possível que vários servidores Azure AD Connect partilhem a mesma base de dados ADSync. O método "use a base de dados existente" permite-lhe reutilizar uma base de dados ADSync existente com um novo servidor Azure AD Connect. Não apoia a partilha.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Passos para instalar o Azure AD Connect com o modo "use base de dados"
1.  Baixe o instalador Azure AD Connect (AzureADConnect.MSI) para o servidor Windows. Clique duas vezes no instalador Azure AD Connect para começar a instalar o Azure AD Connect.
2.  Assim que a instalação do MSI estiver concluída, o assistente do Azure AD Connect é iniciado com a configuração do modo Express. Feche o ecrã ao clicar no ícone Sair.
![Bem-vindo](./media/how-to-connect-install-existing-database/db1.png)
3.  Inicie uma nova linha de comandos ou a sessão do PowerShell. Navegue para a pasta "C:\Program Files\Microsoft Azure Ative Directory Connect". Execute o comando .\AzureADConnect.exe /useexistingdatabase para iniciar o assistente do Azure AD Connect no modo de configuração "Utilizar base de dados existente".

> [!NOTE]
> Utilize o interruptor **/UseExistingDatabase** apenas quando a base de dados já contiver dados de uma instalação anterior do Azure AD Connect. Por exemplo, quando está a passar de uma base de dados local para uma base de dados completa do SQL Server ou quando o servidor Azure AD Connect foi reconstruído e restaurou uma cópia de segurança SQL da base de dados ADSync a partir de uma instalação anterior do Azure AD Connect. Se a base de dados estiver vazia, isto é, não contém quaisquer dados de uma instalação anterior do Azure AD Connect, ignore este passo.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. É recebido com o ecrã de Boas-vindas ao Azure AD Connect. Depois de aceitar os termos de licenciamento e o aviso de privacidade, clique em **Continuar**.
   ![Bem-vindo](./media/how-to-connect-install-existing-database/db3.png)
1. No ecrã **Instalar componentes necessários**, a opção **Utilizar um servidor existente do SQL Server** está ativada. Especifique o nome do servidor SQL que está a alojar a base de dados ADSync. Se a instância do motor SQL utilizada para alojar a base de dados ADSync não for a instância predefinida no servidor SQL, tem de especificar o nome da instância do motor SQL. Além disso, se a navegação do SQL Server não estiver ativada, também tem de especificar o número de porta da instância do motor SQL. Por exemplo:         
   ![Bem-vindo](./media/how-to-connect-install-existing-database/db4.png)           

1. No ecrã **Ligar ao Azure AD**, tem de apresentar as credenciais de um administrador global do seu diretório do Azure AD. A recomendação é utilizar uma conta no domínio predefinido onmicrosoft.com. Esta conta é utilizada apenas para criar uma conta de serviço no Azure AD e deixa de ser utilizada uma vez concluído o assistente.
   ![Ligar](./media/how-to-connect-install-existing-database/db5.png)
 
1. No ecrã **Ligar aos diretórios**, a floresta do AD existente configurada para a sincronização de diretórios está listada com um ícone vermelho junto à mesma. Para sincronizar alterações de uma floresta do AD local, precisa de uma conta do AD DS. O assistente do Azure AD Connect não consegue obter as credenciais da conta do AD DS armazenadas na base de dados do ADSync porque as credenciais estão encriptadas e só podem ser desencriptadas pelo servidor do Azure AD Connect anterior. Clique em **Alterar Credenciais** para especificar a conta do AD DS para a floresta do AD.
   ![Diretórios](./media/how-to-connect-install-existing-database/db6.png)
 
 
1. Na caixa de diálogo pop-up, pode (i) apresentar uma credencial de Administrador Enterprise e permitir ao Azure AD Connect criar a conta do AD DS para si ou (ii) criar a conta do AD DS manualmente e dar a respetiva credencial ao Azure AD Connect. Assim que tiver selecionado uma opção e apresentado as credenciais exigidas, clique em **OK** para fechar a caixa de diálogo pop-up.
   ![Bem-vindo](./media/how-to-connect-install-existing-database/db7.png)
 
 
1. Depois de apresentar as credenciais, o ícone com uma cruz vermelha é substituído por um ícone com um visto verde. Clique em **Seguinte**.
   ![Bem-vindo](./media/how-to-connect-install-existing-database/db8.png)
 
 
1. No ecrã **Preparado para configurar**, clique em **Instalar**.
   ![Bem-vindo](./media/how-to-connect-install-existing-database/db9.png)
 
 
1. Assim que a instalação estiver concluída, o servidor do Azure AD Connect é ativado automaticamente para o Modo de Teste. É recomendado que reveja se existem alterações inesperadas na configuração do servidor e nas exportações pendentes antes de desativar o Modo de Teste. 

## <a name="post-installation-tasks"></a>Tarefas de pós-instalação
Ao restaurar uma cópia de segurança criada por uma versão do Azure AD Connect antes do 1.2.65.0, o servidor de paragem selecionará automaticamente um método de entrada de **'Não Configurar**' . Enquanto as preferências de sincronização de hash de palavra-passe e de recompra de palavras-passe serão restauradas, deve alterar posteriormente o método de entrada para corresponder às outras políticas em vigor para o seu servidor de sincronização ativa.  A não conclusão destas etapas pode impedir que os utilizadores entrem em sessão caso este servidor se torne ativo.  

Utilize a tabela abaixo para verificar quaisquer passos adicionais necessários.

|Funcionalidade|Passos|
|-----|-----|
|Sincronização de Hash password| as definições de sincronização de hash de palavra-passe e de reversão de palavra-passe estão totalmente restauradas para versões do Azure AD Connect a partir de 1.2.65.0.  Se restaurar utilizando uma versão mais antiga do Azure AD Connect, reveja as definições de opção de sincronização para estas funcionalidades para garantir que correspondem ao servidor de sincronização ativa.  Não devem ser necessários outros passos de configuração.|
|Federação com o AD FS|As autenticações Azure continuarão a utilizar a política AD FS configurada para o seu servidor de sincronização ativa.  Se utilizar o Azure AD Connect para gerir a sua quinta AD FS, pode alterar opcionalmente o método de entrada para a federação AD FS em preparação para que o seu servidor de espera se torne a instância de sincronização ativa.   Se as opções do dispositivo estiverem ativadas no servidor de sincronização ativa, configure essas opções neste servidor executando a tarefa "Configurar as opções do dispositivo".|
|Autenticação pass-through e inscrição individual do ambiente de trabalho|Atualize o sinal no método para combinar com a configuração do seu servidor de sincronização ativo.  Se isto não for seguido antes de promover o servidor para a autenticação primária, a autenticação pass-through juntamente com o Single Sign In será desativada e o seu inquilino poderá ficar bloqueado se não tiver sincronização de hash de palavra-passe como sinal de back-up na opção. Note também que quando ativar a autenticação pass-through no modo de encenação, será instalado um novo agente de autenticação, registado e executado como um agente de alta disponibilidade que aceitará assinar nos pedidos.|
|Federação com o PingFederate|As autenticações Azure continuarão a utilizar a política PingFederate configurada para o seu servidor de sincronização ativa.  Pode alterar opcionalmente o método de inatividade para PingFederate em preparação para que o seu servidor de espera se torne a instância de sincronização ativa.  Este passo pode ser adiado até que precise federar domínios adicionais com PingFederate.|

## <a name="next-steps"></a>Passos seguintes

- Agora que já tem o Azure AD Connect instalado, pode [verificar a instalação e atribuir licenças](how-to-connect-post-installation.md).
- Saiba mais acerca destas funcionalidades que foram ativadas com a instalação: [Impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](how-to-connect-health-sync.md).
- Saiba mais acerca destes tópicos comuns: [agendador e como acionar a sincronização](how-to-connect-sync-feature-scheduler.md).
- Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
