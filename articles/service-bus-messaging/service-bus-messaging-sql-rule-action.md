---
title: Referência sintaxe SQLRuleAction no Ônibus de Serviço Azure
description: Este artigo fornece uma referência para a sintaxe SQLRuleAction. As ações são escritas em sintaxe baseada em língua SQL que é realizada contra uma mensagem intermediada.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 37615e39577ef60cccc9df91b61a6aa24ca794d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759633"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>Referência sintaxe SQLRuleAction para ônibus de serviço Azure

A *SqlRuleAction* é um exemplo da classe [SqlRuleAction,](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) e representa um conjunto de ações escritas na sintaxe baseada em linguagem SQL que é realizada contra uma [Mensagem Intermediada](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Este artigo lista detalhes sobre a gramática de ação de regra SQL.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Argumentos  
  
-   `<scope>`é uma cadeia opcional que `<property_name>`indica o alcance do . Valores `sys` válidos são ou `user`. O `sys` valor indica `<property_name>` o âmbito do sistema onde é um nome de propriedade pública da [Classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`indica o `<property_name>` âmbito do utilizador onde é uma chave do dicionário [classe BrokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) `user`âmbito é o `<scope>` âmbito padrão se não for especificado.  
  
### <a name="remarks"></a>Observações  

Uma tentativa de aceder a uma propriedade do sistema inexistente é um erro, enquanto uma tentativa de aceder a uma propriedade de utilizador inexistente não é um erro. Em vez disso, uma propriedade de utilizador inexistente é avaliada internamente como um valor desconhecido. Um valor desconhecido é tratado especialmente durante a avaliação do operador.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentos  
 `<regular_identifier>`é uma cadeia representada pela seguinte expressão regular:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Isto significa qualquer corda que comece com uma letra e seja seguida por um ou mais sublinhados/letra/dígito.  
  
 `[:IsLetter:]`significa qualquer personagem Unicode que seja categorizado como uma letra Unicode. `System.Char.IsLetter(c)``true` devoluções `c` se for uma letra Unicode.  
  
 `[:IsDigit:]`significa qualquer personagem Unicode que seja categorizado como um dígito decimal. `System.Char.IsDigit(c)``true` devoluções `c` se for um dígito Unicode.  
  
 A `<regular_identifier>` não pode ser uma palavra-chave reservada.  
  
 `<delimited_identifier>`é qualquer corda que seja fechada com suportes quadrados esquerdo/direito ([]). Um suporte quadrado direito é representado como dois suportes quadrados direito. Seguem-se exemplos de: `<delimited_identifier>`  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`é qualquer corda que seja fechada com duas aspas. Uma marca dupla de citação no identificador é representada como duas marcas duplas de citação. Não é aconselhável utilizar identificadores citados porque pode facilmente ser confundido com uma constante de corda. Utilize um identificador delimitado, se possível. Segue-se um `<quoted_identifier>`exemplo de:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>padrão  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações
  
 `<pattern>`deve ser uma expressão que é avaliada como uma corda. É usado como um padrão para o operador LIKE.      Pode conter os seguintes caracteres wildcard:  
  
-   `%`: Qualquer série de caracteres zero ou mais.  
  
-   `_`: Qualquer personagem único.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações
  
 `<escape_char>`deve ser uma expressão que é avaliada como uma cadeia de comprimento 1. É usado como um personagem de fuga para o operador LIKE.  
  
 Por `property LIKE 'ABC\%' ESCAPE '\'` exemplo, `ABC%` combina em vez `ABC`de uma corda que começa com .  
  
## <a name="constant"></a>constante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentos  
  
-   `<integer_constant>`é uma série de números que não estão fechados em aspas e não contêm pontos decimais. Os valores são `System.Int64` armazenados internamente e seguem a mesma gama.  
  
     Seguem-se exemplos de longas constantes:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`é uma série de números que não estão fechados em aspas, e contêm um ponto decimal. Os valores são `System.Double` armazenados internamente e seguem a mesma gama/precisão.  
  
     Numa versão futura, este número pode ser armazenado num tipo de dados diferente para suportar a semântica `System.Double` `<decimal_constant>`exata dos números, pelo que não deve confiar no facto de o tipo de dados subjacente ser para .  
  
     Seguem-se exemplos de constantes decimais:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`é um número escrito em notação científica. Os valores são `System.Double` armazenados internamente e seguem a mesma gama/precisão. Seguem-se exemplos de constantes de número supressão:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Observações
  
As constantes booleanas são `TRUE` representadas pelas palavras-chave ou `FALSE`. Os valores são `System.Boolean`armazenados como .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Observações
  
As constantes de cordas são fechadas em letras únicas e incluem quaisquer caracteres Unicode válidos. Uma única marca de citação incorporada numa constante de corda é representada como duas únicas marcas de citação.  
  
## <a name="function"></a>função  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Observações  

A `newid()` função devolve um **System.Guid** gerado pelo `System.Guid.NewGuid()` método.  
  
A `property(name)` função devolve o valor `name`do imóvel referenciado por . O `name` valor pode ser qualquer expressão válida que retorne um valor de cadeia.  
  
## <a name="considerations"></a>Considerações

- O SET é usado para criar um novo imóvel ou atualizar o valor de um imóvel existente.
- Remove é usado para remover uma propriedade.
- O SET realiza uma conversão implícita, se possível, quando o tipo de expressão e o tipo de propriedade existente são diferentes.
- A ação falha se as propriedades do sistema inexistentes forem referenciadas.
- A ação não falha se as propriedades de utilizador inexistentes forem referenciadas.
- Uma propriedade de utilizador inexistente é avaliada internamente como "Desconhecida", seguindo a mesma semântica que o [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) ao avaliar os operadores.

## <a name="next-steps"></a>Passos seguintes

- [Classe SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Classe SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
