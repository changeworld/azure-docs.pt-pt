---
title: Problemas de autenticação no Azure HDInsight
description: Problemas de autenticação no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896125"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Problemas de autenticação no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

Em clusters seguros apoiados por Azure Data Lake (Gen1 ou Gen2), quando os utilizadores de domínio sintetizarem nos serviços de cluster através do HDI Gateway (como iniciar a sessão no portal Apache Ambari), o HDI Gateway tentará obter primeiro um símbolo da OAuth do Azure Ative Directory (Azure AD) . e, em seguida, obter um bilhete Kerberos da Azure AD DS. A autenticação pode falhar em qualquer uma destas fases. Este artigo destina-se a depurar algumas dessas questões.

Quando a autenticação falhar, será solicitado para credenciais. Se cancelar este diálogo, a mensagem de erro será impressa. Aqui estão algumas das mensagens de erro comuns:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant ou unauthorized_client, 50126

### <a name="issue"></a>Problema

O sessão é de falhas para utilizadores federados com código de erro 50126 (assinar com sucesso para utilizadores na nuvem). A mensagem de erro é semelhante a:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Causa

O código de erro da AD `AllowCloudPasswordValidation` Azure 50126 significa que a apólice não foi definida pelo inquilino.

### <a name="resolution"></a>Resolução

O Administrador da Empresa do inquilino Azure AD deve permitir que a Azure AD utilize hashes de senha para utilizadores apoiados pela ADFS.  Aplique `AllowCloudPasswordValidationPolicy` o tal como mostrado no artigo Use o Pacote de [Segurança Empresarial no HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant ou unauthorized_client, 50034

### <a name="issue"></a>Problema

O sessão falha com o código de erro 50034. A mensagem de erro é semelhante a:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Causa

O nome do utilizador está incorreto (não existe). O utilizador não está a utilizar o mesmo nome de utilizador que é utilizado no portal Azure.

### <a name="resolution"></a>Resolução

Use o mesmo nome de utilizador que funciona nesse portal.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant ou unauthorized_client, 50053

### <a name="issue"></a>Problema

A conta de utilizador está bloqueada, código de erro 50053. A mensagem de erro é semelhante a:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Causa

Demasiados sinais nas tentativas com uma senha incorreta.

### <a name="resolution"></a>Resolução

Aguarde mais ou menos 30 minutos, pare quaisquer aplicações que possam estar a tentar autenticar.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant ou unauthorized_client, 50053

### <a name="issue"></a>Problema

Palavra-passe expirada, código de erro 50053. A mensagem de erro é semelhante a:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Causa

A palavra-passe está caducada.

### <a name="resolution"></a>Resolução

Mude a palavra-passe no portal Azure (no seu sistema no local) e, em seguida, aguarde 30 minutos para que a sincronização o apanhe.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problema

Receba mensagem `interaction_required`de erro.

### <a name="cause"></a>Causa

A política de acesso condicional ou a MFA está a ser aplicada ao utilizador. Como a autenticação interativa ainda não é suportada, a política de Acesso condicional/MFA não deverá estar ativada para o utilizador ou cluster. Se optar por isentar o cluster (política de isenção `ServiceEndpoints` baseada em endereçoIP), certifique-se de que o AD está ativado para esse vnet.

### <a name="resolution"></a>Resolução

Utilize a política de acesso condicional e isentar os clusters HDInisght do MFA, como mostrado no [Configure um cluster HDInsight com pacote](./apache-domain-joined-configure-using-azure-adds.md)de segurança empresarial utilizando os Serviços de Domínio do Diretório Ativo Azure .

---

## <a name="sign-in-denied"></a>Assine por ser negado

### <a name="issue"></a>Problema

O sinal é negado.

### <a name="cause"></a>Causa

Para chegar a esta fase, a sua autenticação OAuth não é um problema, mas a autenticação kerberos é. Se este cluster é apoiado pela ADLS, o sinal de OAuth conseguiu antes de Kerberos auth ser tentado. Nos aglomerados WASB, o sinal de OAuth não é tentado. Pode haver muitas razões para a falha de Kerberos - como as hashes de senha estão dessincronizadas, a conta de utilizador bloqueada no Azure ADDS, e assim por diante. A palavra-passe só sincroniza quando o utilizador muda de senha. Quando criar a instância Azure AD DS, começará a sincronizar palavras-passe que são alteradas após a criação. Não sincronizará retroativamente as palavras-passe que foram definidas antes da sua criação.

### <a name="resolution"></a>Resolução

Se acha que as palavras-passe podem não estar sincronizadas, tente alterar a palavra-passe e aguarde alguns minutos para sincronizar.

Tente sSH num You terá de tentar autenticar (kinit) usando as mesmas credenciais de utilizador, a partir de uma máquina que está unida ao domínio. SSH na cabeça/nó de borda com um utilizador local e, em seguida, executar kinit.

---

## <a name="kinit-fails"></a>kinit falha

### <a name="issue"></a>Problema

Kinit falha.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

Para que o kinit tenha `sAMAccountName` sucesso, precisa de saber o seu (este é o nome da conta curta sem o reino). `sAMAccountName`é geralmente o prefixo `bob@contoso.com`da conta (como bob in). Para alguns utilizadores, pode ser diferente. Você precisará da capacidade de navegar/pesquisar o `sAMAccountName`diretório para aprender o seu .

Formas `sAMAccountName`de encontrar:

* Se conseguir iniciar sessão em Ambari usando o administrador ambari local, veja a lista de utilizadores.

* Se tiver uma máquina de [janelas unida](../../active-directory-domain-services/manage-domain.md)ao domínio, pode utilizar as ferramentas padrão do Windows AD para navegar. Isto requer uma conta de trabalho no domínio.

* A partir do nó da cabeça, você pode usar comandos SAMBA para pesquisar. Isto requer uma sessão de Kerberos válida (kinit bem-sucedido). pesquisa de anúncios líquidos "(userPrincipalName=bob*)"

    Os resultados de pesquisa/navegação devem mostrar-lhe o `sAMAccountName` atributo. Além disso, você poderia `pwdLastSet` `badPasswordTime`olhar `userPrincipalName` para outros atributos como , etc. para ver se essas propriedades correspondem ao que você espera.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit falha com falha de pré-autenticação

### <a name="issue"></a>Problema

Kinit falha `Preauthentication` com o fracasso.

### <a name="cause"></a>Causa

Nome de utilizador ou palavra-passe incorreto.

### <a name="resolution"></a>Resolução

Verifique o seu nome de utilizador e senha. Verifique também se existem outras propriedades descritas acima. Para permitir a depuração `export KRB5_TRACE=/tmp/krb.log` verbosa, corra da sessão antes de experimentar kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Comando Job / HDFS falha devido a TokenNotFoundException

### <a name="issue"></a>Problema

O comando Job / HDFS falha devido a `TokenNotFoundException`.

### <a name="cause"></a>Causa

O sinal de acesso oAuth exigido não foi encontrado para o trabalho/comando ter sucesso. O condutor da ADLS/ABFS tentará recuperar o sinal de acesso OAuth do serviço de credencial antes de fazer pedidos de armazenamento. Este token é registado quando faz o insessão no portal Ambari utilizando o mesmo utilizador.

### <a name="resolution"></a>Resolução

Certifique-se de que conseguiu aceder ao portal Ambari uma vez através do nome de utilizador cuja identidade é usada para executar o trabalho.

---

## <a name="error-fetching-access-token"></a>Erro de obtenção de ficha de acesso

### <a name="issue"></a>Problema

O utilizador recebe `Error fetching access token`uma mensagem de erro.

### <a name="cause"></a>Causa

Este erro ocorre intermitentemente quando os utilizadores tentam aceder ao ADLS Gen2 utilizando ACLs e o token Kerberos expirou.

### <a name="resolution"></a>Resolução

* Para azure Data Lake Storage Gen1, limpe a cache do navegador e volte a entrar em Ambari.

* Para azure Data Lake Storage `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>` Gen2, Corra para o utilizador que o utilizador está a tentar iniciar sessão como

---

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
