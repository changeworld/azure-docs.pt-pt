---
title: incluir ficheiro
description: incluir ficheiro
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 09/12/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: e7a6f7b4ba4219483cd3eb8f4600bc94213df131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973424"
---
Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Criar uma Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

Siga estes passos para criar uma Base de Dados do Azure para o servidor PostgreSQL:
1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Selecione **Bases de Dados**, na página **Nova**, e selecione **Base de Dados do Azure para o PostgreSQL**, na página **Bases de Dados**.
3. Para a opção de implementação, clique no botão **Criar** sob o grupo de **servidor hiperescala (Citus).**
4. Preencha o formulário com os detalhes do novo servidor com as seguintes informações:
   - Grupo de recursos: clique no novo link **Criar** abaixo da caixa de texto para este campo. Introduza um nome como **myresourcegroup**.
   - Nome do grupo do servidor: introduza um nome único para o novo grupo de servidores, que também será utilizado para um subdomínio do servidor.
   - Nome de utilizador administrador: atualmente necessário para ser o **citus**de valor, e não pode ser alterado.
   - Palavra-passe: deve ter pelo menos oito caracteres de comprimento e conter caracteres de três das seguintes categorias – letras maiúsculas inglesas, letras minúsculas inglesas, números (0-9) e caracteres não alfanuméricos (!, $, #, %, e assim por diante.)
   - Localização: utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

   > [!IMPORTANT]
   > A palavra-passe de administrador do servidor que especifica aqui é necessária para iniciar sessão no servidor e nas suas bases de dados. Lembre-se ou grave estas informações para utilização posterior.

5. Clique no **grupo de servidor configure**. Deixe as definições nessa secção inalteradas e clique em **Guardar**.
6. Clique em **Seguinte : A rede >** na parte inferior do ecrã.

7. No separador **Networking,** clique no botão de rádio **de ponta pública.**
   ![Ponto final público selecionado](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Clique no link + Adicione o **endereço IP do cliente atual**.
   ![IP cliente adicionado](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > O servidor PostgreSQL do Azure comunica através da porta 5432. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Em caso afirmativo, não pode ligar-se ao seu cluster De hiperescala (Citus), a menos que o seu departamento de TI abra a porta 5432.
   >

9. Clique em **Rever + criar** e, em seguida, **criar** para fornecer o servidor. O aprovisionamento demora alguns minutos.
10. A página redirecionará para monitorizar a implementação. Quando as alterações de estado ao vivo da **sua implementação estiverem em curso** para a sua **implementação,** clique no item do menu **Outputs** à esquerda da página.
11. A página de saídas conterá um nome de anfitrião coordenador com um botão ao lado para copiar o valor para a área de reprodução. Grave esta informação para posterior utilização.

## <a name="connect-to-the-database-using-psql"></a>Ligar à base de dados utilizando o psql

Quando cria a sua Base de Dados Azure para servidor PostgreSQL, é criada uma base de dados padrão chamada **citus.** Para se ligar ao seu servidor de base de dados, precisa de uma cadeia de ligação e da senha de administração.

1. Obtenha a corda de ligação. Na página do grupo do servidor clique no item do menu de **cordas de Ligação.** (Está em **Definições**.) Encontre a corda marcada **psql.** Será da forma:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Copie o fio. Terá de substituir "{your\_password}" pela palavra-passe administrativa que escolheu anteriormente. O sistema não armazena a sua palavra-passe de texto simples e por isso não pode exibi-la para si na cadeia de ligação.

2. Abra uma janela terminal no seu computador local.

3. No momento, ligue-se à sua Base de Dados Azure para o servidor PostgreSQL com o utilitário [psql.](https://www.postgresql.org/docs/current/app-psql.html) Passe a sua cadeia de ligação em aspas, certificando-se de que contém a sua palavra-passe:
   ```bash
   psql "host=..."
   ```

   Por exemplo, o seguinte comando liga-se ao nó coordenador do grupo de **servidormydemoserver:**

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
