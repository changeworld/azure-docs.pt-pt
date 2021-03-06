---
title: Ficheiro de configuração Android MSAL / Azure
titleSuffix: Microsoft identity platform
description: Uma visão geral do ficheiro de configuração da Biblioteca de Autenticação do Android Microsoft (MSAL), que representa a configuração de uma aplicação no Diretório Ativo do Azure.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050383"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Ficheiro de configuração da Biblioteca de Autenticação do Android Microsoft

A Biblioteca de Autenticação do Microsoft Android (MSAL) envia com um [ficheiro JSON](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) de configuração padrão que personaliza para definir o comportamento da sua aplicação de cliente público para coisas como a autoridade padrão, que autoridades irá usar, e assim por diante.

Este artigo irá ajudá-lo a compreender as várias definições no ficheiro de configuração e como especificar o ficheiro de configuração a utilizar na sua aplicação baseada em MSAL.

## <a name="configuration-settings"></a>Definições de configuração

### <a name="general-settings"></a>Definições gerais

| Propriedade | Tipo de Dados | Necessário | Notas |
|-----------|------------|-------------|-------|
| `client_id` | Cadeia | Sim | Id do cliente da sua aplicação na página de inscrição da [aplicação](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Cadeia | Sim | Redirecionamento URI da sua aplicação a partir da página de inscrição da [aplicação](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `authorities` | >\<da Autoridade de Lista | Não | A lista de autoridades que a sua app precisa |
| `authorization_user_agent` | Agente de Autorização (enum) | Não | Valores `DEFAULT`possíveis: , `BROWSER``WEBVIEW` |
| `http` | HttpConfiguração | Não | Configurar `HttpUrlConnection` `connect_timeout` e`read_timeout` |
| `logging` | Configuração madeireira | Não | Especifica o nível de registo. As configurações `pii_enabled`opcionais incluem: `log_level`, que `ERROR` `WARNING`tem `INFO`um `VERBOSE`valor booleano, e, que leva, , ou . |

### <a name="client_id"></a>client_id

O ID do cliente ou id da aplicação que foi criado quando registou a sua aplicação.

### <a name="redirect_uri"></a>redirect_uri

O URI redirecionado que registou quando registou o seu pedido. Se o URI redirecionar para uma aplicação de corretor, consulte [o Redirect URI para aplicações de clientes públicos](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) para garantir que está a usar o formato URI de redirecionamento correto para a sua aplicação de corretor.

### <a name="authorities"></a>autoridades

A lista de autoridades que são conhecidas e confiáveis por si. Além das autoridades aqui listadas, a MSAL também questiona a Microsoft para obter uma lista de nuvens e autoridades conhecidas pela Microsoft. Nesta lista de autoridades, especifique o tipo de autoridade `"audience"`e quaisquer parâmetros opcionais adicionais, tais como , que devem alinhar-se com o público da sua aplicação com base no registo da sua aplicação. Segue-se uma lista de autoridades:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Autoridade mapada aAD & público para pontos finais da plataforma de identidade da Microsoft

| Tipo | Audiência | ID do inquilino | Authority_Url | Ponto final resultante | Notas |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common`é um pseudónimo inquilino para onde está a conta. Como um inquilino específico do Azure Ative Directory ou o sistema de conta microsoft. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Apenas as contas presentes no contoso.com podem adquirir um símbolo. Qualquer domínio verificado, ou o lIfA do inquilino, pode ser usado como identificação do inquilino. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | Apenas as contas do Diretório Ativo Azure podem ser utilizadas com este ponto final. As contas da Microsoft podem ser membros de organizações. Para adquirir um símbolo usando uma conta microsoft para um recurso de uma organização, especifique o inquilino organizacional do qual você deseja o símbolo. |
| AAD | Conta PessoalMicrosoft | | | `https://login.microsoftonline.com/consumers` | Apenas as contas da Microsoft podem utilizar este ponto final. |
| B2C | | | Ver Ponto Final Resultante | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Apenas contas presentes no contoso.onmicrosoft.com inquilino podem adquirir um símbolo. Neste exemplo, a política B2C faz parte do caminho URL da Autoridade. |

> [!NOTE]
> A validação da autoridade não pode ser ativada e desativada no MSAL.
> As autoridades são conhecidas como o desenvolvedor especificado através da configuração ou conhecidos pela Microsoft através de metadados.
> Se a MSAL receber um pedido de um símbolo `MsalClientException` a `UnknownAuthority` uma autoridade desconhecida, um tipo de resultados.

#### <a name="authority-properties"></a>Propriedades da autoridade

| Propriedade | Tipo de dados  | Necessário | Notas |
|-----------|-------------|-----------|--------|
| `type` | Cadeia | Sim | Espelha o público ou a conta digite os alvos da sua aplicação. Valores `AAD`possíveis:`B2C` |
| `audience` | Objeto | Não | Só se aplica`AAD`quando digita= . Especifica a identidade dos alvos da sua aplicação. Use o valor do registo da sua aplicação |
| `authority_url` | Cadeia | Sim | Só é exigido`B2C`quando digitar= . Especifica o URL ou a política de autoridade que a sua aplicação deve usar  |
| `default` | boolean | Sim | É `"default":true` necessário um único quando uma ou mais autoridades são especificadas. |

#### <a name="audience-properties"></a>Propriedades do Público

| Propriedade | Tipo de Dados  | Necessário | Notas |
|-----------|-------------|------------|-------|
| `type` | Cadeia | Sim | Especifica o público que a sua aplicação quer atingir. Valores `AzureADandPersonalMicrosoftAccount`possíveis: , `PersonalMicrosoftAccount`, `AzureADMultipleOrgs``AzureADMyOrg` |
| `tenant_id` | Cadeia | Sim | Só é `"type":"AzureADMyOrg"`necessário quando. Opcional para `type` outros valores. Este pode ser um `contoso.com`domínio de inquilino, `72f988bf-86f1-41af-91ab-2d7cd011db46`como, ou uma identificação de inquilino como) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Indica se deve utilizar uma webview incorporada ou o navegador predefinido no dispositivo, ao assinar numa conta ou autorizar o acesso a um recurso.

Valores possíveis:
- `DEFAULT`: Prefere o navegador do sistema. Utiliza a visão web incorporada se um navegador não estiver disponível no dispositivo.
- `WEBVIEW`: Utilize a vista web incorporada.
- `BROWSER`: Utiliza o navegador predefinido no dispositivo.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Para clientes que suportem várias nuvens nacionais, especifique `true`. A plataforma de identidade da Microsoft irá então redirecionar automaticamente para a nuvem nacional correta durante a autorização e resgate simbólico. Pode determinar a nuvem nacional da conta assinada examinando a `AuthenticationResult`autoridade associada ao . Note que `AuthenticationResult` não fornece o endereço final específico da nuvem nacional do recurso para o qual solicita um símbolo.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Uma booleana que indica se está a usar um corretor de identidade microsoft compatível com o redirecionamento URI. Reserve `false` para se não quiser utilizar o corretor dentro da sua aplicação.

Se estiver a usar a Autoridade AAD `"MicrosoftPersonalAccount"`com audiência definida para , o corretor não será usado.

### <a name="http"></a>http

Configure as configurações globais para os intervalos de tempo em HTTP, tais como:

| Propriedade | Tipo de dados | Necessário | Notas |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Não | Tempo em milissegundos |
| `read_timeout` | int | Não | Tempo em milissegundos |

### <a name="logging"></a>registo

As seguintes configurações globais são para o abate:

| Propriedade | Tipo de Dados  | Necessário | Notas |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | Não | Se emitir dados pessoais |
| `log_level`   | boolean | Não | Que log mensagens para saída |
| `logcat_enabled` | boolean | Não | Se deve sair para registar o gato para além da interface de exploração madeireira |

### <a name="account_mode"></a>account_mode

Especifica quantas contas podem ser usadas dentro da sua aplicação de cada vez. Os valores possíveis são:

- `MULTIPLE`(Predefinido)
- `SINGLE`

Construir um `PublicClientApplication` modo de conta que não corresponda a esta definição resultará numa exceção.

Para obter mais informações sobre as diferenças entre contas individuais e múltiplas, consulte [aplicações de conta única e múltipla .](single-multi-account.md)

### <a name="browser_safelist"></a>browser_safelist

Uma lista de navegadores compatíveis com mSAL. Estes navegadores lidam corretamente com redirecionamentos para intenções personalizadas. Pode adicionar a esta lista. O predefinido é fornecido na configuração predefinida mostrada abaixo.
``
## <a name="the-default-msal-configuration-file"></a>O ficheiro de configuração padrão do MSAL

A configuração padrão do MSAL que os navios com MSAL são mostradas abaixo. Pode ver a versão mais recente no [GitHub.](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)

Esta configuração é complementada por valores que fornece. Os valores que fornece sobrepõem-se aos incumprimentos.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Configuração básica de exemplo

O exemplo seguinte ilustra uma configuração básica que especifica o ID do cliente, redireciona o URI, se um redirecionamento de corretor está registado e uma lista de autoridades.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Como utilizar um ficheiro de configuração

1. Criar um ficheiro de configuração. Recomendamos que crie o `res/raw/auth_config.json`seu ficheiro de configuração personalizado em . Mas pode colocá-lo em qualquer lugar que quiser.
2. Diga à MSAL onde procurar a `PublicClientApplication`sua configuração quando construir o . Por exemplo:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
