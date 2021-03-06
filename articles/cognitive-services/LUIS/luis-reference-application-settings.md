---
title: Definições de candidatura - LUIS
description: As definições de aplicações para aplicações de compreensão linguística dos Serviços Cognitivos Azure estão armazenadas na aplicação e portal.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382206"
---
# <a name="application-settings"></a>Definições da aplicação

Estas definições de aplicação são armazenadas na aplicação [exportada](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) e [atualizadas](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com as APIs rest. Alterar as definições da versão da aplicação repõe o estado de treino da aplicação para destreinado.

Aprenda [conceitos](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de diacríticos e pontuação.

|Definição|Valor predefinido|Notas|
|--|--|--|
|Normalizar Punctuação|Verdadeiro|Remove a pontuação.|
|Normalizar Os críticos da Diadia|Verdadeiro|Remove os diacritics.|

## <a name="diacritics-normalization"></a>Normalização dos diacríticos

Ligue a normalização da expressão para os diacríticos `settings` do seu ficheiro de aplicação LUIS JSON no parâmetro.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

As seguintes declarações mostram como a normalização dos diacritics tem impacto nas expressões:

|Com diacritics definido para falso|Com diacritics definidos para verdade|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Suporte linguístico para diacríticos

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Diacríticos portugueses `pt-br` brasileiros

|Diacritics definido para falso|Diacritics definido para verdade|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>Diacríticos holandeses `nl-nl`

|Diacritics definido para falso|Diacritics definido para verdade|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Diacríticos franceses `fr-`

Isto inclui subculturas francesas e canadianas.

|Diacritics definido para falso|Diacritics definido para verdade|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>Diacríticos alemães `de-de`

|Diacritics definido para falso|Diacritics definido para verdade|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Diacríticos italianos `it-it`

|Diacritics definido para falso|Diacritics definido para verdade|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Diacríticos espanhóis `es-`

Isto inclui tanto o espanhol como o mexicano canadiano.

|Diacritics definido para falso|Diacritics definido para verdade|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalização da pontuação

Ligue a normalização da expressão para pontuação no ficheiro da `settings` aplicação LUIS JSON no parâmetro.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

As seguintes declarações mostram como a pontuação impacta as expressões:

|Com pontuação definida para Falso|Com pontuação definida para True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Pontuação removida

A pontuação a seguir `NormalizePunctuation` é removida com a verdadeira.

|Pontuação|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|

## <a name="next-steps"></a>Passos seguintes

* Aprenda [conceitos](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de diacríticos e pontuação.
