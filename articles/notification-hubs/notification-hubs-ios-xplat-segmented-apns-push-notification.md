---
title: Azure Notification Hubs kullanarak belirli iOS cihazlarına anında iletme bildirimleri gönderin | Microsoft Docs
description: Bu öğreticide, belirli iOS cihazlarına anında iletme bildirimleri göndermek için Azure Notification Hubs kullanmayı öğreneceksiniz.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 2cb979491e247a4d44b9ae9ae27c433fb3f436d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579230"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Öğretici: Azure Notification Hubs kullanarak belirli iOS cihazlarına anında iletme bildirimleri gönderin

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Genel Bakış

Bu öğreticide, bir iOS uygulamasına uyarı haber bildirimleri yayınlamak için Azure Notification Hubs 'nin nasıl kullanılacağı gösterilmektedir. Bu tamamlandığında, ilgilendiğiniz son haber kategorilerine kaydolabilir ve yalnızca bu kategorilerin anında iletme bildirimlerini alabilirsiniz. Bu senaryo, daha önce ilgisini belirtmiş kullanıcı gruplarına bildirim gönderilmesi gereken RSS okuyucu, müzik hayranlarına yönelik uygulamalar vb. birçok uygulama için ortak düzendir.

Yayın senaryoları, bildirim hub’ında bir kayıt oluştururken bir veya daha fazla *etiket* dahil edilerek etkinleştirilir. Bildirimler bir etikete gönderildiğinde, etiket için kaydedilen cihazlar bildirimi alır. Etiketler basitçe birer dize olduğundan, önceden hazırlanması gerekmez. Etiketler hakkında daha fazla bilgi için bkz. [Notification Hubs Yönlendirme ve Etiket İfadeleri](notification-hubs-tags-segment-push-message.md).

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Uygulamaya kategori seçimi ekleme
> * Etiketli bildirimler gönderme
> * Cihazdan bildirim gönder
> * Uygulamayı çalıştırma ve bildirimler oluşturma

## <a name="prerequisites"></a>Önkoşullar

Bu konu, [öğretici: Azure Notification Hubs kullanarak iOS uygulamalarına anında iletme bildirimleri aracılığıyla][get-started]oluşturduğunuz uygulamayı oluşturur. Bu öğreticiye başlamadan önce, [Azure Notification Hubs kullanarak iOS uygulamalarına anında iletme bildirimleri][get-started]zaten tamamlanmış olmalıdır.

## <a name="add-category-selection-to-the-app"></a>Uygulamaya kategori seçimi ekleme

İlk adım, kullanıcı ARABIRIMI öğelerini, kullanıcının kaydedileceği kategorileri seçmesini sağlayan mevcut görsel taslağınızı eklemektir. Bir kullanıcı tarafından seçilen kategoriler cihazda depolanır. Uygulama başlatıldığında, etiketler olarak seçilen kategorilerle bildirim hub’ınızda bir cihaz kaydı oluşturulur.

1. **MainStoryboard_iPhone. görsel taslağınızdan** aşağıdaki bileşenleri nesne kitaplığından ekleyin:

   * "Son haberler" metni olan bir etiket,
   * Kategori metinleri "Dünya", "siyatik", "Iş", "teknoloji", "bilim", "spor", "spor" içeren Etiketler
   * Her kategori için bir adet olmak üzere altı anahtar, her anahtar **durumunu** varsayılan olarak **kapalı** olacak şekilde ayarlar.
   * "Abone ol" etiketli bir düğme

     Görsel taslağınızı aşağıdaki gibi görünmelidir:

     ![Xcode arabirim Oluşturucusu][3]

2. Yardımcı düzenleyicide tüm anahtarlar için bir dış grup oluşturun ve bunları "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch" olarak çağırın

3. Düğme adlı düğmelisiniz için bir eylem oluşturun `subscribe` ; `ViewController.h` aşağıdaki kodu içermelidir:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Adlı yeni bir **Cocoa Touch sınıfı** oluşturun `Notifications` . Aşağıdaki kodu, Notifications. h dosyasının interface bölümüne kopyalayın:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Aşağıdaki import yönergesini bildirimlere ekleyin. d:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Aşağıdaki kodu, bildirimler. d dosyasının uygulama bölümüne kopyalayın.

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
        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Bu sınıf, bu cihazın aldığı haber kategorilerini depolamak ve almak için yerel depolama alanını kullanır. Ayrıca, bir [şablon](notification-hubs-templates-cross-platform-push-messages.md) kaydı kullanarak bu kategorilere kaydolmak için bir yöntem içerir.

7. `AppDelegate.h`Dosyasında, için bir içeri aktarma açıklaması ekleyin `Notifications.h` ve sınıfın bir örneği için bir özellik ekleyin `Notifications` :

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. `didFinishLaunchingWithOptions`İçindeki yönteminde `AppDelegate.m` , yönteminin başındaki bildirimler örneğini başlatmak için kodu ekleyin.  
    `HUBNAME` ve `HUBLISTENACCESS` (içinde tanımlanan `hubinfo.h` ), `<hub name>` `<connection string with listen access>` Bildirim Hub 'ı adı ve daha önce edindiğiniz *Defaultlistensharedaccesssignature* bağlantı dizesi ile değiştirilmelidir.

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Bir istemci uygulaması ile dağıtılmış kimlik bilgileri genellikle güvenli olmadığından yalnızca istemci uygulamanızla dinleme erişimi için anahtarı dağıtmanız gerekir. Dinleme erişimi, uygulamanızın bildirimlere kaydolmasını sağlar, ancak mevcut kayıtlar değiştirilemez ve bildirimler gönderilemez. Tam erişim anahtarı, güvenli bir arka uç hizmetinde bildirimler göndermek ve mevcut kayıtları değiştirmek için kullanılır.

9. `didRegisterForRemoteNotificationsWithDeviceToken`İçindeki yönteminde, `AppDelegate.m` yöntemi içindeki kodu aşağıdaki kodla değiştirin ve cihaz belirtecini `notifications` sınıfa geçirin. `notifications`Sınıfı, kategoriler ile bildirimler için kayıt işlemini gerçekleştirir. Kullanıcı kategori seçimlerini değiştirirse, `subscribeWithCategories` güncelleştirmek için **abone ol** düğmesine yanıt olarak yöntemi çağırın.

    > [!NOTE]
    > Apple Anında İletilen Bildirim Servisi (APNS) tarafından atanan cihaz belirteci herhangi bir zamanda değiştirebildiğinden, bildirim hatalarından kaçınmak için sık sık bildirimlere kaydolmanız gerekir. Bu örnek, uygulama her başlatıldığında bildirimlere kaydolur. Sık sık çalıştırılan uygulamalar için, önceki kayıttan bu yana bir günden az zaman geçtiyse bant genişliğini korumak için günde birkaç kere kaydı atlayabilirsiniz.

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

    Bu noktada, yönteminde başka kod olmaması gerekir `didRegisterForRemoteNotificationsWithDeviceToken` .

10. Aşağıdaki yöntemlerin ' de zaten mevcut olması gerekir `AppDelegate.m` [Notification Hubs öğreticisiyle çalışmaya başlama][get-started] . Aksi takdirde, bunları ekleyin.

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

    Bu yöntem, uygulama çalışırken bir basit **Uıalert** görüntüleyerek alınan bildirimleri işler.

11. İçinde `ViewController.m` , için bir `import` ifade ekleyin `AppDelegate.h` ve aşağıdaki kodu Xcode tarafından oluşturulan `subscribe` yönteme kopyalayın. Bu kod, kullanıcının Kullanıcı arabiriminde seçtiği yeni kategori etiketlerini kullanmak için bildirim kaydını güncelleştirir.

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

    Bu yöntem, bir `NSMutableArray` Kategoriler oluşturur ve bu `Notifications` sınıfı, listeyi yerel depolama alanında depolamak için kullanır ve karşılık gelen etiketleri Bildirim Hub 'ınızla kaydeder. Kategoriler değiştirildiğinde kayıt yeni kategorilerle yeniden oluşturulur.

12. İçinde `ViewController.m` , `viewDidLoad` daha önce kaydedilen kategorilere göre Kullanıcı arabirimini ayarlamak için yöntemine aşağıdaki kodu ekleyin.

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

Uygulama artık uygulama başlatıldığında bildirim hub 'ına kaydolmak için kullanılan cihaz yerel depolama alanında bir kategori kümesini saklayabilir. Kullanıcı çalışma zamanında kategorilerin seçimini değiştirebilir ve `subscribe` cihaz kaydını güncelleştirmek için yönteme tıklayabilirsiniz. Ardından uygulamayı, son haber bildirimlerini doğrudan uygulamanın kendisinde gönderecek şekilde güncelleştirebilirsiniz.

## <a name="optional-send-tagged-notifications"></a>seçim Etiketli bildirimler gönder

Visual Studio 'ya erişiminiz yoksa, sonraki bölüme atlayabilir ve uygulamanın kendisinden bildirim gönderebilirsiniz. Ayrıca, Bildirim Hub 'ınızın hata ayıklama sekmesini kullanarak [Azure Portal] uygun şablon bildirimini de gönderebilirsiniz.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>seçim Cihazdan bildirim gönder

Normalde bildirimler bir arka uç hizmeti tarafından gönderilebilir, ancak uygulamadan doğrudan haber bildirimleri gönderebilirsiniz. Bunu yapmak için, `SendNotificationRESTAPI` [Notification Hubs ile çalışmaya başlama][get-started] öğreticisinde tanımladığınız yöntemi güncelleştirin.

1. İçinde `ViewController.m` , `SendNotificationRESTAPI` category etiketi için bir parametre kabul etmek ve uygun [şablon](notification-hubs-templates-cross-platform-push-messages.md) bildirimini göndermesi için yöntemini aşağıdaki gibi güncelleştirin.

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

2. İçinde `ViewController.m` , `Send Notification` aşağıdaki kodda gösterildiği gibi eylemi güncelleştirin. Her bir etiketi kullanarak bildirimleri tek tek ve birden çok platforma gönderecek şekilde gönderir.

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

3. Projenizi yeniden derleyin ve derleme hatası olmadığından emin olun.

## <a name="run-the-app-and-generate-notifications"></a>Uygulamayı çalıştırma ve bildirimler oluşturma

1. Projeyi oluşturmak ve uygulamayı başlatmak için Çalıştır düğmesine basın. Abone olmak ve **abone ol** düğmesine basın için bazı önemli haber seçeneklerini belirleyin. Abone olunan bildirimleri belirten bir iletişim kutusu görmeniz gerekir.

    ![İOS üzerinde örnek bildirim][1]

    **Abone ol**' u seçtiğinizde, uygulama seçilen kategorileri etiketlere dönüştürür ve Bildirim Hub 'ından seçilen Etiketler için yeni bir cihaz kaydı ister.

2. Son Haberler olarak gönderilecek bir ileti girin ve ardından **bildirim gönder** düğmesine basın. Alternatif olarak, bildirimler oluşturmak için .NET konsol uygulamasını çalıştırın.

    ![İOS 'da bildirim tercihlerini değiştirme][2]

3. Son haberlere abone olan her cihaz, gönderdiğiniz son haber bildirimlerini alır.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Kategoriler için kayıtlı olan belirli iOS cihazlarına yayın bildirimleri gönderdiniz. Yerelleştirilmiş bildirimleri gönderme hakkında bilgi edinmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Yerelleştirilmiş anında iletme bildirimleri gönderme](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/dn530749(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[get-started]: ios-sdk-get-started.md
[Azure portalı]: https://portal.azure.com
