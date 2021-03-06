---
title: Visão geral do tecido de serviço e dos recipientes
description: Uma visão geral do Tecido de Serviço e a utilização de recipientes para implantar aplicações de microserviços. Este artigo fornece uma visão geral de como os recipientes podem ser usados e as capacidades disponíveis no Tecido de Serviço.
ms.topic: conceptual
ms.date: 8/8/2018
ms.openlocfilehash: 884cefa3d6a60f55269afac73c40b9f6b21518f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458217"
---
# <a name="service-fabric-and-containers"></a>Tecido de serviço e recipientes

## <a name="introduction"></a>Introdução

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços e contentores dimensionáveis e fiáveis.

Service Fabric é [o orquestrador](service-fabric-cluster-resource-manager-introduction.md) de contentores da Microsoft para a implementação de microserviços através de um conjunto de máquinas. O Service Fabric beneficia das lições aprendidas durante os seus anos de funcionamento de serviços na Microsoft em larga escala.

Os microsserviços podem ser desenvolvidos de muitas formas, desde a utilização dos [modelos de programação do Service Fabric](service-fabric-choose-framework.md) e do [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) até à implementação de [qualquer código à sua escolha](service-fabric-guest-executables-introduction.md). Ou, se apenas quiser [implantar e gerir contentores,](service-fabric-containers-overview.md)o Service Fabric também é uma ótima escolha.

Por padrão, o Service Fabric implanta e ativa estes serviços como processos. Os processos proporcionam a ativação mais rápida e o uso de maior densidade dos recursos num cluster. O Serviço Fabric também pode implantar serviços em imagens de contentores. Também pode misturar serviços em processos e serviços em contentores, na mesma aplicação.

Para entrar e experimentar recipientes em Tecido de Serviço, experimente um arranque rápido, tutorial ou amostra:  

[Quickstart: Implemente uma aplicação de contentor Linux para o Tecido de Serviço](service-fabric-quickstart-containers-linux.md)  
[Quickstart: Implemente uma aplicação de contentor windows para o Tecido de Serviço](service-fabric-quickstart-containers.md)  
[Colocar uma aplicação .NET existente num contentor](service-fabric-host-app-in-a-container.md)  
[Exemplos de Contentor do Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>O que são recipientes

Os contentores resolvem o problema de executar aplicações de forma fiável em diferentes ambientes informáticos, proporcionando um ambiente imutável para a aplicação a funcionar. Os contentores envolvem uma aplicação e todas as suas dependências, como bibliotecas e ficheiros de configuração, na sua própria 'caixa' isolada que contém tudo o que é necessário para executar o software dentro do contentor. Onde quer que o recipiente esteja em funcionamento, a aplicação no seu interior tem sempre tudo o que precisa para ser executada, como as versões certas das suas bibliotecas dependentes, quaisquer ficheiros de configuração e qualquer outra coisa que precise de executar.

Os contentores funcionam diretamente em cima do núcleo e têm uma visão isolada do sistema de ficheiros e de outros recursos. Uma aplicação num contentor não tem conhecimento de quaisquer outras aplicações ou processos fora do seu contentor. Cada aplicação e o seu tempo de funcionamento, dependências e bibliotecas de sistemas funcionam dentro de um contentor com acesso total e privado à visão isolada do próprio contentor do sistema operativo. Além de facilitar a disponibilização de todas as dependências da sua aplicação, necessita de funcionar em diferentes ambientes de computação, a segurança e o isolamento de recursos são benefícios importantes da utilização de contentores com tecido de serviço-- que de outra forma executa serviços em um processo.

Em comparação com as máquinas virtuais, os recipientes têm as seguintes vantagens:

* **Pequeno**: Os recipientes utilizam um único espaço de armazenamento e versões de camadas e atualizações para aumentar a eficiência.
* **Rápido**: Os recipientes não têm de iniciar todo um sistema operativo, para que possam começar muito mais rápido - tipicamente em segundos.
* **Portabilidade**: Uma imagem de aplicação contentorizada pode ser transportada para correr na nuvem, no local, dentro de máquinas virtuais ou diretamente em máquinas físicas.
* **Governação de recursos**: Um recipiente pode limitar os recursos físicos que pode consumir no seu hospedeiro.

### <a name="container-types-and-supported-environments"></a>Tipos de contentores e ambientes apoiados

O Service Fabric suporta recipientes tanto no Linux como no Windows e suporta o modo de isolamento Hyper-V no Windows.

#### <a name="docker-containers-on-linux"></a>Contentores de estivadores em Linux

Docker fornece APIs para criar e gerir recipientes em cima de recipientes de kernel Linux. Docker Hub fornece um repositório central para armazenar e recuperar imagens de contentores.
Para um tutorial baseado em Linux, consulte [Create your first Service Fabric container application on Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Contentores do Windows Server

O Windows Server 2016 fornece dois tipos diferentes de recipientes que diferem por nível de isolamento. Os recipientes do Windows Server e do Docker são semelhantes porque ambos têm espaço de nome e isolamento do sistema de ficheiros, enquanto partilham o núcleo com o hospedeiro em que estão a funcionar. No Linux, este isolamento tem sido tradicionalmente fornecido por cgroups e espaços de nome, e os recipientes do Windows Server comportam-se da mesma forma.

Os recipientes windows com suporte Hyper-V proporcionam mais isolamento e segurança porque nenhum contentor partilha o núcleo do sistema operativo com qualquer outro recipiente, ou com o hospedeiro. Com este maior nível de isolamento de segurança, os contentores ativados por Hyper-V são direcionados para cenários potencialmente hostis e multi-inquilinos.
Para um tutorial baseado no Windows, consulte [Create your first Service Fabric container application on Windows](service-fabric-get-started-containers.md).

O número seguinte mostra os diferentes tipos de virtualização e níveis de isolamento disponíveis.
![Plataforma do Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Cenários para a utilização de contentores

Aqui estão exemplos típicos onde um recipiente é uma boa escolha:

* **IIS levantar e mudar:** Pode colocar uma aplicação [MVC ASP.NET](https://www.asp.net/mvc) existente num recipiente em vez de a migrar para ASP.NET Core. Estas aplicações ASP.NET MVC dependem dos Serviços de Informação da Internet (IIS). Pode embalar estas aplicações em imagens de contentores a partir da imagem IIS pré-criada e implantá-las com tecido de serviço. Consulte [imagens de contentores no Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) para obter informações sobre os recipientes do Windows.

* **Misture recipientes e microserviços de tecido**de serviço : Utilize uma imagem de recipiente existente para parte da sua aplicação. Por exemplo, pode utilizar o [recipiente NGINX](https://hub.docker.com/_/nginx/) para a extremidade frontal web da sua aplicação e serviços imponentes para o cálculo back-end mais intensivo.

* Reduzir o **impacto dos serviços de "vizinhos barulhentos":** Pode utilizar a capacidade de governação de recursos dos contentores para restringir os recursos que um serviço utiliza num hospedeiro. Se os serviços puderem consumir muitos recursos e afetar o desempenho de outros (como uma operação de longa duração, semelhante a consultas), considere colocar estes serviços em contentores que tenham governação de recursos.

## <a name="service-fabric-support-for-containers"></a>Suporte do Service Fabric para contentores

O Service Fabric suporta a implementação de contentores Docker no Linux e nos recipientes Do Windows Server no Windows Server 2016, juntamente com suporte para o modo de isolamento Hyper-V. 

O Service Fabric fornece um modelo de [aplicação](service-fabric-application-model.md) no qual um recipiente representa um hospedeiro de aplicação no qual são colocadas várias réplicas de serviço. O Service Fabric também suporta um [cenário executável](service-fabric-guest-executables-introduction.md) de hóspedes no qual você não usa os modelos de programação de Tecido de Serviço incorporado, mas em vez disso embala uma aplicação existente, escrita usando qualquer idioma ou enquadramento, dentro de um recipiente. Este cenário é o caso comum de utilização dos contentores.

Também pode executar [serviços de Tecido de Serviço dentro de um recipiente](service-fabric-services-inside-containers.md). O suporte para funcionamento dos serviços de tecido de serviço dentro de contentores é atualmente limitado.

O Service Fabric fornece várias capacidades de contentores que o ajudam a construir aplicações compostas por microserviços contentorizados, tais como:

* Implantação e ativação da imagem do recipiente.
* Governação de recursos incluindo a fixação de valores de recursos por padrão nos clusters Azure.
* Autenticação repositória.
* Porta de contentores para o mapeamento do porto de acolhimento.
* Descoberta e comunicação contentor-a-recipiente.
* Capacidade de configurar e definir variáveis ambientais.
* Capacidade de definir credenciais de segurança no recipiente.
* Uma escolha de diferentes modos de rede para recipientes.

Para uma visão geral abrangente do suporte de contentores em Azure, como como criar um cluster Kubernetes com o Serviço Azure Kubernetes, como criar um registo privado de Docker no Registo de Contentores Azure, e muito mais, ver [Azure for Containers.](https://docs.microsoft.com/azure/containers/)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre o suporte que o Tecido de Serviço fornece para os recipientes de funcionamento. Em seguida, vamos analisar exemplos de cada uma das funcionalidades para lhe mostrar como usá-las.

[Criar a sua primeira aplicação de contentor do Service Fabric no Linux](service-fabric-get-started-containers-linux.md)  
[Criar a sua primeira aplicação de contentor do Service Fabric no Windows](service-fabric-get-started-containers.md)  
[Saiba mais sobre os recipientes do Windows](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png