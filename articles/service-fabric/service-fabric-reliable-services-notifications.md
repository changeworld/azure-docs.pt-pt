---
title: Notificações de Serviços Fiáveis
description: Documentação conceptual para notificações de Serviços Fiáveis de Serviços para Gestor de Estado Fiável e Dicionário Fiável
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: 1f3239ea1da252ccd84c6572b562756c8fd1677d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639569"
---
# <a name="reliable-services-notifications"></a>Notificações de Serviços Fiáveis
As notificações permitem que os clientes rastreiem as alterações que estão a ser feitas a um objeto em que estão interessados. Dois tipos de notificações de suporte de objetos: *Gestor de Estado fiável* e Dicionário *Fiável*.

As razões comuns para a utilização das notificações são:

* Construção materializou pontos de vista, tais como índices secundários ou vistas filtradas agregadas do estado da réplica. Um exemplo é um índice classificado de todas as chaves no Dicionário Fiável.
* Envio de dados de monitorização, como o número de utilizadores adicionados na última hora.

As notificações são disparadas como parte da aplicação de operações. Por isso, as notificações devem ser tratadas o mais rápido possível, e eventos sincronizados não devem incluir quaisquer operações caras.

## <a name="reliable-state-manager-notifications"></a>Notificações fiáveis do Gestor do Estado
O Gestor estatal fiável fornece notificações para os seguintes eventos:

* Transação
  * Consolidação
* Gestor do Estado
  * Reconstruir
  * Adição de um estado fiável
  * Remoção de um estado fiável

O Gestor de Estado fiável rastreia as transações de bordo atuais. A única alteração no estado de transação que faz com que uma notificação seja destemitida é uma transação que está a ser cometida.

O Gestor de Estado confiável mantém uma coleção de estados fiáveis como o Dicionário Fiável e a Fila Fiável. O Gestor do Estado fiável dispara notificações quando esta recolha muda: um estado fiável é adicionado ou removido, ou toda a coleção é reconstruída.
A coleção "Reliable State Manager" é reconstruída em três casos:

* Recuperação: Quando uma réplica começa, recupera o seu estado anterior a partir do disco. No final da recuperação, utiliza o **NotifyStateManagerChangedEventArgs** para disparar um evento que contém o conjunto de estados de confiança recuperados.
* Cópia completa: Antes que uma réplica possa juntar-se ao conjunto de configuração, tem de ser construída. Às vezes, isto requer uma cópia completa do estado do Gestor de Estado fiável da réplica primária a aplicar à réplica secundária ociosa. O Gestor de Estado fiável na réplica secundária usa **o NotifyStateManagerChangedEventArgs** para disparar um evento que contém o conjunto de estados fiáveis que adquiriu a partir da réplica primária.
* Restaurar: Em cenários de recuperação de desastres, o estado da réplica pode ser restaurado a partir de uma cópia de segurança via **RestoreAsync**. Nesses casos, o Gestor de Estado Fiável na réplica primária utiliza o **NotifyStateManagerChangedEventArgs** para disparar um evento que contém o conjunto de estados fiáveis que restaurou a partir da cópia de segurança.

Para se registar para notificações de transações e/ou notificações de gerente do Estado, tem de se registar com os eventos **Transações Changed** ou **StateManagerChanged** no Reliable State Manager. Um lugar comum para se registar com estes manipuladores de eventos é o construtor do seu serviço estatal. Quando se registar no construtor, não perderá nenhuma notificação causada por uma mudança durante a vida do **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

O manipulador de **eventos TransactionChanged** utiliza **NotificaçõesChangeEventArgs** para fornecer detalhes sobre o evento. Contém a propriedade de ação (por exemplo, **NotifyTransactionChangedAction.Commit)** que especifica o tipo de alteração. Contém também o imóvel de transações que fornece uma referência à transação que mudou.

> [!NOTE]
> Hoje em dia, os eventos **Transatores Alterados** só são levantados se a transação for cometida. A ação é então igual a **NotifyTransactionChangedAction.Commit**. Mas no futuro, os eventos poderão ser levantados para outros tipos de mudanças de estado de transação. Recomendamos verificar a ação e processar o evento apenas se for um que você espera.
> 
> 

Segue-se um exemplo **de TransacçõesAlter** o manipulador de eventos.

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

O manipulador **de eventos StateManagerChanged** utiliza **o NotifyStateManagerChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyStateManagerChangedEventArgs** tem duas subclasses: **NotificaçãoStateManagerRebuildEventArgs** e **NotificastateManagerSingleEntityChangedEventArgs**.
Utiliza a propriedade de ação em **NotifyStateManagerChangedEventArgs** para lançar **o NotifyStateManagerChangedEventArgs** para a subclasse correta:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** and **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Segue-se um exemplo do gestor de notificações **StateManagerChanged.**

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Notificações fiáveis do Dicionário
O Dicionário Fiável fornece notificações para os seguintes eventos:

* Reconstruir: Chamado quando **o ReliableDictionary** recuperou o seu estado de um estado local recuperado ou copiado ou cópia.
* Claro: Chamado quando o estado do **ReliableDictionary** foi limpo através do método **ClearAsync.**
* Adicione: Chamado quando um item foi adicionado ao **ReliableDictionary**.
* Atualização: Chamado quando um item no **IReliableDictionary** foi atualizado.
* Remover: Chamado quando um item no **IReliableDictionary** tiver sido eliminado.

Para obter notificações fiáveis do Dicionário, precisa de se registar com o manipulador de **eventos DictionaryChanged** no **IReliableDictionary**. Um lugar comum para se registar com estes manipuladores de eventos está no **ReliableStateManager.StateManagerChanged** adicionar notificação.
Registar-se quando **o IReliableDictionary** é adicionado ao **IReliableStateManager** garante que não perderá nenhuma notificação.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** é o método da amostra que o exemplo **onStateManagerChangedHandler** anterior chama.
> 
> 

O código anterior define a interface **IReliableNotificationAsyncCallback,** juntamente com o **DictionaryChanged**. Como o **NotifyDictionaryRebuildEventArgs** contém uma interface **IAsyncEnumerable--** que precisa de ser enumerada assíncronamente -- as notificações de reconstrução são disparadas através da **RebuildNotificationAsyncCallback** em vez do **OnDictionaryChangedHandler**.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> No código anterior, no âmbito do processamento da notificação de reconstrução, primeiro o estado agregado mantido é ilibado. Como a recolha fiável está a ser reconstruída com um estado novo, todas as notificações anteriores são irrelevantes.
> 
> 

O manipulador de **eventos DictionaryChanged** utiliza **NotificadictionaryChangedEventArgs** para fornecer detalhes sobre o evento.
**NotificadicionárioChangedEventArgs** tem cinco subclasses. Utilize a propriedade de ação em **NotifyDictionaryChangedEventArgs** para lançar **NotificadicionárioChangedEventArgs** para a subclasse correta:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add**: **NotificaçãoDicionárioItemAddedEventArgs**
* **NotificadicionárioChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Recomendações
* *Faça* eventos de notificação completos o mais rápido possível.
* *Não* execute quaisquer operações dispendiosas (por exemplo, operações de I/O) como parte de eventos sincronizados.
* *Verifique* o tipo de ação antes de processar o evento. Novos tipos de ação poderão ser adicionados no futuro.

Eis algumas coisas a ter em mente:

* As notificações são disparadas como parte da execução de uma operação. Por exemplo, uma notificação de restauro é disparada como o último passo de uma operação de restauro. Um restauro não terminará até que o evento de notificação seja processado.
* Uma vez que as notificações são disparadas como parte das operações de aplicação, os clientes vêem apenas notificações para operações comprometidas localmente. E porque as operações só são garantidas localmente (por outras palavras, registadas), podem ou não ser desfeitas no futuro.
* No caminho do redo, é disparada uma única notificação para cada operação aplicada. Isto significa que se a transação T1 incluir Create(X), Delete(X) e Create(X), receberá uma notificação para a criação de X, uma para a eliminação, e outra para a criação novamente, por essa ordem.
* Para operações que contenham múltiplas operações, as operações são aplicadas na ordem em que foram recebidas na réplica primária do utilizador.
* Como parte do processamento de falsos progressos, algumas operações podem ser desfeitas. São levantadas notificações para tais operações de desfazer, rolando o estado da réplica de volta a um ponto estável. Uma diferença importante de notificações de desfazer é que os eventos que têm chaves duplicadas são agregados. Por exemplo, se a transação T1 estiver a ser desfeita, verá uma única notificação para Delete(X).

## <a name="next-steps"></a>Passos seguintes
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Início rápido dos Serviços Fiáveis](service-fabric-reliable-services-quick-start.md)
* [Backup e restauro de Serviços Fiáveis (recuperação de desastres)](service-fabric-reliable-services-backup-restore.md)
* [Referência do desenvolvedor para Coleções Fiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

