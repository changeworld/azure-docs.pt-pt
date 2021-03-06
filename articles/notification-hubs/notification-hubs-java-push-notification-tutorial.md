---
title: Como utilizar hubs de notificação Azure com Java
description: Aprenda a usar hubs de notificação Azure a partir de um java back-end.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d48973cc7c5ed1fc7ae3f96128d488f3f1df3a05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263868"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Como usar centros de notificação de Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Este tópico descreve as principais características do novo Hub de Notificação Azure Java SDK totalmente suportado.
Este projeto é um projeto de código aberto e você pode ver todo o código SDK em [Java SDK].

Em geral, pode aceder a todas as funcionalidades do Notification Hubs a partir de um back-end Java/PHP/Python/Ruby utilizando a interface DE REPOUSO Do Hub de Notificação, tal como descrito no tópico MSDN Centros de [Notificação REST APIs](https://msdn.microsoft.com/library/dn223264.aspx). Este Java SDK fornece um invólucro fino sobre estas interfaces REST em Java.

O SDK suporta atualmente:

* CRUD em Centros de Notificação
* CRUD em Inscrições
* Gestão de Instalações
* Registos de Importação/Exportação
* Envios regulares
* Envios agendados
* Operações de asincronização via Java NIO
* Plataformas suportadas: APNS (iOS), FCM (Android), WNS (aplicações Windows Store), MPNS(Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android sem serviços da Google)

## <a name="sdk-usage"></a>Utilização do SDK

### <a name="compile-and-build"></a>Compilar e construir

Use [Maven]

Para construir:

    mvn package

## <a name="code"></a>Código

### <a name="notification-hub-cruds"></a>Centros de notificação CRUDs

**Criar um NamespaceManager:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Criar centro de notificação:**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 OU

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Obter Centro de Notificação:**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**Centro de Notificação de Atualização:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Eliminar o Centro de Notificação:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>CruDs de inscrição

**Criar um cliente Do Hub de Notificação:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Criar registo do Windows:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**Criar registo iOS:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

Da mesma forma, pode criar registos para Android (FCM), Windows Phone (MPNS) e Kindle Fire (ADM).

**Criar registos de modelos:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Criar registos utilizando a criação de identificação de registo + padrão de upsert:**

Remove duplicados devido a quaisquer respostas perdidas se armazenar iDs de registo no dispositivo:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Registos de atualização:**

    ```java
    hub.updateRegistration(reg);
    ```

**Eliminar registos:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Registos de consultas:**

* **Obtenha um único registo:**

    ```java
    hub.getRegistration(regid);
    ```

* **Obtenha todas as inscrições no centro:**

    ```java
    hub.getRegistrations();
    ```

* **Obtenha inscrições com etiqueta:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Obtenha inscrições por canal:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Todas as consultas de recolha apoiam $top e fichas de continuação.

### <a name="installation-api-usage"></a>Utilização da API de instalação

A Instalação API é um mecanismo alternativo para a gestão do registo. Em vez de manter vários registos, que não são triviais e podem ser facilmente feitos de forma incorreta ou ineficiente, agora é possível utilizar um objeto de instalação SINGLE.

A instalação contém tudo o que precisa: canal de impulso (token do dispositivo), etiquetas, modelos, azulejos secundários (para WNS e APNS). Não precisa de ligar mais para obter identificação - basta gerar GUID ou qualquer outro identificador, mantê-lo no dispositivo e enviar para o seu backend juntamente com o canal push (token do dispositivo).

No final, só deve fazer uma `CreateOrUpdateInstallation`única chamada para; é totalmente idempotente, por isso sinta-se livre para voltar a tentar, se necessário.

Como exemplo para o Amazon Kindle Fire:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Se quiser atualizá-lo:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

Para cenários avançados, utilize a capacidade de atualização parcial, que permite modificar apenas propriedades específicas do objeto de instalação. A atualização parcial é o subconjunto das operações do Patch JSON que pode ser executada contra o objeto de instalação.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Excluir instalação:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch`e `Delete` eventualmente são `Get`consistentes com . A sua operação solicitada vai para a fila do sistema durante a chamada e é executada em segundo plano. O Get não foi concebido para o cenário principal de tempo de funcionar, mas apenas para fins de depuração e resolução de problemas, é fortemente estrangulado pelo serviço.

Enviar fluxo para Instalações é o mesmo que para as Inscrições. Para direcionar a notificação para a instalação específica - utilize apenas a etiqueta "InstallationId:{desejado-id}". Para este caso, o código é:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

Para um dos vários modelos:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Notificações de Horários (disponíveis para standard tier)

O mesmo que o envio regular, mas com um parâmetro adicional - agendadoTime, que diz quando a notificação deve ser entregue. O serviço aceita qualquer ponto de tempo entre agora + 5 minutos e agora + 7 dias.

**Agende uma notificação nativa do Windows:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Importação/Exportação (disponível para standard tier)

Poderá ser necessário efetuar operações a granel contra registos. Normalmente é para integração com outro sistema ou uma correção massiva para atualizar as etiquetas. Não recomendamos a utilização do fluxo Get/Update se estiverem envolvidos milhares de registos. A capacidade de importação/exportação do sistema foi concebida para cobrir o cenário. Você fornecerá acesso a um recipiente blob sob a sua conta de armazenamento como fonte de dados e localização para a saída.

**Apresentar um emprego de exportação:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Apresentar um emprego de importação:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Espere até que um trabalho esteja feito:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Arranja todos os empregos:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**URI com assinatura SAS:**

 Este URL é o URL de um ficheiro ou recipiente de bolha mais um conjunto de parâmetros como permissões e tempo de validade mais a assinatura de todas estas coisas feitas usando a chave SAS da conta. O Azure Storage Java SDK tem capacidades ricas, incluindo a criação destes URIs. Como alternativa simples, dê `ImportExportE2E` uma olhada na classe de teste (a partir da localização GitHub) que tem implementação básica e compacta do algoritmo de assinatura.

### <a name="send-notifications"></a>Enviar Notificações

O objeto de notificação é simplesmente um corpo com cabeçalhos, alguns métodos de utilidade ajudam na construção dos objetos de notificações nativos e modelos.

* **Windows Store e Windows Phone 8.1 (não-Silverlight)**

    ```java
    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);
    ```

* **iOS**

    ```java
    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);
    ```

* **Android**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createFcmNotification(message);
    hub.sendNotification(n);
    ```

* **Windows Phone 8.0 e 8.1 Silverlight**

    ```java
    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);
    ```

* **Fogo de Kindle**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **Enviar para Tags**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Enviar para tag expressão**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Enviar notificação de modelo**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

A execução do seu código Java deverá agora produzir uma notificação que apareça no seu dispositivo-alvo.

## <a name="next-steps"></a><a name="next-steps"></a>Próximos Passos

Este tópico mostrou-lhe como criar um simples cliente Java REST para Centros de Notificação. A partir daqui, pode:

* Descarregue o [Java SDK]completo, que contém todo o código SDK.
* Brinque com as amostras:
  * [Começar com centros de notificação]
  * [Enviar notícias de última hora]
  * [Enviar notícias de última hora localizadas]
  * [Enviar notificações a utilizadores autenticados]
  * [Enviar notificações inter-plataformas a utilizadores autenticados]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Começar com centros de notificação]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Enviar notícias de última hora]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Enviar notícias de última hora localizadas]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Enviar notificações a utilizadores autenticados]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Enviar notificações inter-plataformas a utilizadores autenticados]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
