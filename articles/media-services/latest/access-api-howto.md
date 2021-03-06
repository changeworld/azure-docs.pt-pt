---
title: Começar com a autenticação azure AD
description: Saiba como aceder à autenticação azure Ative Directory (Azure AD) para consumir a API azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 6e1ab30e8b4cf44f7d1f82fd94fb9bf854915557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478256"
---
# <a name="get-credentials-to-access-media-services-api"></a>Obtenha credenciais para aceder à API dos Serviços de Media  

Quando utiliza a autenticação Azure AD para aceder à API dos Serviços De Comunicação Social, tem duas opções de autenticação:

- **Autenticação do principal de serviço** (recomendado)

    Autenticar um serviço. As aplicações que usam normalmente este método de autenticação são aplicações que executam serviços de daemon, serviços de nível médio ou empregos programados: aplicações web, aplicações de função, aplicações lógicas, APIs ou um microserviço.
- **Autenticação do utilizador**

    Autenticar uma pessoa que está a usar a app para interagir com os recursos dos Media Services. A aplicação interativa deve primeiro solicitar ao utilizador credenciais. Um exemplo é uma aplicação de consola de gestão usada por utilizadores autorizados para monitorizar trabalhos de codificação ou streaming ao vivo. 

Este artigo descreve passos para obter credenciais para aceder à API dos Serviços de Media. Escolha entre os seguintes separadores.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver uma conta, comece com um [teste gratuito azure.](https://azure.microsoft.com/pricing/free-trial/) 
- Uma conta dos Media Services. Para mais informações, consulte [Criar uma conta Azure Media Services utilizando o portal Azure](create-account-howto.md).

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

### <a name="api-access"></a>Acesso a API 

A página de **acesso API** permite selecionar o método de autenticação que pretende utilizar para se ligar à API. A página também fornece os valores que precisa para ligar à API.

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta Media Services.
2. Selecione como ligar à API dos Serviços de Media.
3. No âmbito **do Connect to Media Services API,** selecione a versão API dos Media Services a que pretende ligar (V3 é a versão mais recente do serviço).

### <a name="service-principal-authentication--recommended"></a>Autenticação do principal de serviço (recomendado)

Autentica um serviço utilizando uma aplicação azure Ative Directory (Azure AD) e secreto. Isto é recomendado para quaisquer serviços de nível médio que chamem para a API dos Serviços de Comunicação Social. Exemplos são Web Apps, Funções, Aplicações Lógicas, APIs e microserviços. Este é o método de autenticação recomendado.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Gerencie a sua app Azure AD e o seu segredo

A **aplicação E secção secreta Do Gestão da Sua AAD** permite selecionar ou criar uma nova aplicação Azure AD e gerar um segredo. Para efeitos de segurança, o segredo não pode ser mostrado depois da lâmina estar fechada. A aplicação utiliza o ID da aplicação e o segredo para a autenticação para obter um símbolo válido para os serviços de comunicação social.

Certifique-se de que tem permissões suficientes para registar uma candidatura com o seu inquilino Azure AD e atribuir a candidatura a um papel na sua subscrição Azure. Para mais informações, consulte [as permissões necessárias](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

#### <a name="connect-to-media-services-api"></a>Ligação à API dos Serviços de Comunicação Social

A **API Connect to Media Services** fornece-lhe valores que utiliza para ligar a sua aplicação principal de serviço. Pode obter valores de texto ou copiar os blocos JSON ou XML.

### <a name="user-authentication"></a>Autenticação de utilizador

Esta opção poderia ser usada para autenticar um funcionário ou membro de um Diretório Ativo Azure que está a usar uma app para interagir com os recursos dos Media Services. A aplicação interativa deve primeiro solicitar ao utilizador as credenciais do utilizador. Este método de autenticação só deve ser utilizado para aplicações de Gestão.

#### <a name="connect-to-media-services-api"></a>Ligação à API dos Serviços de Comunicação Social

Copie as suas credenciais para ligar a sua aplicação de utilizador da secção **Connect to Media Services API.** Pode obter valores de texto ou copiar os blocos JSON ou XML.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Upload, codificação e streaming de vídeos com Media Services v3](stream-files-tutorial-with-api.md).
