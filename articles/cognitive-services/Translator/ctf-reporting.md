---
title: Relatório do Quadro de Tradução Colaborativa (CTF) - API de texto de tradutor
titleSuffix: Azure Cognitive Services
description: Como utilizar o relatório do Quadro de Tradução Colaborativa (CTF).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 6a197095d97e67f7548e60375148cff57e47b797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595933"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Como utilizar os relatórios do Collaborative Translation Framework (CTF)

> [!NOTE]
> Este método está premeditado. Não está disponível em V3.0 da API de Texto tradutor.
> 
> O Quadro de Traduções Colaborativas (CTF), anteriormente disponível para V2.0 da API de Texto tradutor, foi depreciado a partir de 1 de fevereiro de 2018. As funções AddTranslation e AddTranslationArray permitem que os utilizadores permitam correções através do Quadro de Tradução Colaborativa. Depois de 31 de janeiro de 2018, estas duas funções não aceitaram novas candidaturas de frases, e os utilizadores receberam uma mensagem de erro. Estas funções foram retiradas e não serão substituídas.

O Quadro Colaborativo de Tradução (CTF) Reporting API devolve estatísticas e conteúdos reais na loja CTF. Esta API é diferente do método GetTranslations() porque:
* Devolve o conteúdo traduzido e a sua contagem total apenas a partir da sua conta (appId ou conta Azure Marketplace).
* Devolve o conteúdo traduzido e a sua contagem total sem exigir uma correspondência da frase de origem.
* Não devolve a tradução automática (tradução automática).

## <a name="endpoint"></a>Ponto Final
O ponto final da API de relatório supor ctf éhttps://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Métodos
| Nome |    Descrição|
|:---|:---|
| Método GetUserTranslationCounts | Obtenha contagens das traduções que são criadas pelo utilizador. |
| Método getUserTranslations | Recupera as traduções que são criadas pelo utilizador. |

Estes métodos permitem::
* Recupere o conjunto completo de traduções e correções de utilizadores sob o ID da sua conta para download.
* Obtenha a lista dos colaboradores frequentes. Certifique-se de que o nome de utilizador correto está fornecido no AddTranslation().
* Construa uma interface de utilizador (UI) que permita aos seus utilizadores de confiança ver todos os candidatos disponíveis, se necessário restrito a uma parte do seu site, com base no prefixo URI.

> [!NOTE]
> Ambos os métodos são relativamente lentos e caros. Recomenda-se usá-los com moderação.

## <a name="getusertranslationcounts-method"></a>Método GetUserTranslationCounts

Este método obtém a contagem de traduções que são criadas pelo utilizador. Fornece a lista de contagens de tradução agruparadas pelos parâmetros de pedido uriPrefix, de, para utilizador, minRating e maxRating, os parâmetros de pedido.

**Sintaxe**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Parâmetros**

| Parâmetro | Descrição |
|:---|:---|
| appId | **Obrigatório** Se o cabeçalho de autorização for utilizado, deixe o campo apástica vazio eespecifique uma cadeia contendo "Bearer" + " + ficha de acesso.|
| uriPrefix | **Opcional** Uma cadeia contendo prefixo de URI da tradução.|
| De | **Opcional** Uma cadeia que representa o código linguístico do texto de tradução. |
| para | **Opcional** Uma cadeia que representa o código linguístico para traduzir o texto.|
| minRating| **Opcional** Um valor inteiro que representa a classificação de qualidade mínima para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| maxRating| **Opcional** Um valor inteiro que representa a classificação máxima de qualidade para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| utilizador | **Opcional** Uma cadeia que é usada para filtrar o resultado com base no autor da submissão. |
| categoria| **Opcional** Uma cadeia que contenha a categoria ou domínio da tradução. Este parâmetro suporta apenas a opção por defeito geral.|
| minDateUtc| **Opcional** A data de quando quer recuperar as traduções. A data deve estar no formato UTC. |
| maxDateUtc| **Opcional** A data até quando quiser recuperar as traduções. A data deve estar no formato UTC. |
| saltar| **Opcional** O número de resultados que pretende saltar numa página. Por exemplo, se desejar o salto das primeiras 20 linhas dos resultados e a visualização do 21º registo de resultados, especifique 20 para este parâmetro. O valor padrão para este parâmetro é 0.|
| tomar | **Opcional** O número de resultados que quer recuperar. O número máximo de cada pedido é de 100. A predefinição é 100.|

> [!NOTE]
> Os parâmetros de skip e take request permitem paginação para um grande número de registos de resultados.

**Valor de devolução**

O conjunto de resultados contém um conjunto do **UserTranslationCount**. Cada UserTranslationCount tem os seguintes elementos:

| Campo | Descrição |
|:---|:---|
| Contagem| O número de resultados que são recuperados|
| De | A língua de origem|
| Classificação| A classificação aplicada pelo submissão na chamada do método AddTranslation()|
| Para| A linguagem-alvo|
| Uri| O URI aplicado na chamada do método AddTranslation()|
| Utilizador| O nome do utilizador|

**Exceções**

| Exceção | Mensagem | Condições |
|:---|:---|:---|
| ArgumentOutoutRangeexception | O parâmetro '**maxDateUtc**' deve ser maior ou igual a '**minDateUtc**'.| O valor do parâmetro **maxDateUtc** é inferior ao valor do parâmetro **minDateUtc**.|
| Tradução ApiException | Ip está acima da quota.| <ul><li>O limite para o número de pedidos por minuto é atingido.</li><li>O tamanho do pedido permanece limitado a 10000 caracteres.</li><li>Uma quota de hora e diária limitam o número de caracteres que a Microsoft Tradutor API irá aceitar.</li></ul>|
| Tradução ApiException | O AppId está acima da quota.| O ID da aplicação excedeu a quota horária ou diária.|

> [!NOTE]
> A quota ajustar-se-á para garantir a equidade entre todos os utilizadores do serviço.

**Ver exemplos de código no GitHib**
* [C #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Método GetUserTranslations

Este método recupera as traduções que são criadas pelo utilizador. Fornece as traduções agruparadas pelo uriPrefix, de, para, utilizador, e minRating e maxRating os parâmetros de pedido.

**Sintaxe**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Parâmetros**

| Parâmetro | Descrição |
|:---|:---|
| appId | **Obrigatório** Se o cabeçalho de autorização for utilizado, deixe o campo apástica vazio eespecifique uma cadeia contendo "Bearer" + " + ficha de acesso.|
| uriPrefix| **Opcional** Uma cadeia contendo prefixo de URI da tradução.|
| De| **Opcional** Uma cadeia que representa o código linguístico do texto de tradução.|
| para| **Opcional** Uma cadeia que representa o código linguístico para traduzir o texto.|
| minRating| **Opcional** Um valor inteiro que representa a classificação de qualidade mínima para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| maxRating| **Opcional** Um valor inteiro que representa a classificação máxima de qualidade para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| utilizador| **Opcional. Uma cadeia que é usada para filtrar o resultado com base no autor da submissão**|
| categoria| **Opcional** Uma cadeia que contenha a categoria ou domínio da tradução. Este parâmetro suporta apenas a opção por defeito geral.|
| minDateUtc| **Opcional** A data de quando quer recuperar as traduções. A data deve estar no formato UTC.|
| maxDateUtc| **Opcional** A data até quando quiser recuperar as traduções. A data deve estar no formato UTC.|
| saltar| **Opcional** O número de resultados que pretende saltar numa página. Por exemplo, se desejar o salto das primeiras 20 linhas dos resultados e a visualização do 21º registo de resultados, especifique 20 para este parâmetro. O valor padrão para este parâmetro é 0.|
| tomar| **Opcional** O número de resultados que quer recuperar. O número máximo de cada pedido é de 100. O padrão é 50.|

> [!NOTE]
> Os parâmetros de skip e take request permitem paginação para um grande número de registos de resultados.

**Valor de devolução**

O conjunto de resultados contém um conjunto da **Tradução**do Utilizador . Cada UserTranslation tem os seguintes elementos:

| Campo | Descrição |
|:---|:---|
| CriadoDateUtc| A data de criação da entrada utilizando addtranslation()|
| De| A língua de origem|
| Texto Original| O texto de linguagem fonte que é usado ao submeter o pedido|
|Classificação |A classificação aplicada pelo submissão na chamada do método AddTranslation()|
|Para|    A linguagem-alvo|
|Texto traduzido|    A tradução apresentada na chamada do método AddTranslation()|
|Uri|   O URI aplicado na chamada do método AddTranslation()|
|Utilizador   |O nome do utilizador|

**Exceções**

| Exceção | Mensagem | Condições |
|:---|:---|:---|
| ArgumentOutoutRangeexception | O parâmetro '**maxDateUtc**' deve ser maior ou igual a '**minDateUtc**'.| O valor do parâmetro **maxDateUtc** é inferior ao valor do parâmetro **minDateUtc**.|
| Tradução ApiException | Ip está acima da quota.| <ul><li>O limite para o número de pedidos por minuto é atingido.</li><li>O tamanho do pedido permanece limitado a 10000 caracteres.</li><li>Uma quota de hora e diária limitam o número de caracteres que a Microsoft Tradutor API irá aceitar.</li></ul>|
| Tradução ApiException | O AppId está acima da quota.| O ID da aplicação excedeu a quota horária ou diária.|

> [!NOTE]
> A quota ajustar-se-á para garantir a equidade entre todos os utilizadores do serviço.

**Ver exemplos de código no GitHib**
* [C #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
