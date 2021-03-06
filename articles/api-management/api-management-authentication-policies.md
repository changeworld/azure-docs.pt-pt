---
title: Políticas de autenticação da API Azure API Microsoft Docs
description: Conheça as políticas de autenticação disponíveis para utilização na Gestão aPI azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 828f738ff8923dc8194e2449f5fb0be74ef45ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473562"
---
# <a name="api-management-authentication-policies"></a>Políticas de autenticação da Gestão de API
Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre a adição e configuração de políticas, consulte [Políticas na Gestão da API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a>Políticas de autenticação

-   [Autenticar com Básico](api-management-authentication-policies.md#Basic) - Autenticar com um serviço backend utilizando a autenticação Básica.

-   [Autenticar com certificado de cliente](api-management-authentication-policies.md#ClientCertificate) - Autenticar com um serviço backend utilizando certificados de cliente.

-   [Autenticar com identidade gerida](api-management-authentication-policies.md#ManagedIdentity) - Autenticar com a [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para o serviço de Gestão API.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a>Autenticar com Básico
 Utilize `authentication-basic` a política para autenticar com um serviço de backend utilizando a autenticação Básica. Esta política define efetivamente o cabeçalho de autorização http para o valor correspondente às credenciais fornecidas na política.

### <a name="policy-statement"></a>Declaração política

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Exemplo

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|autenticação-básico|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|o nome de utilizador|Especifica o nome de utilizador da credencial básica.|Sim|N/D|
|palavra-passe|Especifica a palavra-passe da credencial básica.|Sim|N/D|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada

-   **Âmbitos de política:** todos os âmbitos

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a>Autenticação com certificado de cliente
 Utilize `authentication-certificate` a política para autenticar com um serviço de backend utilizando certificado de cliente. O certificado tem de ser instalado primeiro [na API Management](https://go.microsoft.com/fwlink/?LinkID=511599) e é identificado pela sua impressão digital.

### <a name="policy-statement"></a>Declaração política

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Exemplos

Neste exemplo, o certificado de cliente é identificado pela sua impressão digital.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
Neste exemplo, o certificado de cliente é identificado pelo nome do recurso.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|autenticação-certificado|Elemento de raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|impressão de polegar|A impressão digital para o certificado de cliente.|Ou `thumbprint` `certificate-id` deve estar presente.|N/D|  
|certificado-id|O nome do recurso do certificado.|Ou `thumbprint` `certificate-id` deve estar presente.|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.  
  
-   **Secções políticas:** entrada  
  
-   **Âmbitos de política:** todos os âmbitos  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a>Autenticar com identidade gerida  
 Utilize `authentication-managed-identity` a política para autenticar com um serviço de backend utilizando a identidade gerida do serviço de Gestão API. Esta política utiliza essencialmente a identidade gerida para obter um sinal de acesso do Azure Ative Directory para aceder ao recurso especificado. Após a obtenção com sucesso do símbolo, a apólice definirá o valor do símbolo no `Authorization` cabeçalho utilizando o `Bearer` esquema.
  
### <a name="policy-statement"></a>Declaração política  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exemplo  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Use identidade gerida para autenticar com um serviço de backend
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Utilize a identidade gerida na política de pedido de envio
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|autenticação-gerida identidade |Elemento de raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|recurso|Cadeia. O ID da app da Web Target API (recurso seguro) no Diretório Ativo Azure.|Sim|N/D|  
|nome de saída-token-variável|Cadeia. Nome da variável de contexto que receberá `string`valor simbólico como tipo de objeto . |Não|N/D|  
|ignorar erro|Boolean. Se for `true`definido, o gasoduto de política continuará a executar mesmo que não seja obtido um token de acesso.|Não|false|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.  
  
-   **Secções políticas:** entrada  
  
-   **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos seguintes
Para mais informações que trabalhem com políticas, consulte:

+ [Políticas em Gestão aPi](api-management-howto-policies.md)
+ [Transforme APIs](transform-api.md)
+ [Referência política](api-management-policy-reference.md) para uma lista completa de declarações políticas e suas configurações
+ [Amostras políticas](policy-samples.md)
