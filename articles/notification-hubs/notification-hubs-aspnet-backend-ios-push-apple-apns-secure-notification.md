---
title: İOS için Azure Notification Hubs güvenli gönderimi
description: Azure 'dan bir iOS uygulamasına güvenli anında iletme bildirimleri göndermeyi öğrenin. Kod örnekleri, amaç-C ve C# dilinde yazılmıştır.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a6c85ba017656bd312ddfe3d5f6d98014a3dc89a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90090355"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs güvenli gönderim

> [!div class="op_single_selector"]
> * [Windows Evrensel](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Genel Bakış

Microsoft Azure anında iletme bildirimi desteği, mobil platformlar için hem tüketici hem de kurumsal uygulamalarda anında iletme bildirimlerinin uygulanmasını büyük ölçüde kolaylaştıran, kullanımı kolay, çok platformlu, ölçeği genişletilmiş bir gönderim altyapısına erişmenizi sağlar.

Yasal düzenlemeler veya güvenlik kısıtlamaları nedeniyle, bazen bir uygulama, bildirime standart anında iletme bildirimi altyapısı üzerinden aktarılamayan bir şey eklemek isteyebilir. Bu öğreticide, istemci aygıtı ile uygulama arka ucu arasında güvenli, kimliği doğrulanmış bir bağlantıyla gizli bilgiler göndererek aynı deneyimin nasıl elde edileceğini açıklanmaktadır.

Yüksek düzeyde, akış şu şekildedir:

1. Uygulama arka ucu:
   * Güvenli yükü arka uç veritabanında depolar.
   * Bu bildirimin KIMLIĞINI cihaza gönderir (güvenli bilgi gönderilmez).
2. Bildirim alırken cihazdaki uygulama:
   * Cihaz, güvenli yük isteyen arka uca iletişim kurar.
   * Uygulama, yükü cihazda bir bildirim olarak gösterebilir.

Önceki akışta (ve bu öğreticide), Kullanıcı oturum açtıktan sonra cihazın yerel depolamada bir kimlik doğrulama belirteci depoladığını varsaytık. Bu, cihazın bu belirteci kullanarak bildirimin güvenli yükünü alabilmesi için sorunsuz bir deneyim sağlar. Uygulamanız, kimlik doğrulama belirteçlerini cihazda depolamaz veya bu belirteçlerin kullanım tarihi dolmuşsa, bildirim alındıktan sonra cihaz uygulaması, kullanıcıdan uygulamayı başlatması istenmeden ilgili genel bir bildirim görüntülemelidir. Uygulama daha sonra kullanıcının kimliğini doğrular ve bildirim yükünü gösterir.

Bu güvenli gönderim öğreticisinde güvenli bir anında iletme bildirimi gönderme gösterilmektedir. Öğretici [kullanıcılara bildirme](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) öğreticisini oluşturur, bu nedenle öncelikle bu öğreticideki adımları tamamlamalısınız.

> [!NOTE]
> Bu öğreticide, Bildirim Hub 'ınızı [Azure Notification Hubs kullanarak iOS uygulamalarına anında iletme bildirimleri gönderme](ios-sdk-get-started.md)konusunda açıklandığı gibi oluşturduğunuzu ve yapılandırdığınızı varsaymaktadır.

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>İOS projesini değiştirme

Yalnızca bir bildirimin *kimliğini* göndermek için uygulamanızın arka ucuna göre değiştirildiğimize göre, bu bildirimi işleyecek iOS uygulamanızı değiştirmeniz ve görüntülenecek güvenli iletiyi almak için arka uca geri çağrı yapmanız gerekir.

Bu hedefe ulaşmak için, uygulama arka ucundan güvenli içerik almak üzere mantığı yazmanız gerekir.

1. ' De `AppDelegate.m` , uygulamanın arka uca gönderilen BILDIRIM kimliğini işleyeceği şekilde sessiz bildirimler için kaydoldığından emin olun. `UIRemoteNotificationTypeNewsstandContentAvailability`Didsonlandırhlaunchingwithoptions içindeki seçeneği ekleyin:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. `AppDelegate.m`Aşağıdaki bildirimle en üstteki uygulama ekleme bölümünde:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Ardından, `{back-end endpoint}` daha önce elde edilen arka ucunuzla birlikte yer tutucuyu yerine, uygulama bölümüne aşağıdaki kodu ekleyin:

    ```objc
    NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    {
        // check if authenticated
        ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
        NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                completion([json objectForKey:@"Payload"], nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }
    ```

    Bu yöntem, paylaşılan tercihlerinde depolanan kimlik bilgilerini kullanarak bildirim içeriğini almak için uygulamanızın arka uca çağrı yapın.

4. Şimdi gelen bildirimi işleyin ve görüntülenecek içeriği almak için yukarıdaki yöntemi kullanın. İlk olarak, bir anında iletme bildirimi alırken iOS uygulamanızı arka planda çalışacak şekilde etkinleştirin. **Xcode**'da sol panelde uygulama projenizi seçin ve ardından merkezi bölmedeki **hedefler** bölümünde ana uygulama hedefi ' ne tıklayın.
5. Sonra, orta bölmenizi en üstteki **yetenekler** sekmesine tıklayın ve **uzak bildirimler** kutusunu işaretleyin.

    ![Seçili uygulama projesi ve Yetenekler sekmesi açık olan XCode ekran görüntüsü. Uzaktan bildirimler onay kutusu seçilidir.][IOS1]

6. İçinde `AppDelegate.m` anında iletme bildirimlerini işlemek için aşağıdaki yöntemi ekleyin:

    ```objc
    -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
        NSLog(@"%@", userInfo);

        [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
            if (!error) {
                // show local notification
                UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                localNotification.alertBody = payload;
                localNotification.timeZone = [NSTimeZone defaultTimeZone];
                [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                completionHandler(UIBackgroundFetchResultNewData);
            } else {
                completionHandler(UIBackgroundFetchResultFailed);
            }
        }];

    }
    ```

    Eksik kimlik doğrulama üstbilgisi özelliği veya arka uç tarafından ret durumlarının işlenmesi tercih edilir. Bu durumların belirli işleme, genellikle hedef Kullanıcı deneyiminize bağlıdır. Bir seçenek, kullanıcının gerçek bildirimi almak için kimlik doğrulaması yapması için bir genel istem ile bir bildirim görüntülemektir.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı çalıştırmak için şunları yapın:

1. XCode 'da uygulamayı fiziksel bir iOS cihazında çalıştırın (anında iletme bildirimleri benzeticide çalışmaz).
2. İOS uygulama kullanıcı arabiriminde, bir Kullanıcı adı ve parola girin. Bunlar herhangi bir dize olabilir, ancak aynı değer olmalıdır.
3. İOS uygulama kullanıcı arabiriminde **oturum aç**' a tıklayın. Sonra **gönderme gönder**' e tıklayın. Bildirim merkezinizde güvenli bildirimin görüntülendiğini görmeniz gerekir.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
