---
title: 'Quickstart: Use Azure Cache para Redis com Node.js'
description: Neste arranque rápido, aprenderá a usar O Cache Azure para Redis com Node.js e node_redis.
author: yegu-ms
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2018
ms.author: yegu
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 88703581c507b79c1b10e0f8741c99e64d204a7e
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010873"
---
# <a name="quickstart-use-azure-cache-for-redis-with-nodejs"></a>Quickstart: Use Azure Cache para Redis com Node.js

Neste arranque rápido, incorpora o Azure Cache for Redis numa aplicação Node.js para ter acesso a uma cache segura e dedicada que é acessível a partir de qualquer aplicação dentro do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [node_redis,](https://github.com/mranney/node_redis)que pode instalar `npm install redis`com o comando . 

Para obter exemplos de utilização de outros clientes Node.js, consulte a documentação individual para os clientes Node.js listados em [Clientes Node.js Redis](https://redis.io/clients#nodejs).

## <a name="create-a-cache"></a>Criar uma cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


Adicione variáveis de ambiente ao **NOME DO ANFITRIÃO** e à chave de acesso **Primária**. Vai utilizar estas variáveis do seu código em vez de incluir informações confidenciais diretamente no código.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>Ligar à cache

As mais recentes construções de [node_redis](https://github.com/mranney/node_redis) fornecem suporte para a ligação ao Azure Cache for Redis usando TLS. O exemplo que se segue mostra como ligar-se ao Azure Cache for Redis utilizando o ponto final TLS de 6380. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

Não crie uma ligação nova para cada operação no código. Em vez disso, reutilize ligações tanto quanto possível. 

## <a name="create-a-new-nodejs-app"></a>Criar uma aplicação nova em Node.js

Crie um novo ficheiro de script com o nome *redistest.js*. Utilize o `npm install redis bluebird` comando para instalar as embalagens necessárias.

Adicione o seguinte exemplo de JavaScript ao ficheiro. Este código mostra-lhe como se ligar a um Azure Cache para redis, utilizando o nome do anfitrião da cache e variáveis ambientais chave. O código também armazena e obtém um valor de cadeia na cache. Os comandos `PING` e `CLIENT LIST` também são executados. Para obter mais exemplos de utilização de Redis com o cliente [node_redis](https://github.com/mranney/node_redis), veja [https://redis.js.org/](https://redis.js.org/).

```js
var redis = require("redis");
var bluebird = require("bluebird");

// Convert Redis client API to use promises, to make it usable with async/await syntax
bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the TLS port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

Execute o script com o Node.js.

```
node redistest.js
```

No exemplo abaixo, pode ver que a chave `Message` tinha anteriormente um valor em cache, o qual foi definido com a Consola Redis no portal do Azure. A aplicação atualizou esse valor em cache. A aplicação também executou os comandos `PING` e `CLIENT LIST`.

![App Redis Cache concluída](./media/cache-nodejs-get-started/redis-cache-app-complete.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se avançar para o próximo tutorial, pode manter os recursos que criou neste início rápido e reutilizá-los.

Caso contrário, se tiver concluído a aplicação de exemplo do início rápido, pode eliminar os recursos do Azure criados neste início rápido, para evitar encargos. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos contidos no mesmo serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.
>

Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

No **Filter por caixa** de texto, introduza o nome do seu grupo de recursos. As instruções neste artigo utilizaram um grupo de recursos denominado *TestResources*. No seu grupo de recursos **...** na lista de resultados, selecione... em **seguida, elimine**o grupo de recursos .

![Eliminar o grupo de recursos Azure](./media/cache-nodejs-get-started/redis-cache-delete-resource-group.png)

É-lhe pedido que confirme a eliminação do grupo de recursos. Insira o nome do seu grupo de recursos para confirmar e selecione **Eliminar**.

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar o Azure Cache para Redis a partir de uma aplicação Node.js. Continue para o próximo quickstart a usar O Cache Azure para Redis com uma aplicação web ASP.NET.

> [!div class="nextstepaction"]
> [Crie uma ASP.NET aplicação web que utilize um Azure Cache para Redis.](./cache-web-app-howto.md)
