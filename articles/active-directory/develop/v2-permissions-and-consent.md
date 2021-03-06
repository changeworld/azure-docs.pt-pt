---
title: Âmbitos, permissões e consentimento da plataforma de identidade da Microsoft
description: Uma descrição da autorização no ponto final da plataforma de identidade da Microsoft, incluindo âmbitos, permissões e consentimento.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: aaddev, fasttrack-edit
ms.openlocfilehash: 55055f65e1b725e079b60e960837e05558ef08d6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886216"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Permissões e consentimento no ponto final da plataforma de identidade da Microsoft

As aplicações que se integram com a plataforma de identidade da Microsoft seguem um modelo de autorização que dá aos utilizadores e administradores o controlo sobre a forma como os dados podem ser acedidos. A implementação do modelo de autorização foi atualizada no ponto final da plataforma de identidade da Microsoft, e altera a forma como uma aplicação deve interagir com a plataforma de identidade da Microsoft. Este artigo abrange os conceitos básicos deste modelo de autorização, incluindo âmbitos, permissões e consentimento.

> [!NOTE]
> O ponto final da plataforma de identidade da Microsoft não suporta todos os cenários e funcionalidades. Para determinar se deve utilizar o ponto final da plataforma de identidade da Microsoft, leia sobre [as limitações](active-directory-v2-limitations.md)da plataforma de identidade da Microsoft .

## <a name="scopes-and-permissions"></a>Âmbitos e permissões

A plataforma de identidade da Microsoft implementa o protocolo de autorização [OAuth 2.0.](active-directory-v2-protocols.md) O OAuth 2.0 é um método através do qual uma aplicação de terceiros pode aceder a recursos hospedados na Web em nome de um utilizador. Qualquer recurso web-hostque que se integre com a plataforma de identidade da Microsoft tem um identificador de recursos, ou *ID DE aplicação URI*. Por exemplo, alguns dos recursos web da Microsoft incluem:

* Microsoft Graph:`https://graph.microsoft.com`
* Office 365 Mail API:`https://outlook.office.com`
* Cofre de chaves Azure:`https://vault.azure.net`

> [!NOTE]
> Recomendamos vivamente que utilize o Microsoft Graph em vez do Office 365 Mail API, etc.

O mesmo acontece com quaisquer recursos de terceiros que tenham integrado a plataforma de identidade da Microsoft. Qualquer um destes recursos também pode definir um conjunto de permissões que podem ser usadas para dividir a funcionalidade desse recurso em pedaços menores. Como exemplo, o [Microsoft Graph](https://graph.microsoft.com) definiu permissões para fazer as seguintes tarefas, entre outras:

* Leia o calendário de um utilizador
* Escreva para o calendário de um utilizador
* Enviar correio como utilizador

Ao definir este tipo de permissões, o recurso tem um controlo fino sobre os seus dados e como a funcionalidade API é exposta. Uma aplicação de terceiros pode solicitar estas permissões a utilizadores e administradores, que devem aprovar o pedido antes de a app poder aceder a dados ou agir em nome de um utilizador. Ao analisar a funcionalidade do recurso em conjuntos de permissões mais pequenos, as aplicações de terceiros podem ser construídas para solicitar apenas as permissões específicas de que necessitam para desempenhar em funções a sua função. Os utilizadores e administradores podem saber exatamente a que dados a app tem acesso, e podem estar mais confiantes de que não se está a comportar com intenções maliciosas. Os desenvolvedores devem sempre respeitar o conceito de menor privilégio, pedindo apenas as permissões de que necessitam para que as suas aplicações funcionem.

No OAuth 2.0, este tipo de permissões são *chamados de âmbitos*. Também são frequentemente *referidas como permissões.* Uma permissão está representada na plataforma de identidade da Microsoft como um valor de cadeia. Continuando com o exemplo do Microsoft Graph, o valor de cadeia para cada permissão é:

* Leia o calendário de um utilizador usando`Calendars.Read`
* Escreva para o calendário de um utilizador usando`Calendars.ReadWrite`
* Enviar correio como utilizador usando por`Mail.Send`

Uma aplicação mais frequentemente solicita estas permissões especificando os âmbitos nos pedidos para a plataforma de identidade da Microsoft autorizar o ponto final. No entanto, certas permissões de privilégio elevado só podem ser concedidas através do consentimento do administrador e solicitadas/concedidas através do [ponto final](v2-permissions-and-consent.md#admin-restricted-permissions)do consentimento do administrador . Leia para saber mais.

## <a name="permission-types"></a>Tipos de permissão

A plataforma de identidade da Microsoft suporta dois tipos de permissões: **permissões delegadas** e **permissões de aplicação.**

* **As permissões delegadas** são utilizadas por apps que têm um utilizador inscrito. Para estas aplicações, ou o utilizador ou um administrador consente com as permissões que a aplicação solicita, e a aplicação é delegada permissão para agir como o utilizador inscrito ao efetuar chamadas para o recurso alvo. Algumas permissões delegadas podem ser consentidas por utilizadores não administrativos, mas algumas permissões mais privilegiadas requerem o consentimento do [administrador.](v2-permissions-and-consent.md#admin-restricted-permissions) Para saber quais as funções de administrador que podem consentir com permissões delegadas, consulte [permissões de funções do Administrador em Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **As permissões** de aplicação são utilizadas por apps que funcionam sem um utilizador inscrito presente; por exemplo, aplicações que funcionam como serviços de fundo ou daemons.  As permissões de pedido só podem ser [consentidas por um administrador.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

_Permissões eficazes_ são as permissões que a sua aplicação terá ao fazer pedidos para o recurso alvo. É importante compreender a diferença entre as permissões delegadas e de aplicação que a sua aplicação é concedida e as suas permissões eficazes ao fazer chamadas para o recurso alvo.

- Para permissões delegadas, as _permissões efetivas_ da sua aplicação serão a intersecção menos privilegiada das permissões delegadas que a app foi concedida (por consentimento) e os privilégios do utilizador atualmente assinado. A aplicação nunca pode ter mais privilégios do que o utilizador com sessão iniciada. Nas organizações, os privilégios do utilizador com sessão iniciada podem ser determinados por uma política ou por associação a uma ou mais funções de administrador. Para saber quais as funções de administrador que podem consentir com permissões delegadas, consulte [permissões de funções do Administrador em Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Por exemplo, assuma que a sua aplicação foi concedida ao _Utilizador.ReadWrite.Toda_ a permissão delegada. Esta permissão concede nominalmente permissão à aplicação para ler e atualizar o perfil de todos os utilizadores de uma organização. Se o utilizador com sessão iniciada for administrador global, a aplicação poderá atualizar o perfil de todos os utilizadores da organização. No entanto, se o utilizador de saque não estiver numa função de administrador, a sua aplicação poderá atualizar apenas o perfil do utilizador inscrito. Não poderá atualizar os perfis dos outros utilizadores da organização porque o utilizador em cujo nome tem permissão para agir não tem esses privilégios.
  
- Para permissões de aplicação, as _permissões efetivas_ da sua aplicação serão o nível completo de privilégios implícitos pela permissão. Por exemplo, uma aplicação que tenha o _User.ReadWrite.Todas as_ permissões de aplicação podem atualizar o perfil de cada utilizador da organização. 

## <a name="openid-connect-scopes"></a>AbraID Connect scopes

A implementação da plataforma de identidade Microsoft do OpenID Connect tem alguns `openid` `email`âmbitos bem definidos que não se aplicam a um recurso específico: , , `profile`e `offline_access`. Os `address` `phone` âmbitos de ligação e OpenID não são suportados.

### <a name="openid"></a>openid

Se uma aplicação eexecuta o sessão utilizando `openid` o [OpenID Connect,](active-directory-v2-protocols.md)deve solicitar o âmbito. O `openid` âmbito mostra na página de consentimento da conta de trabalho como a permissão "Iniciar sessão" e na página de consentimento pessoal da conta da Microsoft como a permissão "Ver o seu perfil e ligar-se a apps e serviços utilizando a sua conta Microsoft". Com esta permissão, uma aplicação pode receber um identificador único para o utilizador sob a forma da `sub` reclamação. Também dá acesso à aplicação ao ponto final do UserInfo. O `openid` âmbito pode ser utilizado na plataforma de identidade da Microsoft para adquirir tokens de ID, que podem ser usados pela aplicação para autenticação.

### <a name="email"></a>e-mail

O `email` âmbito pode ser `openid` utilizado com o âmbito e qualquer outro. Dá à aplicação acesso ao endereço de e-mail `email` primário do utilizador sob a forma da reclamação. A `email` reclamação só está incluída num símbolo se um endereço de e-mail estiver associado à conta de utilizador, o que nem sempre é o caso. Se utilizar `email` o âmbito, a sua aplicação deve `email` estar preparada para lidar com um caso em que a reclamação não exista no token.

### <a name="profile"></a>perfil

O `profile` âmbito pode ser `openid` utilizado com o âmbito e qualquer outro. Dá à aplicação acesso a uma quantidade substancial de informação sobre o utilizador. A informação a que pode aceder inclui, mas não se limita a, o nome, apelido, nome de utilizador preferido e ID do objeto. Para obter uma lista completa das reclamações de perfil disponíveis [ `id_tokens` ](id-tokens.md)no parâmetro id_tokens para um utilizador específico, consulte a referência .

### <a name="offline_access"></a>offline_access

O [ `offline_access` âmbito](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) dá à sua app acesso aos recursos em nome do utilizador por um período prolongado. Na página de consentimento, este âmbito aparece como a permissão "Manter o acesso aos dados a que lhe deu acesso". Quando um utilizador `offline_access` aprova o âmbito, a sua aplicação pode receber fichas de atualização da plataforma de identidade da Microsoft. As fichas refrescantes são de longa duração. A sua aplicação pode obter novos tokens de acesso à medida que os mais velhos expiram.

> [!NOTE]
> Esta permissão aparece em todos os ecrãs de consentimento hoje em dia, mesmo para fluxos que não fornecem um token refrescante (o [fluxo implícito](v2-oauth2-implicit-grant-flow.md)).  Isto é para cobrir cenários em que um cliente pode começar dentro do fluxo implícito, e depois passar para o fluxo de código onde é esperado um token de atualização.

Na plataforma de identidade da Microsoft (pedidos feitos para o ponto final `offline_access` v2.0), a sua aplicação deve solicitar explicitamente o âmbito, para receber fichas de atualização. Isto significa que quando resgatar um código de autorização no fluxo de código de [autorização OAuth 2.0,](active-directory-v2-protocols.md)receberá apenas um sinal de acesso a `/token` partir do ponto final. O sinal de acesso é válido por um curto período de tempo. O sinal de acesso geralmente expira em uma hora. Nessa altura, a sua aplicação precisa de `/authorize` redirecionar o utilizador para o ponto final para obter um novo código de autorização. Durante este redirecionamento, dependendo do tipo de app, o utilizador poderá ter de voltar a introduzir as suas credenciais ou consentir novamente com permissões. 

Para obter mais informações sobre como obter e usar tokens de atualização, consulte a referência do protocolo da [plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Solicitando o consentimento individual do utilizador

Num pedido de autorização [OpenID Connect ou OAuth 2.0,](active-directory-v2-protocols.md) uma aplicação pode solicitar as permissões de que necessita utilizando o parâmetro de `scope` consulta. Por exemplo, quando um utilizador faz o inserido numa aplicação, a aplicação envia um pedido como o seguinte exemplo (com quebras de linha adicionadas para legibilidade):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

O `scope` parâmetro é uma lista separada do espaço de permissões delegadas que a aplicação está a solicitar. Cada permissão é indicada através da adesão do valor de permissão ao identificador do recurso (o ID DE Aplicação URI). No exemplo do pedido, a aplicação necessita de permissão para ler o calendário do utilizador e enviar correio como utilizador.

Depois de o utilizador introduzir as suas credenciais, a plataforma de identidade da Microsoft verifica se há um registo correspondente do consentimento do *utilizador*. Se o utilizador não tiver consentido com nenhuma das permissões solicitadas no passado, nem um administrador consentiu nestas permissões em nome de toda a organização, o ponto final da plataforma de identidade da Microsoft pede ao utilizador que conceda as permissões solicitadas.

> [!NOTE]
>Neste momento, `offline_access` as permissões ("Manter o acesso aos `user.read` dados a que lhe foi dado acesso") e ("Inscreva-se e leia o seu perfil") são automaticamente incluídas no consentimento inicial de uma aplicação.  Estas permissões são geralmente necessárias `offline_access` para uma funcionalidade adequada da aplicação - dá à `user.read` app acesso `sub` a fichas de atualização, críticas para aplicações nativas e web, ao mesmo tempo que dá acesso à reclamação, permitindo ao cliente ou app identificar corretamente o utilizador ao longo do tempo e aceder a informações rudimentares do utilizador.  

![Screenshot de exemplo que mostra consentimento da conta de trabalho](./media/v2-permissions-and-consent/work_account_consent.png)

Quando o utilizador aprova o pedido de permissão, o consentimento é registado e o utilizador não tem de consentir novamente nos subsequentes insinos para a aplicação.

## <a name="requesting-consent-for-an-entire-tenant"></a>Solicitando consentimento para um inquilino inteiro

Muitas vezes, quando uma organização compra uma licença ou subscrição para uma aplicação, a organização quer configurar proativamente o pedido de utilização por todos os membros da organização. Como parte deste processo, um administrador pode conceder o consentimento para que o pedido atue em nome de qualquer utilizador do inquilino. Se o administrador conceder o consentimento para todo o inquilino, os utilizadores da organização não verão uma página de consentimento para a aplicação.

Para solicitar o consentimento para permissões delegadas para todos os utilizadores de um inquilino, a sua aplicação pode utilizar o ponto final de consentimento do administrador.

Além disso, as aplicações devem usar o ponto final do consentimento do administrador para solicitar permissões de pedido.

## <a name="admin-restricted-permissions"></a>Permissões restritas a administradores

Algumas permissões de privilégio saem no ecossistema da Microsoft podem ser definidas como restritas à *administração.* Exemplos deste tipo de permissões incluem o seguinte:

* Leia todos os perfis completos do utilizador usando`User.Read.All`
* Escreva dados para o diretório de uma organização usando`Directory.ReadWrite.All`
* Leia todos os grupos no diretório de uma organização usando`Groups.Read.All`

Embora um utilizador de consumo possa conceder um acesso a este tipo de dados, os utilizadores organizacionais estão impedidos de conceder acesso ao mesmo conjunto de dados sensíveis da empresa. Se a sua aplicação solicitar o acesso a uma dessas permissões de um utilizador organizacional, o utilizador recebe uma mensagem de erro que diz não estar autorizada a consentir com as permissões da sua aplicação.

Se a sua aplicação necessitar de acesso a âmbitos restritos de administração para organizações, deve solicitá-las diretamente a um administrador da empresa, também utilizando o ponto final de consentimento administrativo, descrito em seguida.

Se o pedido estiver a solicitar permissões delegadas de alto privilégio e um administrador conceder essas permissões através do ponto final do consentimento do administrador, o consentimento é concedido a todos os utilizadores do inquilino.

Se o pedido estiver a solicitar permissões de pedido e um administrador conceder essas permissões através do ponto final do consentimento do administrador, esta subvenção não é feita em nome de nenhum utilizador específico. Em vez disso, o pedido do cliente é concedido *diretamente*a permissões . Este tipo de permissões são utilizados apenas por serviços daemon e outras aplicações não interativas que funcionam em segundo plano.

## <a name="using-the-admin-consent-endpoint"></a>Utilização do ponto final do consentimento da administração

> [!NOTE] 
> Por favor, note depois de conceder o consentimento do administrador usando o ponto final do consentimento da administração, você terminou de conceder consentimento de administrador e os utilizadores não precisam realizar quaisquer ações adicionais adicionais. Após a concessão do consentimento da administração, os utilizadores podem obter um sinal de acesso através de um fluxo típico de auth e o token de acesso resultante terá as permissões consentidas. 

Quando um Administrador da Empresa utiliza a sua aplicação e é direcionado para o ponto final de autorização, a plataforma de identidade da Microsoft irá detetar o papel do utilizador e perguntar-lhe se gostaria de consentir em nome de todo o inquilino pelas permissões que solicitou. No entanto, existe também um ponto final dedicado ao consentimento do administrador que pode usar se quiser solicitar proativamente que um administrador conceda autorização em nome de todo o inquilino. A utilização deste ponto final também é necessária para solicitar permissões de pedido (que não podem ser solicitadas usando o ponto final de autorização).

Se seguir estes passos, a sua aplicação pode solicitar permissões a todos os utilizadores de um inquilino, incluindo âmbitos restritos à administração. Esta é uma operação de alto privilégio e só deve ser feita se necessário para o seu cenário.

Para ver uma amostra de código que implemente os passos, consulte a [amostra de âmbitos restritos à administração](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicite as permissões no portal de registo da aplicação

As aplicações podem notar quais as permissões que necessitam (delegadas e de aplicação) no portal de registo da aplicação.  Isto permite a `/.default` utilização do âmbito e a opção "Grant admin consent" do portal Azure.  Em geral, é a melhor prática garantir que as permissões definidas estáticamente para uma determinada aplicação são um superconjunto das permissões que irá solicitar dinamicamente/incrementalmente.

> [!NOTE]
>As permissões de aplicação só [`/.default`](#the-default-scope) podem ser solicitadas através do uso de - por isso, se a sua aplicação precisar de permissões de aplicação, certifique-se de que estão listadas no portal de registo da aplicação.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Para configurar a lista de permissões estáticas solicitadas para um pedido

1. Vá à sua aplicação no portal Azure – Experiência de registos de [aplicações,](https://go.microsoft.com/fwlink/?linkid=2083908) ou [crie uma app](quickstart-register-app.md) se ainda não o fez.
2. Localize a secção **de permissões DaPI** e dentro das permissões da API clique em Adicionar uma permissão.
3. Selecione o **Microsoft Graph** na lista de APIs disponíveis e, em seguida, adicione as permissões que a sua aplicação necessita.
3. **Guarde** o registo da aplicação.

### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: Inscreva o utilizador na sua aplicação

Normalmente, quando se constrói uma aplicação que utiliza o ponto final do consentimento do administrador, a aplicação precisa de uma página ou vista na qual o administrador pode aprovar as permissões da app. Esta página pode fazer parte do fluxo de inscrição da aplicação, parte das definições da aplicação, ou pode ser um fluxo dedicado de "connect". Em muitos casos, faz sentido que a app mostre esta visão de "connect" apenas depois de um utilizador ter assinado com uma conta de trabalho ou escola da Microsoft.

Ao assinar o utilizador na sua aplicação, pode identificar a organização a que pertence o administrador antes de pedir-lhes que aprovem as permissões necessárias. Embora não seja estritamente necessário, pode ajudá-lo a criar uma experiência mais intuitiva para os seus utilizadores organizacionais. Para iniciar sessão com o utilizador, siga os [nossos tutoriais](active-directory-v2-protocols.md)de protocolo de plataforma de identidade da Microsoft .

### <a name="request-the-permissions-from-a-directory-admin"></a>Solicite as permissões de um administrador de diretório

Quando estiver pronto para solicitar permissões ao administrador da sua organização, pode redirecionar o utilizador para o *ponto final*de consentimento da plataforma de identidade da Microsoft .

```
// Line breaks are for legibility only.
  GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
  &scope=
  https://graph.microsoft.com/calendars.read 
  https://graph.microsoft.com/mail.send
```


| Parâmetro        | Condição        | Descrição                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Necessário | O inquilino do diretório de quem quer pedir autorização. Pode ser fornecido em formato de nome GUIA ou amigável OU genericamente referenciado com organizações como visto no exemplo. Não utilize "comum", uma vez que as contas pessoais não podem dar consentimento à administração, exceto no contexto de um inquilino. Para garantir a melhor compatibilidade com contas pessoais que gerem os inquilinos, utilize o ID do inquilino sempre que possível. |
| `client_id` | Necessário | O ID de **Aplicação (cliente)** que o [portal Azure – App registra](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída à sua app. |
| `redirect_uri` | Necessário |O URI redireciona do URI onde pretende que a resposta seja enviada para a sua aplicação manusear. Deve corresponder exatamente a uma das URIs redirecionais que registou no portal de registo da aplicação. |
| `state` | Recomendado | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queiras. Utilize o Estado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que se encontrava. |
|`scope`        | Necessário        | Define o conjunto de permissões que estão a ser solicitadas pelo pedido. Isto pode ser estático (utilizando) [`/.default`](#the-default-scope)ou âmbitos dinâmicos.  Isto pode incluir os âmbitos`openid` `profile`oIDC ( , , `email`). Se necessitar de permissões `/.default` de pedido, deve utilizar para solicitar a lista de permissões estáticas.  | 


Neste momento, a Azure AD exige que um administrador inquilino assine o pedido. Pede-se ao administrador que aprove todas as `scope` permissões que solicitou no parâmetro.  Se tiver usado um`/.default`valor estático , funcionará como o ponto final de consentimento da v1.0 e solicitará o consentimento de todos os âmbitos encontrados nas permissões necessárias para a app.

#### <a name="successful-response"></a>Resposta bem sucedida

Se o administrador aprovar as permissões para a sua aplicação, a resposta bem sucedida é a seguinte:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- |
| `tenant` | O inquilino de diretório que concedeu ao seu pedido as permissões que solicitou, em formato GUID. |
| `state` | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queiras. O Estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que se encontrava. |
| `admin_consent` | Será definido `True`para . |

#### <a name="error-response"></a>Resposta de erro

Se o administrador não aprovar as permissões para a sua aplicação, a resposta falhada é a seguinte:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem, e pode ser usada para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro. |

Depois de ter recebido uma resposta bem sucedida do ponto final do consentimento da administração, a sua aplicação obteve as permissões que solicitou. Em seguida, pode pedir um sinal para o recurso que quiser.

## <a name="using-permissions"></a>Usando permissões

Depois de o utilizador consentir em permissões para a sua aplicação, a sua aplicação pode adquirir fichas de acesso que representam a permissão da sua aplicação para aceder a um recurso com alguma capacidade. Um token de acesso pode ser usado apenas para um único recurso, mas codificado dentro do token de acesso é cada permissão que a sua aplicação foi concedida para esse recurso. Para adquirir um token de acesso, a sua aplicação pode fazer um pedido para a plataforma de identidade da Microsoft token endpoint, como este:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Pode utilizar o token de acesso resultante nos pedidos http para o recurso. Indica de forma fiável ao recurso que a sua aplicação tem a permissão adequada para executar uma tarefa específica. 

Para obter mais informações sobre o protocolo OAuth 2.0 e como obter fichas de acesso, consulte a referência do [protocolo final da plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>O âmbito /.padrão

Pode utilizar `/.default` o âmbito para ajudar a migrar as suas aplicações do ponto final v1.0 para o ponto final da plataforma de identidade da Microsoft. Esta é uma mira incorporada para cada aplicação que se refere à lista estática de permissões configuradas no registo de candidatura. Um `scope` valor `https://graph.microsoft.com/.default` é funcionalmente o mesmo que os `resource=https://graph.microsoft.com` pontos finais v1.0 - nomeadamente, solicita um símbolo com os âmbitos no Microsoft Graph que a aplicação registou no portal Azure.  É construído utilizando o recurso `/.default` URI + (por exemplo, `https://contosoApp.com`se o recurso URI `https://contosoApp.com/.default`for, então o âmbito solicitado seria).  Consulte a [secção de cortes](#trailing-slash-and-default) de rasto para os casos em que deve incluir um segundo corte para solicitar corretamente o símbolo.

O âmbito /.padrão pode ser usado em qualquer fluxo OAuth 2.0, mas é necessário no fluxo de [credenciais](v2-oauth2-client-creds-grant-flow.md)de fluxo e de cliente em [nome](v2-oauth2-on-behalf-of-flow.md) de entrada, bem como ao utilizar o ponto final de consentimento da administração v2 para solicitar permissões de pedido.  

> [!NOTE]
> Os clientes não podem`/.default`combinar consentimento estático e dinâmico num único pedido. Assim, `scope=https://graph.microsoft.com/.default+mail.read` resultará num erro devido à combinação de tipos de âmbito.

### <a name="default-and-consent"></a>/.padrão e consentimento

O `/.default` âmbito despoleta o comportamento do ponto `prompt=consent` final v1.0 também. Solicita consentimento para todas as permissões registadas pelo pedido, independentemente do recurso. Se incluído como parte do `/.default` pedido, o âmbito devolve um símbolo que contém os âmbitos para o recurso solicitado.

### <a name="default-when-the-user-has-already-given-consent"></a>/.padrão quando o utilizador já deu consentimento

Como `/.default` é funcionalmente `resource`idêntico ao comportamento do ponto final do v1.0, traz consigo o comportamento de consentimento do ponto final v1.0 também. Ou seja, `/.default` apenas desencadeia um pedido de consentimento se não tiver sido concedida qualquer permissão entre o cliente e o recurso pelo utilizador. Se tal consentimento existir, então será devolvido um símbolo contendo todos os âmbitos concedidos pelo utilizador para esse recurso. No entanto, se não tiver `prompt=consent` sido concedida qualquer permissão, ou se o parâmetro tiver sido fornecido, será apresentado um pedido de consentimento para todos os âmbitos registados pelo pedido do cliente.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exemplo 1: O utilizador, ou administrador de inquilino, concedeu permissões

Neste exemplo, o utilizador (ou administrador de inquilino) concedeu `mail.read` `user.read`ao cliente as permissões do Microsoft Graph e . Se o cliente fizer `scope=https://graph.microsoft.com/.default`um pedido de , então nenhum pedido de consentimento será mostrado independentemente do conteúdo das aplicações do cliente permissões registadas para o Microsoft Graph. Um símbolo seria devolvido contendo `mail.read` os `user.read`âmbitos e .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exemplo 2: O utilizador não concedeu permissões entre o cliente e o recurso

Neste exemplo, não existe consentimento para o utilizador entre o cliente e o Microsoft Graph. O cliente registou-se para as `user.read` permissões e `contacts.read` `https://vault.azure.net/user_impersonation`permissões, bem como para o âmbito do Cofre chave Azure. Quando o cliente solicitar um `scope=https://graph.microsoft.com/.default`sinal para , o utilizador `user.read` `contacts.read`verá um ecrã `user_impersonation` de consentimento para os , e os âmbitos do Cofre chave. O símbolo devolvido terá `user.read` apenas os e `contacts.read` âmbitos nele e só será utilizável contra o Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Exemplo 3: O utilizador consentiu e o cliente solicita âmbitos adicionais

Neste exemplo, o utilizador já `mail.read` consentiu para o cliente. O cliente registou-se `contacts.read` no seu registo. Quando o cliente fizer um pedido `scope=https://graph.microsoft.com/.default` de um `prompt=consent`token usando e solicitar o consentimento através de , então o utilizador verá um ecrã de consentimento para todas (e apenas) as permissões registadas pela aplicação. `contacts.read`estará presente no ecrã de `mail.read` consentimento, mas não. O símbolo devolvido será para o `mail.read` Microsoft `contacts.read`Graph e conterá e .

### <a name="using-the-default-scope-with-the-client"></a>Utilização do âmbito /.padrão com o cliente

Existe um caso `/.default` especial do âmbito em `/.default` que um cliente solicita o seu próprio âmbito. O exemplo que se segue demonstra este cenário.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Isto produz um ecrã de consentimento para todas as permissões registadas `/.default`(se aplicável com base nas descrições acima referidas de consentimento e ), em seguida, devolve uma id_token, em vez de um sinal de acesso.  Este comportamento existe para certos clientes legados que se deslocam da ADAL para a MSAL, e **não devem** ser utilizados por novos clientes que direcionem o ponto final da plataforma de identidade da Microsoft.  

### <a name="trailing-slash-and-default"></a>Trailing slash e /.default

Alguns uris de recursos`https://contoso.com/` têm um `https://contoso.com`corte de rastos ( ao contrário ), que pode causar problemas com validação simbólica.  Isto pode ocorrer principalmente quando se pede um`https://management.azure.com/`sinal para a Azure Resource Management (), que tem um corte de rasto no seu recurso URI e exige que esteja presente quando o símbolo é solicitado.  Assim, ao solicitar um `https://management.azure.com/` símbolo `/.default`para e `https://management.azure.com//.default` utilizar , deve solicitar - note o duplo corte! 

Em geral - se tiver validado que o símbolo está a ser emitido, e o símbolo está a ser rejeitado pela API que deve aceitá-lo, considere adicionar um segundo corte e tentar novamente. Isto acontece porque o servidor de login emite um `scope` símbolo com `/.default` o público que combina com os URIs no parâmetro - com removido da extremidade.  Se isto remover o corte de encaminhamento, o servidor de login ainda processa o pedido e valida-o contra o URI de recurso, mesmo que já não correspondam - isto não é normal e não deve ser invocado pela sua aplicação.  

## <a name="troubleshooting-permissions-and-consent"></a>Permissões e consentimento de resolução de problemas

Se você ou os utilizadores da sua aplicação estiverem a ver erros inesperados durante o processo de consentimento, consulte este artigo para medidas de resolução de problemas: [Erro inesperado ao executar o consentimento de uma aplicação](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
