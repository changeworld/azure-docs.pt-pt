---
title: Gerir o estado do Tecido de Serviço Azure
description: Aprenda sobre o acesso, a poupança e a remoção do estado para um Ator Fiável de Tecido de Serviço Azure, e considerações ao conceber uma aplicação.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 788c337a37ec66c5aa1521c5cd9f2816ed7a8bf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645638"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Acesso, salvar e remover o estado de Atores Fiáveis
[Os Atores Fiáveis](service-fabric-reliable-actors-introduction.md) são objetos de um fio único que podem encapsular a lógica e o estado e manter o estado de forma fiável. Cada instância de ator tem o seu próprio [gestor de estado](service-fabric-reliable-actors-state-management.md): uma estrutura de dados semelhante ao dicionário que armazena seguramente pares chave/valor. O gerente do Estado é um invólucro em torno de um provedor do estado. Pode usá-lo para armazenar dados independentemente da definição de [persistência.](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication)

As chaves do gerente do estado devem ser cordas. Os valores são genéricos e podem ser de qualquer tipo, incluindo tipos personalizados. Os valores armazenados no gestor do Estado devem ser um contrato de dados, pois podem ser transmitidos através da rede a outros nós durante a replicação e podem ser escritos em disco, dependendo da persistência do estado de um ator.

O gestor do Estado expõe métodos comuns de dicionário para gerir o estado, semelhantes aos encontrados no Dicionário Fiável.

Para obter informações, consulte [as melhores práticas na gestão](service-fabric-reliable-actors-state-management.md#best-practices)do estado do ator.

## <a name="access-state"></a>Estado de acesso
O Estado é acedido através do gerente do Estado por chave. Os métodos de gerente de estado são todos assíncronos porque podem exigir i/O disco quando os atores persistiram. Após o primeiro acesso, os objetos do Estado são cached na memória. Repita as operações de acesso acedam diretamente a objetos da memória e regressem sincronizadamente sem incorrer em I/O do disco ou na sobrecarga de comutação de contexto assíncrona. Um objeto de Estado é removido da cache nos seguintes casos:

* Um método de ator lança uma exceção não tratada depois de recuperar um objeto do gerente do estado.
* Um ator é reativado, quer após ser desativado, quer após a falha.
* As páginas do provedor do Estado dizem ao disco. Este comportamento depende da implementação do provedor do Estado. O fornecedor de `Persisted` estado padrão para a definição tem este comportamento.

Pode recuperar o estado *Get* utilizando uma operação `KeyNotFoundException`padrão Get `NoSuchElementException`que atira (C#) ou (Java) se não existir uma entrada para a chave:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Também pode recuperar o estado utilizando um método *TryGet* que não atira se uma entrada não existir para uma chave:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Salvar o estado
Os métodos de recuperação do gestor do Estado devolvem uma referência a um objeto na memória local. Modificar este objeto apenas na memória local não faz com que seja guardado de forma duradoura. Quando um objeto é recuperado do gestor do Estado e modificado, este deve ser reinserido no gestor do Estado para ser guardado de forma duradoura.

Pode inserir o estado utilizando um *Conjunto*incondicional, que é o equivalente à `dictionary["key"] = value` sintaxe:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Pode adicionar estado utilizando um método *Add.* Este método `InvalidOperationException`lança (C#) ou `IllegalStateException`(Java) quando tenta adicionar uma chave que já existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Também pode adicionar estado utilizando um método *TryAdd.* Este método não atira quando tenta adicionar uma chave que já existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

No final de um método de ator, o gestor do Estado salva automaticamente quaisquer valores que tenham sido adicionados ou modificados por uma operação de inserção ou atualização. Um "save" pode incluir persistir no disco e na replicação, dependendo das definições utilizadas. Os valores que não foram modificados não são persistentes nem replicados. Se não foram modificados valores, a operação de salvamento não faz nada. Se a poupança falhar, o estado modificado é descartado e o estado original é recarregado.

Também pode salvar o estado `SaveStateAsync` manualmente, ligando para o método na base do ator:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Remover estado
Pode remover o estado permanentemente do gerente do estado de um ator, ligando para o método *Remover.* Este método `KeyNotFoundException`lança (C#) ou `NoSuchElementException`(Java) quando tenta remover uma chave que não existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Também pode remover o estado permanentemente utilizando o método *TryRemove.* Este método não atira quando tenta remover uma chave que não existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Passos seguintes

O estado que está armazenado em Atores Fiáveis deve ser serializado antes de ser escrito em disco e replicado para alta disponibilidade. Saiba mais sobre a serialização do [tipo Ator.](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

Em seguida, saiba mais sobre diagnósticos de [ator e monitorização de desempenho.](service-fabric-reliable-actors-diagnostics.md)
