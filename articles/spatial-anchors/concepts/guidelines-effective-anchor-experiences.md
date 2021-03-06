---
title: Orientações para experiências eficazes de âncora
description: Diretrizes e considerações para criar e localizar âncoras eficazmente utilizando âncoras espaciais Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9a24da8d76f401f534eccf33312fbf0c2bee9f5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270527"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Criar uma experiência de âncora eficaz usando âncoras espaciais Azure

Este artigo fornece diretrizes e considerações para ajudá-lo a criar e localizar âncoras eficazmente usando âncoras espaciais.

## <a name="good-anchors"></a>Boas âncoras

As Âncoras Espaciais ajudam-no a criar boas âncoras. É importante investir tempo na educação ou orientação dos utilizadores na sua experiência de utilizador (UX) para criar boas âncoras. Ao investir na criação de boas âncoras à frente, ajuda os utilizadores finais a encontrar âncoras de forma fiável:

- Através de diferentes dispositivos.
- Em vários momentos.
- Em diferentes condições de iluminação.
- Das perspetivas desejadas dentro do espaço.

## <a name="static-and-dynamic-locations"></a>Locais estáticos e dinâmicos

Parte da conceção da experiência de âncora é escolher os locais. Os locais serão estáticos e definidos por um administrador do espaço? Ou serão dinâmicos e definidos pelo utilizador?

Um gerente de uma loja de retalho pode querer uma experiência estática na loja para cativar os utilizadores a visitar. Mas o desenvolvedor de um jogo de tabuleiro de realidade mista provavelmente quereria deixar os utilizadores escolherem onde jogar.

Para localizações estáticas, pode ensinar os administradores a passar em tempo a curadoria do espaço com boas âncoras.

Para localizações dinâmicas, deve pensar em como ensina ou guia os utilizadores no seu UX para criar boas âncoras.

## <a name="stable-visual-features"></a>Características visuais estáveis

Os sistemas de rastreio visual utilizados em dispositivos de realidade mista e realidade aumentada dependem de características visuais do ambiente. Para obter a experiência mais fiável:

- *Crie* âncoras em locais que tenham características visuais estáveis (isto é, características que não mudam frequentemente).

- *Não* crie âncoras em grandes superfícies em branco que não tenham características distintivas.

- *Não* crie âncoras em materiais altamente reflexivos.

- *Não* crie âncoras em superfícies onde o padrão se repita, como carpete ou papel de parede.

![Exemplos de um bom ambiente para âncoras e um mau ambiente para âncoras](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Várias perspetivas de visualização

Ao criar uma âncora, pense nas pessoas que mais tarde tentarão localizar a âncora.

Considere, por exemplo, uma âncora no meio de uma sala que tem duas portas. É provável que os utilizadores entrem na sala a partir de qualquer das portas. Ao criar a âncora, terá de digitalizar a sua posição a partir de ambas as portas. Muda perspetivas para capturar dados ambientais em torno da âncora para que os utilizadores possam localizar a âncora de qualquer porta.

Em geral, ao criar uma âncora, scaneie-a das perspetivas das pessoas que tentarão localizá-la. Por isso, se estiver a colocar conteúdo virtual numa escultura ao ar livre, faz sentido andar à volta da escultura, enquanto a digitaliza, enquanto cria a âncora. Se a sua âncora está no canto de uma sala, só há uma direção para se aproximar. Ao criar esta âncora, pode scaneá-la apenas desta perspetiva.

## <a name="multiple-anchors"></a>Âncoras múltiplas

A iluminação pode fazer a diferença nas funcionalidades visuais que uma aplicação deteta. Âncoras criadas com forte luz natural podem ser difíceis de localizar em luz artificial, e vice-versa.

Se tiver este problema, pode ajudar a criar duas âncoras. No mesmo local, crie uma âncora à luz do dia e outra à luz artificial. A sua aplicação pode então consultar ambas as âncoras. Quando uma das âncoras estiver localizada, a aplicação terá uma pose para a âncora.

Da mesma forma, em ambientes onde as características visuais mudam porque a maioria dos objetos se movem, várias âncoras podem ajudar. Quando uma âncora se torna muito difícil de encontrar devido a mudanças significativas no ambiente, pode substituir a âncora por uma nova. Você pode fazê-lo, por exemplo, em uma loja de retalho onde o layout é atualizado a cada poucos meses.

## <a name="targets-and-rooms"></a>Alvos e quartos

Em muitos casos, uma âncora é um ponto de entrada para a experiência da sua aplicação. Você vai querer passar por este passo de forma rápida e fiável para que os utilizadores possam entrar na sua experiência. Gastar tempo em como os utilizadores vão encontrar as suas âncoras é um passo importante de design. É útil pensar em encontrar âncoras em termos de dois cenários amplos: alvos e *quartos.* *rooms*

### <a name="targets"></a>Destinos

No cenário alvo, a localização de uma âncora é bem conhecida. Por exemplo, numa aplicação fictícia de pintura de realidade mista, um utilizador coloca uma tela virtual na parede. Ela instrui os outros utilizadores na sala a apontar os seus dispositivos para o mesmo local na parede para localizar a âncora e iniciar a experiência.

Outro exemplo de um cenário alvo pode ser um sinal num café que diz: "Scan para ofertas." O café colocou uma âncora aqui. À medida que os utilizadores digitalizam o sinal, localizam a âncora e entram na experiência de realidade aumentada para encontrar ofertas no café.

No cenário alvo, as fotos podem ajudar. Se mostrar aos utilizadores uma foto do alvo pretendido no seu dispositivo, eles podem rapidamente identificar o que digitalizar no mundo real. Por exemplo, pode ajudar os seus utilizadores a chegar à área geral de um alvo pretendido utilizando O GPS. Quando o utilizador chega, a sua aplicação mostra uma foto do alvo. O utilizador olha ao redor do espaço, encontra o alvo, e procura a âncora.

![Ilustração de uma âncora, mostrando uma foto do alvo no dispositivo móvel de um utilizador](./media/start-here-edit.png)

### <a name="rooms"></a>Quartos

No cenário da sala, os utilizadores entram num espaço simplesmente sabendo que há uma âncora aqui em algum lugar. Os utilizadores digitalizam o espaço com o seu dispositivo e localizam rapidamente a âncora.

Esta experiência normalmente requer que você crie âncoras bem curadas, como discutido em Várias perspetivas de visualização. Se você digitalizou a sala de muitas perspetivas quando criou a âncora, os utilizadores podem digitalizar em qualquer lugar quando tentam localizá-lo.

![Ilustração de como um utilizador pode digitalizar um quarto para encontrar uma âncora](./media/scan-room.png)

Essencialmente, passa mais tempo a digitalizar o espaço quando cria a âncora para que os utilizadores posteriores possam digitalizar e localizar a âncora rapidamente. À medida que crias a tua experiência, terás de considerar esta importante troca.

O exemplo da aplicação de pintura de realidade mista que discutimos anteriormente não funciona bem como um cenário de sala. Aqui, o utilizador que coloca a âncora quer que outros se juntem à experiência rapidamente. Os utilizadores não querem esperar para iniciar a experiência até que a sala esteja bem digitalizada. Como todos os utilizadores sabem exatamente onde apontar o seu dispositivo para localizar as âncoras, este exemplo funciona melhor como um cenário de alvo.

## <a name="anchor-location"></a>Localização da âncora

Os sistemas de rastreio visual dependem das características visuais num ambiente. Quanto mais características visuais um exame inclui, maior é a probabilidade de encontrar uma âncora.

Siga as diretrizes gerais nesta secção para construir um UX que incentive uma digitalização útil do ambiente.

Primeiro, se o utilizador não localizar uma âncora dentro de alguns segundos, a aplicação deve incentivar os utilizadores a mover em movimento o dispositivo para capturar mais perspetivas. A aplicação também pode incentivar os utilizadores a moverem-se pelo ambiente para procurarem a âncora de mais perspetivas. Quanto mais perspetivas de recurso o dispositivo vê, melhor.

Para cenários-alvo, peça ao utilizador que se mova em torno do alvo para vê-lo de diferentes perspetivas. Por outras palavras, peça ao utilizador que capture o alvo de novas perspetivas até que a âncora esteja localizada.

Para cenários de quarto, peça ao utilizador para digitalizar lentamente a sala. Por exemplo, peça ao utilizador para se virar para capturar 180 graus ou mesmo 360 graus da sala. Ou peça ao utilizador para ver o quarto de uma nova perspetiva.

O método mais significativo é digitalizar através da sala. Uma varredura em toda a sala captura mais características visuais do ambiente do que uma varredura de uma parede próxima, por exemplo. Uma varredura de uma parede próxima não capturará tantas características visuais úteis do ambiente.

Não é útil mover repetidamente o dispositivo de um lado para o outro quando se procura uma âncora. Isto simplesmente captura os mesmos pontos da mesma perspetiva.

## <a name="experience-tests"></a>Testes de experiência

Neste artigo, discutimos as diretrizes gerais. Com as Âncoras Espaciais, estás a escrever aplicações que interagem com o mundo real. Por isso, deve dedicar tempo para testar os cenários de âncora da sua aplicação em ambientes reais. Isto é especialmente verdade para ambientes que representam onde espera que os seus utilizadores utilizem a app.
