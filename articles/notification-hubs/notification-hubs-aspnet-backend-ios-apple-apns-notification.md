---
title: Azure Notification Hubs kullanarak belirli kullanıcılara anında iletme bildirimleri gönderin | Microsoft Docs
description: Azure Notification Hubs kullanarak belirli iOS kullanıcılarına anında iletme bildirimleri göndermeyi öğrenin.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 167c666c536ee33531fd069dbd1edb530331a9f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016972"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Öğretici: Azure Notification Hubs kullanarak belirli kullanıcılara anında iletme bildirimleri gönderme

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Bu öğreticide, belirli bir cihazdaki belirli bir uygulama kullanıcısına anında iletme bildirimleri göndermek için Azure Bildirim Hubs’ı nasıl kullanacağınız gösterilmektedir. ASP.NET bir WebAPI arka ucu, istemcilerin kimliğini doğrulamak ve [uygulama arka ucunuzun kayıt](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)kılavuzunda gösterildiği gibi bildirimler oluşturmak için kullanılır.

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * WebAPI projesi oluşturma
> * WebAPI arka ucunda istemcilerin kimliğini doğrulama
> * WebAPI arka ucunu kullanarak bildirimlere kaydolma
> * WebAPI arka ucundan bildirim gönderme
> * Yeni WebAPI arka ucunu yayımlama
> * İOS uygulamanızı değiştirme
> * Uygulamayı test edin

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, Bildirim Hub 'ınızı [Azure Notification Hubs kullanarak iOS uygulamalarına anında iletme bildirimleri gönderme](ios-sdk-get-started.md)konusunda açıklandığı gibi oluşturduğunuzu ve yapılandırdığınızı varsaymaktadır. Bu öğretici Ayrıca [güvenli gönderim (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) öğreticisinin önkoşuludur.
Arka uç hizmetiniz olarak Mobile Apps kullanmak istiyorsanız, [Push Ile çalışmaya başlama Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)bakın.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>İOS uygulamanızı değiştirme

1. [Azure Notification Hubs öğreticisini kullanarak iOS uygulamalarına anında iletme bildirimleri göndermek için](ios-sdk-get-started.md) oluşturduğunuz tek sayfalı görünüm uygulamasını açın.

   > [!NOTE]
   > Bu bölümde, projenizin boş bir kuruluş adıyla yapılandırıldığı varsayılır. Aksi takdirde, kuruluşunuzun adını tüm sınıf adlarına ekleyin.

2. `Main.storyboard`Dosyasında, nesne kitaplığından ekran görüntüsünde gösterilen bileşenleri ekleyin.

    ![Xcode Interface Builder 'da görsel taslağı Düzenle][1]

   * **Kullanıcı adı**: yer tutucu metnine sahip bir UITextField, gönder sonuçlarının hemen altındaki *Kullanıcı adı*' nı girin ve sol ve sağ kenar boşluklarına ve bu sonuçları gönder etiketinin altına göre kısıtlanmıştır.
   * **Parola**: yer tutucu metin Içeren bir UITextField, Kullanıcı adı metin alanının hemen altındaki *parolayı girin* ve sol ve sağ kenar boşluklarıyla ve Kullanıcı adı metin alanının altına sınırlanır. *Dönüş anahtarı* altında, öznitelik denetçisinde **güvenli metin girişi** seçeneğini işaretleyin.
   * **Oturum aç**: parola metin alanının hemen altındaki etiketli bir uıbutton ve *Denetim-Içerik* altındaki öznitelikler denetçisinde **etkin** seçeneğinin işaretini kaldırın
   * **WNS**: Bu, hub 'da ayarlandıysa bildirim Windows bildirim hizmeti gönderilmesini etkinleştirmek için etiketleyip geçiş yapın. Bkz. [Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) Başlangıç Öğreticisi.
   * **GCM**: hub 'da ayarlandıysa Google Cloud Messaging bildirimin gönderilmesini sağlamak için etiket ve geçiş yapın. Bkz. [Android Başlarken](notification-hubs-android-push-notification-google-gcm-get-started.md) öğreticisi.
   * **APNs**: bildirimin Apple platform bildirim hizmetine gönderilmesini sağlamak için etiket ve anahtar.
   * **Alıcı Kullanıcı adı:** yalnızca GCM etiketinin hemen altında yer tutucu metin, *alıcı Kullanıcı adı etiketi* olan bir uitextfield ve sol ve sağ kenar boşluklarıyla ve GCM etiketinin altında kısıtlanmış.

     [Azure Notification Hubs öğreticisi kullanılarak iOS uygulamalarına anında iletme bildirimleri gönderme](ios-sdk-get-started.md) hakkında bazı bileşenler eklenmiştir.

3. Görünümdeki bileşenlerden **CTRL** 'e sürükleyin `ViewController.h` ve bu yeni aykırı değerleri ekleyin:

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. İçinde `ViewController.h` , `#define` içeri aktarma deyimlerinizin ardından aşağıdakini ekleyin. `<Your backend endpoint>`Yer tutucusunu, önceki bölümde uygulamanızın arka ucunu dağıtmak için kullandığınız hedef URL ile değiştirin. Örneğin `http://your_backend.azurewebsites.net` :

    ```objc
    #define BACKEND_ENDPOINT @"<Your backend endpoint>"
    ```

5. Projenizde, `RegisterClient` oluşturduğunuz ASP.net arka ucu ile arabirim adlı yeni bir Cocoa Touch sınıfı oluşturun. Öğesinden devralan sınıfı oluşturun `NSObject` . Ardından aşağıdaki kodu öğesine ekleyin `RegisterClient.h` :

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. İçinde, `RegisterClient.m` `@interface` bölümünü güncelleştirin:

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. `@implementation`RegisterClient. d içindeki bölümü aşağıdaki kodla değiştirin:

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    Bu kod, uygulama arka ucunuza REST çağrıları gerçekleştirmek üzere NSURLSession kullanarak [uygulama arka Ucinizden kayıt yaptırarak](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) açıklanan mantığı uygular ve NSUserDefaults, Bildirim Hub 'ı tarafından döndürülen RegistrationId 'yi yerel olarak depolar.

    Bu sınıf, `authorizationHeader` düzgün çalışması için özelliğinin ayarlanmasını gerektirir. Bu özellik `ViewController` , oturum açma işleminden sonra sınıf tarafından ayarlanır.

8. İçinde `ViewController.h` `#import` için bir ifade ekleyin `RegisterClient.h` . Ardından, cihaz belirteci için bir bildirim ve bölümündeki bir örneğe başvuru ekleyin `RegisterClient` `@interface` :

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. ViewController. d ' de, bölümüne bir özel yöntem bildirimi ekleyin `@interface` :

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Aşağıdaki kod parçacığı güvenli bir kimlik doğrulama düzeni değildir,,    `createAndSetAuthenticationHeaderWithUsername:AndPassword:` kayıt istemci sınıfı (örneğin OAuth, Active Directory) tarafından tüketilen bir kimlik doğrulama belirteci üreten özel kimlik doğrulama mekanizmasıyla birlikte uygulamanızı yerine geçirmeniz gerekir.

10. Ardından bölümüne, `@implementation` `ViewController.m` cihaz belirtecinin ve kimlik doğrulama üst bilgisinin ayarlanmasına yönelik uygulamayı ekleyen aşağıdaki kodu ekleyin.

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    Cihaz belirtecinin ayarlanması, **oturum açma** düğmesine nasıl izin verdiğine dikkat edin. Bunun nedeni, oturum açma eyleminin bir parçası olduğu için, görünüm denetleyicisi uygulama arka ucuna anında iletme bildirimleri kaydeder. Cihaz belirteci düzgün şekilde kuruluncaya kadar **oturum açma** eyleminin erişilebilir olmasını istemezsiniz. Daha önce, ikincisinde daha önce olduğu sürece, anında iletme kaydından oturum açma bilgilerini ayrışırsınız.

11. ViewController. d ' de, **oturum açma** düğinizde eylem yöntemini ve ASP.net arka ucunu kullanarak bildirim iletisini göndermek için bir yöntemi uygulamak üzere aşağıdaki kod parçacıklarını kullanın.

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. ASP.NET arka ucunu kullanmak ve bir anahtar tarafından etkinleştirilen tüm PNS 'ye göndermek için **bildirim gönder** düğmesine yönelik eylemi güncelleştirin.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. `ViewDidLoad`İşlevinde, örneği oluşturmak için aşağıdakini ekleyin `RegisterClient` ve metin alanlarınız için temsilciyi ayarlayın.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Şimdi `AppDelegate.m` , yönteminin tüm içeriğini kaldırın `application:didRegisterForPushNotificationWithDeviceToken:` ve aşağıdaki ile değiştirin (görünüm denetleyicisinin APNs 'dan alınan en son cihaz belirtecini içerdiğinden emin olmak için):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Son olarak `AppDelegate.m` , aşağıdaki yönteme sahip olduğunuzdan emin olun:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Uygulamayı test edin

1. XCode 'da uygulamayı fiziksel bir iOS cihazında çalıştırın (anında iletme bildirimleri Benzetici içinde çalışmaz).
2. İOS uygulama kullanıcı arabiriminde, hem Kullanıcı adı hem de parola için aynı değeri girin. Sonra **oturum aç**' a tıklayın.

    ![iOS test uygulaması][2]

3. Kayıt başarısını bildiren bir açılır pencere görmeniz gerekir. **Tamam**'a tıklayın.

    ![iOS test bildirimi görüntülendi][3]

4. **Alıcı Kullanıcı adı etiketi* metin alanına, başka bir cihazdan kayıtla kullanılan Kullanıcı adı etiketini girin.
5. Bir bildirim iletisi girin ve **bildirim gönder**' e tıklayın. Yalnızca alıcı Kullanıcı adı etiketiyle kayıt olan cihazlar bildirim iletisini alır. Yalnızca bu kullanıcılara gönderilir.

    ![iOS test etiketli bildirimi][4]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kayıtlarıyla ilişkili etiketleri olan belirli kullanıcılara anında iletme bildirimleri göndermeyi öğrendiniz. Konum tabanlı anında iletme bildirimleri göndermeyi öğrenmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Konum temelli anında iletme bildirimleri gönderme](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
