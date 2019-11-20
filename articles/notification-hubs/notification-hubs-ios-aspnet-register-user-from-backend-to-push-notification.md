---
title: Web API 'sini kullanarak geçerli kullanıcıyı anında iletme bildirimleri için kaydetme | Microsoft Docs
description: ASP.NET Web API 'SI tarafından kayıt gerçekleştirildiğinde Azure Notification Hubs ile bir iOS uygulamasında anında iletme bildirimi kaydı isteme hakkında bilgi edinin.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 3fec04a1a45f8b154e27a1e5303e44111f4cb421
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211879"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>ASP.NET kullanarak geçerli kullanıcıyı anında iletme bildirimleri için Kaydet

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Genel Bakış

Bu konuda, ASP.NET Web API 'SI tarafından kayıt gerçekleştirildiğinde Azure Notification Hubs anında iletme bildirimi kaydı isteği gösterilmektedir. Bu konu, [kullanıcılara Notification Hubs bildirme]öğreticisini genişletir. Kimliği doğrulanmış mobil hizmeti oluşturmak için bu öğreticide gereken adımları zaten tamamlamış olmanız gerekir. Kullanıcıları bilgilendir senaryosu hakkında daha fazla bilgi için bkz. [Kullanıcılara Notification Hubs bildirme].

## <a name="update-your-app"></a>Uygulamanızı güncelleştirme

1. MainStoryboard_iPhone. görsel taslağınızdan, nesne kitaplığından aşağıdaki bileşenleri ekleyin:

   * **Etiket**: "Notification Hubs ile kullanıcıya gönderim"
   * **Etiket**: InstallationID
   * **Etiket**: Kullanıcısını
   * **Metin alanı**: Kullanıcısını
   * **Etiket**: Parolayı
   * **Metin alanı**: Parolayı
   * **Düğme**: LOGIN

     Bu noktada, film şeridi aşağıdaki gibi görünür:

     ![][0]

2. Yardımcı düzenleyicide, tüm anahtarlamalı denetimler için bir dış grup oluşturun ve bunları çağırın, metin alanlarını görünüm denetleyicisi (temsilci) ile bağlayın ve **oturum açma** düğmesi Için bir **eylem** oluşturun.

    ![][1]

    BreakingNewsViewController. h dosyanız artık aşağıdaki kodu içermelidir:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Adlı `DeviceInfo`bir sınıf oluşturun ve aşağıdaki kodu DeviceInfo. h dosyasının interface bölümüne kopyalayın:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Aşağıdaki kodu DeviceInfo. d dosyasının uygulama bölümüne kopyalayın:

    ```objc
    @synthesize installationId = _installationId;

    - (id)init {
        if (!(self = [super init]))
            return nil;

        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
        if(!_installationId) {
            CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
            _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
            CFRelease(newUUID);

            //store the install ID so we don't generate a new one next time
            [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
            [defaults synchronize];
        }

        return self;
    }

    - (NSString*)getDeviceTokenInHex {
        const unsigned *tokenBytes = [[self deviceToken] bytes];
        NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
        return hexToken;
    }
    ```

5. PushToUserAppDelegate. h içinde, aşağıdaki özelliği Singleton olarak ekleyin:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. Pushtouserappdelegate. d içindeki yöntemindeaşağıdakikoduekleyin:`didFinishLaunchingWithOptions`

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    İlk satır `DeviceInfo` tek başlatılır. İkinci satır, [Notification Hubs kullanmaya başlayın] öğreticisini zaten tamamladıysanız zaten mevcut olan anında iletme bildirimleri için kaydı başlatır.
7. Pushtouserappdelegate. d içinde, yöntemini `didRegisterForRemoteNotificationsWithDeviceToken` appdelegate içinde uygulayın ve aşağıdaki kodu ekleyin:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Bu, isteğin cihaz belirtecini ayarlar.

   > [!NOTE]
   > Bu noktada, bu yöntemde başka bir kod olmamalıdır. [Notification Hubs ile çalışmaya başlama](notification-hubs-ios-apple-push-notification-apns-get-started.md) öğreticisini tamamladıktan sonra `registerNativeWithDeviceToken` eklenen metoda zaten bir çağrı varsa, bu çağrıyı kullanıma almanız veya kaldırmanız gerekir.

8. `PushToUserAppDelegate.m` Dosyasında aşağıdaki işleyici yöntemini ekleyin:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Bu yöntem, uygulamanız çalışırken bildirimler aldığında Kullanıcı arabiriminde bir uyarı görüntüler.

9. `PushToUserViewController.m` Dosyasını açın ve aşağıdaki uygulamada klavyeyi döndürün:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. Dosyasındaki yönteminde, etiketi`installationId` aşağıdaki gibi başlatın: `viewDidLoad` `PushToUserViewController.m`

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Aşağıdaki özellikleri arabirimine `PushToUserViewController.m`ekleyin:

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Ardından, aşağıdaki uygulamayı ekleyin:

    ```objc
    - (NSOperationQueue *)downloadQueue {
        if (!_downloadQueue) {
            _downloadQueue = [[NSOperationQueue alloc] init];
            _downloadQueue.name = @"Download Queue";
            _downloadQueue.maxConcurrentOperationCount = 1;
        }
        return _downloadQueue;
    }

    // base64 encoding
    - (NSString*)base64forData:(NSData*)theData
    {
        const uint8_t* input = (const uint8_t*)[theData bytes];
        NSInteger length = [theData length];

        static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

        NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
        uint8_t* output = (uint8_t*)data.mutableBytes;

        NSInteger i;
        for (i=0; i < length; i += 3) {
            NSInteger value = 0;
            NSInteger j;
            for (j = i; j < (i + 3); j++) {
                value <<= 8;

                if (j < length) {
                    value |= (0xFF & input[j]);
                }
            }

            NSInteger theIndex = (i / 3) * 4;
            output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
            output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
            output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
            output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
        }

        return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
    }
    ```

13. Aşağıdaki kodu `login` Xcode tarafından oluşturulan işleyici yöntemine kopyalayın:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

    // build JSON
    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

    // build auth string
    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
    [request setHTTPMethod:@"POST"];
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

    // connect with POST
    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
        // add UIAlert depending on response.
        if (error != nil) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
            if ([httpResponse statusCode] == 200) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
            }
        } else {
            NSLog(@"error: %@", error);
        }
    }];
    ```

    Bu yöntem, anında iletme bildirimleri için bir yükleme KIMLIĞI ve kanalı alır ve cihaz türü ile birlikte Notification Hubs bir kayıt oluşturan kimliği doğrulanmış Web API yöntemine gönderilir. Bu Web API 'SI [Kullanıcılara Notification Hubs bildirme]bölümünde tanımlanmıştır.

Artık istemci uygulaması güncelleştirildiğinden, [Kullanıcılara Notification Hubs bildirme] ' e geri dönün ve mobil hizmeti Notification Hubs kullanarak bildirim gönderecek şekilde güncelleştirin.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Kullanıcılara Notification Hubs bildirme]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notification Hubs kullanmaya başlayın]: notification-hubs-ios-apple-push-notification-apns-get-started.md
