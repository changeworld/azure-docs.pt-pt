---
title: Amostras de código para Diretório Ativo Azure v1.0 [ Microsoft Docs
description: Fornece um índice de amostras de código azure Ative (v1.0 endpoint), organizadas por cenário.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ae283529abb3b71ee50fc710dd1ebe0d17a12be0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154836"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Amostras de código de diretório ativo Azure (v1.0 ponto final)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Pode utilizar o Microsoft Azure Ative Directory (Azure AD) para adicionar autenticação e autorização às suas aplicações web e APIs web.

Esta secção fornece links para amostras que pode usar para saber mais sobre o ponto final da AD Azure. Estas amostras mostram como é feito juntamente com os códigos que pode usar nas suas aplicações. Na página da amostra de código, você encontrará tópicos de leitura-me detalhados que ajudam com requisitos, instalação e configuração. E o código é comentado para ajudá-lo a entender as secções críticas.

> [!NOTE]
> Se estiver interessado em amostras de código Azure AD V2, consulte amostras de [código v2.0 por cenário](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Para compreender o cenário básico de cada tipo de amostra, consulte cenários de [autenticação para AD Azure](v1-authentication-scenarios.md).

Também pode contribuir para as nossas amostras no GitHub. Para saber como, consulte [amostras e documentação do Microsoft Azure Ative Directory.](https://github.com/Azure-Samples?page=3&query=active-directory)

## <a name="single-page-applications"></a>Aplicações de página única

Esta amostra mostra como escrever uma aplicação de uma página única protegida com a AD Azure.

 Plataforma | Chama a sua própria API | Chama outra Web API
 -------- |  --------------------- | ------------------ 
![Esta imagem mostra o logótipo JavaScript](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Esta imagem mostra o logótipo Angular JS](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Aplicações Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplicações Web que saem nos utilizadores, chamando Microsoft Graph, ou um Web API com a identidade do utilizador

As seguintes amostras ilustram aplicações web que assinam utilizadores. Algumas destas aplicações também chamam o Microsoft Graph ou a sua própria Web API, em nome do utilizador inscrito.

 Plataforma | Apenas sinais nos utilizadores | Chamadas Microsoft Graph | Chama outro ASP.NET ou ASP.NET Core 2.0 Web API
 -------- | ------------------- | --------------------- | -------------------------
![Esta imagem mostra o logótipo ASP.NET](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Esta imagem mostra o logótipo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 |  </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD Graph) |
![Esta imagem mostra o logótipo python](media/sample-v2-code/logo-python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Esta imagem mostra o registo de Java](media/sample-v2-code/logo-java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Esta imagem mostra o logótipo PHP](media/sample-v2-code/logo-php.png) | | [php-grafapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplicações web que demonstram controlo de acesso baseado em papéis (autorização)

As amostras que se seguem mostram como implementar o controlo de acesso baseado em funções (RBAC). O RBAC é utilizado para restringir as permissões de certas funcionalidades numa aplicação web a determinados utilizadores. Os utilizadores são autorizados dependendo de pertencerem a um **grupo Azure AD** ou de terem uma determinada **função**de aplicação.

Plataforma | Sample |
 -------- | ------------------- |
![Esta imagem mostra o logótipo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Uma aplicação web .NET 4.5 MVC que utiliza **funções** de AD Azure para autorização

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Aplicações de desktop e clientes públicos móveis que chamam Microsoft Graph ou uma Web API

As seguintes amostras ilustram aplicações de clientes públicos (aplicações deskto/pmobile) que acedem ao Microsoft Graph ou a uma API Web em nome de um utilizador. Dependendo dos dispositivos e plataformas, as aplicações podem inscrever os utilizadores de diferentes formas (fluxos/subvenções):

- Interativamente
- Silenciosamente (com autenticação integrada do Windows no Windows, ou nome de utilizador/palavra-passe)
- Delegando o sessão interativo noutro dispositivo (fluxo de código do dispositivo utilizado em dispositivos que não fornecem controlos web)

Aplicação do cliente | Plataforma | Fluxo/Concessão | Chamadas Microsoft Graph | Chama um ASP.NET ou ASP.NET Core 2.x Web API
------------------ | -------- | ---------- | -------------------- | -------------------------
Ambiente de trabalho (WPF)           | ![Esta imagem mostra o logótipo .NET/C#](media/sample-v2-code/logo-net.png)  | Interativo | Parte do [dotnet-nativo-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-nativo-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validação](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Móvel (UWP)            | .![Esta imagem mostra o .NET/C#/UWP](media/sample-v2-code/logo-windows.png)   | Interativo | [dotnet-nativo-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Esta amostra utiliza [WAM,](/windows/uwp/security/web-account-manager)não [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (aplicação UWP usando ADAL.NET para chamar um único inquilino Web API) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (aplicação UWP usando ADAL.NET para chamar uma API Web multi-inquilino)|
Mobile (Android, iOS, UWP)   | ![Esta imagem mostra o .NET/C# (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interativo | [dotnet-nativo-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | ![Esta imagem mostra o logótipo Android](media/sample-v2-code/logo-android.png) | Interativo |   [android](https://github.com/Azure-Samples/active-directory-android) |
Móvel (iOS)           | ![Esta imagem mostra iOS / Objetivo C ou Swift](media/sample-v2-code/logo-ios.png) | Interativo |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Ambiente de trabalho (Consola)          | ![Esta imagem mostra o logótipo .NET/C#](media/sample-v2-code/logo-net.png) | Nome de utilizador / Palavra-passe </p>  Autenticação Integrada do Windows. | | [dotnet-nativo-cabeça sem cabeça](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Ambiente de trabalho (Consola)          | ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo-java.png) | Nome de utilizador / Palavra-passe | | [java-nativo-cabeça sem cabeça](https://github.com/Azure-Samples/active-directory-java-native-headless)
Ambiente de trabalho (Consola)           | ![Esta imagem mostra o logótipo .NET Core/C#](media/sample-v2-code/logo-netcore.png) | Fluxo de código do dispositivo | | [dotnet-dispositivoperfil](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Aplicações Daemon (aceder a APIs web com a identidade da aplicação)

As seguintes amostras mostram aplicações de desktop ou web que acedem ao Microsoft Graph ou a uma API web sem utilizador (com a identidade da aplicação).

Aplicação do cliente | Plataforma | Fluxo/Concessão | Chama um ASP.NET ou ASP.NET Core 2.0 Web API
------------------ | -------- | ---------- | -------------------- 
Aplicação Daemon (Consola)          | ![Esta imagem mostra o logótipo .NET](media/sample-v2-code/logo-netframework.png) | Credenciais de cliente com segredo de aplicação ou certificado | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificado-credencial](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Aplicação Daemon (Consola)         | ![Esta imagem mostra o logótipo .NET](media/sample-v2-code/logo-netcore.png) | Credenciais de cliente com certificado| [dotnetcore-daemon-certificado-credencial](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET Web App  | ![Esta imagem mostra o logótipo .NET](media/sample-v2-code/logo-netframework.png) | Credenciais de cliente | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>APIs da Web

### <a name="web-api-protected-by-azure-active-directory"></a>Web API protegida por Diretório Ativo Azure

A amostra que se segue mostra como proteger um API web node.js com AD Azure.

Nas secções anteriores deste artigo, também pode encontrar outras amostras que ilustram uma aplicação de cliente **chamando** uma ASP.NET ou ASP.NET Core **Web API**. Estas amostras não são mencionadas novamente nesta secção, mas você vai encontrá-las na última coluna das tabelas acima ou abaixo

| Plataforma | Sample |
|--------|-------------------|
| ![Esta imagem mostra o logótipo Dode.js](media/sample-v2-code/logo-nodejs.png)  | [nó-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Web API chamando Microsoft Graph ou outro Web API

As seguintes amostras demonstram uma API web que chama outra API web. A segunda amostra mostra como lidar com o Acesso Condicional.

| Plataforma |  Chamadas Microsoft Graph | Chama outro ASP.NET ou ASP.NET Core 2.0 Web API |
| -------- |  --------------------- | ------------------------- |
| ![Esta imagem mostra o logótipo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Outras amostras do Microsoft Graph

Para amostras e tutoriais que demonstrem diferentes padrões de utilização para a Microsoft Graph API, incluindo a autenticação com a AD Azure, consulte as [amostras comunitárias do Microsoft Graph & Tutoriais](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Consulte também

- [Guia de desenvolvedor estoma ativo azure](v1-overview.md)
- [Bibliotecas de autenticação de diretório ativo Azure](active-directory-authentication-libraries.md)
- [Microsoft Graph API conceptual e referência](https://docs.microsoft.com/graph/use-the-api)
