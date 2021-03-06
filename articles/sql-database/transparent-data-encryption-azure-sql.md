---
title: Encriptação de Dados Transparente
description: Uma visão geral da encriptação transparente de dados para a Base de Dados SQL e Synapse SQL em Azure Synapse Analytics. O documento cobre os seus benefícios e as opções de configuração, que inclui encriptação transparente de dados gerida pelo serviço e Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 04/10/2020
ms.openlocfilehash: 87abdb37ff7773b0205a2ce3ee21689a10c459d7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113538"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Encriptação transparente de dados para Base de Dados SQL e Azure Synapse

[A encriptação transparente de dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) ajuda a proteger a Base de Dados Azure SQL, a Instância Gerida Azure SQL e a Synapse SQL no Azure Synapse Analytics contra a ameaça de atividade offline maliciosa, encriptando dados em repouso. Realiza a encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações inativos e não carece de alterações à aplicação. Por predefinição, o TDE está ativado para todas as bases de dados Azure SQL recentemente implantadas e precisa de ser ativado manualmente para bases de dados mais antigas da Base de Dados Azure SQL, Azure SQL Managed, ou Azure Synapse.

O TDE executa encriptação e desencriptação de I/O em tempo real e desencriptação dos dados ao nível da página. Cada página é desencriptada quando é lida na memória e encriptada antes de ser escrita no disco. O TDE encripta o armazenamento de uma base de dados inteira utilizando uma chave simétrica chamada Chave de Encriptação de Base de Dados (DEK). No arranque da base de dados, o DEK encriptado é desencriptado e depois utilizado para desencriptação e reencriptação dos ficheiros da base de dados no processo do Motor de Base de Dados do Servidor SQL. O DEK está protegido pelo protetor TDE. O protetor TDE é um certificado gerido pelo serviço (encriptação transparente de dados gerida pelo serviço) ou uma chave assimétrica armazenada no [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (encriptação transparente de dados gerida pelo cliente). 

Para a Base de Dados Azure SQL e para o Azure Synapse, o protetor TDE é definido ao nível lógico do servidor SQL e é herdado por todas as bases de dados associadas a esse servidor. Para o Azure SQL Managed Instance (recurso BYOK na pré-visualização), o protetor TDE é definido ao nível da instância e é herdado por todas as bases de dados encriptadas nessa instância. O *servidor* de termo refere-se tanto ao servidor como ao longo deste documento, a menos que indicado de forma diferente.

> [!IMPORTANT]
> Todas as bases de dados Azure SQL recém-criadas são encriptadas por padrão utilizando encriptação transparente de dados gerida pelo serviço. As bases de dados SQL existentes criadas antes de maio de 2017 e as bases de dados SQL criadas através de restauração, geo-replicação e cópia de base de dados não são encriptadas por padrão. As bases de dados existentes de Instância Gerida criadas antes de fevereiro de 2019 não são encriptadas por padrão. Bases de dados geridas por exemplo criadas através do estado de encriptação herdado restaurar a partir da fonte.

> [!NOTE]
> O TDE não pode ser utilizado para encriptar a base de dados lógica **principal** na Base de Dados SQL.  A base de dados **principal** contém objetos necessários para realizar as operações de TDE nas bases de dados dos utilizadores.


## <a name="service-managed-transparent-data-encryption"></a>Encriptação transparente de dados gerida pelo serviço

Em Azure, a definição padrão para TDE é que o DEK está protegido por um certificado de servidor incorporado. O certificado de servidor incorporado é único para cada servidor e o algoritmo de encriptação utilizado é AES 256. Se uma base de dados estiver numa relação de geo-replicação, tanto as bases de dados primárias como geosecundárias estão protegidas pela chave do servidor principal da base de dados. Se duas bases de dados estiverem ligadas ao mesmo servidor, também partilham o mesmo certificado incorporado.  A Microsoft roda automaticamente estes certificados em conformidade com a política de segurança interna e a chave-raiz está protegida por uma loja secreta interna da Microsoft.  Os clientes podem verificar o cumprimento da Base de Dados SQL das políticas de segurança interna em relatórios de auditoria independentes de terceiros disponíveis no [Microsoft Trust Center](https://servicetrust.microsoft.com/).

A Microsoft também se move perfeitamente e gere as chaves conforme necessário para a geo-replicação e restauros.


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Encriptação de dados transparente gerida pelo cliente - Traga a sua própria chave

O TDE gerido pelo cliente também é referido como suporte Bring Your Own Key (BYOK) para TDE. Neste cenário, o Protetor TDE que encripta o DEK é uma chave assimétrica gerida pelo cliente, que é armazenada num cofre de chaves Azure (sistema de gestão de chaves externas baseado na nuvem da Azure) e nunca sai do cofre chave. O Protetor TDE pode ser gerado pelo cofre chave [ou transferido para o cofre chave](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) a partir de um dispositivo de módulo de segurança de hardware (HSM) no local. A Base de Dados SQL precisa de ser concedida permissões ao cofre chave do cliente para desencriptar e encriptar o DEK. Se as permissões do servidor lógico SQL para o cofre chave forem revogadas, uma base de dados será inacessível, e todos os dados são encriptados

Com a integração do TDE com o Cofre de Chaves Azure, os utilizadores podem controlar tarefas de gestão chave, incluindo rotações de chaves, permissões de cofre chave, backups chave e permitir auditoria/reporte em todos os protetores TDE utilizando a funcionalidade Azure Key Vault. A Key Vault fornece uma gestão central de chaves, alavanca shSMs monitorizados de forma rigorosa, e permite a separação de deveres entre a gestão de chaves e dados para ajudar a cumprir as políticas de segurança.
Para saber mais sobre o BYOK para a Base de Dados Azure SQL e Azure Synapse, consulte a [encriptação transparente de dados com integração do Cofre chave Azure](transparent-data-encryption-byok-azure-sql.md).

Para começar a utilizar o TDE com a integração do Cofre de Chaves Azure, consulte o como orientar [a encriptação de dados transparente, utilizando](transparent-data-encryption-byok-azure-sql-configure.md)a sua própria chave a partir do Key Vault .

## <a name="move-a-transparent-data-encryption-protected-database"></a>Mover uma base de dados transparente protegida por encriptação de dados

Não precisas de desencriptar bases de dados para operações dentro do Azure. As definições de TDE na base de dados de origem ou na base de dados primária são herdadas de forma transparente no alvo. As operações incluídas envolvem:

- Georrestauro
- Restauro ponto-a-tempo de autosserviço
- Restauração de uma base de dados eliminada
- Georreplicação ativa
- Criação de uma cópia da base de dados
- Restaurar o ficheiro de backup para a Instância Gerida Azure SQL

> [!IMPORTANT]
> A obtenção de cópias de reserva manual de uma base de dados encriptada por TDE gerido pelo serviço não é permitida na Instância Gerida pelo Azure SQL, uma vez que o certificado utilizado para encriptação não é acessível. Utilize a função de restauro ponto-a-tempo para mover este tipo de base de dados para outra Instância Gerida.

Quando exporta uma base de dados protegida por TDE, o conteúdo exportado da base de dados não está encriptado. Este conteúdo exportado é armazenado em ficheiros BACPAC não encriptados. Certifique-se de que protege adequadamente os ficheiros BACPAC e ativa o TDE após a importação da nova base de dados.

Por exemplo, se o ficheiro BACPAC for exportado a partir de uma instância de SQL Server no local, o conteúdo importado da nova base de dados não é automaticamente encriptado. Da mesma forma, se o ficheiro BACPAC for exportado para uma instância de SQL Server no local, a nova base de dados também não é encriptada automaticamente.

A única exceção é quando exporta de e para uma base de dados SQL. O TDE está ativado na nova base de dados, mas o ficheiro BACPAC ainda não está encriptado.


## <a name="manage-transparent-data-encryption"></a>Gerir encriptação de dados transparente
# <a name="portal"></a>[Portal](#tab/azure-portal)
Gerencie o TDE no portal Azure.

Para configurar o TDE através do portal Azure, deve estar ligado como O Proprietário, Colaborador ou Gestor de Segurança SQL.

Liga e desliga o TDE no nível da base de dados. Para ativar o TDE numa base de dados, vá ao [portal Azure](https://portal.azure.com) e inscreva-se na sua conta De administrador ou colaborador Azure. Encontre as definições do TDE na sua base de dados do utilizador. Por padrão, é utilizada encriptação transparente de dados gerida pelo serviço. Um certificado TDE é gerado automaticamente para o servidor que contém a base de dados. Para o Azure SQL Managed Instance, utilize t-SQL para ligar e desligar o TDE numa base de dados.

![Encriptação transparente de dados gerida pelo serviço](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

Definiu a chave principal do TDE, conhecida como protetorTDE, ao nível do servidor. Para utilizar o Suporte TDE com suporte BYOK e proteger as suas bases de dados com uma chave do Key Vault, abra as definições de TDE sob o seu servidor.

![Encriptação de dados transparente com suporte bring your own key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Gerencie o TDE utilizando o PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para configurar o TDE através da PowerShell, deve estar ligado como o Proprietário, Colaborador ou Gestor de Segurança SQL.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets para Base de Dados Azure SQL e Azure Synapse

Utilize os seguintes cmdlets para a Base de Dados Azure SQL e para o Azure Synapse:

| Cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Permite ou desativa encriptação de dados transparente para uma base de dados|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Obtém o estado de encriptação de dados transparente para uma base de dados |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Verifica o progresso da encriptação para uma base de dados |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Adiciona uma chave chave vault a uma instância do Servidor SQL |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Obtém as chaves do cofre chave para um servidor de base de dados Azure SQL  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Define o protetor transparente de encriptação de dados para uma instância do Servidor SQL |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Obtém o protetor transparente de encriptação de dados |
| [Remover-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Remove uma chave do cofre chave de uma instância do Servidor SQL |
|  | |

> [!IMPORTANT]
> Para o Azure SQL Managed Instance, utilize o comando de base de dados T-SQL [ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) para ligar e desligar o TDE num nível de base de dados e verificar a amostra de [script PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) para gerir o TDE a nível de instância.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Gerencie o TDE utilizando a Transact-SQL.

Ligue-se à base de dados utilizando um login que seja um administrador ou membro da função **de gestor na** base de dados principal.

| Comando | Descrição |
| --- | --- |
| [ALTER DATABASE (Base de Dados Azure SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | Configurar encriptação ON/OFF encripta ou desencripta uma base de dados |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Devolve informações sobre o estado de encriptação de uma base de dados e as suas chaves de encriptação de base de dados associadas |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Devolve informações sobre o estado de encriptação de cada nó de Synapse Azure e as suas chaves de encriptação de base de dados associadas |
|  | |

Não é possível mudar o protetor TDE para uma chave do Key Vault utilizando o Transact-SQL. Utilize o PowerShell ou o portal Azure.

# <a name="rest-api"></a>[API REST](#tab/azure-RESTAPI)
Gerencie o TDE utilizando a API REST.

Para configurar o TDE através da API REST, deve estar ligado como O Proprietário, Colaborador ou Gestor de Segurança SQL.
Utilize o seguinte conjunto de comandos para a Base de Dados Azure SQL e para o Azure Synapse:

| Comando | Descrição |
| --- | --- |
|[Criar ou atualizar servidor](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Adiciona uma identidade de Diretório Ativo Azure a uma instância do Servidor SQL (usada para conceder acesso ao Cofre chave)|
|[Criar ou atualizar a chave do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Adiciona uma chave chave vault a uma instância do Servidor SQL|
|[Excluir a chave do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Remove uma chave do cofre chave de uma instância do Servidor SQL|
|[Obtenha chaves do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Obtém uma chave chave chave vault de uma instância do Servidor SQL|
|[Lista de chaves do servidor por servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Obtém as chaves do cofre chave para uma instância do Servidor SQL |
|[Criar ou atualizar protetor de encriptação](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Define o protetor TDE para uma instância do Servidor SQL|
|[Obter Protetor de Encriptação](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Obtém o protetor TDE para uma instância de Servidor SQL|
|[Lista protetores de encriptação por servidor](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Obtém os protetores TDE para uma instância de Servidor SQL |
|[Criar ou atualizar configuração transparente de encriptação de dados](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Habilita ou desativa o TDE para uma base de dados|
|[Obter configuração transparente de encriptação de dados](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Obtém a configuração TDE para uma base de dados|
|[Lista resultados transparentes de configuração de encriptação de dados](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Obtém o resultado da encriptação para uma base de dados|

## <a name="see-also"></a>Veja também
- O Servidor SQL que funciona numa máquina virtual Azure também pode usar uma chave assimétrica do Key Vault. Os passos de configuração são diferentes de usar uma chave assimétrica na Base de Dados SQL e na Instância Gerida SQL. Para mais informações, consulte a [gestão da chave Extensível utilizando o Cofre de Chaves Azure (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Para obter uma descrição geral do TDE, consulte [a encriptação](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)transparente de dados .
- Para saber mais sobre o TDE com suporte BYOK para a Base de Dados Azure SQL, Azure SQL Managed Instance e Azure Synapse, consulte [encriptação](transparent-data-encryption-byok-azure-sql.md)transparente de dados com suporte Bring Your Own Key .
- Para começar a utilizar o Suporte TDE com a Bring Your Own Key, consulte a [encriptação de dados transparente, utilizando](transparent-data-encryption-byok-azure-sql-configure.md)a sua própria chave a partir do Cofre de Chaves .
- Para mais informações sobre o Cofre chave, consulte [o acesso seguro a um cofre chave](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
