---
title: Use configuração dinâmica numa aplicação Spring Boot
titleSuffix: Azure App Configuration
description: Saiba como atualizar dinamicamente os dados de configuração para aplicações de Boot de primavera
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216764"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Tutorial: Use a configuração dinâmica numa aplicação Java Spring

A biblioteca de clientes da Bota de Arranque de Configuração da Aplicação suporta a atualização de um conjunto de configurações a pedido, sem causar o reinício de uma aplicação. A biblioteca do cliente caches cada configuração para evitar muitas chamadas para a loja de configuração. A operação de atualização não atualiza o valor até que o valor em cache tenha expirado, mesmo quando o valor tenha mudado na loja de configuração. O tempo de validade predefinido para cada pedido é de 30 segundos. Pode ser ultrapassado, se necessário.

Pode verificar se estão atualizadas as definições a pedido, ligando para `AppConfigurationRefresh`o método. `refreshConfigurations()`

Em alternativa, pode `spring-cloud-azure-appconfiguration-config-web` utilizar o pacote, que `spring-web` requer uma dependência para lidar com a atualização automatizada.

## <a name="use-automated-refresh"></a>Use atualização automatizada

Para utilizar uma atualização automatizada, comece com uma aplicação Spring Boot que utiliza a Configuração de Aplicações, como a aplicação que cria seguindo o [quickstart spring boot para configuração](quickstart-java-spring-app.md)de aplicações .

Em seguida, abra o ficheiro *pom.xml* num `<dependency>` `spring-cloud-azure-appconfiguration-config-web`editor de texto e adicione um para .

**Nuvem de primavera 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Nuvem de primavera 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Guarde o ficheiro e, em seguida, construa e execute a sua aplicação como de costume.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ativou a sua aplicação Spring Boot para atualizar dinamicamente as definições de configuração a partir da Configuração da Aplicação. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
