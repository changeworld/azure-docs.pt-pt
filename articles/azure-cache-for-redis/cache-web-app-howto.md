---
title: Crie uma ASP.NET aplicação web com Azure Cache para Redis
description: Neste arranque rápido, aprende-se a criar uma ASP.NET aplicação web com Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 155993bb3da781e698398ed8ddffa626e8f6cb2d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74927075"
---
# <a name="quickstart-use-azure-cache-for-redis-with-an-aspnet-web-app"></a>Quickstart: Use Azure Cache para Redis com uma aplicação web ASP.NET 

Neste arranque rápido, você usa o Visual Studio 2019 para criar uma aplicação web ASP.NET que se conecta ao Azure Cache para redis para armazenar e recuperar dados da cache. Em seguida, implementa a aplicação para o Azure App Service.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) com o **ASP.NET e desenvolvimento web** e cargas de trabalho de desenvolvimento **Azure.**

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. Open Visual Studio, e depois selecione **File** >**New** > **Project**.

2. Na caixa de diálogo **Novo Projeto**, siga os passos seguintes:

    ![Criar o projeto](./media/cache-web-app-howto/cache-create-project.png)

    a. Na lista **Modelos**, expanda o nó **Visual C#**.

    b. Selecione **Cloud**.

    c. Selecione **Aplicação Web ASP.NET**.

    d. Verifique se está selecionado o **.NET Framework 4.5.2** ou superior.

    e. Na caixa **Nome**, dê um nome ao projeto. Neste exemplo, utilizámos **ContosoTeamStats**.

    f. Selecione **OK**.
   
3. Selecione **MVC** como tipo de projeto.

4. Certifique-se de que **Sem Autenticação** está especificado para as definições **Autenticação**. Dependendo da versão do Visual Studio, a predefinição **Autenticação** pode ser outra. Para alterá-la,selecione **Alterar Autenticação** e, em seguida, **Sem Autenticação**.

5. Selecione **OK** para criar o projeto.

## <a name="create-a-cache"></a>Criar uma cache

Em seguida, crie a cache para a aplicação.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

#### <a name="to-edit-the-cachesecretsconfig-file"></a>Para editar o ficheiro *CacheSecrets.config*

1. Crie um ficheiro no seu computador chamado *CacheSecrets.config*. Coloque-o num local onde não será verificado com o código fonte da sua aplicação de amostra. Neste início rápido, o ficheiro *CacheSecrets.config* está localizado em *C:\AppSecrets\CacheSecrets.config*.

1. Edite o ficheiro *CacheSecrets.config*. Em seguida, adicione o seguinte conteúdo:

    ```xml
    <appSettings>
        <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<access-key>"/>
    </appSettings>
    ```

1. Substitua `<cache-name>` pelo nome do anfitrião da cache.

1. Substitua `<access-key>` pela chave primária da sua cache.

    > [!TIP]
    > Pode utilizar a chave de acesso secundária durante a rotação da chave como uma chave alternativa enquanto regenera a chave de acesso primária.
   >
1. Guarde o ficheiro.

## <a name="update-the-mvc-application"></a>Atualizar a aplicação MVC

Nesta secção, atualiza a aplicação para suportar uma nova vista que apresenta um teste simples contra o Azure Cache para Redis.

* [Atualizar o ficheiro web.config com uma definição de aplicação para a cache](#update-the-webconfig-file-with-an-app-setting-for-the-cache)
* Configurar a aplicação para utilizar o cliente StackExchange.Redis
* Atualizar o HomeController e o Esquema
* Adicionar uma nova vista da RedisCache

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Atualizar o ficheiro web.config com uma definição de aplicação para a cache

Quando executa a aplicação localmente, a informação em *CacheSecrets.config* é usada para ligar ao seu Azure Cache para a instância Redis. Posteriormente, vai implementar esta aplicação no Azure. Nessa altura, vai configurar uma definição de aplicação no Azure que a aplicação utiliza para obter as informações de ligação da cache em vez deste ficheiro. 

Uma vez que o ficheiro *CacheSecrets.config* não está implementado no Azure com a sua aplicação, só pode utilizá-lo durante o teste da aplicação localmente. Mantenha estas informações o mais seguras possíveis para impedir o acesso malicioso aos seus dados em cache.

#### <a name="to-update-the-webconfig-file"></a>Para atualizar o ficheiro *web.config*
1. Em **Explorador de Soluções**, faça duplo clique no ficheiro *web.config* para o abrir.

    ![Web.config](./media/cache-web-app-howto/cache-web-config.png)

2. No ficheiro *web.config*, encontre o elemento `<appSetting>`. Em seguida, adicione o atributo `file` seguinte. Se utilizou um nome de ficheiro ou localização diferentes, substitua estes valores pelos valores que são mostrados no exemplo.

* Antes: `<appSettings>`
* Depois:`<appSettings file="C:\AppSecrets\CacheSecrets.config">`

O tempo de execução do ASP.NET une o conteúdo do ficheiro externo e a marcação no elemento `<appSettings>`. O tempo de execução ignora o atributo de ficheiro se não for possível localizar o ficheiro especificado. Os segredos (a cadeia de ligação para a cache) não são incluídos como parte do código fonte da aplicação. Quando implementa a sua aplicação web para o Azure, o ficheiro *CacheSecrets.config* não está implantado.

### <a name="to-configure-the-application-to-use-stackexchangeredis"></a>Para configurar a aplicação para utilizar o StackExchange.Redis

1. Para configurar a aplicação para utilizar o pacote NuGet [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) para o Visual Studio, selecione **Ferramentas > Gestor de Pacotes NuGet > Consola do Gestor de Pacotes**.

2. Execute o seguinte comando a partir da janela `Package Manager Console`:

    ```powershell
    Install-Package StackExchange.Redis
    ```

3. O pacote NuGet descarrega e adiciona as referências de montagem necessárias para a sua aplicação de cliente aceder ao Azure Cache para Redis com o StackExchange.Azure Cache para o cliente Redis. Se preferir utilizar uma versão com um nome forte da biblioteca de cliente `StackExchange.Redis`, instale o pacote `StackExchange.Redis.StrongName`.

### <a name="to-update-the-homecontroller-and-layout"></a>Para atualizar o HomeController e o Esquema

1. No **Explorador de Soluções**, expanda a pasta **Controladores** e, em seguida, abra o ficheiro *HomeController.cs*.

2. Adicione as duas instruções `using` abaixo na parte superior do ficheiro para suportar as definições da aplicação e do cliente da cache.

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

3. Adicione o seguinte método à classe `HomeController` para suportar uma nova ação `RedisCache` que executa alguns comandos numa nova cache.

    ```csharp
        public ActionResult RedisCache()
        {
            ViewBag.Message = "A simple example with Azure Cache for Redis on ASP.NET.";

            var lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
            {
                string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
                return ConnectionMultiplexer.Connect(cacheConnection);
            });

            // Connection refers to a property that returns a ConnectionMultiplexer
            // as shown in the previous example.
            IDatabase cache = lazyConnection.Value.GetDatabase();

            // Perform cache operations using the cache object...

            // Simple PING command
            ViewBag.command1 = "PING";
            ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

            // Simple get and put of integral data types into the cache
            ViewBag.command2 = "GET Message";
            ViewBag.command2Result = cache.StringGet("Message").ToString();

            ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
            ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

            // Demonstrate "SET Message" executed as expected...
            ViewBag.command4 = "GET Message";
            ViewBag.command4Result = cache.StringGet("Message").ToString();

            // Get the client list, useful to see if connection list is growing...
            ViewBag.command5 = "CLIENT LIST";
            ViewBag.command5Result = cache.Execute("CLIENT", "LIST").ToString().Replace(" id=", "\rid=");

            lazyConnection.Value.Dispose();

            return View();
        }
    ```

4. No **Explorador de Soluções**, expanda a pasta **Vistas** > **Partilhado**. Em seguida, abra o ficheiro *_Layout.cshtml*.

    Substituir:
    
    ```csharp
    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    Por:

    ```csharp
    @Html.ActionLink("Azure Cache for Redis Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

### <a name="to-add-a-new-rediscache-view"></a>Para adicionar uma nova vista da RedisCache

1. No **Explorador de Soluções**, expanda a pasta **Vistas** e, em seguida, clique com o botão direito do rato na pasta **Raiz**. Escolha **adicionar** > **vista...**.

2. Na caixa de diálogo **Adicionar Vista**, introduza **RedisCache** como o Nome da Vista. Em seguida, selecione **Adicionar**.

3. Substitua o código no ficheiro *RedisCache.cshtml* pelo código seguinte:

    ```csharp
    @{
        ViewBag.Title = "Azure Cache for Redis Test";
    }

    <h2>@ViewBag.Title.</h2>
    <h3>@ViewBag.Message</h3>
    <br /><br />
    <table border="1" cellpadding="10">
        <tr>
            <th>Command</th>
            <th>Result</th>
        </tr>
        <tr>
            <td>@ViewBag.command1</td>
            <td><pre>@ViewBag.command1Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command2</td>
            <td><pre>@ViewBag.command2Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command3</td>
            <td><pre>@ViewBag.command3Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command4</td>
            <td><pre>@ViewBag.command4Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command5</td>
            <td><pre>@ViewBag.command5Result</pre></td>
        </tr>
    </table>
    ```

## <a name="run-the-app-locally"></a>Executar a aplicação localmente

Por padrão, o projeto está configurado para hospedar a app localmente no [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) para testes e depuração.

### <a name="to-run-the-app-locally"></a>Para executar a aplicação localmente
1. No Estúdio Visual, selecione **Debug** > **Start Debugging** para construir e iniciar a aplicação localmente para testes e depuração.

2. No navegador, **selecione Azure Cache para Redis Test** na barra de navegação.

3. No exemplo seguinte, `Message` a chave anteriormente tinha um valor em cache, que foi definido através da utilização da consola Azure Cache para Redis no portal. A aplicação atualizou esse valor em cache. A aplicação também executou os comandos `PING` e `CLIENT LIST`.

    ![Teste simples concluído no local](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Publicar e executar no Azure

Depois de ter testado com êxito a aplicação localmente, pode implementar a aplicação no Azure e executá-la na cloud.

### <a name="to-publish-the-app-to-azure"></a>Para publicar a aplicação no Azure

1. No Visual Studio, clique com o botão direito do rato no nó do projeto no Explorador de Soluções. Em seguida, selecione **Publicar**.

    ![Publicar](./media/cache-web-app-howto/cache-publish-app.png)

2. Selecione **Serviço de Aplicações do Microsoft Azure**, selecione **Criar Novo** e, em seguida, selecione **Publicar**.

    ![Publicar no Serviço de Aplicações](./media/cache-web-app-howto/cache-publish-to-app-service.png)

3. Na caixa de diálogo **Criar Serviço de Aplicações**, faça as seguintes alterações:

    | Definição | Valor recomendado | Descrição |
    | ------- | :---------------: | ----------- |
    | **Nome da aplicação** | Utilize a predefinição. | O nome da aplicação é o nome do anfitrião da aplicação quando é implementada no Azure. O nome pode ter um sufixo de carimbo de data/hora adicionado ao mesmo para torná-lo exclusivo, caso seja necessário. |
    | **Assinatura** | Escolha a sua subscrição do Azure. | Esta subscrição é debitada relativamente a quaisquer custos de alojamento relacionados. Se tiver várias subscrições do Azure, verifique se a subscrição pretendida está selecionada.|
    | **Grupo de recursos** | Utilize o mesmo grupo de recursos onde criou a cache (por exemplo, *TestResourceGroup*). | O grupo de recursos ajuda-o a gerir todos os recursos como um grupo. Mais tarde, quando pretender eliminar a aplicação, basta apenas eliminar o grupo. |
    | **Plano de serviço de aplicações** | Selecione **Novo** e, em seguida, crie um novo plano do Serviço de Aplicações com o nome *TestingPlan*. <br />Utilize a mesma **Localização** que serviu para criar a cache. <br />Escolha **Gratuito** para o tamanho. | Um plano de Serviço de Aplicações define um conjunto de recursos de computação para a execução da aplicação Web. |

    ![Caixa de diálogo Serviço de Aplicações](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

4. Depois de configurar as definições de alojamento do Serviço de Aplicações, selecione **Criar**.

5. Monitorize a janela **Saída** no Visual Studio para ver o estado de publicação. Depois de a aplicação ter sido publicada, o URL da aplicação é registado:

    ![Saída da publicação](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>Adicionar a definição da aplicação à cache

Depois de a nova aplicação ter sido publicada, adicione uma nova definição de aplicação. Esta definição é utilizada para armazenar as informações de ligação da cache. 

#### <a name="to-add-the-app-setting"></a>Para adicionar a definição de aplicação 

1. Escreva o nome da aplicação na barra de pesquisa, na parte superior do portal do Azure, para localizar a nova aplicação que acabou de criar.

    ![Encontrar a aplicação](./media/cache-web-app-howto/cache-find-app-service.png)

2. Adicione uma nova definição de aplicação denominada **CacheConnection** para a aplicação utilizar para se ligar à cache. Utilize o mesmo valor que configurou para `CacheConnection` no ficheiro *CacheSecrets.config*. O valor contém o nome do anfitrião da cache e a chave de acesso.

    ![Adicionar definição de aplicação](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>Executar a aplicação no Azure

No browser, aceda ao URL da aplicação. O URL é apresentado nos resultados da operação de publicação na janela de saída do Visual Studio. Também é fornecido no portal do Azure na página de descrição geral da aplicação que criou.

Selecione **Azure Cache para Redis Test** na barra de navegação para testar o acesso à cache.

![Teste simples concluído do Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se avançar para o próximo tutorial, pode manter os recursos que criou neste início rápido e reutilizá-los.

Caso contrário, se tiver concluído o exemplo de aplicação do início rápido, pode eliminar os recursos do Azure que criou neste início rápido, para evitar encargos. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. Quando elimina um grupo de recursos, todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.

### <a name="to-delete-a-resource-group"></a>Para eliminar um grupo de recursos

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

2. Na caixa **Filtrar por nome...**, escreva o nome do grupo de recursos. As instruções neste artigo utilizaram um grupo de recursos denominado *TestResources*. No grupo de recursos na lista de resultados, selecione **...** e, em seguida, selecione **Eliminar grupo de recursos**.

    ![Eliminar](./media/cache-web-app-howto/cache-delete-resource-group.png)

É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos para confirmar e, em seguida, selecione **Eliminar**.

Após alguns instantes, o grupo de recursos e todos os respetivos recursos são eliminados.

## <a name="next-steps"></a>Passos seguintes

No próximo tutorial, você usa Azure Cache para Redis num cenário mais realista para melhorar o desempenho de uma app. Vai atualizar esta aplicação com os resultados de classificação da cache com o padrão cache-aside com ASP.NET e uma base de dados.

> [!div class="nextstepaction"]
> [Codificar uma classificação cache-aside no ASP.NET](cache-web-app-cache-aside-leaderboard.md)
