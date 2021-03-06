---
title: Gestão estatal de Atores Fiáveis
description: Descreve como o estado de Atores Fiáveis é gerido, persistido e replicado para alta disponibilidade.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 9962d4333e458243670d1005ad2ccfbc0bb7c92a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348907"
---
# <a name="reliable-actors-state-management"></a>Gestão estatal de Atores Fiáveis
Os Atores Fiáveis são objetos de um fio único que podem encapsular a lógica e o estado. Como os atores funcionam em Serviços Fiáveis, podem manter o estado de forma fiável usando os mesmos mecanismos de persistência e replicação. Desta forma, os atores não perdem o seu estado após falhas, após a reativação após a recolha de lixo, ou quando são movidos entre nós num aglomerado devido ao equilíbrio de recursos ou melhorias.

## <a name="state-persistence-and-replication"></a>Persistência e replicação do Estado
Todos os Atores Fiáveis são considerados *estatais* porque cada ator mapeia para uma identificação única. Isto significa que as chamadas repetidas para a mesma identificação do ator são direcionadas para a mesma instância de ator. Num sistema apátrida, pelo contrário, as chamadas de clientes não são garantidas para serem encaminhadas para o mesmo servidor todas as vezes. Por esta razão, os serviços de atores são sempre serviços audaciosos.

Apesar de os atores serem considerados estatais, isso não significa que tenham de armazenar o Estado de forma fiável. Os atores podem escolher o nível de persistência e replicação do Estado com base nos seus requisitos de armazenamento de dados:

* **Estado persistido**: O Estado é persistido no disco e é replicado em três ou mais réplicas. O estado persistido é a opção de armazenamento estatal mais duradoura, onde o estado pode persistir através de uma completa paragem do cluster.
* **Estado volátil**: O Estado é replicado em três ou mais réplicas e apenas guardado na memória. O estado volátil fornece resiliência contra a falha do nó e a falha do ator, e durante as melhorias e o equilíbrio de recursos. No entanto, o estado não é persistido no disco. Então, se todas as réplicas forem perdidas ao mesmo tempo, o estado também está perdido.
* **Nenhum estado persistido**: O Estado não é replicado ou escrito em disco, apenas uso para atores que não precisam de manter o estado de forma fiável.

Cada nível de persistência é simplesmente um *fornecedor de estado* diferente e configuração de *replicação* do seu serviço. Se o estado é ou não escrito ao disco depende do fornecedor estatal -- o componente de um serviço fiável que armazena o estado. A replicação depende de quantas réplicas um serviço é implantado. Tal como acontece com os Serviços Fiáveis, tanto o fornecedor do Estado como a contagem de réplicas podem ser facilmente definidos manualmente. A estrutura do ator fornece um atributo que, quando usado num ator, seleciona automaticamente um fornecedor de estado padrão e gera automaticamente configurações para a contagem de réplicas para alcançar uma destas três definições de persistência. O atributo statePersistence não é herdado por classe derivada, cada tipo de Ator deve fornecer o seu nível de Persistência do Estado.

### <a name="persisted-state"></a>Estado persistido
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Esta definição utiliza um fornecedor estatal que armazena dados no disco e automaticamente define a contagem de réplicas do serviço para 3.

### <a name="volatile-state"></a>Estado volátil
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Esta definição utiliza um fornecedor de estado apenas na memória e define a contagem de réplicas para 3.

### <a name="no-persisted-state"></a>Nenhum estado persistido
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Esta definição utiliza um fornecedor de estado apenas na memória e define a contagem de réplicas para 1.

### <a name="defaults-and-generated-settings"></a>Predefinições e configurações geradas
Quando estiver a `StatePersistence` utilizar o atributo, um fornecedor de estado é automaticamente selecionado para si no momento em que o serviço de ator começa. A contagem de réplicas, no entanto, é definida no momento da compilação pelo ator do Estúdio Visual construir ferramentas. As ferramentas de construção geram automaticamente um *serviço predefinido* para o serviço de ator em ApplicationManifest.xml. Os parâmetros são criados para o tamanho do conjunto de **réplicas min** e para o tamanho do conjunto de **réplicas**do alvo .

Pode alterar estes parâmetros manualmente. Mas cada `StatePersistence` vez que o atributo é alterado, os parâmetros são `StatePersistence` definidos para os valores de tamanho definido de réplica padrão para o atributo selecionado, sobrepondo-se a quaisquer valores anteriores. Por outras palavras, os valores que definiu em ServiceManifest.xml `StatePersistence` *são apenas* ultrapassados no momento em que altera o valor do atributo.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Gestor do Estado
Cada instância de ator tem o seu próprio gestor de estado: uma estrutura de dados tipo dicionário que armazena seguramente pares chave/valor. O gerente do Estado é um invólucro em torno de um provedor do estado. Pode usá-lo para armazenar dados independentemente da definição de persistência. Não oferece garantias de que um serviço de ator em execução possa ser alterado de um estado volátil (apenas na memória) para uma definição de estado persistente através de uma atualização de rolo, preservando dados. No entanto, é possível alterar a contagem de réplicas para um serviço de corrida.

As chaves do gerente do estado devem ser cordas. Os valores são genéricos e podem ser de qualquer tipo, incluindo tipos personalizados. Os valores armazenados no gestor do Estado devem ser um contrato de dados, pois podem ser transmitidos através da rede a outros nós durante a replicação e podem ser escritos em disco, dependendo da persistência do estado de um ator.

O gestor do Estado expõe métodos comuns de dicionário para gerir o estado, semelhantes aos encontrados no Dicionário Fiável.

Por exemplo, como gerir o estado do ator, ler [Access, salvar e remover o estado de Reliable Actors](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Melhores práticas
Aqui estão algumas práticas sugeridas e dicas de resolução de problemas para gerir o seu estado de ator.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Faça o estado do ator o mais granular possível
Isto é fundamental para o desempenho e utilização de recursos da sua aplicação. Sempre que há qualquer escrita/atualização para o "estado nomeado" de um ator, todo o valor correspondente a esse "estado nomeado" é serializado e enviado pela rede para as réplicas secundárias.  Os secundários escrevem-no ao disco local e respondem à réplica primária. Quando a primária recebe reconhecimentos de um quórum de réplicas secundárias, escreve o estado ao seu disco local. Por exemplo, suponha que o valor é uma classe que tem 20 membros e um tamanho de 1 MB. Mesmo que só tenha modificado um dos membros da classe que é de tamanho 1 KB, acaba por pagar o custo da serialização e da rede e dos escritos em disco para o total de 1 MB. Da mesma forma, se o valor for uma coleção (como uma lista, matriz ou dicionário), você paga o custo pela coleção completa mesmo que modifique um dos seus membros. A interface do StateManager da classe ator é como um dicionário. Deve sempre modelar a estrutura de dados que representa o estado do ator em cima deste dicionário.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Gerir corretamente o ciclo de vida do ator
Deve ter uma política clara sobre como gerir a dimensão do estado em cada partição de um serviço de ator. O seu serviço de ator deve ter um número fixo de atores e reutilizá-los o máximo possível. Se criar continuamente novos atores, deve eliminá-los assim que terminarem o seu trabalho. O quadro do ator armazena alguns metadados sobre cada ator que existe. Apagar todo o estado de um ator não remove metadados sobre aquele ator. Deve eliminar o ator (ver [apagar atores e o seu estado)](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)para remover toda a informação sobre ele armazenada no sistema. Como uma verificação extra, deve consultar o serviço do ator (ver [atores enumeradores)](service-fabric-reliable-actors-enumerate.md)de vez em quando para se certificar de que o número de atores está dentro do intervalo esperado.
 
Se alguma vez vir que o tamanho do ficheiro de base de dados de um Serviço de Ator está a aumentar para além do tamanho esperado, certifique-se de que está a seguir as orientações anteriores. Se está a seguir estas diretrizes e ainda está a ser problemas de tamanho de ficheiros de base de dados, deve [abrir um bilhete](service-fabric-support.md) de apoio com a equipa de produtos para obter ajuda.

## <a name="next-steps"></a>Passos seguintes

O estado que está armazenado em Atores Fiáveis deve ser serializado antes de ser escrito em disco e replicado para alta disponibilidade. Saiba mais sobre a serialização do [tipo Ator.](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

Em seguida, saiba mais sobre diagnósticos de [ator e monitorização de desempenho.](service-fabric-reliable-actors-diagnostics.md)
