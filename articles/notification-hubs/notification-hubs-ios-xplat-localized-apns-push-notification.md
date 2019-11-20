---
title: Azure Notification Hubs kullanarak iOS 'a yerelleştirilmiş anında iletme bildirimleri gönderin | Microsoft Docs
description: Azure Notification Hubs kullanarak iOS cihazlarına yerelleştirilmiş bildirimleri gönderme hakkında bilgi edinin.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a8614156be5d516d16aff698b604cf0e661d7311
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385654"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Öğretici: Azure Notification Hubs kullanarak iOS 'a yerelleştirilmiş anında iletme bildirimleri gönderme

> [!div class="op_single_selector"]
> * [Windows Mağazası C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

Bu öğreticide, Azure Notification Hubs [Şablonlar](notification-hubs-templates-cross-platform-push-messages.md) özelliğinin, dil ve cihaz tarafından yerelleştirilmiş son haberler bildirimleri yayınlamak için nasıl kullanılacağı gösterilmektedir. Bu öğreticide, [Son dakika haberleri göndermek için Notification Hubs kullanma]iOS uygulaması ile başlayabiliriz. Tamamlandığında, ilgilendiğiniz kategoriler için kaydolabilirsiniz, bildirimlerin alınacağı bir dil belirtebilir ve yalnızca seçili kategoriler için bu dilin anında iletme bildirimlerini alabilirsiniz.

Bu senaryonun iki bölümü vardır:

* iOS uygulaması, istemci cihazların bir dil belirlemesine ve farklı bir son haber kategorilerine abone olmasına olanak sağlar;
* Arka uç, Azure Notification Hubs **etiket** ve **şablon** özelliklerini kullanarak bildirimleri yayınlar.

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Uygulama kullanıcı arabirimini güncelleştir
> * İOS uygulamasını oluşturma
> * .NET konsol uygulamasından yerelleştirilmiş şablon bildirimleri gönder
> * Cihazdan yerelleştirilmiş şablon bildirimleri gönder

## <a name="overview"></a>Genel Bakış

[Son dakika haberleri göndermek için Notification Hubs kullanma], farklı haber kategorilerine yönelik bildirimlere abone olmak için **Etiketler** kullanan bir uygulama derlediniz. Ancak birçok uygulama, birden çok pazarda hedef ve yerelleştirme gerektirir. Bu, bildirimlerin içeriklerinin kendileri için yerelleştirilmiş olması ve doğru cihaz kümesine teslim edilmesi anlamına gelir. Bu öğreticide, yerelleştirilmiş son haberler bildirimlerini kolayca sunmak için Notification Hubs **şablon** özelliğinin nasıl kullanılacağı gösterilmektedir.

> [!NOTE]
> Yerelleştirilmiş bildirimler göndermenin bir yolu, her bir etiketin birden çok sürümünü oluşturmaktır. Örneğin, Ingilizce, Fransızca ve MANDARIN desteği için dünya haberleri için üç farklı etikete ihtiyacınız vardır: "world_en", "world_fr" ve "world_ch". Daha sonra bu etiketlerin her birine Dünya haberlerinin yerelleştirilmiş bir sürümünü göndermeniz gerekir. Bu konu başlığı altında, etiketleri ve birden çok ileti gönderme gereksinimini ortadan kaldırmak için şablonları kullanırsınız.

Şablonlar, belirli bir cihazın nasıl bildirim alacağını belirtmenin bir yoludur. Şablon, uygulama arka ucunuz tarafından gönderilen iletinin parçası olan özelliklere başvurarak tam yük biçimini belirtir. Bu durumda, desteklenen tüm dilleri içeren bir yerel ayara belirsiz ileti gönderirsiniz:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Ardından cihazların doğru özelliğe başvuran bir şablonla kaydolmanızı sağlayabilirsiniz. Örneğin, aşağıdaki sözdizimini kullanarak Fransızca haber kayıtlarına kaydolmak isteyen bir iOS uygulaması:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Şablonlar hakkında daha fazla bilgi için bkz. [Şablonlar](notification-hubs-templates-cross-platform-push-messages.md) makalesi.

## <a name="prerequisites"></a>Önkoşullar

* [Belirli iOS cihazlarına anında iletme bildirimlerini](notification-hubs-ios-xplat-segmented-apns-push-notification.md) doldurun ve bu öğretici doğrudan bu kodla oluşturulduğundan kodun kullanılabilir olmasını sağlayabilirsiniz.
* Visual Studio 2019 isteğe bağlıdır.

## <a name="update-the-app-user-interface"></a>Uygulama kullanıcı arabirimini güncelleştir

Bu bölümde, şablonları kullanarak yerelleştirilmiş son haberler göndermek üzere [Son dakika haberleri göndermek için Notification Hubs kullanma] konusunda oluşturduğunuz son haberler uygulamasını değiştirirsiniz.

@No__t-0 ' da, üç dile bölünmüş bir denetim ekleyin: Ingilizce, Fransızca ve MANDARIN.

![İOS UI görsel taslağı oluşturma][13]

Ardından, aşağıdaki görüntüde gösterildiği gibi ViewController. h 'nize bir ıbutlet eklediğinizden emin olun:

![Anahtarlar için aykırı izin oluştur][14]

## <a name="build-the-ios-app"></a>İOS uygulamasını oluşturma

1. @No__t-0 ' a, `retrieveLocale` metodunu ekleyin ve aşağıdaki kodda gösterildiği gibi mağaza ve abone olma yöntemlerini değiştirin:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    @No__t-0 ' da, `storeCategoriesAndSubscribe` yöntemini `locale` parametresini ekleyerek ve Kullanıcı varsayılanlarına depolayarak değiştirin:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Ardından, yerel ayarı eklemek için *Subscribe* yöntemini değiştirin:

    ```objc
    - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

        NSString* localeString;
        switch (locale) {
            case 0:
                localeString = @"English";
                break;
            case 1:
                localeString = @"French";
                break;
            case 2:
                localeString = @"Mandarin";
                break;
        }

        NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    @No__t-1 yerine `registerTemplateWithDeviceToken` yöntemini kullanırsınız. Bir şablona kaydoldığınızda, JSON şablonunu ve ayrıca şablon için bir ad belirtmeniz gerekir (uygulama farklı şablonları kaydetmek isteyebilir). Bu haber için bildirimleri aldığınızdan emin olmak istediğiniz şekilde kategorilerinizi Etiketler olarak kaydettiğinizden emin olun.

    Kullanıcı varsayılan ayarlarından yerel ayarı almak için bir yöntem ekleyin:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. @No__t-0 sınıfını değiştirdiğimize göre, `ViewController` ' in yeni `UISegmentControl` ' yi kullandığına emin olmanız gerekir. Şu anda seçili olan yerel ayarı gösterdiğinizden emin olmak için `viewDidLoad` yöntemine aşağıdaki satırı ekleyin:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Ardından, `subscribe` yönteminde, `storeCategoriesAndSubscribe` ' e yapılan çağrı aşağıdaki kodla değiştirin:

    ```objc
    [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                    @"Subscribed!" delegate:nil cancelButtonTitle:
                                    @"OK" otherButtonTitles:nil, nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

3. Son olarak, uygulamanızın başlatıldığı sırada kaydınızı doğru şekilde yenileyebilmeniz için AppDelegate. d dosyanızda `didRegisterForRemoteNotificationsWithDeviceToken` yöntemini güncelleştirmeniz gerekir. Aşağıdaki kodla `subscribe` bildirimleri yöntemine yönelik çağrın adını değiştirin:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>seçim .NET konsol uygulamasından yerelleştirilmiş şablon bildirimleri gönder

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>seçim Cihazdan yerelleştirilmiş şablon bildirimleri gönder

Visual Studio 'ya erişiminiz yoksa veya yalnızca cihazdaki uygulamadan yerelleştirilmiş şablon bildirimlerinin gönderilmesini test etmek istiyorsanız. Yerelleştirilmiş şablon parametrelerini, önceki öğreticide tanımladığınız `SendNotificationRESTAPI` yöntemine ekleyebilirsiniz.

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
    json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
            \"News_English\":\"Breaking %@ News in English : %@\","
            \"News_French\":\"Breaking %@ News in French : %@\","
            \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
            categoryTag, self.notificationMessage.text,
            categoryTag, self.notificationMessage.text,  // insert English localized news here
            categoryTag, self.notificationMessage.text,  // insert French localized news here
            categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

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

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide iOS cihazlarına yerelleştirilmiş bildirimler gönderdiniz. İOS uygulamalarının belirli kullanıcılarına bildirim gönderme hakkında bilgi edinmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Belirli kullanıcılara anında iletme bildirimleri gönderme](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Son dakika haberleri göndermek için Notification Hubs kullanma]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
