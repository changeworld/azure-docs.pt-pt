---
title: Como desativar funções em Funções Azure
description: Aprenda a desativar e ativar funções em Funções Azure.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: fb8edf635856078655b8640ba0e1723fdd5e8a5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116140"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desativar funções em Funções Azure

Este artigo explica como desativar uma função nas Funções Azure. *Desativar* uma função significa fazer com que o tempo de funcionamento ignore o gatilho automático definido para a função. Isto permite evitar que uma função específica funcione sem parar toda a aplicação de funções.

A forma recomendada de desativar uma função `AzureWebJobs.<FUNCTION_NAME>.Disabled`é utilizando uma definição de aplicação no formato . Pode criar e modificar esta definição de aplicação de várias formas, incluindo utilizando o [ClI Azur e](/cli/azure/) a partir do separador **Manage** da sua função no [portal Azure](https://portal.azure.com). 

> [!NOTE]  
> Quando desativa uma função ativada pelo HTTP utilizando os métodos descritos neste artigo, o ponto final ainda pode ser acessível quando estiver em funcionamento no seu computador local.  

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

No Azure CLI, utiliza-se o [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) comando para criar e modificar a definição da aplicação. O comando seguinte desativa uma função nomeada `QueueTrigger` através da criação de uma definição de aplicação com o nome `AzureWebJobs.QueueTrigger.Disabled` definido para `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Para reativar a função, reexecutar o `false`mesmo comando com um valor de .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Use o Portal

Também pode utilizar o interruptor **'Estado de função'** no separador **'Gerir'** da função. O interruptor funciona criando e `AzureWebJobs.<FUNCTION_NAME>.Disabled` apagando a definição da aplicação.

![Interruptor de estado de função](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Outros métodos

Embora o método de definição de aplicações seja recomendado para todos os idiomas e versões de tempo de execução, existem várias outras formas de desativar as funções. Estes métodos, que variam em termos de linguagem e versão de tempo de execução, são mantidos para retrocompatibilidade. 

### <a name="c-class-libraries"></a>Bibliotecas de classe C#

Numa função de biblioteca de `Disable` classe, também pode usar o atributo para evitar que a função seja desencadeada. Pode utilizar o atributo sem um parâmetro de construção, como mostra o seguinte exemplo:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

O atributo sem parâmetro de construção requer que recompile e reimplante o projeto para alterar o estado dedesactivado da função. Uma forma mais flexível de usar o atributo é incluir um parâmetro de construção que se refere a uma definição de app Boolean, como mostra o seguinte exemplo:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Este método permite-lhe ativar e desativar a função alterando a definição da aplicação, sem recompilar ou redistribuir. A alteração de uma definição de aplicação faz com que a aplicação de funções reinicie, pelo que a mudança de estado desativada é imediatamente reconhecida.

> [!IMPORTANT]
> O `Disabled` atributo é a única maneira de desativar uma função de biblioteca de classes. O ficheiro *função.json* gerado para uma função de biblioteca de classes não deve ser editado diretamente. Se editar esse ficheiro, o `disabled` que quer que faça à propriedade não terá qualquer efeito.
>
> O mesmo se aplica ao interruptor **de estado função** no separador **Gerir,** uma vez que funciona alterando o ficheiro *função.json.*
>
> Além disso, note que o portal pode indicar que a função é desativada quando não está.

### <a name="functions-1x---scripting-languages"></a>Funções 1.x - scriptling idiomas

Na versão 1.x, também `disabled` pode utilizar a propriedade do ficheiro *function.json* para dizer ao tempo de execução para não desencadear uma função. Este método funciona apenas para scripts linguagens como c# script e JavaScript. A `disabled` propriedade pode `true` ser definida para ou para o nome de uma definição de app:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
ou 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

No segundo exemplo, a função é desativada quando existe uma `true` definição de aplicação que é chamada IS_DISABLED e está definida para ou 1.

Pode editar o ficheiro no portal Azure ou utilizar o interruptor **'Estado de função'** no separador **'Gerir'** da função. O interruptor do portal funciona alterando o ficheiro *função.json.*

![Interruptor de estado de função](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Passos seguintes

Este artigo é sobre desativar os gatilhos automáticos. Para obter mais informações sobre os gatilhos, consulte [Gatilhos e encadernações](functions-triggers-bindings.md).
