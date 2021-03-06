---
title: Ativar a verificação de senha única (OTP)
titleSuffix: Azure AD B2C
description: Aprenda a configurar um cenário de senha única (OTP) utilizando políticas personalizadas Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd5fed45332c73c633db1137bdc23aea66fd3403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332772"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Defina um perfil técnico de senha única numa política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte para gerir a geração e verificação de uma senha única. Utilize um perfil técnico para gerar um código e, em seguida, verifique esse código mais tarde.

O perfil técnico de senha única também pode devolver uma mensagem de erro durante a verificação do código. Desenhe a integração com a senha única utilizando um perfil técnico de **Validação**. Um perfil técnico de validação chama o perfil técnico de senha única para verificar um código. O perfil técnico de validação valida os dados fornecidos pelo utilizador antes da viagem do utilizador continuar. Com o perfil técnico de validação, uma mensagem de erro é exibida numa página autoafirmada.

## <a name="protocol"></a>Protocolo

O **atributo** nome do elemento **protocolo** `Proprietary`tem de ser definido para . O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolos que é utilizado pelo Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo que se segue mostra um perfil técnico de senha única:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Gerar código

O primeiro modo deste perfil técnico é gerar um código. Abaixo estão as opções que podem ser configuradas para este modo.

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações necessárias para enviar ao fornecedor de protocolos de senha única. Também pode mapear o nome da sua reclamação para o nome definido abaixo.

| ReivindicaçãoReferenceid | Necessário | Descrição |
| --------- | -------- | ----------- |
| identificador | Sim | O identificador para identificar o utilizador que precisa de verificar o código mais tarde. É comumente usado como o identificador do destino para onde o código é entregue, por exemplo, endereço de e-mail ou número de telefone. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **inputClaimsTransformation** que são usados para modificar as reclamações de entrada ou gerar novos antes de enviar para o fornecedor de protocolo de senha única.

### <a name="output-claims"></a>Reclamações de produção

O elemento **OutputClaims** contém uma lista de reclamações geradas pelo fornecedor de protocolos de senha única. Também pode mapear o nome da sua reclamação para o nome definido abaixo.

| ReivindicaçãoReferenceid | Necessário | Descrição |
| --------- | -------- | ----------- |
| otpGenerated | Sim | O código gerado cuja sessão é gerida pelo Azure AD B2C. |

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

### <a name="metadata"></a>Metadados

As seguintes definições podem ser utilizadas para configurar o modo de geração de códigos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Não | Tempo em segundos até a expiração do código. Mínimo: `60`; Máximo: `1200`; Predefinição: `600`. |
| Comprimento de código | Não | Comprimento do código. O valor predefinido é `6`. |
| Conjunto de caracteres | Não | O conjunto de caracteres para o código, formatado para uso numa expressão regular. Por exemplo, `a-z0-9A-Z`. O valor predefinido é `0-9`. O conjunto de caracteres deve incluir um mínimo de 10 caracteres diferentes no conjunto especificado. |
| Tentativas de Numretry | Não | O número de tentativas de verificação antes do código é considerado inválido. O valor predefinido é `5`. |
| Operação | Sim | A operação a ser realizada. Valor possível: `GenerateCode`. |
| Reutilização Do SameCode | Não | Se um código duplicado deve ser dado em vez de gerar um novo código quando o código não expirou e ainda é válido. O valor predefinido é `false`. |

### <a name="example"></a>Exemplo

O exemplo `TechnicalProfile` que se segue é utilizado para gerar um código:

```XML
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verificar código

O segundo modo deste perfil técnico é verificar um código. Abaixo estão as opções que podem ser configuradas para este modo.

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações necessárias para enviar ao fornecedor de protocolos de senha única. Também pode mapear o nome da sua reclamação para o nome definido abaixo.

| ReivindicaçãoReferenceid | Necessário | Descrição |
| --------- | -------- | ----------- |
| identificador | Sim | O identificador para identificar o utilizador que já gerou um código. É comumente usado como o identificador do destino para onde o código é entregue, por exemplo, endereço de e-mail ou número de telefone. |
| otpToVerificar | Sim | O código de verificação fornecido pelo utilizador. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **inputClaimsTransformation** que são usados para modificar as reclamações de entrada ou gerar novos antes de enviar para o fornecedor de protocolo de senha única.

### <a name="output-claims"></a>Reclamações de produção

Não existem pedidos de saída fornecidos durante a verificação do código deste prestador de protocolos.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

### <a name="metadata"></a>Metadados

As seguintes definições podem ser utilizadas para codificar o modo de verificação:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | A operação a ser realizada. Valor possível: `VerifyCode`. |


### <a name="ui-elements"></a>Elementos da IU

Os seguintes metadados podem ser utilizados para configurar as mensagens de erro apresentadas após falha de verificação de código. Os metadados devem ser configurados no perfil técnico [autoafirmado.](self-asserted-technical-profile.md) As mensagens de erro podem ser [localizadas.](localization-string-ids.md#one-time-password-error-messages)

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| UserMessageifSessionNão existe | Não | A mensagem a mostrar ao utilizador se a sessão de verificação de código tiver expirado. Ou o código expirou ou o código nunca foi gerado para um determinado identificador. |
| UserMessageIfMaxRetryTryTry | Não | A mensagem para mostrar ao utilizador se tiver excedido as tentativas máximas de verificação permitidas. |
| UserMessageIfInvalidcode | Não | A mensagem para mostrar ao utilizador se tiver fornecido um código inválido. |
|UserMessageifSessionConflict|Não| A mensagem a mostrar ao utilizador se o código não puder ser verificado.|

### <a name="example"></a>Exemplo

É utilizado `TechnicalProfile` o seguinte exemplo para a verificação de um código:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Passos seguintes

Consulte o seguinte artigo, por exemplo, de utilização de um perfil técnico de senha única com verificação personalizada de e-mail:

- [Verificação personalizada de e-mail no Diretório Ativo Azure B2C](custom-email.md)

