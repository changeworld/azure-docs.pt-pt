---
title: Gerir serviços de análise azure [ Serviços de Análise Azure] Microsoft Docs
description: Este artigo descreve as ferramentas usadas para gerir tarefas de administração e gestão para um servidor de Serviços de Análise Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28d7b2955c84833841760e441cd2919181e22bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572801"
---
# <a name="manage-analysis-services"></a>Gerir o Analysis Services
Uma vez criado um servidor de Serviços de Análise em Azure, pode haver algumas tarefas de administração e gestão que você precisa realizar imediatamente ou em algum momento na estrada. Por exemplo, executar o processamento para os dados de atualização, controlar quem pode aceder aos modelos no seu servidor ou monitorizar a saúde do seu servidor. Algumas tarefas de gestão só podem ser executadas no portal Azure, outras no SQL Server Management Studio (SSMS), e algumas tarefas podem ser realizadas em qualquer um deles.

## <a name="azure-portal"></a>Portal do Azure
[O portal Azure](https://portal.azure.com/) é onde pode criar e eliminar servidores, monitorizar os recursos do servidor, alterar o tamanho e gerir quem tem acesso aos seus servidores.  Se tiver alguns problemas, também pode apresentar um pedido de apoio.

![Obter o nome do servidor no Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Ligar-se ao seu servidor em Azure é como ligar-se a uma instância de servidor na sua própria organização. A partir de SSMS, você pode executar muitas das mesmas tarefas, tais como processar dados ou criar um script de processamento, gerir funções e usar powerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Transferir e instalar o SSMS
Para obter todas as funcionalidades mais recentes e a experiência mais suave ao ligar-se ao seu servidor De serviços de análise Azure, certifique-se de que está a usar a versão mais recente do SSMS. 

Baixe o [Estúdio de Gestão de Servidores SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Para se conectar com SSMS
 Ao utilizar o SSMS, antes de se ligar ao seu servidor pela primeira vez, certifique-se de que o seu nome de utilizador está incluído no grupo de Administração de Serviços de Análise. Para saber mais, consulte os [administradores do Servidor e os utilizadores](#server-administrators-and-database-users) da base de dados mais tarde neste artigo.

1. Antes de se ligar, tem de obter o nome do servidor. No **portal do Azure** > servidor > **Descrição geral** > **Nome do servidor**, copie o nome do servidor.
   
    ![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. No SSMS > **Object Explorer**, clique em **Ligar** > **Analysis Services**.
3. Na caixa de diálogo **Connect to Server,** cola no nome do servidor, depois na **Autenticação,** escolha um dos seguintes tipos de autenticação:   
    > [!NOTE]
    > Tipo de autenticação, **Diretório Ativo - Universal com suporte mFA,** é recomendado.

    > [!NOTE]
    > Se iniciar sessão com uma Conta Microsoft, ID ao vivo, Yahoo, Gmail, etc., deixe o campo de palavra-passe em branco. É solicitado a uma senha depois de clicar em Connect.

    **Autenticação do Windows** para utilizar o seu domínio Windows\username e credenciais de palavra-passe.

    **Autenticação de palavra-passe** de diretório ativo para usar uma conta organizacional. Por exemplo, ao ligar-se a partir de um computador não-domínio.

    **Diretório Ativo - Suporte Universal com MFA** para utilizar [autenticação não interativa ou multifactor](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Ligar no SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Administradores de servidores e utilizadores de bases de dados
Nos Serviços de Análise do Azure, existem dois tipos de utilizadores, administradores de servidores e utilizadores de bases de dados. Ambos os tipos de utilizadores devem estar no seu Diretório Ativo Azure e devem ser especificados por endereço de e-mail organizacional ou UPN. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador).


## <a name="troubleshooting-connection-problems"></a>Problemas de ligação de resolução de problemas
Ao ligar-se utilizando sSMS, se tiver problemas, poderá ter de limpar a cache de login. Nada está em cache para disco. Para limpar a cache, feche e reinicie o processo de ligação. 

## <a name="next-steps"></a>Passos seguintes
Se ainda não implementou um modelo tabular para o seu novo servidor, agora é uma boa altura. Para obter mais informações, consulte [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Implementar no Azure Analysis Services).

Se implementou um modelo no seu servidor, está pronto para se ligar ao mesmo usando um cliente ou navegador. Para saber mais, consulte [Obter dados do servidor azure Analysis Services](analysis-services-connect.md).

