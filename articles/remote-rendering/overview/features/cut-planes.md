---
title: Aviões cortados
description: Explica o que são os aviões cortados e como usá-los
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681028"
---
# <a name="cut-planes"></a>Aviões cortados

Um *plano de corte* é uma característica visual que corta pixels de um lado de um plano virtual, revelando o interior das [malshes.](../../concepts/meshes.md)
A imagem abaixo demonstra o efeito. A esquerda mostra a malha original, à direita pode olhar para dentro da malha:

![Corte de avião](./media/cutplane-1.png)

## <a name="limitations"></a>Limitações

* Por enquanto, a Azure Remote Rendering suporta um **máximo de oito aviões de corte ativo.** Pode criar mais componentes de plano sinuosos, mas se tentar permitir mais simultaneamente, ignorará a ativação. Desative primeiro outros aviões se quiser mudar o componente que deve afetar a cena.
* Cada plano cortado afeta todos os objetos telecomandados. Atualmente, não existe forma de excluir objetos específicos ou peças de malha.
* Os aviões cortados são puramente uma característica visual, não afetam o resultado das [consultas espaciais.](spatial-queries.md) Se quiser fazer um ray em uma malha aberta, pode ajustar o ponto de partida do raio para estar no plano de corte. Desta forma o raio só pode atingir partes visíveis.

## <a name="performance-considerations"></a>Considerações de desempenho

Cada avião de corte ativo incorre num pequeno custo durante a renderização. Desative ou apague os aviões cortados quando não são necessários.

## <a name="cutplanecomponent"></a>Componente cutplane

Adicione um plano de corte ao local criando um *CutPlaneComponent*. A localização e orientação do avião é determinada pela [entidade](../../concepts/entities.md)proprietária do componente.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Propriedades CutPlaneComponent

As seguintes propriedades são expostas num componente de plano cortado:

* **Habilitado:** Pode desligar temporariamente os planos cortados desativando o componente. Aviões de corte deficientes não incorrem em sobrecargas e também não contam com o limite global de aviões de corte.

* **Normal:** Especifica qual é a direção (+X,-X,+Y,-Y,+Z,-Z) é usada como o plano normal. Esta direção é relativa à orientação da entidade proprietária. Mova e rode a entidade proprietária para a colocação exata.

* **FadeColor** e **FadeLength:**

  Se o valor alfa do *FadeColor* não for zero, os pixels próximos do plano de corte desaparecerão em direção à parte RGB do FadeColor. A força do canal alfa determina se irá desaparecer totalmente em direção à cor desvanecida ou apenas parcialmente. *FadeLength* define sobre qual distância esta distância terá lugar.

## <a name="next-steps"></a>Passos seguintes

* [Renderização unilateral](single-sided-rendering.md)
* [Consultas espaciais](spatial-queries.md)
