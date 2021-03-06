---
title: Conjuntos fonéticos de fala - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como fazer mapas de alfabeto fonético do serviço de fala para o Alfabeto Fonético Internacional (IPA) e quando utilizar o conjunto.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675332"
---
# <a name="speech-service-phonetic-sets"></a>Conjuntos fonéticos do serviço de fala

O serviço de Fala define alfabetos fonéticos ("conjuntos telefónicos" para abreviar), compostos por sete línguas; `en-US` `fr-FR` `de-DE`E. `es-ES` `ja-JP` `zh-CN` `zh-TW` O telefone de serviço da Fala normalmente mapeia para o <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Alfabeto Fonético <span class="docon docon-navigate-external x-hidden-focus"> </span>Internacional (IPA) </a>. Os conjuntos de telefones do serviço de fala são usados em conjunto com a Linguagem de Marcação da Síntese da [Fala (SSML),](speech-synthesis-markup.md)como parte da oferta de serviço de texto à fala. Neste artigo, você vai aprender como estes conjuntos de telefone são mapeados e quando usar o conjunto de telefone.

# <a name="en-us"></a>[en-EUA](#tab/en-US)

### <a name="english-suprasegmentals"></a>Suprasegmentais ingleses

| Exemplo 1 (Início para consoante, palavra inicial para vogal) | Exemplo 2 (Intervocalic para consonante, palavra núcleo medial para vogal) | Exemplo 3 (Coda para consoante, palavra final para vogal) | Comentários |
|--|--|--|--|
| hambúrguer /b er **1** r - g ax r/ | falafel /f ax - l aa **1** - f ax l/ | guitarra /g ih - t aa **1** r/ | O telefone do serviço de fala definido colocar o stress após a vogal da sílaba stressada |
| inoportuno /ih **2** - n aa - p ax r - t uw 1 n/ | dissemelhança /d ih - s ih **2**- m ax - l eh 1 - r ax - t iy/ | mão de obra /w er 1 r k - f ao **2** rs/ | O telefone do serviço de fala definido colocar o stress após a vogal da sílaba sub-stressada |

### <a name="english-vowels"></a>Vogais inglesas

| `sapi` | `ipa` | Exemplo 1     | Exemplo 2 | Exemplo 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **ea**t       | f**ee**l  | vall**ey**                  |
| Ih     | `ɪ`   | **i**f        | f**i**i  |                             |
| ey     | `eɪ`  | **um**te       | g**um**te  | d**ay**                     |
| eh     | `ɛ`   | **e**muito     | p**e**t   | m**eh** (palavra rara finalmente) |
| ae     | `æ`   | **um**ctive    | c**um**t   | n**ah** (palavra rara finalmente) |
| aa     | `ɑ`   | **o**bstinado | p**o**ppy | r**ah** (palavra rara finalmente) |
| ao     | `ɔ`   | **o**intervalo    | c**au**se | Ut**ah**                    |
| uh     | `ʊ`   | b**oo**k      |           |                             |
| ow     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| uw     | `u`   | **U**ber      | b**oo**st | t**oo**                     |
| ah     | `ʌ`   | **u**ncle     | **c u**t   |                             |
| ay     | `aɪ`  | **i**ce       | b**i**te  | fl**y**                     |
| aw     | `aʊ`  | **ou**t       | s**ou**th | c**ow**                     |
| oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | **Yu**ma      | h**u**homem | f**ew**                     |
| machado     | `ə`   | **um**ir       | wom**a**n | são**um**                    |

### <a name="english-r-colored-vowels"></a>Vogais de cor R inglesa

| `sapi` | `ipa` | Exemplo 1    | Exemplo 2      | Exemplo 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **orelha**s     | t**ir**amisu   | n**orelha**   |
| eh r   | `ɛɹ`  | **avião aéreo** | app**ar**ently | sc**ar**e  |
| uh r   | `ʊɹ`  |              |                | c**o seu**e   |
| ay r   | `aɪɹ` | **Terra ire**  | f**ir**eplace  | ch**oir**  |
| aw r   | `aʊɹ` | **hora**s    | p**devedor**ful   | é**o nosso**   |
| ao r   | `ɔɹ`  | **ou**ange   | m**ou**al      | s**remo**   |
| aa r   | `ɑɹ`  | **ar**tist   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **orelha**th    | b**ir**d       | f**o seu**    |
| ax r   | `ɚ`   |              | todos**er**gy    | supp**er** |

### <a name="english-semivowels"></a>Semivowels Ingleses

| `sapi` | `ipa` | Exemplo 1           | Exemplo 2  | Exemplo 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**ith, s**ue de** | al**w**ays |           |
| S      | `j`   | **y**ard, f**e**w   | em**i**em  |           |

### <a name="english-aspirated-oral-stops"></a>Paragens orais aspiradas em inglês

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2   | Exemplo 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**ut   | ha**pp**en  | fla**p**   |
| b      | `b`   | **b**ig   | num**b**er  | cra**b**   |
| t      | `t`   | **t**alk  | capi**t**al | sough**t** |
| d      | `d`   | **d**ig   | correu**d**om  | ro**d**    |
| K      | `k`   | **c**ut   | sla**ck**er | Ira**q**   |
| g      | `g`   | **g**o    | um**g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>Paragens nasal inglesas

| `sapi` | `ipa` | Exemplo 1        | Exemplo 2  | Exemplo 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**em, esmagar   | ca**m**era | roo**m**    |
| n      | `n`   | **n**o, s**n**ow | te**n**t   | chicke**n** |
| ng     | `ŋ`   |                  | li**n**k   | s**ing**    |

### <a name="english-fricatives"></a>Fricações inglesas

| `sapi` | `ipa` | Exemplo 1   | Exemplo 2        | Exemplo 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f**ork    | le**f**t         | hal**f**   |
| v      | `v`   | **v**alue   | e**v**ent        | lo**v**e   |
| th     | `θ`   | **th**em    | empa**th**y      | mon**th**  |
| dh     | `ð`   | **th**en    | mo**th**er       | smoo**th** |
| t      | `s`   | **é**     | ri**s**k         | facto**s**  |
| z      | `z`   | **z**ap     | bu**s**y         | criança**s**   |
| sh     | `ʃ`   | **sh** e    | abrerevia**ti**em | ru**sh**   |
| zh     | `ʒ`   | **J**acques | apelo**ure**     | gara**g**e |
| h      | `h`   | **h**elp    | en**h**ance      | **a-h**a!  |

### <a name="english-affricates"></a>Affricatos ingleses

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2    | Exemplo 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**em  | fu**t**ure   | atta**ch** |
| jh     | `dʒ`  | **j**oy   | ori**g**inal | oran**g**e |

### <a name="english-approximants"></a>Aproximantes ingleses

| `sapi` | `ipa` | Exemplo 1          | Exemplo 2  | Exemplo 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id, g**l**anúncio  | pa**l**ás | chi**ll** |
| r      | `ɹ`   | **r**ed, b**r**ing | bo**rr**ow | ta**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Suprasegmentais franceses

O conjunto de telefone sintetizador do serviço de fala coloca o stress após a vogal da sílaba stressada, no entanto; o `fr-FR` conjunto de telefones do serviço de fala não suporta a subcostuina IPA '. Se o substressipa for necessário, deve utilizar diretamente o IPA.

### <a name="french-vowels"></a>Vogais francesas

| `sapi` | `ipa` | Exemplo 1     | Exemplo 2       | Exemplo 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **um**rbre     | p**um**tte       | ir**um**   |
| aa     | `ɑ`   |               | p**â te**        | p**a**s   |
| aa ~   | `ɑ̃`  | **en**fant    | enf**en**t      | t**em**ps |
| machado     | `ə`   |               | p**e**tite      | l**e**    |
| eh     | `ɛ`   | **e**lle      | p**e**rdu       | ét**ai**t |
| eu     | `ø`   | **Œu**fs      | cr**eu**ser     | qu**eu**  |
| ey     | `e`   | ému           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | **im**portant | p**ein**ture    | tapete**em** |
| iy     | `i`   | **i**dée      | animal de estimação**i**te      | eu**i** sou   |
| oe     | `œ`   | **œu**f       | p**eu**r        |           |
| Oh     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| Oh~   | `ɔ̃`  | **em**ze      | r**em**deur     | b**em**   |
| ow     | `o`   | **au**diteur  | b**eau**golpe    | p**ô**    |
| oe ~   | `œ̃ ` | **un**        | l**un**di       | br**un**  |
| uw     | `u`   | **ou**trage   | intr**ou**vable | **ou**    |
| uy     | `y`   | **u**ne       | p**u**nir       | él**u**   |

### <a name="french-consonants"></a>Consoantes francesas

| `sapi` | `ipa` | Exemplo 1   | Exemplo 2     | Exemplo 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête    | ha**b**ille   | ro**b**e                         |
| d      | `d`   | **d**ire    | ron**d**eur   | chau**d**e                       |
| f      | `f`   | **f**emme   | su**ff**ixe   | bo**f**                          |
| g      | `g`   | **g**auche  | é**g**ale     | ba**gu**e                        |
| ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)**parque** |
| hy     | `ɥ`   | h**u**ile   | n**u**ire     |                                  |
| K      | `k`   | **c**arte   | é**c**aille   | ser**c**                          |
| l      | `l`   | **l**ong    | é**l**ire     | ba**l**                          |
| m      | `m`   | **m**adame  | ai**m**er     | po**mm**e                        |
| n      | `n`   | **n**ous    | te**n**ir     | bo**nn**e                        |
| nj     | `ɲ`   |             |               | pei**gn**e                       |
| p      | `p`   | **p**atte   | re**p**como     | ca**p**                          |
| r      | `ʁ`   | **r**em     | cha**r**iot   | sentir**r**                       |
| t      | `s`   | **s**ourir  | um**ss**ez     | pa**ss**e                        |
| sh     | `ʃ`   | **ch**anter | ma**ch**ine   | po**ch**e                        |
| t      | `t`   | **t**ête    | ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | em**v**entrar  | rê**v**e                         |
| w      | `w`   | **ou**i     | f**ou**ine    |                                  |
| S      | `j`   | **y**od     | p**i**étiner  | Marse**ille**                    |
| z      | `z`   | **z **éro   | rai**s**onner | ro**s**e                         |
| zh     | `ʒ`   | **j**ardin  | homem**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | quan**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *Apenas para algumas palavras estrangeiras.*

> [!TIP]
> O `fr-FR` conjunto telefónico do serviço de fala não suporta `n‿`as `t‿`seguintes ligações francesas, e `z‿`. Se forem necessários, deve considerar a utilização direta do IPA.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Suprasegmentais alemães

| Exemplo 1 (Início para consoante, palavra inicial para vogal) | Exemplo 2 (Intervocalic para consonante, palavra núcleo medial para vogal) | Exemplo 3 (Coda para consoante, palavra final para vogal) | Comentários |
|--|--|--|--|
| anders **/a 1** n - d ax r/ | Multiplikationszeichen /m uh l - t iy - p l iy - k a - ts y ow **1** n s - ts ay - c n/ | Biologie /b iy - ow - ow - g iy **1**/ | O telefone do serviço de fala definido colocar o stress após a vogal da sílaba stressada |
| Allgemeinwissen **/a 2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgungsfirma /a 1 p - f a l - ^ eh n t - z oh **2** ax r - g uh ng s - f ih ax r - m a/ | Computertomographie /k oh m - p y uw 1 - t ax r - t ow - ow - g r a - f iy **2**/ | O telefone do serviço de fala definido colocar o stress após a vogal da sílaba sub-stressada |

### <a name="german-vowels"></a>Vogais alemãs

| `sapi` | `ipa`     | Exemplo 1                             | Exemplo 2     | Exemplo 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a:     | `aː`      | **Um**ber                              | Maßst**a**b   | Schem**a**                         |
| a      | `a`       | **Uma**queda                            | B**a**ch      | Agath**a**                         |
| Oh     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| eh:    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1</sup>](#de-v-1) Fasci**ae** |
| eh     | `ɛ`       | **ä**ndern                            | Proz**e**nt   | Amygdal**ae**                      |
| machado     | `ə`       | [<sup>2</sup>](#de-v-2)'v**e**rstauen | Aach**e**n    | Frag**e**                          |
| iy     | `iː`      | **Corri.**                              | abb**ie**gt   | Relativitätstheor**ou**            |
| Ih     | `ɪ`       | **I**nnung                            | s**i**ngen    | Madeira**y**                          |
| eu     | `øː`      | **Ö**sen                              | abl**ö**sten  | Malm**ö**                          |
| ow     | `o`, `oː` | **o**hne                              | Balk**o**n    | Trept**ow**                        |
| oe     | `œ`       | **Ö**ffnung                           | bef**ö**rdern |                                    |
| ey     | `e`, `eː` | **E**berhard                          | abf**e**gt    | b                                  |
| uw     | `uː`      | **U**do                               | H**u**t       | Akk**u**                           |
| uh     | `ʊ`       | **U**nterschiedes                     | b**u**nt      |                                    |
| ue     | `yː`      | **Ü**bermut                           | pfl**ü**gt    | Homens**ü**                           |
| uy     | `ʏ`       | **ü**ppig                             | S**y**caule    |                                    |

<a id="de-v-1"></a>
**1** *Apenas em palavras de origem estrangeira, tais como: Fasci**ae**.*<br>
<a id="de-v-2"></a>
**2** *Palavra-intialmente apenas em palavras de origem estrangeira, como **Um**ppointment. Silabável-inicialmente em: 'v**e**rstauen.*

### <a name="german-diphthong"></a>Difthong alemão

| `sapi` | `ipa`       | Exemplo 1    | Exemplo 2          | Exemplo 3 |
|--------|-------------|--------------|--------------------|-----------|
| ay     | `ai`        | **ei**nsam   | Unabhängigk**ei**t | Abt**ei** |
| aw     | `au`        | **au**ßen    | abb**au**st        | St**au**  |
| oy     | `ɔy`, `ɔʏ̯` | **Eu**phorie | tr**äu**mt         | sch**eu** |

### <a name="german-semivowels"></a>Semi-vogais alemãs

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2    | Exemplo 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd**er**n | **locker** |

### <a name="german-consonants"></a>Consoantes alemães

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2 | Exemplo 3 |
|--|--|--|--|--|
| b | `b` | **B**ank |  | [<sup>1</sup>](#de-c-1) Pu**b** |  |
| c | `ç` | **Ch**emie | mögli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken | [<sup>3</sup>](#de-c-3) Len**d**l | [<sup>4</sup>](#de-c-4) Clau**d**e |  |
| jh | `ʤ` | **J**eff | gemana**g**t | [<sup>5</sup>](#de-c-5) Chan**g**e |
| f | `f` | **F**Ahrtdauer | angri**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g**ut |  | [<sup>6</sup>](#de-c-6) Gre**g** |  |
| h | `h` | **Ausanbau** |  |  |  |
| S | `j` | **J**od | Reakt**i**em | hu**i** |  |
| K | `k` | **K**oma | Aspe**k**t | Flec**k** |  |
| l | `l` | **l au** | ähne**l**n | zuvie**l** |  |
| m | `m` | **M**ut | A**m**t | Leh**m** |  |
| n | `n` | **n**un | u**n**d | Huh**n** |  |
| ng | `ŋ` | [<sup>7</sup>](#de-c-7)**Ng**uyen | Schwa**nk** | R**ing** |  |
| p | `p` | **P**artner | abru**p**t | Ti**p** |  |
| pf | `pf` | **Pf**erd | barragem**pf**t | Para**pf** |  |
| r | `ʀ`, `r`, `ʁ` | **R**eise | knu**rr**t | Haa**r** |  |
| t | `s` | [<sup>8</sup>](#de-c-8)**S**taccato | bi**s**t | mie**s** |  |
| sh | `ʃ` | **Sch**ule | mi**sch**t | lappi**sch** |  |
| t | `t` | **T**raum | S**t**raße | Mu**t** |  |
| ts | `ts` | **Z**ug | Ar**z**t | Wit**z** |  |
| ch | `tʃ` | **Tsch**echien | aufgepu**tsch tsch**t | bundesdeu**tsch** |  |
| v | `v` | **w**inken | Q**u**alle | [<sup>9</sup>](#de-c-9) Gr**oo**ve |  |
| x | [<sup>10,</sup>](#de-c-10)`x`[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) Ba**ch**erach | Ma**ch**t mögli**ch**st | Schma**ch** 'i**ch** |
| z | `z` | **s**uper |  |  |  |
| zh | `ʒ` | **G**enre | B**re**ezinski | Edvi**g**e |

<a id="de-c-1"></a>
**1** *Apenas em palavras de origem estrangeira, tais como: Pu**b**.*<br>
<a id="de-c-2"></a>
**2** *"ch" macio depois de "e" e "i"*<br>
<a id="de-c-3"></a>
**3** *Apenas em palavras de origem estrangeira, tais como: Len**d**l.*<br>
<a id="de-c-4"></a>
**4** *Apenas em palavras de origem estrangeira como: Clau**d**e.*<br>
<a id="de-c-5"></a>
**5** *Apenas em palavras de origem estrangeira como: Chan**g**e.*<br>
<a id="de-c-6"></a>
**6** *Palavra-terminal apenas em palavras de origem estrangeira, como Gre**g**.*<br>
<a id="de-c-7"></a>
**7** *Apenas em palavras de origem estrangeira como: **Ng**uyen.*<br>
<a id="de-c-8"></a>
**8** *Apenas em palavras de origem estrangeira como: **S**taccato.*<br>
<a id="de-c-9"></a>
**9** *Apenas em palavras de origem estrangeira, tais como: Gr**oo**ve.*<br>
<a id="de-c-10"></a>
**10** *O `x` IPA é um "ch" duro depois de todas as vogais não frontais (a, aa, oh, uh, uw e o diphthong aw).*<br>
<a id="de-c-11"></a>
**11** *O `ç` IPA é um 'ch' suave após vogais frontais (ih, iy, eh, ae, uy, ue, oe, eu também em diphthongs ay, oy) e consoantes*<br>
<a id="de-c-12"></a>
**12** *Palavra-inicialmente apenas em palavras de origem estrangeira, tais como: **J**uan. Sílaba inicialmente também em palavras como: Ba**ch**erach.*<br>

### <a name="german-oral-consonants"></a>Consoantes orais alemãs

| `sapi` | `ipa` | Exemplo 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich /b ax - ^ a 1 x t - l ih c/ |

> [!NOTE]
> Precisamos adicionar um telefone\] [gs entre duas vogais distintas, exceto que as duas vogais são uma difthong genuína. Esta consoante oral é uma paragem glotal, <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">para mais <span class="docon docon-navigate-external x-hidden-focus"> </a> </a>informações, ver paragem glotal .

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Vogais espanholas

| `sapi` | `ipa` | Exemplo 1    | Exemplo 2     | Exemplo 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **um**lto     | c**um**ntar    | cas**a**     |
| i      | `i`   | **i**bérica  | av**i**spa    | imposto**i**     |
| e      | `e`   | **e**lefante | em**e**nto    | elefant**e** |
| o      | `o`   | **o**caso    | enc**o**ntrar | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**nta     | Juanl**u**   |

### <a name="spanish-consonants"></a>Consoantes espanhóis

| `sapi` | `ipa`      | Exemplo 1  | Exemplo 2      | Exemplo 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | sou**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b**     |
| ch     | `tʃ`       | **ch**eque | **co ch**e      | Marraque**ch** |
| d      | `d`        | **d**edo   |                | portlan**d**   |
|        | `ð`        |            | de**d**o       | verda**d**     |
| f      | `f`        | **f**ácil  | ele**f**ante   | pu**f**        |
| g      | `g`        | **g**anga  |                | dópin**g**     |
|        | `ɣ`        |            | um**g**ua       | tuare**g**     |
| j      | `j`        | **i**odo   | cal**i**ente   | re**y**        |
| jj     | `j.j` `jj` |            | vi**vai**um      |                |
| K      | `k`        | **c**oche  | bo**c**a       | titáni**c**    |
| l      | `l`        | **l**ápiz  | **a l a**        | corde**l**     |
| ll     | `ʎ`        | **ll**ave  | desarro**ll**o |                |
| m      | `m`        | **m**ordem | um**m**ar       | álbu**m**      |
| n      | `n`        | **n**ada   | ce**n**a       | rató**n**      |
| nj     | `ɲ`        | **ñ**aña   | ara**ñ**azo    |                |
| p      | `p`        | **p**oca   | para**p**o       | sto**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r**      |
| rr     | `r`        | **r**adio  | co**rr**e      | pu**rr**       |
| t      | `s`        | **s**aco   | va**s**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | um**t**ar       | disque**t**    |
| th     | `θ`        | **z**ebra  | um**z**ul       | lápi**z**      |
| w      | `w`        | h**u**eso  | ag**u**a       | gua**u**       |
| x      | `x`        | **j**ota   | um**j**o        | relo**j**      |

> [!TIP]
> O `es-ES` conjunto de telefones do serviço de `β`fala `ð`não `ɣ`suporta o seguinte IPA espanhol, e . Se forem necessários, deve considerar a utilização direta do IPA.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

O telefone de `zh-CN` serviço da Fala definido é baseado no conjunto <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">pinyin <span class="docon docon-navigate-external x-hidden-focus"></span> </a> do telefone nativo.

### <a name="tone"></a>Tom

| Tom pinyin | `sapi` | Exemplo de caráter |
|-------------|--------|-------------------|
| mā          | ma 1  | 妈                 |
| má          | ma 2  | 麻                 |
| m .          | ma 3  | 马                 |
| mà          | ma 4  | 骂                 |
| ma          | ma 5  | 嘛                 |

#### <a name="example"></a>Exemplo

| Caráter | Serviço de voz                |
|-----------|-------------------------------|
| 组织关系      | zu 3 - zhi 1 - guan 1 - xi 5 |
| 累进        | lei 3 -jin 4                 |
| 西宅巷       | xi 1 - zhai 2 - xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

O telefone de `zh-TW` serviço da Fala definido é baseado no conjunto <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">bopomofo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> do telefone nativo.

### <a name="tone"></a>Tom

| Tom de serviço de fala | Tom Bopomofo | Exemplo (palavra) | Telefones de serviço de fala | Rio Bopomofo | Pinyin () |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | vazio         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | d .          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇ  ㄗ˙               | 一ㄥˇ  ㄗ˙  | ysng zi    |

#### <a name="example"></a>Exemplo

| Caráter | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

O telefone do `ja-JP` serviço de fala definido é baseado no conjunto <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">kana <span class="docon docon-navigate-external x-hidden-focus"></span> </a> do telefone nativo.

### <a name="stress"></a>Stress

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ`mainstress |
| `+`    | `ˌ`substresse  |

#### <a name="example"></a>Exemplo

| Caráter | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | gođwφseji   |
| 所有者       | ショュ'ウ?ャ | Elejécio |
| 最適化       | サィテキカ+  | sajitecika - |

***