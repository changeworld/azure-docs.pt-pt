---
title: Sintaxe de consulta Lucene
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe de consulta lucene completa, como usado na Pesquisa Cognitiva Azure para wildcard, pesquisa fuzzy, RegEx, e outras construções avançadas de consulta.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: f4c3330b23b8b724cdbf5d7e09eec8a8dd5b8cfa
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258988"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Sintaxe lucene consulta em Pesquisa Cognitiva Azure

Pode escrever consultas contra a Pesquisa Cognitiva Azure com base na rica sintaxe [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) para formulários especializados de consulta: wildcard, pesquisa difusa, pesquisa de proximidade, expressões regulares são alguns exemplos. Grande parte da sintaxe Lucene Query Parser é [implementada intacta na Pesquisa Cognitiva de Azure,](search-lucene-query-architecture.md) `$filter` com exceção das pesquisas de alcance que são *construídas* em Pesquisa Cognitiva Azure através de expressões. 

> [!NOTE]
> A sintaxe lucene completa é usada para expressões de consulta passadas no parâmetro de pesquisa da API de [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) de **Pesquisa,** não confundir com a [sintaxe OData](query-odata-filter-orderby-syntax.md) utilizada para o parâmetro [$filter](search-filters.md) daquela API. Estas diferentes sintaxes têm as suas próprias regras para construir consultas, fugir de cordas, e assim por diante.

## <a name="invoke-full-parsing"></a>Invocar a análise completa

Defina `queryType` o parâmetro de pesquisa para especificar qual o parser a utilizar. Os valores válidos incluem, `simple|full`como `simple` padrão, e `full` para Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Exemplo mostrando sintaxe completa

O exemplo seguinte encontra documentos no índice utilizando a sintaxe lucene consulta, evidente no `queryType=full` parâmetro. Esta consulta devolve hotéis onde o campo de categoria contém o termo "orçamento" e todos os campos pesquisáveis que contêm a frase "recentemente renovado". Os documentos que contêm a frase "recentemente renovado" são classificados como mais elevados em resultado do valor de aumento do termo (3).  

O `searchMode=all` parâmetro é relevante neste exemplo. Sempre que os operadores estão na `searchMode=all` consulta, deve geralmente definir para garantir que *todos os* critérios são combinados.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Em alternativa, utilize post:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Para exemplos adicionais, consulte exemplos de [sintaxe de consulta Lucene para construir consultas em Pesquisa Cognitiva Azure](search-query-lucene-examples.md). Para mais detalhes sobre a especificação do contingente completo dos parâmetros de consulta, consulte documentos de [pesquisa &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  A Azure Cognitive Search também suporta a [Sintaxe](query-simple-syntax.md)de Consulta Simples, uma linguagem de consulta simples e robusta que pode ser usada para uma pesquisa simples de palavras-chave.  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a>Fundamentos da sintaxe  

os seguintes fundamentos da sintaxe aplicam-se a todas as consultas que utilizam a sintaxe Lucene.  

### <a name="operator-evaluation-in-context"></a>Avaliação do operador em contexto

A colocação determina se um símbolo é interpretado como um operador ou apenas outro personagem numa corda.

Por exemplo, na sintaxe completa lucene, o tilde (~) é usado tanto para pesquisa difusa como para pesquisa de proximidade. Quando colocado após uma frase citada, ~ invoca a procura de proximidade. Quando colocado no final de um período, ~ invoca pesquisa difusa.

Dentro de um termo, como "business~analyst", o personagem não é avaliado como um operador. Neste caso, assumindo que a consulta é um termo ou consulta de frase, a [pesquisa completa de texto](search-lucene-query-architecture.md) com análise [lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis) tira o ~ e quebra o termo "business~analyst" em dois: business OR analyst.

O exemplo acima é o tilde (~), mas o mesmo princípio aplica-se a todos os operadores.

### <a name="escaping-special-characters"></a>Escapando de personagens especiais

Para utilizar qualquer um dos operadores de pesquisa como parte do texto de pesquisa,`\`escape ao personagem preserndo-o com um único backslash ( ). Por exemplo, para uma `https://`pesquisa `://` de wildcard em , onde `search=https\:\/\/*`faz parte da corda de consulta, você especificaria . Da mesma forma, um padrão de `\+1 \(800\) 642\-7676`número de telefone escapado pode parecer este .

Os caracteres especiais que requerem a fuga incluem:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Embora escapar mantém os tokens juntos, [a análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis) durante a indexação pode desmontá-las. Por exemplo, o analisador lucene padrão quebrará palavras sobre hífens, whitespace e outros personagens. Se precisar de caracteres especiais na corda de consulta, pode precisar de um analisador que os preserve no índice. Algumas escolhas incluem [analisadores](index-add-language-analyzers.md)de linguagem natural da Microsoft, que preserva palavras hifenizadas, ou um analisador personalizado para padrões mais complexos. Para mais informações, consulte [termos parciais, padrões e caracteres especiais.](search-query-partial-matching.md)

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificar caracteres inseguros e reservados em URLs

Certifique-se de que todos os caracteres inseguros e reservados estão codificados num URL. Por exemplo, '#' é um personagem inseguro porque é um identificador de fragmento/âncora num URL. O caracteres deve ser `%23` codificado se for utilizado num URL. '&' e '=' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva Azure. Consulte [o RFC1738: Localizadores de recursos uniformes (URL)](https://www.ietf.org/rfc/rfc1738.txt) para obter mais detalhes.

Personagens ``" ` < > # % { } | \ ^ ~ [ ]``inseguros são. Os caracteres `; / ? : @ = + &`reservados são.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Limites de tamanho de consulta

 Há um limite para o tamanho de consultas que pode enviar para a Pesquisa Cognitiva Azure. Especificamente, pode ter no máximo 1024 cláusulas (expressões separadas por E, OR, e assim por diante). Há também um limite de aproximadamente 32 KB sobre o tamanho de qualquer termo individual numa consulta. Se a sua aplicação gerar consultas de pesquisa programáticamente, recomendamos que a desenhá-lo de forma a não gerar consultas de tamanho ilimitado.  

### <a name="precedence-operators-grouping"></a>Operadores de precedência (agrupamento)

 Pode utilizar parênteses para criar subqueries, incluindo operadores dentro da declaração parêntestica. Por exemplo, `motel+(wifi||luxury)` procurará documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

O agrupamento de campo é semelhante, mas traça o agrupamento para um único campo. Por exemplo, `hotelAmenities:(gym+(wifi||pool))` procura no campo "hotelAmenities" para "ginásio" e "wifi", ou "ginásio" e "piscina".  

##  <a name="boolean-search"></a><a name="bkmk_boolean"></a>Pesquisa booleana

 Especifique sempre os operadores booleanos de texto (E, OU, NÃO) em todas as tampas.  

### <a name="or-operator-or-or-"></a>Ou `OR` operador ou`||`

O operador OR é uma barra vertical ou um caracteres de tubo. Por exemplo: `wifi || luxury` procurará documentos que contenham "wifi" ou "luxo" ou ambos. Como o OR é o operador de conjunção `wifi luxury` predefinido, `wifi || luxury`também pode deixá-lo de fora, de tal forma que é o equivalente a .

### <a name="and-operator-and--or-"></a>E `AND`operador, `&&` ou`+`

O e o operador é um ampersand ou um sinal de acompanhante. Por exemplo: `wifi && luxury` procurará documentos que contenham "wifi" e "luxo". O caráter plus (+) é utilizado para os termos necessários. Por exemplo, `+wifi +luxury` estipula que ambos os termos devem figurar algures no campo de um único documento.

### <a name="not-operator-not--or--"></a>NÃO `NOT`operador, `!` ou`-`

O operador NOT é um sinal negativo. Por exemplo, `wifi –luxury` procurará documentos `wifi` que tenham o `luxury`termo e/ou não tenham .

O parâmetro modo de **pesquisa** num pedido de consulta controla se um termo com o operador NOT é ANDed ou ORed com outros termos na consulta (assumindo que não existe nenhum `+` ou `|` operador nos outros termos). Os valores válidos incluem `any` ou `all`.

`searchMode=any`aumenta a recolha de consultas, incluindo mais `-` resultados, e por padrão será interpretado como "OU NÃO". Por exemplo, `wifi -luxury` combinará documentos `wifi` que contenham o termo `luxury`ou os que não contenham o termo .

`searchMode=all`aumenta a precisão das consultas, incluindo menos resultados, e por padrão - será interpretado como "AND NOT". Por exemplo, `wifi -luxury` combinará documentos `wifi` que contenham o termo e não contenham o termo "luxo". Este é, sem dúvida, `-` um comportamento mais intuitivo para o operador. Por isso, deve `searchMode=all` considerar `searchMode=any` a utilização em vez de se pretender otimizar as pesquisas `-` de precisão em vez de se recordar, *e* os utilizadores usam frequentemente o operador nas pesquisas.

Ao decidir sobre uma definição de modo de **pesquisa,** considere os padrões de interação do utilizador para consultas em várias aplicações. Os utilizadores que procuram informações são mais propensos a incluir um operador numa consulta, em oposição a sites de e-commerce que têm mais estruturas de navegação incorporadas.

##  <a name="fielded-search"></a><a name="bkmk_fields"></a>Pesquisa no terreno

Pode definir uma operação de `fieldName:searchExpression` pesquisa em campo com a sintaxe, onde a expressão de pesquisa pode ser uma única palavra ou uma frase, ou uma expressão mais complexa em parênteses, opcionalmente com os operadores booleanos. Alguns exemplos incluem o seguinte:  

- género:jazz NÃO história  

- artistas:("Miles Davis" "John Coltrane")

Certifique-se de colocar várias cordas dentro das aspas se quiser que ambas as cordas sejam avaliadas `artists` como uma única entidade, neste caso à procura de dois artistas distintos no campo.  

O campo especificado `fieldName:searchExpression` deve `searchable` ser um campo.  Consulte o [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos de índice são usados nas definições de campo.  

> [!NOTE]
> Ao utilizar expressões de pesquisa em campo, `searchFields` não precisa de utilizar o parâmetro porque cada expressão de pesquisa em campo tem um nome de campo explicitamente especificado. No entanto, ainda `searchFields` pode utilizar o parâmetro se quiser fazer uma consulta onde algumas partes são traçadas para um campo específico, e o resto pode aplicar-se a vários campos. Por exemplo, a `search=genre:jazz NOT history&searchFields=description` consulta `jazz` corresponderia apenas `genre` ao `NOT history` campo, enquanto combinava com o `description` campo. O nome de `fieldName:searchExpression` campo fornecido tem `searchFields` sempre precedência sobre o parâmetro, razão `genre` pela `searchFields` qual, neste exemplo, não precisamos de incluir no parâmetro.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>Pesquisa fuzzy

Uma pesquisa difusa encontra fósforos em termos que têm uma construção semelhante, expandindo um termo até ao máximo de 50 termos que cumprem os critérios de distância de dois ou menos. Para mais informações, consulte a [pesquisa fuzzy](search-query-fuzzy.md).

 Para fazer uma pesquisa difusa, utilize o símbolo tilde "~" no final de uma única palavra com um parâmetro opcional, um número entre 0 e 2 (padrão), que especifica a distância de edição. Por exemplo, "blue~" ou "blue~1" devolveriam "azul", "blues" e "cola".

 A pesquisa fuzzy só pode ser aplicada em termos, não em frases, mas você pode anexar o tilde a cada termo individualmente em um nome ou frase multi-partes. Por exemplo, "Unviersty~ de ~"Wshington~" corresponderia à "Universidade de Washington".
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a>Pesquisa de proximidade

As pesquisas de proximidade são usadas para encontrar termos que se aproximam uns dos outros num documento. Insira um símbolo de tilde "~" no final de uma frase seguida pelo número de palavras que criam o limite de proximidade. Por exemplo, `"hotel airport"~5` encontrarão os termos "hotel" e "aeroporto" dentro de 5 palavras um do outro num documento.  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a>Reforço de prazos

O aumento de prazo refere-se ao ranking de um documento mais elevado se contiver o termo reforçado, em relação a documentos que não contêm o termo. Isto difere de marcar perfis em que os perfis de pontuação impulsionam certos campos, em vez de termos específicos.  

O exemplo que se segue ajuda a ilustrar as diferenças. Suponha que há um perfil de pontuação que impulsiona os fósforos num determinado campo, digamos *género* no exemplo do [musicstoreindex.](index-add-scoring-profiles.md#bkmk_ex) O aumento de prazos poderia ser usado para aumentar ainda mais certos termos de pesquisa superiores aos outros. Por exemplo, `rock^2 electronic` irá aumentar os documentos que contêm os termos de pesquisa no campo de género mais alto do que outros campos pesquisáveis no índice. Além disso, os documentos que contenham o termo de pesquisa *rock* serão classificados acima do outro termo de pesquisa *eletrónico* como resultado do valor de aumento do termo (2).  

 Para impulsionar um termo use o cuidador, "^", símbolo com um fator de impulso (um número) no final do período que você está procurando. Também pode impulsionar frases. Quanto maior for o fator de impulso, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de impulso é 1. Embora o fator de impulso tenha de ser positivo, pode ser inferior a 1 (por exemplo, 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a>Pesquisa regular de expressão  
 Uma pesquisa de expressão regular encontra uma correspondência baseada no conteúdo entre as barras dianteiras "/", conforme documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Por exemplo, encontrar documentos que contenham `/[mh]otel/`"motel" ou "hotel", especifique. As pesquisas regulares de expressão são compatíveis com palavras simples.

Algumas ferramentas e línguas impõem requisitos adicionais de caráter de fuga. Para a JSON, as cordas que incluem um corte para `search=/.*microsoft.com\/azure\/.*/` a `search=/.* <string-placeholder>.*/` frente são escapadas `microsoft.com\/azure\/` com um corte para trás: "microsoft.com/azure/" torna-se onde configura a expressão regular, e é a corda com um corte para a frente escapou.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a>Pesquisa de Wildcard  

Você pode usar sintaxe geralmente reconhecida para múltiplas (*) ou única (?) pesquisas de caracteres wildcard. Note que o parser lucene da consulta suporta o uso destes símbolos com um único termo, e não uma frase.

A pesquisa prefixo também utiliza`*`o caracteres asterisco . Por exemplo, uma expressão `search=note*` de consulta de devoluções "caderno" ou "bloco de notas". A sintaxe lucene completa não é necessária para a pesquisa prefixo. A simples sintaxe apoia este cenário.

A pesquisa do `*` sufixo, onde ou `?` precede a corda, requer sintaxe lucene completa e uma expressão regular (você não pode usar um * ou ? símbolo como o primeiro personagem de uma pesquisa). Dado o termo "alfanumérico",`search=/.*numeric.*/`uma expressão de consulta de ( ) vai encontrar a correspondência.

> [!NOTE]  
> Durante a consulta, as consultas formuladas como prefixo, sufixo, wildcard ou expressões regulares são passadas como está na árvore de consulta, contornando a [análise lexical.](search-lucene-query-architecture.md#stage-2-lexical-analysis) Os fósforos só serão encontrados se o índice contiver as cordas no formato que a sua consulta especifica. Na maioria dos casos, você precisará de um analisador alternativo durante a indexação que preserve a integridade da cadeia para que o termo parcial e a correspondência de padrões sejam bem sucedidos. Para mais informações, consulte a pesquisa parcial a prazo em consultas de [Pesquisa Cognitiva Azure](search-query-partial-matching.md).

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a>Pontuação de wildcard e consultas regex

A Pesquisa Cognitiva Azure utiliza pontuação baseada em frequência[(TF-IDF)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)para consultas de texto. No entanto, para perguntas wildcard e regex onde o âmbito dos termos pode potencialmente ser amplo, o fator de frequência é ignorado para evitar que o ranking distorça para fósforos de termos mais raros. Todos os jogos são tratados igualmente para pesquisas de wildcard e regex.

## <a name="see-also"></a>Consulte também

+ [Exemplos de consulta para pesquisa simples](search-query-simple-examples.md)
+ [Exemplos de consulta para pesquisa completa lucene](search-query-lucene-examples.md)
+ [Documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Sintaxe de expressão OData para filtros e triagem](query-odata-filter-orderby-syntax.md)   
+ [Sintaxe de consulta simples em Pesquisa Cognitiva Azure](query-simple-syntax.md)   
