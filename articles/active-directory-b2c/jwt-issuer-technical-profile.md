---
title: Defina um perfil técnico para um emitente JWT numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico para um emitente web JSON (JWT) numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c23648d70192607b2a5b977dcdd445931e995154
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671791"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico para um emitente de token JWT numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) emite vários tipos de fichas de segurança à medida que processa cada fluxo de autenticação. Um perfil técnico para um emissor de fichas JWT emite um símbolo JWT que é devolvido à aplicação do partido que depende. Normalmente, este perfil técnico é o último passo de orquestração na jornada do utilizador.

## <a name="protocol"></a>Protocolo

O **atributo** nome do elemento **protocolo** `None`tem de ser definido para . Desajuste o elemento `JWT` **OutputTokenFormat** para .

O exemplo que se `JwtIssuer`segue mostra um perfil técnico para:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Entrada, saída e persistir reclamações

Os elementos **InputClaims**, **OutputClaims**e **PersistClaims** estão vazios ou ausentes. Os **elementos InutputClaimsTransformations** and **OutputClaimsTransformations** também estão ausentes.

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Sim | A alegação que deve ser utilizada como a alegação de identidade do utilizador dentro dos códigos de autorização OAuth2 e fichas de atualização. Por predefinição, deve `objectId`defini-lo para, a menos que especifique um tipo de reclamação de SubjectNamingInfo diferente. |
| EnviarTokenResponseBodyWithJsonNumbers | Não | Sempre definido `true`para . Para o formato legado onde os valores numéricos são `false`dados como cordas em vez de números JSON, definidopara . Este atributo é necessário para clientes que tenham tomado uma dependência de uma implementação anterior que devolveu propriedades como cordas. |
| token_lifetime_secs | Não | Acesso a toda a vida. A vida útil do token portador da OAuth 2.0 usado para ter acesso a um recurso protegido. O predefinido é de 3.600 segundos (1 hora). O mínimo (inclusivo) é de 300 segundos (5 minutos). O máximo (inclusivo) é de 86.400 segundos (24 horas). |
| id_token_lifetime_secs | Não | Identidade token vida inteira. O predefinido é de 3.600 segundos (1 hora). O mínimo (inclusivo) é de 300 segundos (5 minutos). O máximo (inclusivo) é de 86.400 (24 horas). |
| refresh_token_lifetime_secs | Não | Refrescar token vida. O período máximo de tempo antes do qual um token de atualização pode ser utilizado para adquirir um novo sinal de acesso, se a sua aplicação tivesse sido concedida o âmbito de offline_access. O predefinido é de 120.9600 segundos (14 dias). O mínimo (inclusivo) é de 86.400 segundos (24 horas). O máximo (inclusivo) é de 7.776.000 segundos (90 dias). |
| rolling_refresh_token_lifetime_secs | Não | Refrescar a janela deslizante de fichas. Após este período de corrido, o utilizador é obrigado a reautenticar, independentemente do período de validade do mais recente token de atualização adquirido pela aplicação. Se não quiser impor uma vida útil deslizante, detete o valor de allow_infinite_rolling_refresh_token para `true`. O predefinido é de 7.776.000 segundos (90 dias). O mínimo (inclusivo) é de 86.400 segundos (24 horas). O máximo (inclusive) é de 31.536.000 segundos (365 dias). |
| allow_infinite_rolling_refresh_token | Não | Se estiver `true`programado para, a janela deslizante de token refrescante nunca expira. |
| Padrão de reclamação de emissão | Não | Controla a alegação do Emitente (iss). Um dos valores:<ul><li>AuthorityAndTenantGuid - A alegação do ISS `login.microsoftonline` `tenant-name.b2clogin.com`inclui o seu nome\/de domínio, como ou, e o seu identificador de inquilino https: /login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - A alegação do ISS `login.microsoftonline` `tenant-name.b2clogin.com`inclui o seu nome de domínio, como ou, o seu identificador de inquilino e o seu nome de política partidária. https:\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> Valor predefinido: AuthorityAndTenantGuid |
| AutenticaçãoContextoReferênciaPadrão de Reclamações | Não | Controla `acr` o valor da reclamação.<ul><li>Nenhum - Azure AD B2C não emite a alegação do ACR</li><li>PolicyId - `acr` a alegação contém o nome da política</li></ul>As opções para definir este valor são TFP (política-quadro de confiança) e ACR (referência de contexto de autenticação). Recomenda-se definir este valor para a TFP, `<Item>` para `Key="AuthenticationContextReferenceClaimPattern"` definir o `None`valor, garantir o com o existente e o valor é . Na sua política partidária `<OutputClaims>` de base, adicione `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`o item, adicione este elemento . Certifique-se também de que a sua política contém o tipo de reclamação`<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| Não | O identificador de uma viagem de utilizador que deve ser executada `/token` durante a [atualização de um](authorization-code-flow.md#4-refresh-the-token) pedido de acesso ao post de acesso ao ponto final. |

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento CryptographicKeys contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| issuer_secret | Sim | O certificado X509 (conjunto de teclas RSA) para usar para assinar o símbolo JWT. Esta é `B2C_1A_TokenSigningKeyContainer` a chave que cofiguraem em [Começar com políticas personalizadas.](custom-policy-get-started.md) |
| issuer_refresh_token_key | Sim | O certificado X509 (conjunto de teclas RSA) para utilizar para encriptar o token de atualização. Configuraste a `B2C_1A_TokenEncryptionKeyContainer` chave em [Começar com políticas personalizadas](custom-policy-get-started.md) |














