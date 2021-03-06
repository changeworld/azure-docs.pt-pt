---
title: Render dados personalizados num mapa de rasters / Microsoft Azure Maps
description: Neste artigo, você aprenderá a renderizar dados personalizados num mapa de raster usando o Serviço de imagem estática do Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335507"
---
# <a name="render-custom-data-on-a-raster-map"></a>Render dados personalizados num mapa de raster

Este artigo explica como usar o serviço de [imagem estática,](https://docs.microsoft.com/rest/api/maps/render/getmapimage)com funcionalidade de composição de imagem, para permitir sobreposições em cima de um mapa de raster. A composição da imagem inclui a capacidade de recuperar um azulejo raster, com dados adicionais como pinos personalizados, etiquetas e sobreposições de geometria.

Para tornar os pinos personalizados, rótulos e sobreposições de geometria, pode utilizar a aplicação Postman. Pode utilizar [APIs](https://docs.microsoft.com/rest/api/maps/data) do Serviço de Dados Do Azure Maps para armazenar e rendersobreções.

> [!Tip]
> Muitas vezes é muito mais rentável usar o Azure Maps Web SDK para mostrar um mapa simples numa página web do que usar o serviço de imagem estática. O SDK web usa azulejos de mapas e, a menos que o utilizador faça panelas e zoom o mapa, muitas vezes gerarão apenas uma fração de uma transação por carga de mapa. Note que o Web SDK do Azure Maps tem opções para desativar a panorâmica e o zooming. Além disso, o Web SDK do Azure Maps fornece um conjunto mais rico de opções de visualização de dados do que um serviço web de mapa estático.  

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Para completar os procedimentos neste artigo, primeiro é necessário criar uma conta Azure Maps e obter a chave da conta dos mapas. Siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma subscrição de conta Azure Maps e siga os passos para obter a [chave](quick-demo-map-app.md#get-the-primary-key-for-your-account) principal para obter a chave principal para a sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Renderpins com rótulos e uma imagem personalizada

> [!Note]
> O procedimento nesta secção requer uma conta Azure Maps nos níveis de preços S0 ou S1.

A conta Azure Maps S0 suporta apenas `pins` uma instância do parâmetro. Permite-lhe renderizar até cinco pinos, especificados no pedido de URL, com uma imagem personalizada.

Para renderizar pinos com etiquetas e uma imagem personalizada, complete estes passos:

1. Crie uma coleção para armazenar os pedidos. Na aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**. Nomeie a coleção e selecione o botão **Criar.** 

2. Para criar o pedido, selecione **Nova** novamente. Na janela **Criar Nova,** selecione **Pedido**. Introduza um **nome de pedido** para os pinos. Selecione a coleção que criou no passo anterior, como a localização para guardar o pedido. Em seguida, selecione **Guardar**.
    
    ![Criar um pedido no Carteiro](./media/how-to-render-custom-data/postman-new.png)

3. Selecione o método GET HTTP no separador construtor e introduza o seguinte URL para criar um pedido GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Aqui está a imagem resultante:

    ![Um pino personalizado com uma etiqueta](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Obtenha dados do armazenamento de dados do Azure Maps

> [!Note]
> O procedimento nesta secção requer uma conta Azure Maps no nível de preços S1.

Também pode obter as informações de localização do caminho e pin utilizando a [API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)de Upload de Dados . Siga os passos abaixo para fazer upload dos dados do caminho e pinos.

1. Na aplicação Postman, abra um novo separador na coleção que criou na secção anterior. Selecione o método POST HTTP no separador construtor e introduza o seguinte URL para fazer um pedido de post:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. No separador **Params,** introduza os seguintes pares chave/valor, que são utilizados para o URL de pedido de POST. Substitua `subscription-key` o valor pela chave de subscrição do Azure Maps.
    
    ![Principais params de chave/valor no Carteiro](./media/how-to-render-custom-data/postman-key-vals.png)

3. No separador **Body,** selecione o formato de entrada crua e escolha o JSON como formato de entrada na lista de dropdown. Forneça este JSON como dados a serem carregados:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Selecione **Enviar** e reveja o cabeçalho de resposta. Após um pedido bem sucedido, o cabeçalho de localização conterá o estado URI para verificar o estado atual do pedido de upload. O estado URI seria do seguinte formato.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Copie o seu estado URI e ateste o parâmetro de chave de subscrição com o valor da chave de subscrição da sua conta Azure Maps. Utilize a mesma chave de subscrição de conta que usou para fazer o upload dos dados. O formato URI de estatuto deve parecer-se com o seguinte:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Para obter o udId, abra um novo separador na aplicação Postman. Selecione o método GET HTTP no separador construtor. Faça um pedido GET no estado URI. Se o seu upload de dados tiver sido bem sucedido, receberá um udId no corpo de resposta. Copie o udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Utilize `udId` o valor recebido da API de upload de dados para renderizar funcionalidades no mapa. Para tal, abra um novo separador na coleção que criou na secção anterior. Selecione o método GET HTTP no separador construtor, substitua a {chave de subscrição} e {udId} com os seus valores e introduza este URL para fazer um pedido GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Aqui está a imagem de resposta:

    ![Obtenha dados do armazenamento de dados do Azure Maps](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Render um polígono com cor e opacidade

> [!Note]
> O procedimento nesta secção requer uma conta Azure Maps no nível de preços S1.


Pode modificar a aparência de um polígono utilizando modificadores de estilo com o parâmetro do [caminho](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. Na aplicação Postman, abra um novo separador na coleção que criou anteriormente. Selecione o método GET HTTP no separador construtor e introduza o seguinte URL para configurar um pedido GET para renderizar um polígono com cor e opacidade:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Aqui está a imagem de resposta:

    ![Render um polígono opaco](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Renderum círculo e pinos com etiquetas personalizadas

> [!Note]
> O procedimento nesta secção requer uma conta Azure Maps no nível de preços S1.


Pode modificar a aparência dos pinos adicionando modificadores de estilo. Por exemplo, para fazer pinos e respetivas etiquetas `sc` maiores ou menores, utilize o modificador "scale style". Este modificador tem um valor superior a zero. Um valor de 1 é a escala padrão. Valores maiores que 1 tornarão os pinos maiores, e valores menores que 1 os tornarão menores. Para obter mais informações sobre modificadores de estilo, consulte [os parâmetros do caminho](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)do serviço de imagem estática .


Siga estes passos para renderizar um círculo e empurrões com etiquetas personalizadas:

1. Na aplicação Postman, abra um novo separador na coleção que criou anteriormente. Selecione o método GET HTTP no separador construtor e introduza este URL para fazer um pedido GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Aqui está a imagem de resposta:

    ![Renderum círculo com pinos personalizados](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Para alterar a cor dos pinos do último passo, mude o modificador de estilo "co". Olhe `pins=default|la15+50|al0.66|lc003C62|co002D62|`para, a cor atual seria especificada como #002D62 em CSS. Digamos que quer mudá-lo para #41d42a. Escreva o novo valor de cor após o `pins=default|la15+50|al0.66|lc003C62|co41D42A|`especificador "co", assim: . Faça um novo pedido GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Aqui está a imagem de resposta depois de mudar as cores dos pinos:

    ![Renderizar um círculo com pinos atualizados](./media/how-to-render-custom-data/circle-updated-pins.png)

Da mesma forma, pode alterar, adicionar e remover outros modificadores de estilo.

## <a name="next-steps"></a>Passos seguintes


* Explore a documentação da API do [Azure Maps.](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* Para saber mais sobre o Serviço de Dados do Azure Maps, consulte a documentação do [serviço.](https://docs.microsoft.com/rest/api/maps/data)

