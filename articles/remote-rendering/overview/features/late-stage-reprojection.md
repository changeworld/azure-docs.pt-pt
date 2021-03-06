---
title: Reprojeção de estágio tardio
description: Informação sobre reprojecção de fase final e como usá-la.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680989"
---
# <a name="late-stage-reprojection"></a>Reprojeção de estágio tardio

*Late Stage Reprojection* (LSR) é uma funcionalidade de hardware que ajuda a estabilizar hologramas quando o utilizador se move.

Espera-se que os modelos estáticos mantenham visualmente a sua posição quando se deslocam à sua volta. Se parecerem instáveis, este comportamento pode indicar problemas de LSR. Lembre-se que transformações dinâmicas adicionais, como animações ou vistas de explosão, podem mascarar este comportamento.

Pode escolher entre dois modos LSR diferentes, nomeadamente **Planar LSR** ou **Depth LSR**. Qual é ativo é determinado se o pedido do cliente submete um tampão de profundidade.

Ambos os modos LSR melhoram a estabilidade do holograma, embora tenham as suas limitações distintas. Comece por tentar Depth LSR, pois está indiscutivelmente a dar melhores resultados na maioria dos casos.

## <a name="choose-lsr-mode-in-unity"></a>Escolha o modo LSR em Unidade

No editor da Unidade, vá ao *Arquivo > Construir Definições.* Selecione *Definições do jogador* na esquerda inferior e, em seguida, verifique em *definições de > XR do Jogador > SDKs de realidade virtual > Realidade Mista do Windows* se a partilha de **tampão** de profundidade é verificada:

![Bandeira ativada por partilha de tampão de profundidade](./media/unity-depth-buffer-sharing-enabled.png)

Se for, a sua aplicação utilizará o Depth LSR, caso contrário utilizará o Planar LSR.

## <a name="depth-lsr"></a>LSR profundidade

Para que a Depth LSR funcione, a aplicação do cliente deve fornecer um tampão de profundidade válido que contenha toda a geometria relevante a considerar durante a LSR.

A profundidade LSR tenta estabilizar a moldura de vídeo com base no conteúdo do tampão de profundidade fornecido. Como consequência, o conteúdo que não lhe foi prestado, como objetos transparentes, não pode ser ajustado pela LSR e pode mostrar instabilidade e artefactos de reprojeção.

## <a name="planar-lsr"></a>Planar LSR

A Planar LSR não tem informações de profundidade por pixel, como o Depth LSR tem. Em vez disso, reprojeta todos os conteúdos com base num plano que deve fornecer cada quadro.

Planar LSR reprojeta os objetos que melhor se encontram perto do plano fornecido. Quanto mais longe um objeto estiver, mais instável vai parecer. Embora a Depth LSR seja melhor a reprojectar objetos em diferentes profundidades, o Planar LSR pode funcionar melhor para o conteúdo alinhar-se bem com um plano.

### <a name="configure-planar-lsr-in-unity"></a>Configure Planar LSR em Unidade

Os parâmetros do avião são derivados de um chamado *ponto*de `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`foco , que você tem que fornecer cada quadro através de . Consulte a [API de Focus Point](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) de Unidade para obter mais detalhes. Se não definir um ponto de foco, um recuo será escolhido para si. No entanto, esse recuo automático conduz frequentemente a resultados sub-ideais.

Você pode calcular o ponto de foco por si mesmo, embora possa fazer sentido baseá-lo no calculado pelo anfitrião de renderização remota. Ligue `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` para obtê-lo. É-lhe pedido que forneça uma moldura coordenada para expressar o ponto de foco. Na maioria dos casos, só vai `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` querer fornecer o resultado daqui.

Normalmente, tanto o cliente como o anfitrião tornam o conteúdo que o outro lado desconhece, como elementos ui no cliente. Portanto, pode fazer sentido combinar o ponto de foco remoto com um calculado localmente.

Os pontos de foco calculados em dois quadros sucessivos podem ser bastante diferentes. Simplesmente usá-los como está pode levar a hologramas que parecem estar saltando ao redor. Para prevenir este comportamento, a interpolação entre os pontos de foco anteriores e atuais é aconselhável.

## <a name="next-steps"></a>Passos seguintes

* [Consultas de desempenho do lado do servidor](performance-queries.md)
