---
title: 'Executar vários serviços dependentes: .NET Core & Visual Studio'
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: Este tutorial mostra-lhe como usar o Azure Dev Spaces e o Visual Studio para depurar uma aplicação multi-serviço .NET Core no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: 7f95c21c2cf5b7adcdb34d7bbe2b1f8314c20333
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75438393"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-with-azure-dev-spaces"></a>Executar vários serviços dependentes: .NET Core e Visual Studio com Azure Dev Spaces

Neste tutorial, você vai aprender a desenvolver aplicações multi-serviço usando espaços Azure Dev, juntamente com alguns dos benefícios adicionais que a Dev Spaces proporciona.

## <a name="call-another-container"></a>Chamar outro contentor
Nesta secção, vai criar um segundo serviço, `mywebapi`e `webfrontend` chamá-lo. Cada serviço vai ser executado em contentores separados. Em seguida, vai realizar a depuração em ambos os contentores.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Transfira o código de exemplo para *mywebapi*
Para ser mais rápido, vamos transferir código de exemplo de um repositório do GitHub. Aceda a https://github.com/Azure/dev-spaces e selecione **Clone or Download** (Clonar ou Transferir) para transferir o repositório do GitHub. O código para esta secção está em `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Execute *mywebapi*
1. Abra o projeto `mywebapi` numa *janela separada do Visual Studio*.
1. Selecione **Azure Dev Spaces** no menu pendente de definições de início, tal como fez anteriormente para o projeto `webfrontend`. Agora, em vez de criar um novo cluster do AKS, selecione o mesmo que já criou. Tal como antes, mantenha a predefinição `default` em Space (Espaço) e clique em **OK**. Na janela Output, poderá notar que o Visual Studio começa a "aquecer" este novo serviço no seu espaço de dev para acelerar as coisas quando começar a depurar.
1. Prima F5 e aguarde que o serviço seja criado e implementado. Saberá que está pronto quando a barra de estado do Visual Studio ficar cor de laranja
1. Tome nota do URL do ponto final apresentado nos **espaços Azure Dev para** o painel AKS na janela **saída.** Terá um aspeto semelhante a `http://localhost:<portnumber>`. Poderá parecer que o contentor está a ser executado localmente. Contudo, na verdade, está a ser executado no espaço de programador no Azure.
2. Quando o projeto `mywebapi` estiver pronto, abra o browser no endereço localhost e acrescente `/api/values` ao URL para invocar a API GET predefinida para `ValuesController`. 
3. Se todos os passos tiverem sido concluídos com êxito, deverá conseguir ver uma resposta do serviço `mywebapi` com um aspeto semelhante ao seguinte.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Efetue um pedido de *webfrontend* para *mywebapi*
Vamos agora escrever código em `webfrontend` que efetua um pedido a `mywebapi`. Mude para a janela do Visual Studio que tem o projeto `webfrontend`. No `HomeController.cs` ficheiro, *substitua* o código para o método About com o seguinte código:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

O exemplo de código anterior reencaminha o cabeçalho `azds-route-as` do pedido a receber para o pedido a enviar. Verá mais tarde como isto facilita uma experiência de desenvolvimento mais produtiva em cenários de [equipa.](team-development-netcore-visualstudio.md)

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste momento, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não for esse o caso, prima F5 no projeto `mywebapi`.
1. Defina um ponto de interrupção no método `Get(int id)` no ficheiro `Controllers/ValuesController.cs` que processa os pedidos GET `api/values/{id}`.
1. No projeto `webfrontend` onde colou o código acima indicado, defina um ponto de interrupção antes de ser enviado um pedido GET para `mywebapi/api/values`.
1. Prima F5 no projeto `webfrontend`. O Visual Studio irá abrir novamente um browser para a porta localhost adequada e a aplicação Web será apresentada.
1. Clique na ligação "**About**" (Acerca de) na parte superior da página para acionar o ponto de interrupção no projeto `webfrontend`. 
1. Prima F10 para continuar. O ponto de interrupção no projeto `mywebapi` é acionado.
1. Prima F5 para continuar. Isso fará com que regresse ao código no projeto `webfrontend`.
1. Se premir F5 uma vez mais, o pedido será concluído e será devolvida uma página no browser. Na aplicação Web, a página About (Sobre) apresentará uma mensagem concatenada pelos dois serviços: "Hello from webfrontend and Hello from mywebapi".

### <a name="well-done"></a>Já está!
Tem agora uma aplicação com vários contentores, na qual cada contentor pode ser desenvolvido e implementado separadamente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça o desenvolvimento de equipas em Espaços Dev](team-development-netcore-visualstudio.md)
