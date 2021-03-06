---
title: Aceda e personalize o portal de desenvolvimento gerido - Azure API Management [ Microsoft Docs
description: Saiba como utilizar a versão gerida do portal de desenvolvimento em Gestão API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: af7c995c11322a538dd9e27a905f1ddbc723e8ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244098"
---
# <a name="access-and-customize-developer-portal"></a>Aceder e personalizar portal de desenvolvimento

O portal de desenvolvimento é um website gerado automaticamente e totalmente personalizável com a documentação das suas APIs. É onde os consumidores de API podem descobrir as suas APIs, aprender a usá-las e solicitar acesso.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Aceda à versão gerida do portal de desenvolvimento
> * Navegue na sua interface administrativa
> * Personalize o conteúdo
> * Publicar as alterações
> * Ver o portal publicado

Pode encontrar mais detalhes sobre o portal de desenvolvimento no portal de desenvolvimento da [API Management Azure](api-management-howto-developer-portal.md).

![Portal de desenvolvimento de gestão API - modo de administração](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Pré-requisitos

- Complete o seguinte quickstart: Criar uma instância de [Gestão API Azure](get-started-create-service-instance.md)
- Importar e publicar uma instância de Gestão API Azure. Para mais informações, consulte [Importar e publicar](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Aceda ao portal como administrador

Siga os passos abaixo para aceder à versão gerida do portal.

1. Vá à sua instância de serviço de Gestão API no portal Azure.
1. Clique no botão **do portal Developer** na barra de navegação superior. Será aberto um novo separador de navegador com uma versão administrativa do portal.

## <a name="understand-the-portals-administrative-interface"></a>Compreender a interface administrativa do portal

### <a name="default-content"></a>Conteúdo predefinido 

Se estiver a aceder ao portal pela primeira vez, o conteúdo predefinido será automaticamente aprovisionado em segundo plano. O conteúdo predefinido foi concebido para mostrar as capacidades do portal e minimizar a quantidade de personalizações necessárias para personalizar o seu portal. Pode saber mais sobre o que está incluído no conteúdo do portal na visão geral do portal de desenvolvimento da [API Management Azure.](api-management-howto-developer-portal.md)

### <a name="visual-editor"></a>Editor visual

Pode personalizar o conteúdo do portal com o editor visual. As secções do menu à esquerda permitem-lhe criar ou modificar páginas, meios, layouts, menus, estilos ou configurações do site. Os itens do menu na parte inferior permitem alternar entre portas de visão (por exemplo, mobile ou desktop), ver os elementos do portal visíveis para utilizadores autenticados ou anónimos, ou guardar ou desfazer ações.

Pode adicionar linhas a uma página clicando num ícone azul com um sinal de mais. Os widgets (por exemplo, texto, imagens ou lista de APIs) podem ser adicionados premindo um ícone cinzento com um sinal de mais. Pode reorganizar itens numa página com a interação arrastar-e-drop. 

### <a name="layouts-and-pages"></a>Layouts e páginas

![Páginas e layouts](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Os layouts definem como as páginas são exibidas. Por exemplo, no conteúdo predefinido, existem dois layouts - um aplica-se à página inicial e o outro a todas as páginas restantes.

Um layout é aplicado a uma página combinando o seu modelo de URL com o URL da página. Por exemplo, o layout `/wiki/*` com um modelo de `/wiki/` URL será aplicado `/wiki/getting-started` `/wiki/styles`em todas as páginas com o segmento no URL: , etc.

Na imagem acima, o conteúdo pertencente ao layout é marcado em azul, enquanto a página é marcada a vermelho. As secções do menu estão marcadas respectivamente.

### <a name="styling-guide"></a>Guia de estilo

![Guia de estilo](media/api-management-howto-developer-portal-customize/styling-guide.png)

O guia de estilo é um painel criado com designers em mente. Permite supervisionar e modelar todos os elementos visuais do seu portal. O estilo é hierárquico - muitos elementos herdam propriedades de outros elementos. Por exemplo, os elementos do botão usam cores para texto e fundo. Para mudar a cor de um botão, é necessário alterar a variante de cor original.

Para editar uma variante, clique nela e selecione o ícone do lápis que aparece em cima dela. Assim que fizeres as alterações na janela pop-up, fecha-a.

### <a name="save-button"></a>Botão de salvação

![Botão de salvação](media/api-management-howto-developer-portal-customize/save-button.png)

Sempre que fizer uma alteração no portal, tem de guardá-la manualmente premindo o botão **Guardar** no menu na parte inferior. Quando guardar as suas alterações, o conteúdo modificado é automaticamente enviado para o seu serviço de Gestão API.

## <a name="customize-the-portals-content"></a>Personalize o conteúdo do portal

Antes de disponibilizar o seu portal aos visitantes, deverá personalizar o conteúdo gerado automaticamente. As alterações recomendadas incluem os layouts, estilos e o conteúdo da página inicial.

> [!NOTE]
> Devido a considerações de integração, as seguintes páginas não `/404` `/500`podem `/captcha` `/change-password`ser `/config.json` `/confirm/invitation`removidas ou movidas sob um URL `/confirm-v2/identities/basic/signup`diferente: `/confirm-v2/password` `/internal-status-0123456789abcdef`. , , , , , `/publish`, , , `/signin`, , . `/signin-sso` `/signup`

### <a name="home-page"></a>Página de boas-vindas

A página **inicial** predefinida está cheia de conteúdo sonoro. Pode remover as secções inteiras com o conteúdo ou manter a estrutura e ajustar os elementos uma a uma. Substitua o texto e as imagens gerados por si próprio e certifique-se de que as ligações apontam para os locais pretendidos.

### <a name="layouts"></a>Layouts

Substitua o logótipo gerado automaticamente na barra de navegação pela sua própria imagem.

### <a name="styling"></a>Estilo

Embora não precise de ajustar quaisquer estilos, pode considerar ajustar determinados elementos. Por exemplo, mude a cor primária para combinar com a cor da sua marca.

### <a name="customization-example"></a>Exemplo de personalização

No vídeo abaixo demonstramos como editar o conteúdo do portal, personalizar o look do site e publicar as alterações.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>Publicar o portal

Para disponibilizar o seu portal e as suas últimas alterações aos visitantes, tem de publicá-lo.

1. Certifique-se de que guardou as suas alterações clicando no ícone **Guardar.**
1. Clique no **site da Publicação** na secção **Operações** do menu. Esta operação poderá demorar alguns minutos.  
    ![Publicar portal](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> O portal precisa de ser republicado após alterações na configuração do serviço de Gestão API, tais como a atribuição de um domínio personalizado, a atualização dos fornecedores de identidade, a definição de delegação, especificando os termos de inscrição e produto, e muito mais.

## <a name="visit-the-published-portal"></a>Visite o portal publicado

Depois de publicar o portal, pode acessá-lo no `https://contoso-api.developer.azure-api.net`mesmo URL que o painel administrativo, por exemplo. Veja-o numa sessão separada de navegador (modo de navegação incógnita/privada) como um visitante externo.

## <a name="apply-the-cors-policy-on-apis"></a>Aplicar a política cors em APIs

Tem de ativar o CORS (partilha de recursos de origem cruzada) nas suas APIs para permitir que os visitantes do seu portal testem as APIs através da consola interativa incorporada. Consulte [este artigo de documentação](api-management-howto-developer-portal.md#cors) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o portal de desenvolvimento:

- [Visão geral do portal de desenvolvimento da Azure API Management](api-management-howto-developer-portal.md)
