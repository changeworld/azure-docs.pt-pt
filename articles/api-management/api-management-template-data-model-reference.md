---
title: Referência do modelo de modelo de gestão da API Azure [ Microsoft Docs
description: Conheça a entidade e as representações de tipo para itens comuns utilizados nos modelos de dados para os modelos do portal de desenvolvimento na Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 5625ff7e4fc51b9b6b894698719247902a480f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243942"
---
# <a name="azure-api-management-template-data-model-reference"></a>Referência do modelo de modelo de gestão da API Azure
Este tópico descreve a entidade e as representações de tipo para itens comuns utilizados nos modelos de dados para os modelos do portal de desenvolvimento na Gestão da API Azure.  
  
 Para obter mais informações sobre o trabalho com modelos, consulte como personalizar o portal de desenvolvimento de [gestão da API utilizando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Referência

-   [API](#API)  
-   [Resumo da API](#APISummary)  
-   [Aplicação](#Application)  
-   [Anexo](#Attachment)  
-   [Exemplo de código](#Sample)  
-   [Comentário](#Comment)  
-   [Filtragem](#Filtering)  
-   [Cabeçalho](#Header)  
-   [Pedido de HTTP](#HTTPRequest)  
-   [Resposta HTTP](#HTTPResponse)  
-   [Edição](#Issue)  
-   [Operação](#Operation)  
-   [Menu de funcionamento](#Menu)  
-   [Artigo de menu de operação](#MenuItem)  
-   [Paginação](#Paging)  
-   [Parâmetro](#Parameter)  
-   [Produto](#Product)  
-   [Fornecedor](#Provider)  
-   [Representação](#Representation)  
-   [Assinatura](#Subscription)  
-   [Resumo de subscrição](#SubscriptionSummary)  
-   [Informação da conta do utilizador](#UserAccountInfo)  
-   [Sessão de inscrição no utilizador](#UseSignIn)  
-   [Inscrição do utilizador](#UserSignUp)  
  
##  <a name="api"></a><a name="API"></a>API  
 A `API` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recursos. Identifica exclusivamente a API no âmbito da atual instância de serviço de Gestão API. O valor é um URL relativo `apis/{id}` `{id}` válido no formato de onde é identificador API. Esta propriedade é apenas leitura.|  
|`name`|string|Nome da API. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|Descrição da API. Não deve estar vazio. Pode incluir etiquetas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`serviceUrl`|string|URL absoluto do serviço de backend implementando esta API.|  
|`path`|string|URL relativo identificando exclusivamente esta API e todos os seus caminhos de recursos dentro da instância de serviço de Gestão API. Está anexado ao URL base de ponto final da API especificado durante a criação da instância de serviço para formar um URL público para esta API.|  
|`protocols`|conjunto de número|Descreve em que protocolos as operações nesta API podem ser invocadas. Os valores permitidos são `1 - http` e, `2 - https`ou ambos.|  
|`authenticationSettings`|[Definições de autenticação do servidor de autorização](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Recolha de definições de autenticação incluídas nesta API.|  
|`subscriptionKeyParameterNames`|objeto|Propriedade opcional que pode ser usada para especificar nomes personalizados para consulta e/ou parâmetros de cabeçalho contendo a chave de subscrição. Quando esta propriedade estiver presente, deve conter pelo menos uma das duas seguintes propriedades.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="api-summary"></a><a name="APISummary"></a>Resumo da API  
 A `API summary` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recursos. Identifica exclusivamente a API no âmbito da atual instância de serviço de Gestão API. O valor é um URL relativo `apis/{id}` `{id}` válido no formato de onde é identificador API. Esta propriedade é apenas leitura.|  
|`name`|string|Nome da API. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|Descrição da API. Não deve estar vazio. Pode incluir etiquetas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
  
##  <a name="application"></a><a name="Application"></a>Candidatura  
 A `application` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|O identificador único da aplicação.|  
|`Title`|string|O título da candidatura.|  
|`Description`|string|A descrição do pedido.|  
|`Url`|URI|O URI para a aplicação.|  
|`Version`|string|Informação de versão para a aplicação.|  
|`Requirements`|string|Uma descrição dos requisitos para o pedido.|  
|`State`|número|O estado atual da aplicação.<br /><br /> - 0 - Registado<br /><br /> - 1 - Submetido<br /><br /> - 2 - Publicado<br /><br /> - 3 - Rejeitado<br /><br /> - 4 - Inédito|  
|`RegistrationDate`|DateTime|A data e hora do pedido foi registada.|  
|`CategoryId`|número|A categoria da candidatura (Finanças, entretenimento, etc.)|  
|`DeveloperId`|string|O identificador único do desenvolvedor que submeteu a aplicação.|  
|`Attachments`|Coleção de entidades de [anexo.](#Attachment)|Quaisquer anexos para a aplicação, tais como imagens ou ícones.|  
|`Icon`|[Anexo](#Attachment)|O ícone da aplicação.|  
  
##  <a name="attachment"></a><a name="Attachment"></a>Anexo  
 A `attachment` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`UniqueId`|string|O identificador único para o anexo.|  
|`Url`|string|A URL do recurso.|  
|`Type`|string|O tipo de anexo.|  
|`ContentType`|string|O tipo de mídia do anexo.|  
  
##  <a name="code-sample"></a><a name="Sample"></a>Amostra de código  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`title`|string|O nome da operação.|  
|`snippet`|string|Esta propriedade é depreciada e não deve ser usada.|  
|`brush`|string|Qual modelo de coloração de sintaxe de código a utilizar ao apresentar a amostra de código. Os valores `php` `java`permitidos `python`são, `plain` `csharp`, `xml`, `objc`, , `ruby`e .|  
|`template`|string|O nome deste modelo de amostra de código.|  
|`body`|string|Um espaço reservado para a parte da amostra de código do corte.|  
|`method`|string|O método HTTP da operação.|  
|`scheme`|string|O protocolo a utilizar para o pedido de operação.|  
|`path`|string|O caminho da operação.|  
|`query`|string|Exemplo de corda de consulta com parâmetros definidos.|  
|`host`|string|O URL da porta de entrada de serviço de Gestão API para a API que contém esta operação.|  
|`headers`|Coleção de entidades [cabeçalho.](#Header)|Cabeçalhos para esta operação.|  
|`parameters`|Coleção de entidades [parametrómetros.](#Parameter)|Parâmetros definidos para esta operação.|  
  
##  <a name="comment"></a><a name="Comment"></a>Comentário  
 A `API` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|número|A identificação do comentário.|  
|`CommentText`|string|O corpo do comentário. Pode incluir HTML.|  
|`DeveloperCompany`|string|O nome da empresa do construtor.|  
|`PostedOn`|DateTime|A data e a hora do comentário foram publicados.|  
  
##  <a name="issue"></a><a name="Issue"></a>Edição  
 A `issue` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|O identificador único para o problema.|  
|`ApiID`|string|O ID da API para a qual este assunto foi reportado.|  
|`Title`|string|Título da questão.|  
|`Description`|string|Descrição do assunto.|  
|`SubscriptionDeveloperName`|string|Primeiro nome do desenvolvedor que reportou o problema.|  
|`IssueState`|string|O estado atual da questão. Os valores possíveis são propostos, abertos, fechados.|  
|`ReportedOn`|DateTime|A data e a hora do assunto foram comunicados.|  
|`Comments`|Coleção de Entidades de [Comentários.](#Comment)|Comentários sobre este assunto.|  
|`Attachments`|Coleção de entidades de [anexo.](api-management-template-data-model-reference.md#Attachment)|Qualquer ligação com o assunto.|  
|`Services`|Coleção de entidades [DaPi.](#API)|As APIs subscritas pelo utilizador que apresentou o problema.|  
  
##  <a name="filtering"></a><a name="Filtering"></a>Filtragem  
 A `filtering` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Pattern`|string|O termo de pesquisa atual; ou `null` se não houver termo de pesquisa.|  
|`Placeholder`|string|O texto a mostrar na caixa de pesquisa quando não há termo de pesquisa especificado.|  
  
##  <a name="header"></a><a name="Header"></a>Cabeçalho  
 Esta secção `parameter` descreve a representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|-----------------|----------|  
|`name`|string|Nome do parâmetro.|  
|`description`|string|Descrição do parâmetro.|  
|`value`|string|Valor do cabeçalho.|  
|`typeName`|string|Tipo de dados de valor cabeçalho.|  
|`options`|string|Opções.|  
|`required`|boolean|Se o cabeçalho é necessário.|  
|`readOnly`|boolean|Se o cabeçalho é apenas para leitura.|  
  
##  <a name="http-request"></a><a name="HTTPRequest"></a>Pedido http  
 Esta secção `request` descreve a representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`description`|string|Descrição do pedido de operação.|  
|`headers`|conjunto de entidades [cabeçalho.](#Header)|Peça cabeçalhos.|  
|`parameters`|conjunto de [parâmetros](#Parameter)|Recolha de parâmetros de pedido de operação.|  
|`representations`|conjunto de [Representação](#Representation)|Recolha de representações de pedido de operação.|  
  
##  <a name="http-response"></a><a name="HTTPResponse"></a>Resposta HTTP  
 Esta secção `response` descreve a representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`statusCode`|inteiro positivo|Código de estado de resposta à operação.|  
|`description`|string|Descrição da resposta da operação.|  
|`representations`|conjunto de [Representação](#Representation)|Recolha de representações de resposta à operação.|  
  
##  <a name="operation"></a><a name="Operation"></a>Operação  
 A `operation` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recursos. Identifica exclusivamente a operação dentro da atual instância de serviço de Gestão API. O valor é um URL relativo `apis/{aid}/operations/{id}` `{aid}` válido no formato `{id}` de onde é identificador aPi e é um identificador de operação. Esta propriedade é apenas leitura.|  
|`name`|string|Nome da operação. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|Descrição da operação. Não deve estar vazio. Pode incluir etiquetas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`scheme`|string|Descreve em que protocolos as operações nesta API podem ser invocadas. Os valores `https`permitidos `http` `https`são, `http`ou ambos e .|  
|`uriTemplate`|string|Modelo de URL relativo identificando o recurso-alvo para esta operação. Pode incluir parâmetros. Exemplo: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|O URL de gateway da API Management que acolhe a API.|  
|`httpMethod`|string|Método operação HTTP.|  
|`request`|[Pedido de HTTP](#HTTPRequest)|Uma entidade que contém detalhes de pedido.|  
|`responses`|matriz de [RESPOSTA HTTP](#HTTPResponse)|Conjunto de entidades de resposta [HTTP.](#HTTPResponse)|  
  
##  <a name="operation-menu"></a><a name="Menu"></a>Menu de funcionamento  
 A `operation menu` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`ApiId`|string|A identificação da atual API.|  
|`CurrentOperationId`|string|A identificação da operação atual.|  
|`Action`|string|O tipo de menu.|  
|`MenuItems`|Recolha de entidades de [menu operação.](#MenuItem)|As operações para a Atual API.|  
  
##  <a name="operation-menu-item"></a><a name="MenuItem"></a>Artigo de menu de operação  
 A `operation menu item` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|A identificação da operação.|  
|`Title`|string|A descrição da operação.|  
|`HttpMethod`|string|O método http da operação.|  
  
##  <a name="paging"></a><a name="Paging"></a>Paging  
 A `paging` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Page`|número|O número da página atual.|  
|`PageSize`|número|Os resultados máximos a mostrar numa única página.|  
|`TotalItemCount`|número|O número de itens para exibição.|  
|`ShowAll`|boolean|Quer sho todos os resultados numa única página.|  
|`PageCount`|número|O número de páginas de resultados.|  
  
##  <a name="parameter"></a><a name="Parameter"></a>Parâmetro  
 Esta secção `parameter` descreve a representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|-----------------|----------|  
|`name`|string|Nome do parâmetro.|  
|`description`|string|Descrição do parâmetro.|  
|`value`|string|Valor do parâmetro.|  
|`options`|matriz da cadeia|Valores definidos para valores de parâmetros de consulta.|  
|`required`|boolean|Especifica se o parâmetro é ou não necessário.|  
|`kind`|número|Se este parâmetro é um parâmetro de caminho (1), ou um parâmetro de corda de consulta (2).|  
|`typeName`|string|Tipo parâmetro.|  
  
##  <a name="product"></a><a name="Product"></a>Produto  
 A `product` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recursos. Identifica exclusivamente o produto dentro da atual instância de serviço de Gestão API. O valor é um URL relativo `products/{pid}` `{pid}` válido no formato de onde está um identificador de produto. Esta propriedade é apenas leitura.|  
|`Title`|string|Nome do produto. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`Description`|string|Descrição do produto. Não deve estar vazio. Pode incluir etiquetas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`Terms`|string|Termos de utilização do produto. Os desenvolvedores que tentarem subscrever o produto serão apresentados e obrigados a aceitar estes termos antes de poderem completar o processo de subscrição.|  
|`ProductState`|número|Especifica se o produto é publicado ou não. Os produtos publicados são detetáveis pelos desenvolvedores no portal de desenvolvimento. Os produtos não publicados são visíveis apenas para administradores.<br /><br /> Os valores admissíveis para o estado do produto são:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Especifica se um utilizador pode ter múltiplas subscrições deste produto ao mesmo tempo.|  
|`MultipleSubscriptionsCount`|número|O número máximo de subscrições deste produto é permitido ao utilizador ter ao mesmo tempo.|  
  
##  <a name="provider"></a><a name="Provider"></a>Provedor  
 A `provider` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Properties`|dicionário de cordas|Propriedades para este fornecedor de autenticação.|  
|`AuthenticationType`|string|O tipo de fornecedor. (Diretório Ativo Azure, login no Facebook, Conta Google, Conta Microsoft, Twitter).|  
|`Caption`|string|Mostrar o nome do fornecedor.|  
  
##  <a name="representation"></a><a name="Representation"></a>Representação  
 Esta secção `representation`descreve um .  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`contentType`|string|Especifica um tipo de conteúdo registado ou personalizado `application/xml`para esta representação, por exemplo, .|  
|`sample`|string|Um exemplo da representação.|  
  
##  <a name="subscription"></a><a name="Subscription"></a>Assinatura  
 A `subscription` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recursos. Identifica exclusivamente a subscrição dentro da atual instância de serviço de Gestão API. O valor é um URL relativo `subscriptions/{sid}` `{sid}` válido no formato de onde é identificador de subscrição. Esta propriedade é apenas leitura.|  
|`ProductId`|string|O identificador de recursos do produto do produto subscrito. O valor é um URL relativo `products/{pid}` `{pid}` válido no formato de onde está um identificador de produto.|  
|`ProductTitle`|string|Nome do produto. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`ProductDescription`|string|Descrição do produto. Não deve estar vazio. Pode incluir etiquetas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`ProductDetailsUrl`|string|URL relativo aos detalhes do produto.|  
|`state`|string|O estado da assinatura. Os estados possíveis são:<br /><br /> - `0 - suspended`– a subscrição está bloqueada e o assinante não pode ligar para nenhuma APIs do produto.<br /><br /> - `1 - active`– a subscrição está ativa.<br /><br /> - `2 - expired`– a subscrição atingiu a data de validade e foi desativada.<br /><br /> - `3 - submitted`– o pedido de subscrição foi feito pelo programador, mas ainda não foi aprovado ou rejeitado.<br /><br /> - `4 - rejected`– o pedido de subscrição foi negado por um administrador.<br /><br /> - `5 - cancelled`– a subscrição foi cancelada pelo programador ou administrador.|  
|`DisplayName`|string|Mostrar o nome da subscrição.|  
|`CreatedDate`|dataTempo|A data em que a subscrição foi criada, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|boolean|Se a subscrição pode ser cancelada pelo utilizador atual.|  
|`IsAwaitingApproval`|boolean|Se a subscrição está à espera de aprovação.|  
|`StartDate`|dataTempo|A data de início da subscrição, no `2014-06-24T16:25:00Z`formato ISO 8601: .|  
|`ExpirationDate`|dataTempo|A data de validade da subscrição, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|dataTempo|A data de notificação para a subscrição, `2014-06-24T16:25:00Z`no formato ISO 8601: .|  
|`primaryKey`|string|A chave principal de subscrição. O comprimento máximo é de 256 caracteres.|  
|`secondaryKey`|string|A chave de subscrição secundária. O comprimento máximo é de 256 caracteres.|  
|`CanBeRenewed`|boolean|Se a subscrição pode ser renovada pelo utilizador atual.|  
|`HasExpired`|boolean|Se a subscrição expirou.|  
|`IsRejected`|boolean|Se o pedido de subscrição foi negado.|  
|`CancelUrl`|string|O Url relativo para cancelar a subscrição.|  
|`RenewUrl`|string|O Url relativo para renovar a subscrição.|  
  
##  <a name="subscription-summary"></a><a name="SubscriptionSummary"></a>Resumo de subscrição  
 A `subscription summary` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recursos. Identifica exclusivamente a subscrição dentro da atual instância de serviço de Gestão API. O valor é um URL relativo `subscriptions/{sid}` `{sid}` válido no formato de onde é identificador de subscrição. Esta propriedade é apenas leitura.|  
|`DisplayName`|string|O nome de exibição da subscrição|  
  
##  <a name="user-account-info"></a><a name="UserAccountInfo"></a>Informação da conta do utilizador  
 A `user account info` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`FirstName`|string|Primeiro nome. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`LastName`|string|Sobrenome. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`Email`|string|Endereço de e-mail. Não deve estar vazio e deve ser único dentro da instância de serviço. O comprimento máximo é de 254 caracteres.|  
|`Password`|string|Senha da conta de utilizador.|  
|`NameIdentifier`|string|Identificador de conta, o mesmo que o e-mail do utilizador.|  
|`ProviderName`|string|Nome do fornecedor de autenticação.|  
|`IsBasicAccount`|boolean|Verdade se esta conta foi registada através de e-mail e senha; falso se a conta foi registada usando um fornecedor.|  
  
##  <a name="user-sign-in"></a><a name="UseSignIn"></a>Sessão de sessão do utilizador  
 A `user sign in` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Email`|string|Endereço de e-mail. Não deve estar vazio e deve ser único dentro da instância de serviço. O comprimento máximo é de 254 caracteres.|  
|`Password`|string|Senha da conta de utilizador.|  
|`ReturnUrl`|string|O URL da página onde o utilizador clicou em iniciar sessão.|  
|`RememberMe`|boolean|Se deve guardar as informações do utilizador atual.|  
|`RegistrationEnabled`|boolean|Se o registo está ativado.|  
|`DelegationEnabled`|boolean|Se o sinal delegado está ativado.|  
|`DelegationUrl`|string|O sinal delegado na url, se ativado.|  
|`SsoSignUpUrl`|string|O único sinal no URL para o utilizador, se presente.|  
|`AuxServiceUrl`|string|Se o utilizador atual for administrador, este é um link para a instância de serviço no portal Azure.|  
|`Providers`|Recolha de Entidades [fornecedoras](#Provider)|Os fornecedores de autenticação para este utilizador.|  
|`UserRegistrationTerms`|string|Termos a que um utilizador deve concordar antes de iniciar sessão.|  
|`UserRegistrationTermsEnabled`|boolean|Se os termos estão habilitados.|  
  
##  <a name="user-sign-up"></a><a name="UserSignUp"></a>Inscrição do utilizador  
 A `user sign up` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Valor utilizado pelo controlo [de inscrição.](api-management-page-controls.md#sign-up)|  
|`Password`|string|Senha da conta de utilizador.|  
|`PasswordVerdictLevel`|número|Valor utilizado pelo controlo [de inscrição.](api-management-page-controls.md#sign-up)|  
|`UserRegistrationTerms`|string|Termos a que um utilizador deve concordar antes de iniciar sessão.|  
|`UserRegistrationTermsOptions`|número|Valor utilizado pelo controlo [de inscrição.](api-management-page-controls.md#sign-up)|  
|`ConsentAccepted`|boolean|Valor utilizado pelo controlo [de inscrição.](api-management-page-controls.md#sign-up)|  
|`Email`|string|Endereço de e-mail. Não deve estar vazio e deve ser único dentro da instância de serviço. O comprimento máximo é de 254 caracteres.|  
|`FirstName`|string|Primeiro nome. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`LastName`|string|Sobrenome. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`UserData`|string|Valor utilizado pelo controlo [de inscrição.](api-management-page-controls.md#sign-up)|  
|`NameIdentifier`|string|Valor utilizado pelo controlo [de inscrição.](api-management-page-controls.md#sign-up)|  
|`ProviderName`|string|Nome do fornecedor de autenticação.|

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o trabalho com modelos, consulte como personalizar o portal de desenvolvimento de [gestão da API utilizando modelos](api-management-developer-portal-templates.md).
