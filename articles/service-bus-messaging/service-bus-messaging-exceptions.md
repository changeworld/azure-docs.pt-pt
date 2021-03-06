---
title: Azure Service Bus - exceções de mensagens Microsoft Docs
description: Este artigo fornece uma lista de exceções de mensagens Azure Service Bus e sugeriu ações a tomar quando a exceção ocorre.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: d04902a8d53397b7e7d9712a1c75ce44cc7aa7ad
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880793"
---
# <a name="service-bus-messaging-exceptions"></a>Exceções de mensagens de ônibus de serviço
Este artigo enumera as exceções .NET geradas por .NET Framework APIs. 

## <a name="exception-categories"></a>Categorias de exceções
As APIs de mensagens geram exceções que podem cair nas seguintes categorias, juntamente com a ação associada que pode tomar para tentar corrigi-las. O significado e as causas de uma exceção podem variar dependendo do tipo de entidade de mensagens:

1. Erro de codificação do utilizador[(System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Ação geral: tente fixar o código antes de prosseguir.
2. Erro de configuração/configuração[(Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: reveja a sua configuração e altere se necessário.
3. Exceções transitórias ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Ação geral: voltar a tentar a operação ou notificar os utilizadores. A `RetryPolicy` classe no SDK cliente pode ser configurada para lidar com repetições automaticamente. Para mais informações, consulte [a orientação de Retry](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Outras exceções ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Ação geral: específica do tipo de exceção; consulte a tabela na seguinte secção: 

## <a name="exception-types"></a>Tipos de exceção
A tabela seguinte lista tipos de exceções de mensagens e suas causas, e notas sugeridas ação que você pode tomar.

| **Tipo de exceção** | **Descrição/Causa/Exemplos** | **Ação Sugerida** | **Nota sobre retry automático/imediato** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado pela [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor pode ter concluído a operação solicitada. Pode acontecer devido a atrasos na rede ou outros infraestruturas. |Verifique se o estado do sistema tem consistência e retenta, se necessário. Consulte [as exceções do Timeout](#timeoutexception). |A retentativa pode ajudar em alguns casos; adicionar lógica de retry ao código. |
| [Invalidação de Operação](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de utilizador solicitada não é permitida dentro do servidor ou serviço. Consulte a mensagem de exceção para mais detalhes. Por exemplo, [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) gera esta exceção se a mensagem foi recebida no modo [ReceiveAndDelete.](/dotnet/api/microsoft.azure.servicebus.receivemode) |Verifique o código e a documentação. Certifique-se de que a operação solicitada é válida. |Retry não ajuda. |
| [Operação Cancelamentode Exceção](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É feita uma tentativa de invocar uma operação a um objeto que já foi fechado, abortado ou eliminado. Em casos raros, a transação ambiente já está alienada. |Verifique o código e certifique-se de que não invoca operações num objeto eliminado. |Retry não ajuda. |
| [Exceção não autorizada de Acesso](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não conseguiu adquirir um símbolo, o símbolo é inválido, ou o símbolo não contém as alegações necessárias para fazer a operação. |Certifique-se de que o fornecedor simbólico é criado com os valores corretos. Verifique a configuração do Serviço de Controlo de Acesso. |A retentativa pode ajudar em alguns casos; adicionar lógica de retry ao código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutoutRangeexception](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Um ou mais argumentos fornecidos ao método são inválidos.<br /> O URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém segmentos de caminhos.<br /> O regime URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. <br />O valor da propriedade é superior a 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |Retry não ajuda. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |A entidade associada à operação não existe ou foi eliminada. |Certifique-se de que a entidade existe. |Retry não ajuda. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tente receber uma mensagem com um número de sequência particular. Esta mensagem não foi encontrada. |Certifique-se de que a mensagem ainda não foi recebida. Verifique a fila da carta morta para ver se a mensagem foi escrita. |Retry não ajuda. |
| [Falta de comunicação de mensagens](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |O cliente não é capaz de estabelecer uma ligação com o Service Bus. |Certifique-se de que o nome do anfitrião fornecido está correto e que o hospedeiro está acessível. |A retry pode ajudar se houver problemas de conectividade intermitentes. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |O serviço não é capaz de processar o pedido neste momento. |O cliente pode esperar por um período de tempo, e depois voltar a tentar a operação. |O cliente pode voltar a tentar após determinado intervalo. Se uma nova tentativa resultar numa exceção diferente, verifique o comportamento de novo de tentativa dessa exceção. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |O sinal de bloqueio associado à mensagem expirou, ou o sinal de bloqueio não foi encontrado. |Elimine a mensagem. |Retry não ajuda. |
| [SessãoLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |O bloqueio associado a esta sessão está perdido. |Aborte o objeto [MessageSession.](/dotnet/api/microsoft.servicebus.messaging.messagesession) |Retry não ajuda. |
| [Reexceção de mensagens](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Exceção genérica de mensagens que podem ser lançadas nos seguintes casos:<p>É feita uma tentativa de criar um [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) usando um nome ou caminho que pertença a um tipo de entidade diferente (por exemplo, um tópico).</p><p>É feita uma tentativa de enviar uma mensagem maior que 256 KB. </p>O servidor ou serviço encontrou um erro durante o processamento do pedido. Consulte a mensagem de exceção para mais detalhes. Normalmente é uma exceção transitória.</p><p>O pedido foi encerrado porque a entidade está a ser estrangulada. Código de erro: 50001, 50002, 50008. </p> | Verifique o código e certifique-se de que apenas são utilizados objetos serlizáveis para o corpo da mensagem (ou utilize um serializador personalizado). <p>Verifique a documentação dos tipos de valor suportados das propriedades e utilize apenas tipos suportados.</p><p> Verifique a propriedade [IsTransient.](/dotnet/api/microsoft.servicebus.messaging.messagingexception) Se for **verdade,** pode voltar a tentar a operação. </p>| Se a exceção for devido à aceleração, aguarde alguns segundos e volte a tentar a operação. O comportamento de retry é indefinido e pode não ajudar em outros cenários.|
| [Entidade de Mensagens Já Existe Exceção](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Tente criar uma entidade com um nome que já é usado por outra entidade nesse espaço de nome de serviço. |Eliminar a entidade existente ou escolher um nome diferente para a entidade a criar. |Retry não ajuda. |
| [QuotaExceedException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |A entidade de mensagens atingiu o seu tamanho máximo admissível, ou o número máximo de ligações a um espaço de nome foi ultrapassado. |Criar espaço na entidade recebendo mensagens da entidade ou das suas subfilas. Ver [QuotaExceededException](#quotaexceededexception). |A retry pode ajudar se as mensagens tiverem sido removidas entretanto. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |O Service Bus devolve esta exceção se tentar criar uma ação de regra inválida. O Service Bus atribui esta exceção a uma mensagem riscada se ocorrer um erro durante o processamento da ação de regra para essa mensagem. |Verifique a ação da regra para corrigir. |Retry não ajuda. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |O Ônibus de serviço devolve esta exceção se tentar criar um filtro inválido. O Service Bus atribui esta exceção a uma mensagem retaldida se ocorrer um erro durante o processamento do filtro para essa mensagem. |Verifique se o filtro está correto. |Retry não ajuda. |
| [SessãoNão Pode Ser Bloqueada Exceção](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Tente aceitar uma sessão com um ID de sessão específico, mas a sessão está bloqueada por outro cliente. |Certifique-se de que a sessão está desbloqueada por outros clientes. |A retentativa pode ajudar se a sessão tiver sido lançada de forma interina. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Demasiadas operações fazem parte da transação. |Reduzir o número de operações que fazem parte desta operação. |Retry não ajuda. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Pedido de operação em tempo de funcionamento numa entidade com deficiência. |Ativar a entidade. |A retentativa pode ajudar se a entidade tiver sido ativada interinamente. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |O Service Bus devolve esta exceção se enviar uma mensagem para um tópico que tenha pré-filtração ativado e nenhum dos filtros corresponda. |Certifique-se de que pelo menos um filtro corresponde. |Retry não ajuda. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Uma carga útil de mensagem excede o limite de 256 KB. O limite de 256 KB é o tamanho total da mensagem, que pode incluir propriedades do sistema e qualquer sobrecarga .NET. |Reduza o tamanho da carga útil da mensagem e, em seguida, tente novamente a operação. |Retry não ajuda. |
| [TransacçõesExcepção](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |A transação ambiente *(Transação.Corrente)* é inválida. Pode ter sido concluída ou abortada. A exceção interna pode fornecer informações adicionais. | |Retry não ajuda. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Tenta-se uma operação numa transação que está em dúvida, ou é feita uma tentativa de cometer a transação e a transação fica em dúvida. |A sua aplicação deve lidar com esta exceção (como um caso especial), uma vez que a transação pode já ter sido cometida. |- |

## <a name="quotaexceededexception"></a>QuotaExceedException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indica que foi excedida uma quota de uma entidade específica.

### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, é muitas vezes do tamanho da fila. A propriedade da mensagem de erro contém mais detalhes, como no seguinte exemplo:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

A mensagem afirma que o tópico excedeu o seu limite de tamanho, neste caso 1 GB (o limite de tamanho padrão). 

### <a name="namespaces"></a>Espaços de nomes

Para espaços de nome, [quotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) pode indicar que uma aplicação excedeu o número máximo de ligações a um espaço de nome. Por exemplo:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Causas comuns
Existem duas causas comuns para este erro: a fila das letras mortas e os recetores de mensagens não funcionais.

1. **[Fila de letras mortas](service-bus-dead-letter-queues.md)** Um leitor não está a completar mensagens e as mensagens são devolvidas à fila/tópico quando o bloqueio expira. Pode acontecer se o leitor encontrar uma exceção que o impeça de ligar para [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Depois de uma mensagem ter sido lida 10 vezes, passa para a fila da letra morta por defeito. Este comportamento é controlado pela propriedade [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e tem um valor padrão de 10. À medida que as mensagens se acumulam na fila das cartas mortas, tomam espaço.
   
    Para resolver o problema, leia e complete as mensagens da fila da carta morta, como faria de qualquer outra fila. Pode utilizar o método [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) para ajudar a formatar o caminho da fila de letras mortas.
2. **Recetor parado**. Um recetor deixou de receber mensagens de uma fila ou subscrição. A forma de identificar isto é olhar para a propriedade [QueueDescription.MessageCountDetails,](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) que mostra a desagregação completa das mensagens. Se a propriedade [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) é alta ou em crescimento, então as mensagens não estão a ser lidas tão rapidamente como estão a ser escritas.

## <a name="timeoutexception"></a>TimeoutException
Um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo utilizador está a demorar mais tempo do que o tempo de funcionamento. 

Deve verificar o valor da propriedade [ServicePointManager.DefaultConnectionLimit,](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) uma vez que atingir este limite também pode causar uma [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, o tempo limite é especificado quer na propriedade [MessagingFactorySettings.OperationTimeout,](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) como parte da cadeia de ligação, quer através do [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). A mensagem de erro em si pode variar, mas contém sempre o valor limite especificado para o funcionamento atual. 

## <a name="next-steps"></a>Passos seguintes
Para obter a referência completa do Bus service .NET API, consulte a [referência Azure .NET API](/dotnet/api/overview/azure/service-bus).
Para obter dicas de resolução de problemas, consulte o guia de [resolução de problemas](service-bus-troubleshooting-guide.md)
