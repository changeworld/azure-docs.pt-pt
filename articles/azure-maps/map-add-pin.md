---
title: Adicione uma camada de símbolo a um mapa Microsoft Azure Maps
description: Neste artigo, você aprenderá sobre como usar a camada de símbolo para personalizar um símbolo, e adicionar símbolos num mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209703"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Adicione uma camada de símbolo a um mapa

Ligue um símbolo a uma fonte de dados e use-o para renderizar um ícone ou um texto num determinado ponto. 

As camadas de símbolo são renderizadas usando o WebGL. Use uma camada de símbolo para renderizar grandes coleções de pontos no mapa. Em comparação com o marcador HTML, a camada de símbolo supor um grande número de dados de pontos no mapa, com melhor desempenho. No entanto, a camada de símbolo não suporta elementos CSS e HTML tradicionais para o styling.  

> [!TIP]
> As camadas de símbolo por padrão tornarão as coordenadas de todas as geometrias numa fonte de dados. Para limitar a camada de tal forma que `filter` apenas torna `['==', ['geometry-type'], 'Point']` as `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` características de geometria de pontos definir a propriedade da camada para ou se quiser, você pode incluir características MultiPoint também.

O gestor de sprite de imagem maps carrega imagens personalizadas usadas pela camada de símbolo. Suporta os seguintes formatos de imagem:

- JPEG
- PNG
- SVG
- BMP
- GIF (sem animações)

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

Antes de poder adicionar uma camada de símbolo ao mapa, precisa de dar alguns passos. Primeiro, crie uma fonte de dados e adicione-a ao mapa. Criar uma camada de símbolo. Em seguida, passe a fonte de dados para a camada de símbolo, para recuperar os dados da fonte de dados. Finalmente, adicione dados na fonte de dados, para que haja algo a ser prestado. 

O código abaixo demonstra o que deve ser adicionado ao mapa depois de ter carregado. Esta amostra torna um único ponto no mapa usando uma camada de símbolo. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Existem quatro tipos diferentes de dados de pontos que podem ser adicionados ao mapa:

- GeoJSON Point geometria - Este objeto contém apenas uma coordenada de um ponto e nada mais. A `atlas.data.Point` classe de ajudante pode ser usada para criar facilmente estes objetos.
- GeoJSON MultiPoint geometria - Este objeto contém as coordenadas de vários pontos e nada mais. A `atlas.data.MultiPoint` classe de ajudante pode ser usada para criar facilmente estes objetos.
- GeoJSON Feature - Este objeto consiste em qualquer geometria GeoJSON e um conjunto de propriedades que contêm metadados associados à geometria. A `atlas.data.Feature` classe de ajudante pode ser usada para criar facilmente estes objetos.
- `atlas.Shape`classe é semelhante à funcionalidade GeoJSON. Ambos consistem numa geometria GeoJSON e num conjunto de propriedades que contêm metadados associados à geometria. Se um objeto GeoJSON for adicionado a uma fonte de dados, pode facilmente ser renderizado numa camada. No entanto, se a propriedade coordenada desse objeto GeoJSON for atualizada, a fonte de dados e o mapa não mudam. Isso é porque não há nenhum mecanismo no objeto JSON para desencadear uma atualização. A classe de forma fornece funções para atualizar os dados que contém. Quando uma alteração é feita, a fonte de dados e o mapa são automaticamente notificados e atualizados. 

A amostra de código seguinte cria uma geometria `atlas.Shape` GeoJSON Point e passa-a para a classe para facilitar a atualização. O centro do mapa é inicialmente usado para renderizar um símbolo. Um evento de clique é adicionado ao mapa de modo a que quando `setCoordinates` dispara, as coordenadas do rato são usadas com a função de formas. As coordenadas do rato são gravadas no momento do clique. Em seguida, a `setCoordinates` atualização da localização do símbolo no mapa.

<br/>

<iframe height='500' scrolling='no' title='Mudar a localização do pino' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a localização do <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>pino</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen Switch por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Por padrão, as camadas de símbolo otimizam a renderização dos símbolos escondendo símbolos que se sobrepõem. À medida que se aproxima, os símbolos ocultos tornam-se visíveis. Para desativar esta funcionalidade e renderizar `allowOverlap` todos os `iconOptions` símbolos em todos os momentos, detete a propriedade das opções para `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Adicione um ícone personalizado a uma camada de símbolo

As camadas de símbolo são renderizadas usando o WebGL. Como tal, todos os recursos, como imagens de ícones, devem ser carregados no contexto WebGL. Esta amostra mostra como adicionar um ícone personalizado aos recursos do mapa. Este ícone é então usado para renderizar dados de pontos com um símbolo personalizado no mapa. A `textField` propriedade da camada de símbolo requer uma expressão a especificar. Neste caso, queremos tornar a propriedade da temperatura. Como a temperatura é um número, tem de ser convertida numa corda. Além disso, queremos anexar -lhe "°F". Uma expressão pode ser usada para fazer esta concatenação; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ícone de imagem de símbolo personalizado' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o ícone de imagem de<a href='https://codepen.io/azuremaps'>@azuremaps</a>símbolo <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>personalizado</a> pen por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O Web SDK do Azure Maps fornece vários modelos de imagem personalizáveis que pode usar com a camada de símbolo. Para mais informações, consulte o documento [de como utilizar modelos de imagem.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-symbol-layer"></a>Personalize uma camada de símbolo 

A camada de símbolo tem muitas opções de estilo disponíveis. Aqui está uma ferramenta para testar estas várias opções de estilo.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de símbolo' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/PxVXje/'>opções</a> de camada<a href='https://codepen.io/azuremaps'>@azuremaps</a>de símbolo de caneta por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando pretende renderizar apenas texto com uma camada de `image` símbolo, pode esconder `'none'`o ícone definindo a propriedade das opções do ícone para .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [SímboloCamada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Opções de Matador de Símbolos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Opções de Ícones](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Opções de Texto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma origem de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Como utilizar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar fabricantes html](map-add-bubble-layer.md)
