---
title: Entidades
description: Definição de entidades no âmbito da API de renderização remota Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681951"
---
# <a name="entities"></a>Entidades

Uma *Entidade* representa um objeto móvel no espaço e é o bloco de construção fundamental de conteúdo sinuoso.

## <a name="entity-properties"></a>Propriedades da entidade

As entidades têm uma transformação definida por uma posição, rotação e escala. Por si só, as entidades não têm nenhuma funcionalidade observável. Em vez disso, o comportamento é adicionado através de componentes, que estão ligados a entidades. Por exemplo, a fixação de um [CutPlaneComponent](../overview/features/cut-planes.md) criará um plano cortado na posição da entidade.

O aspeto mais importante da própria entidade é a hierarquia e a transformação hierárquica resultante. Por exemplo, quando várias entidades são ligadas como crianças a uma entidade-mãe partilhada, todas estas entidades podem ser movidas, giradas e dimensionadas em uníssono alterando a transformação da entidade-mãe.

Uma entidade é propriedade única do seu progenitor, o `Entity.Destroy()`que significa que quando o progenitor é destruído com, assim como os seus filhos e todos os [componentes conectados.](components.md) Assim, a remoção de um modelo `Destroy` da cena é realizada chamando `AzureSession.Actions.LoadModelAsync()` o nó raiz `AzureSession.Actions.LoadModelFromSASAsync()`de um modelo, devolvido por ou a sua variante SAS.

As entidades são criadas quando o servidor carrega conteúdo ou quando o utilizador quer adicionar um objeto à cena. Por exemplo, se um utilizador quiser adicionar um plano cortado para visualizar o interior de uma malha, o utilizador pode criar uma entidade onde o avião deve existir e, em seguida, adicionar-lhe o componente de plano cortado.

## <a name="query-functions"></a>Funções de consulta

Existem dois tipos de funções de consulta em entidades: chamadas sincronizadas e assíncronas. Consultas sincronizadas só podem ser usadas para dados que estejam presentes no cliente e não envolvam muita computação. Exemplos são consultas para componentes, transformações de objetos relativos ou relações pai/filho. Consultas assíncronas são usadas para dados que residem apenas no servidor ou envolvem computação extra que seria demasiado caro para ser executado no cliente. Exemplos são consultas de limites espaciais ou consultas de meta dados.

### <a name="querying-components"></a>Componentes de consulta

Para encontrar um componente de `FindComponentOfType`um tipo específico, utilize:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>A consulta transforma-se

Transformar consultas são chamadas sincronizadas sobre o objeto. É importante notar que as transformações consultadas através da API são transformações do espaço local, em relação ao progenitor do objeto. As exceções são objetos de raiz, para os quais o espaço local e o espaço mundial são idênticos.

> [!NOTE]
> Não existe API dedicada para consultar a transformação do espaço mundial de objetos arbitrários.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>Limites espaciais de consulta

As consultas de limites são chamadas assíncronas que operam numa hierarquia completa de objetos, usando uma entidade como raiz. Consulte o capítulo dedicado sobre [os limites](object-bounds.md)dos objetos .

### <a name="querying-metadata"></a>Consulta de metadados

Os metadados são dados adicionais armazenados em objetos, que são ignorados pelo servidor. Os metadados de objetos são essencialmente um conjunto de pares (nome, valor), onde o _valor_ pode ser de tipo numérico, booleano ou de corda. Os metadados podem ser exportados com o modelo.

As consultas de metadados são chamadas assíncronas numa entidade específica. A consulta apenas devolve os metadados de uma única entidade, não a informação fundida de um subgráfico.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

A consulta terá sucesso mesmo que o objeto não possua metadados.

## <a name="next-steps"></a>Passos seguintes

* [Componentes](components.md)
* [Limites de objetos](object-bounds.md)
