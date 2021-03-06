---
title: Personalize um modelo de Marcas com API indexante de vídeo
titleSuffix: Azure Media Services
description: Aprenda a personalizar um modelo de Marcas com a API do Indexer de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127998"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalize um modelo de Marcas com a API indexante de vídeo

O Indexer de vídeo suporta a deteção da marca a partir da fala e do texto visual durante a indexação e reindexação de conteúdos de vídeo e áudio. A funcionalidade de deteção da marca identifica menções de produtos, serviços e empresas sugeridas pela base de dados de marcas bing. Por exemplo, se a Microsoft for mencionada em conteúdos de vídeo ou áudio ou se aparecer em texto visual num vídeo, o Video Indexer deteta-o como uma marca no conteúdo. Um modelo personalizado de Marcas permite-lhe excluir certas marcas de serem detetadas e incluir marcas que deverão fazer parte do seu modelo que podem não estar na base de dados de marcas da Bing.

Para uma visão geral detalhada, consulte [a visão geral](customize-brands-model-overview.md).

Pode utilizar as APIs do Indexer de Vídeo para criar, utilizar e editar modelos personalizados de Marcas detetadas num vídeo, conforme descrito neste tópico. Também pode utilizar o website do Indexer de Vídeo, conforme descrito no [modelo Customize Brands utilizando o website do Indexer de Vídeo](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Criar uma Marca

A [criação de uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) API cria uma nova marca personalizada e adiciona-a ao modelo de Marcas personalizadas para a conta especificada.

> [!NOTE]
> A `enabled` definição (no corpo) coloca a marca na lista *de Incluir* para o Indexante de Vídeo detetar. A `enabled` definição de falso coloca a marca na lista *de Exclus,* para que o Indexer de Vídeo não a detete.

Alguns outros parâmetros que pode definir no corpo:

* O `referenceUrl` valor pode ser qualquer website de referência para a marca, como um link para a sua página na Wikipédia.
* O `tags` valor é uma lista de tags para a marca. Esta etiqueta aparece no campo de *categoria* da marca no site do Indexer de Vídeo. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Cloud".

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que acaba de criar seguindo o formato do exemplo abaixo.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Eliminar uma Marca

A [eliminação](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) de uma marca API remove uma marca do modelo de Marcas personalizadas para a conta especificada. A conta é especificada no `accountId` parâmetro. Uma vez chamada com sucesso, a marca deixará de estar nas listas de *marcas Incluir* ou *Excluir.*

### <a name="response"></a>Resposta

Não há conteúdo devolvido quando a marca é apagada com sucesso.

## <a name="get-a-specific-brand"></a>Obtenha uma marca específica

O [get a marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) API permite-lhe pesquisar os detalhes de uma marca no modelo marcas personalizadas para a conta especificada usando o ID da marca.

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que procurou (usando id da marca) seguindo o formato do exemplo abaixo.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled`ser definido `true` para significar que a marca está na lista *de* `enabled` Incluir para O Indexante de Vídeo detetar, e ser falso significa que a marca está na lista *de Excluir,* para que o Indexer de Vídeo não a detete.

## <a name="update-a-specific-brand"></a>Atualizar uma marca específica

A [atualização a api](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) marca permite-lhe pesquisar os detalhes de uma marca no modelo marcas personalizadas para a conta especificada usando o ID da marca.

### <a name="response"></a>Resposta

A resposta fornece as informações atualizadas sobre a marca que atualizou seguindo o formato do exemplo abaixo.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Obter todas as Marcas

O [get all brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) API devolve todas as marcas no modelo marcas personalizadas para a conta especificada, independentemente de a marca estar na lista de marcas *Incluir* ou *Excluir.*

### <a name="response"></a>Resposta

A resposta fornece uma lista de todas as marcas na sua conta e cada um dos seus detalhes seguindo o formato do exemplo abaixo.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> A marca chamada *Exemplo* está na lista *de Incluir* para O Indexante de Vídeo para detetar, e a marca chamada *Exemplo2* está na lista *de Exclus,* por isso o Indexer de Vídeo não o detetará.

## <a name="get-brands-model-settings"></a>Obtenha configurações de modelo de Marcas

As [definições](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) de get brands API devolve as definições do modelo Marcas na conta especificada. As configurações do modelo Brands representam se a deteção da base de dados das marcas Bing está ativada ou não. Se as marcas Bing não estiverem ativadas, o Video Indexer apenas detetará marcas do modelo personalizado marcas da conta especificada.

### <a name="response"></a>Resposta

A resposta mostra se as marcas Bing estão habilitadas seguindo o formato do exemplo abaixo.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`ser definido para verdadeiro representa que as marcas Bing estão habilitadas. Se `useBuiltin` for falso, as marcas Bing são desativadas. O `state` valor pode ser ignorado porque foi depreciado.

## <a name="update-brands-model-settings"></a>Atualizar as definições do modelo Marcas

A [atualização marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) API atualiza as definições do modelo Marcas na conta especificada. As configurações do modelo Brands representam se a deteção da base de dados das marcas Bing está ativada ou não. Se as marcas Bing não estiverem ativadas, o Video Indexer apenas detetará marcas do modelo personalizado marcas da conta especificada.

A `useBuiltIn` bandeira definida como verdadeira significa que as marcas Bing estão ativadas. Se `useBuiltin` for falso, as marcas Bing são desativadas.

### <a name="response"></a>Resposta

Não há conteúdo devolvido quando a definição do modelo Brands é atualizada com sucesso.

## <a name="next-steps"></a>Passos seguintes

[Personalizar modelo de Marcas usando o site](customize-brands-model-with-website.md)
