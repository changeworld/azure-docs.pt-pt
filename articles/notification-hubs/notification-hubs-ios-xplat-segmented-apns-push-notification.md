---
title: Envie notificações push para dispositivos específicos do iOS utilizando hubs de notificação do Azure [ Hubs de Notificação' Microsoft Docs
description: Neste tutorial, aprende a utilizar hubs de notificação do Azure para enviar notificações push para dispositivos específicos do iOS.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: a775963f1b0fa19cd687c839f527f4a078c76864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127000"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Tutorial: Enviar notificações push para dispositivos específicos do iOS usando hubs de notificação Azure

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como usar o Azure Notification Hubs para transmitir notificações de última hora para uma aplicação iOS. Quando estiver concluído, pode inscrever-se nas categorias de notícias de última hora que lhe interessa e receber apenas notificações push para essas categorias. Este é um cenário com um padrão comum para muitas aplicações em que as notificações têm de ser enviadas para grupos de utilizadores que mostraram anteriormente interesse nas mesmas, por exemplo, leitor de RSS, aplicações para fãs de música, etc.

Os cenários de transmissão são ativados ao incluir uma ou mais *etiquetas* durante a criação de um registo no Hub de Notificação. Quando as notificações são enviadas para uma etiqueta, os dispositivos que se registaram para a etiqueta recebem a notificação. Como as etiquetas são simples cadeias, não têm de ser aprovisionadas com antecedência. Para obter mais informações sobre etiquetas, consulte [Encaminhamento de Hubs de Notificação e Expressões de Etiqueta](notification-hubs-tags-segment-push-message.md).

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Adicione uma seleção de categorias à app
> * Enviar notificações marcadas
> * Enviar notificações do dispositivo
> * Executar a aplicação e gerar notificações

## <a name="prerequisites"></a>Pré-requisitos

Este tópico baseia-se na aplicação que criou no [Tutorial: Push notificações para aplicações iOS utilizando hubs de notificação Azure][get-started]. Antes de iniciar este tutorial, já deve ter concluído [o Tutorial: Push notificações para aplicações iOS utilizando hubs de notificação Azure][get-started].

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias à aplicação

O primeiro passo é adicionar os elementos UI ao seu storyboard existente que permitem ao utilizador selecionar categorias para se registar. As categorias selecionadas por um utilizador são armazenadas no dispositivo. Quando a aplicação é iniciada, é criado um registo do dispositivo no seu hub de notificação com as categorias selecionadas como etiquetas.

1. No seu **MainStoryboard_iPhone.storyboard** adicione os seguintes componentes da biblioteca de objetos:

   * Uma etiqueta com texto "Notícias de Última Hora",
   * Etiquetas com textos de categoria "Mundo", "Política", "Negócios", "Tecnologia", Ciência", "Desporto",
   * Seis interruptores, um por categoria, desemprem cada **Estado** de comutador por defeito. **Off**
   * Um botão com o rótulo "Subscrever"

     O seu storyboard deve ser o seguinte:

     ![Construtor de interface xcode][3]

2. No editor assistente, crie pontos de venda para todos os switches e chame-os de "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

3. Crie uma Ação `subscribe`para o seu botão chamado; deve `ViewController.h` conter o seguinte código:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Crie uma nova Classe `Notifications` **Cocoa Touch** chamada . Copie o seguinte código na secção de interface do ficheiro Notificações.h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Adicione a seguinte diretiva relativa à importação às notificações.m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Copie o seguinte código na secção de implementação do ficheiro Notificações.m.

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Esta classe utiliza armazenamento local para armazenar e recuperar as categorias de notícias que este dispositivo recebe. Além disso, contém um método para se registar para estas categorias usando um registo [de modelo.](notification-hubs-templates-cross-platform-push-messages.md)

7. No `AppDelegate.h` ficheiro, adicione uma `Notifications.h` declaração de importação para e `Notifications` adicione um imóvel por exemplo da classe:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. No `didFinishLaunchingWithOptions` método `AppDelegate.m`em, adicione o código para inicializar a instância de notificações no início do método.  
    `HUBNAME`e `HUBLISTENACCESS` (definido `hubinfo.h`em ) `<hub name>` já `<connection string with listen access>` deve ter os espaços reservados e os espaços reservados substituídos pelo seu nome de centro de notificação e a cadeia de ligação para *DefaultListenSharedAccessSignature* que obteve anteriormente

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Uma vez que, de um modo geral, as credenciais que são distribuídas com uma aplicação cliente não são seguras, só deve distribuir a chave para acesso de escuta com a sua aplicação cliente. O acesso de escuta permite que a sua aplicação seja registada para receber notificações, mas não é possível modificar os registos existentes nem enviar notificações. A chave de acesso total é utilizada num serviço de back-end protegido para o envio de notificações e a alteração de registos existentes.

9. No `didRegisterForRemoteNotificationsWithDeviceToken` método `AppDelegate.m`em , substitua o código no método com o `notifications` seguinte código para passar o token do dispositivo para a classe. A `notifications` classe realiza o registo para notificações com as categorias. Se o utilizador alterar as `subscribeWithCategories` seleções de categorias, ligue para o método em resposta ao botão **de subscrição** para atualizá-las.

    > [!NOTE]
    > Uma vez que o token do dispositivo atribuído pelo Apple Push Notification Service (APNS) pode ser alterado a qualquer momento, deve registar-se frequentemente para notificações para evitar falhas de notificação. Este exemplo regista-se em notificações sempre que a aplicação é iniciada. Relativamente às aplicações executadas com frequência, ou seja, mais do que uma vez por dia, pode provavelmente ignorar o registo de modo a preservar a largura de banda caso tenha passado menos de um dia desde o registo anterior.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    Neste momento, não deve haver outro `didRegisterForRemoteNotificationsWithDeviceToken` código no método.

10. Os seguintes métodos `AppDelegate.m` já devem estar presentes a partir da conclusão do Tutorial Get started com O Tutor de Centros de [Notificação.][get-started] Se não, adicione-os.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Este método trata das notificações recebidas quando a aplicação está em execução, exibindo um **simples UIAlert**.

11. Em `ViewController.m`, `import` adicione `AppDelegate.h` uma declaração para e copie o `subscribe` seguinte código no método gerado pelo XCode. Este código atualiza o registo de notificação para utilizar as novas etiquetas de categoria que o utilizador escolheu na interface do utilizador.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Este método `NSMutableArray` cria uma de `Notifications` categorias e utiliza a classe para armazenar a lista no armazenamento local e regista as etiquetas correspondentes com o seu centro de notificação. Quando as categorias são alteradas, o registo é recriado com as novas categorias.

12. Em `ViewController.m`, adicione o `viewDidLoad` seguinte código no método para definir a interface do utilizador com base nas categorias previamente guardadas.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

A aplicação pode agora armazenar um conjunto de categorias no dispositivo armazenamento local usado para registar-se com o centro de notificação sempre que a aplicação começa. O utilizador pode alterar a seleção de `subscribe` categorias no tempo de execução e clicar no método para atualizar o registo do dispositivo. Em seguida, atualiza a aplicação para enviar as notificações de notícias de última hora diretamente na própria app.

## <a name="optional-send-tagged-notifications"></a>(opcional) Enviar notificações marcadas

Se não tiver acesso ao Estúdio Visual, pode saltar para a secção seguinte e enviar notificações da própria app. Também pode enviar a notificação de modelo adequada do [portal Azure] utilizando o separador de depuração para o seu centro de notificação.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(opcional) Enviar notificações do dispositivo

Normalmente, as notificações seriam enviadas por um serviço de backend, mas pode enviar notificações de notícias de última hora diretamente da app. Para tal, atualiza o `SendNotificationRESTAPI` método que definiu no Get started com o tutorial De [Notificação Hubs.][get-started]

1. Em `ViewController.m`, `SendNotificationRESTAPI` atualize o método da seguinte forma de que aceite um parâmetro para a etiqueta de categoria e envie a notificação de [modelo](notification-hubs-templates-cross-platform-push-messages.md) adequada.

    ```objc
    - (void)SendNotificationRESTAPI:(NSString*)categoryTag
    {
        NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                    defaultSessionConfiguration] delegate:nil delegateQueue:nil];

        NSString *json;

        // Construct the messages REST endpoint
        NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                            HUBNAME, API_VERSION]];

        // Generated the token to be used in the authorization header.
        NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

        //Create the request to add the template notification message to the hub
        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
        [request setHTTPMethod:@"POST"];

        // Add the category as a tag
        [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

        // Template notification
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

        // Signify template notification format
        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

        // JSON Content-Type
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

        //Authenticate the notification message POST request with the SaS token
        [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

        // Send the REST request
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                    completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    //xmlParser = [[NSXMLParser alloc] initWithData:data];
                    //[xmlParser setDelegate:self];
                    //[xmlParser parse];
                }
            }];

        [dataTask resume];
    }
    ```

2. Em `ViewController.m`, `Send Notification` atualize a ação como mostrado no código que se segue. Para que envie as notificações usando cada etiqueta individualmente e envie para várias plataformas.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Reconstrói o teu projeto e certifica-te de que não tens erros de construção.

## <a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações

1. Prima o botão Executar para compilar o projeto e iniciar a aplicação. Selecione algumas opções de notícias de última hora para subscrever e, em seguida, prima o botão **Subscrever.** Deve consultar um diálogo que indique que as notificações foram subscritas.

    ![Notificação de exemplo no iOS][1]

    Quando escolher **Subscrever,** a aplicação converte as categorias selecionadas em etiquetas e solicita um novo registo de dispositivos para as etiquetas selecionadas a partir do centro de notificação.

2. Introduza uma mensagem a ser enviada como notícia de última hora e prima o botão **Enviar notificação.** Em alternativa, execute a aplicação de consola .NET para gerar notificações.

    ![Alterar preferências de notificação no iOS][2]

3. Cada dispositivo subscrito a notícias de última hora recebe as notificações de última hora que acabou de enviar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, enviou notificações de transmissão para dispositivos específicos do iOS que se registaram para as categorias. Para saber como empurrar notificações localizadas, avance para o seguinte tutorial:

> [!div class="nextstepaction"]
>[Push localized notifications](notification-hubs-ios-xplat-localized-apns-push-notification.md) (Enviar notificações localizadas)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Portal Azure]: https://portal.azure.com
