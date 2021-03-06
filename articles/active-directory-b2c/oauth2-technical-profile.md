---
title: Defina um perfil técnico OAuth2 numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico OAuth2 numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 86ec7a5745a58546faf6f0ff15d6dc5f452baa88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184048"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico OAuth2 numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) presta apoio ao fornecedor de identidade do protocolo OAuth2. O AAuth2 é o protocolo principal de autorização e autenticação delegada. Para mais informações, consulte o [RFC 6749 O Quadro de Autorização 2.0 da OAuth .](https://tools.ietf.org/html/rfc6749) Com um perfil técnico OAuth2, pode federar-se com um fornecedor de identidade baseado em OAuth2, como o Facebook. Federar com um fornecedor de identidade permite que os utilizadores inscrevam-se com as suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocolo

O **atributo** nome do elemento **protocolo** `OAuth2`tem de ser definido para . Por exemplo, o protocolo para o perfil técnico `OAuth2` **Facebook-OAUTH** é:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Reclamações de entrada

Os elementos **InputClaims** e **InputClaimsTransformations** não são necessários. Mas pode querer enviar parâmetros adicionais ao seu fornecedor de identidade. O exemplo seguinte adiciona o parâmetro **de** cadeia `contoso.com` de consulta domain_hint com o valor do pedido de autorização.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Reclamações de produção

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo fornecedor de identidade OAuth2. Poderá ter de mapear o nome da reclamação definida na sua política para o nome definido no fornecedor de identidade. Também pode incluir reclamações que não sejam devolvidas pelo `DefaultValue` fornecedor de identidade desde que derpor o atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

O exemplo que se segue mostra as reclamações devolvidas pelo fornecedor de identidade do Facebook:

- A **first_name** reclamação está mapeada para a reclamação **do nome dado.**
- A **alegação last_name** está mapeada para a alegação de **sobrenome.**
- A reivindicação do nome do **ecrã** sem mapeamento de nomes.
- A reclamação **de e-mail** sem mapeamento de nome.

O perfil técnico também devolve reclamações que não são devolvidas pelo fornecedor de identidade:

- A **alegação do Fornecedor de Identidade** que contém o nome do fornecedor de identidade.
- A **autenticaçãoSource** reclama com um valor predefinido de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_id | Sim | O identificador de aplicação do fornecedor de identidade. |
| IdTokenAudience | Não | O público do id_token. Se especificado, o Azure AD B2C verifica se o token está numa reclamação devolvida pelo fornecedor de identidade e é igual à especificada. |
| authorization_endpoint | Sim | O URL do ponto final de autorização de acordo com o RFC 6749. |
| AccessTokenEndpoint | Sim | O URL do ponto final simbólico de acordo com o RFC 6749. |
| Ponto final de reclamações | Sim | O URL do ponto final da informação do utilizador de acordo com o RFC 6749. |
| AccessTokenResponseFormat | Não | O formato da chamada de ponto final de acesso. Por exemplo, o Facebook requer um método HTTP GET, mas a resposta acessa está no formato JSON. |
| Parâmetros adicionais requestqueryparam | Não | Parâmetros de consulta de pedido adicional. Por exemplo, pode querer enviar parâmetros adicionais ao seu fornecedor de identidade. Pode incluir vários parâmetros usando delimitador vírlimitador de vírvia. |
| ClaimsEndpointAccessTokenName | Não | O nome do parâmetro de corda de consulta de acesso. Alguns fornecedores de identidade apoiam o pedido GET HTTP. Neste caso, o token portador é enviado usando um parâmetro de corda de consulta em vez do cabeçalho de autorização. |
| ClaimsEndpointFormatName | Não | O nome do parâmetro de corda de consulta de formato. Por exemplo, pode definir `format` o nome como neste `https://api.linkedin.com/v1/people/~?format=json`ponto final de reclamações do LinkedIn . |
| ClaimsEndpointFormat | Não | O valor do parâmetro de corda de consulta de formato. Por exemplo, pode definir `json` o valor como neste `https://api.linkedin.com/v1/people/~?format=json`ponto final de reclamações do LinkedIn . |
| ProviderName | Não | O nome do fornecedor de identidade. |
| response_mode | Não | O método que o fornecedor de identidade utiliza para enviar o resultado de volta para O Azure AD B2C. Valores `query`possíveis: `form_post` , `fragment`(predefinido) ou . |
| scope | Não | O âmbito do pedido que é definido de acordo com a especificação do fornecedor de identidade OAuth2. Tais `openid`como, `profile` `email`e . |
| HttpBinding | Não | O http esperado que se liga ao token de acesso e reclama pontos finais simbólicos. Valores `GET` possíveis: ou `POST`.  |
| Nome do ErrorErrorErrorParam | Não | O nome do parâmetro que contém a mensagem de erro devolvida em HTTP 200 (Ok). |
| ExtraParamsInAccessTokenEndpointResponse | Não | Contém os parâmetros extra que podem ser devolvidos na resposta do **AccessTokenEndpoint** por alguns fornecedores de identidade. Por exemplo, a resposta do **AccessTokenEndpoint** contém `openid`um parâmetro extra como , que é um parâmetro obrigatório além do access_token numa cadeia de consulta de pedido **de ReclamaçõesEndpoint.** Vários nomes de parâmetros devem ser escapados e separados pela vírposta '', delimitador. |
| ExtraParamsInClaimsEndpointRequest | Não | Contém os parâmetros extra que podem ser devolvidos no pedido **de ClaimsEndpoint** por alguns fornecedores de identidade. Vários nomes de parâmetros devem ser escapados e separados pela vírposta '', delimitador. |
| Incluir Requerer Resolução de Reclamações  | Não | Para pedidos de entrada e saída, especifica se a resolução de [sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores `true`possíveis: ou `false`  (padrão). Se pretender utilizar uma reclamação no perfil técnico, desempente-a para `true`. |
| ResolveJsonPathsInJsonTokens  | Não | Indica se o perfil técnico resolve os caminhos da JSON. Valores `true`possíveis: ou `false` (padrão). Utilize estes metadados para ler dados de um elemento JSON aninhado. Num [OutputClaim,](technicalprofiles.md#outputclaims)detete o `PartnerClaimType` elemento de caminho JSON que pretende ser de si. Por exemplo: `firstName.localized` `data.0.to.0.email`ou .|

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | O segredo do cliente da aplicação do fornecedor de identidade. A chave criptográfica só é **response_types** necessária se os `code`metadados response_types estiverem definidos para . Neste caso, o Azure AD B2C faz outra chamada para trocar o código de autorização por um sinal de acesso. Se os metadados `id_token`estiverem definidos, pode omitir a chave criptográfica. |

## <a name="redirect-uri"></a>URI de Redirecionamento

Quando configurar o URL redirecionado do `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`seu fornecedor de identidade, introduza . Certifique-se de substituir o **inquilino** pelo nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com) e **políticaId** pelo identificador da sua política (por exemplo, b2c_1a_policy). O URI redirecionado tem de estar em todas as minúsculas.

Se estiver a utilizar o domínio **b2clogin.com** em vez de **login.microsoftonline.com** certifique-se de utilizar b2clogin.com em vez de login.microsoftonline.com.

Exemplos:

- [Adicione o Google+ como fornecedor de identidade OAuth2 usando políticas personalizadas](identity-provider-google-custom.md)













