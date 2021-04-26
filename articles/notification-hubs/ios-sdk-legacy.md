---
title: Azure Notification Hubs ve iOS SDK sürümü 2.0.4 kullanarak iOS 'a anında iletme bildirimleri gönderme
description: Bu öğreticide, iOS cihazlarına (sürüm 2.0.4) anında iletme bildirimleri göndermek için Azure Notification Hubs ve Apple Anında Iletilen bildirim hizmetini nasıl kullanacağınızı öğreneceksiniz.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: a1017f7c7aa0ede9e3c91acd3dba510618e15fb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100628475"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-legacy-api"></a>Öğretici: Azure Notification Hubs (eski API) kullanarak iOS uygulamalarına anında iletme bildirimleri gönderme

Bu öğreticide, Apple eski API 'Leri için Azure Notification Hubs SDK 'sını kullanarak bir iOS uygulamasına anında iletme bildirimleri göndermek için Azure Notification Hubs 'nin nasıl kullanılacağı gösterilmektedir.

Bu öğreticide aşağıdaki adımlar yer almaktadır:

- Örnek bir iOS uygulaması oluşturun.
- İOS uygulamanızı Azure Notification Hubs bağlayın.
- Test anında iletme bildirimleri gönderin.
- Uygulamanızın bildirimleri aldığını doğrulayın.

Bu öğreticinin tüm kodunu [GitHub 'dan](https://github.com/Azure/azure-notificationhubs-ios/tree/main/SampleNHAppLegacyObjC)indirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki önkoşullara sahip olmanız gerekir:

- Anahtarınıza yüklenmiş geçerli bir geliştirici sertifikasıyla birlikte [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)çalıştıran bir Mac.
- İOS sürüm 10 veya üstünü çalıştıran bir iPhone veya iPad.
- Fiziksel cihazınız [Apple portalında](https://developer.apple.com/)kayıtlı ve sertifikanız ile ilişkili.

Devam etmeden önce, Bildirim Hub 'ınızdaki anında iletme kimlik bilgilerini ayarlamak ve yapılandırmak için [iOS uygulamaları Için Azure Notification Hubs](ios-sdk-get-started.md)kullanmaya başlama hakkında önceki öğreticiye gitdiğinizden emin olun. İOS geliştirmeyle ilgili önceki bir deneyim olmasa bile, bu adımları takip edebilmelisiniz.

> [!NOTE]
> Anında iletme bildirimlerinin yapılandırma gereksinimleri nedeniyle, iOS öykünücüsü yerine bir fiziksel iOS cihazında (iPhone veya iPad) anında iletme bildirimleri dağıtmanız ve test etmeniz gerekir.

## <a name="connect-your-ios-app-to-notification-hubs"></a>iOS uygulamanızı Notification Hubs'a bağlama

1. Xcode'da yeni bir iOS projesi oluşturun ve **Single View Application** (Tek Görünüm Uygulaması) şablonunu seçin.

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Şablon seç":::

2. Yeni projeniz için seçenekleri ayarlarken, Apple Developer portalında paket kimliğini açarken kullandığınız **Product Name** (Ürün Adı) ve **Organization Identifier**'nı (Kuruluş Tanımlayıcısı) kullandığınızdan emin olun.

3. Proje Gezgini altında, **hedefler** altında proje adınızı seçin, sonra **imzalama & özellikleri** sekmesini seçin. Apple geliştirici hesabınız için uygun **ekibi** seçtiğinizden emin olun. XCode, paket tanımlayıcınızı temel alarak önceden oluşturduğunuz Sağlama Profilini otomatik olarak aşağı çekmelidir.

   Xcode'da oluşturduğunuz yeni hazırlama profilini göremiyorsanız imzalama kimliğiniz için profilleri yenilemeyi deneyin. Menü çubuğunda **Xcode**'a tıklayın, **Preferences**'a (Tercihler) tıklayın, **Account** (Hesap) sekmesine tıklayın, **View Details** (Ayrıntıları Görüntüle) düğmesine tıklayın, imzalama kimliğinize tıklayın ve ardından sağ alt köşedeki yenile düğmesine tıklayın.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Ayrıntıları görüntüle":::

4. **İmzalama & özellikleri** sekmesinde **+ yetenek**' ı seçin. **Anında Iletme bildirimleri** ' ne çift tıklayarak etkinleştirin.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Özellik":::

5. Azure Notification Hubs SDK modüllerini ekleyin.

   [Cocoapods](https://cocoapods.org/) kullanarak veya ikili dosyaları projenize el Ile ekleyerek Azure Notification Hubs SDK 'sını uygulamanız ile tümleştirebilirsiniz.

   - Cocoapods aracılığıyla tümleştirme: uygulamanıza Azure Notification Hubs SDK 'yı dahil etmek için aşağıdaki bağımlılıkları Pod dosyanıza ekleyin:

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Yeni tanımlanan Pod 'nizi yüklemek ve. xcworkspace Dosyanızı açmak için pod Install 'ı çalıştırın.

         Pod yüklemesi çalıştırılırken **azurenocertificate 'lar-iOS için bir belirtim bulamıyor** gibi bir hata görürseniz, `pod repo update` Cocoapods deposundan en son Pod 'yi almak için komutunu çalıştırın ve pod Install komutunu çalıştırın.

   - Carthage aracılığıyla tümleştirme: uygulamanıza Azure Notification Hubs SDK 'yı eklemek için aşağıdaki bağımlılıkları Cartdosyanıza ekleyin:

      ```ruby
      github "Azure/azure-notificationhubs-ios"
      ```

      - Sonra, derleme bağımlılıklarını güncelleştir:

      ```shell
      $ carthage update
      ```

      Carthage kullanma hakkında daha fazla bilgi için bkz. [Carthage GitHub deposu](https://github.com/Carthage/Carthage).

   - İkili dosyaları projenize kopyalayarak tümleştirme: ikili dosyaları projenize kopyalayarak aşağıdaki gibi tümleştirebilirsiniz:

        - Zip dosyası olarak sunulan [Azure NOTIFICATION HUBS SDK](https://github.com/Azure/azure-notificationhubs-android/releases) çerçevesini indirin ve sıkıştırmayı açın.

        - Xcode'da projenize sağ tıklayın ve **WindowsAzureMessaging.framework** klasörünü Xcode projenize eklemek için **Add Files to** (Dosyaları Şuraya Ekle) seçeneğine tıklayın. **Options** (Seçenekler) seçeneğine tıklayıp **Copy items if needed** (Gerekirse verileri kopyala) öğesinin seçili olduğundan emin olduktan sonra **Add** (Ekle) öğesine tıklayın.

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Çerçeve Ekle":::

6. Azure Notification Hubs 'a bağlanma bilgilerini uygun `<string></string>` bölümde ekleyin.  `--HUB-NAME--` `--CONNECTION-STRING--` Daha önce portaldan elde ettiğiniz gibi, dize değişmez yer tutucuları ve hub adı ve **Defaultlistensharedaccesssignature** ile değiştirin:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>HUB_NAME</key>
        <string>--HUB-NAME--</string>
        <key>CONNECTION_STRING</key>
        <string>--CONNECTION-STRING--</string>
    </dict>
    </plist>
    ```

7. Project **Appdelegate. h** dosyanızı açın ve içeriğini şu kodla değiştirin:

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,   UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end
    ```

8. Proje **Appdelegate. d** dosyasında aşağıdaki `import` deyimleri ekleyin:

    ```objc
    #import "NotificationDetailViewController.h"
    ```

12. **Appdelegate. d** dosyanızda, `didFinishLaunchingWithOptions` iOS sürümünüze bağlı olarak aşağıdaki kod satırını yöntemine ekleyin. Bu kod, cihaz tanıtıcınızı APNs'ye kaydeder:

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. Aynı **Appdelegate. d** dosyasında aşağıdaki kodla sonraki tüm kodu değiştirin `didFinishLaunchingWithOptions` :

    ```objc
    // Tells the app that a remote notification arrived that indicates there is data to be fetched.

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
        NSLog(@"Received remote (silent) notification");
        [self logNotificationDetails:userInfo];
        
        //
        // Let the system know the silent notification has been processed.
        //
        completionHandler(UIBackgroundFetchResultNoData);
    }

    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).

    - (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
        NSMutableSet *tags = [[NSMutableSet alloc] init];
        
        // Load and parse stored tags
        NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
        if (unparsedTags.length > 0) {
            NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];
            [tags addObjectsFromArray:tagsArray];
        }
        
        // Register the device with the Notification Hub.
        // If the device has not already been registered, this will create the registration.
        // If the device has already been registered, this will update the existing registration.
        //
        SBNotificationHub* hub = [self getNotificationHub];
        [hub registerNativeWithDeviceToken:deviceToken tags:tags completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            } else {
                [self showAlert:@"Registered" withTitle:@"Registration Status"];
            }
        }];
    }

    // UNUserNotificationCenterDelegate methods
    //
    // Asks the delegate how to handle a notification that arrived while the app was running in the  foreground.

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
        NSLog(@"Received notification while the application is in the foreground");
        
        // The system calls this delegate method when the app is in the foreground. This allows the app to handle the notification
        // itself (and potentially modify the default system behavior).
        
        // Handle the notification by displaying custom UI.
        //
        [self showNotification:notification.request.content.userInfo];
        
        // Use 'options' to specify which default behaviors to enable.
        // - UNNotificationPresentationOptionsBadge: Apply the notification's badge value to the app’s icon.
        // - UNNotificationPresentationOptionList: Show in the Notification Center
        // - UNNotificationPresentationOptionsSound: Play the sound associated with the notification.
        //
        completionHandler(UNNotificationPresentationOptionsBadge | UNNotificationPresentationOptionsSound);
    }

    // Asks the delegate to process the user's response to a delivered notification.
    //

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
        NSLog(@"Received notification while the application is in the background");
        
        // The system calls this delegate method when the user taps or responds to the system notification.
        
        // Handle the notification response by displaying custom UI
        //
        [self showNotification:response.notification.request.content.userInfo];
        
        // Let the system know the response has been processed.
        //
        completionHandler();
    }

    // App logic and helpers

    - (SBNotificationHub *)getNotificationHub {
        NSString *path = [[NSBundle mainBundle] pathForResource:@"DevSettings" ofType:@"plist"];
        NSDictionary *configValues = [NSDictionary dictionaryWithContentsOfFile:path];
        
        NSString *connectionString = [configValues objectForKey:@"CONNECTION_STRING"];
        NSString *hubName = [configValues objectForKey:@"HUB_NAME"];
        
        return [[SBNotificationHub alloc] initWithConnectionString:connectionString notificationHubPath:hubName];
    }

    - (void)handleRegister {
        UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
        
        UNAuthorizationOptions options =  UNAuthorizationOptionAlert | UNAuthorizationOptionSound | UNAuthorizationOptionBadge;
        [center requestAuthorizationWithOptions:(options) completionHandler:^(BOOL granted, NSError * _Nullable error) {
            if (error != nil) {
                NSLog(@"Error requesting for authorization: %@", error);
            }

            if (granted) {
                NSLog(@"Authorization granted");
            }
        }];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }

    - (void)handleUnregister {
        //
        // Unregister the device with the Notification Hub.
        //
        SBNotificationHub *hub = [self getNotificationHub];
        [hub unregisterNativeWithCompletion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error unregistering for push: %@", error);
            } else {
                [self showAlert:@"Unregistered" withTitle:@"Registration Status"];
            }
        }];
    }

    - (void)logNotificationDetails:(NSDictionary *)userInfo {
        if (userInfo != nil) {
            UIApplicationState state = [UIApplication sharedApplication].applicationState;
            BOOL background = state != UIApplicationStateActive;
            NSLog(@"Received %@notification: \n%@", background ? @"(background) " : @"", userInfo);
        }
    }

    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
        if (title == nil) {
            title = @"Alert";
        }

        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
        [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
        [self logNotificationDetails:userInfo];
        
        NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    Bu kod, **sabitler. h** içinde belirttiğiniz bağlantı bilgilerini kullanarak Bildirim Hub 'ına bağlanır. Daha sonra, hub 'ın bildirim gönderebilmesi için cihaz belirtecini Bildirim Hub 'ına verir.

### <a name="create-notificationdetailviewcontroller-header-file"></a>NotificationDetailViewController üst bilgi dosyası oluştur

1. Önceki yönergelere benzer şekilde, **Notificationdetailviewcontroller. h** adlı başka bir üst bilgi dosyası ekleyin. Yeni üstbilgi dosyasının içeriğini aşağıdaki kodla değiştirin:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface NotificationDetailViewController : UIViewController

   @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
   @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
   @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

   @property (strong, nonatomic) NSDictionary *userInfo;

   - (id)initWithUserInfo:(NSDictionary *)userInfo;

   @end

   NS_ASSUME_NONNULL_END
   ```

2. **Notificationdetailviewcontroller. d** uygulama dosyasını ekleyin. Dosya içeriğini, UIViewController yöntemlerini uygulayan aşağıdaki kodla değiştirin:

   ```objc
   #import "NotificationDetailViewController.h"

   @interface NotificationDetailViewController ()

   @end

   @implementation NotificationDetailViewController

   - (id)initWithUserInfo:(NSDictionary *)userInfo {
        self = [super initWithNibName:@"NotificationDetail" bundle:nil];
        if (self) {
            _userInfo = userInfo;
        }
        return self;
   }

   - (void)viewDidLayoutSubviews {
        [self.titleLabel sizeToFit];
        [self.bodyLabel sizeToFit];
   }

   - (void)viewDidLoad {
        [super viewDidLoad];
        
        NSString *title = nil;
        NSString *body = nil;
        
        NSDictionary *aps = [_userInfo valueForKey:@"aps"];
        NSObject *alertObject = [aps valueForKey:@"alert"];
        if (alertObject != nil) {
            if ([alertObject isKindOfClass:[NSDictionary class]]) {
                NSDictionary *alertDict = (NSDictionary *)alertObject;
                title = [alertDict valueForKey:@"title"];
                body = [alertObject valueForKey:@"body"];
            } else if ([alertObject isKindOfClass:[NSString class]]) {
                body = (NSString *)alertObject;
            } else {
                NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
            }
        }
        
        if (title == nil) {
            title = @"<unset>";
        }
        
        if (body == nil) {
            body = @"<unset>";
        }
        
        self.titleLabel.text = title;
        self.bodyLabel.text = body;
   }

   - (IBAction)handleDismiss:(id)sender {
        [self dismissViewControllerAnimated:YES completion:nil];
   }

   @end
   ```

### <a name="viewcontroller"></a>ViewController

1. Project **ViewController. h** dosyasında aşağıdaki `import` deyimleri ekleyin:

   ```objc
   #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
   #import <UserNotifications/UserNotifications.h>
   ```

2. Ayrıca, **ViewController. h**' de, bildirimden sonra aşağıdaki özellik bildirimlerini ekleyin `@interface` :

   ```objc
   @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
   @property (strong, nonatomic) IBOutlet UIButton *registerButton;
   @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
   ```

3. Projenin **ViewController. d** uygulama dosyasında, dosyanın içeriğini aşağıdaki kodla değiştirin:

   ```objc
   #import "ViewController.h"
   #import "AppDelegate.h"

    static NSString *const kNHUserDefaultTags = @"notification_tags";

   @interface ViewController ()

   @end

   @implementation ViewController

   // UIViewController methods

   - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
        // Simple method to dismiss keyboard when user taps outside of the UITextField.
        [self.view endEditing:YES];
   }

   - (void)viewDidLoad {
        [super viewDidLoad];
        
        // Load raw tags text from storage and initialize the text field
        self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:kNHUserDefaultTags];
   }

   - (IBAction)handleRegister:(id)sender {
        // Save raw tags text in storage
        [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:kNHUserDefaultTags];
        
        // Delegate processing the register action to the app delegate.
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
   }

   - (IBAction)handleUnregister:(id)sender {
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
   }

   @end
   ```

4. Herhangi bir hata olmadığını doğrulamak için cihazınızda uygulamayı derleyin ve çalıştırın.

## <a name="send-test-push-notifications"></a>Test amaçlı anında iletme bildirimleri gönderme

[Azure Portal](https://portal.azure.com/)**Test Gönderimi** seçeneğini kullanarak uygulamanızda bildirim alma testi gerçekleştirebilirsiniz. Bu, cihazınıza test amaçlı anında iletme bildirimi gönderir.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Testi gönder":::

Anında iletme bildirimleri normal olarak, uyumlu bir kitaplık kullanılarak Mobile Apps veya ASP.NET gibi bir arka uç hizmetine gönderilir. Arka uçta bir kitaplık yoksa, bildirim iletilerini göndermek için doğrudan REST API de kullanabilirsiniz.

Bildirim göndermek için gözden geçirmek isteyebileceğiniz diğer bazı öğreticilerin listesi aşağıda verilmiştir:

- Azure Mobile Apps: Notification Hubs ile tümleştirilmiş Mobile Apps arka uçta bildirimlerin nasıl gönderileceği hakkında bir örnek Için bkz. [IOS uygulamanıza anında Iletme bildirimleri ekleme](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).
- ASP.NET: [kullanıcılara anında iletme bildirimleri göndermek için Notification Hubs kullanın](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- Azure Notification Hubs Java SDK'sı: Java'dan bildirim göndermek için bkz. [Java'dan Notification Hubs kullanma](notification-hubs-java-push-notification-tutorial.md). Android Geliştirmesi için Eclipse'te test edilmiştir.
- PHP: [PHP'ye ait Notification Hubs nasıl kullanılır](notification-hubs-php-push-notification-tutorial.md).

## <a name="verify-that-your-app-receives-push-notifications"></a>Uygulamanızın anında iletme bildirimleri aldığını doğrulama

iOS'ta anında iletme bildirimlerini test etmek için, uygulamayı fiziksel bir iOS cihazına dağıtmanız gerekir. İOS simülatörü kullanarak Apple anında iletme bildirimleri gönderemezsiniz.

1. Uygulamayı çalıştırın ve kaydın başarılı olduğunu doğrulayın. Ardından, **Tamam**'a basın.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Kaydet":::

2. Ardından, önceki bölümde açıklandığı gibi [Azure Portal](https://portal.azure.com/)bir test anında iletme bildirimi gönderin.

3. Anında iletme bildirimi, verilen Bildirim Hub 'ından bildirimleri almak için kayıtlı tüm cihazlara gönderilir.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Test gönder":::

## <a name="next-steps"></a>Sonraki adımlar

Bu basit örnekte, tüm kayıtlı iOS cihazlarınıza anında iletme bildirimleri yayınlayacaktır. Belirli iOS cihazlarına anında iletme bildirimleri göndermeyi öğrenmek için aşağıdaki öğreticiye ilerleyin:

[Öğretici: belirli cihazlara anında Iletme bildirimleri gönderme](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Azure Notification Hubs genel bakış](notification-hubs-push-notification-overview.md)
- [REST API 'Leri Notification Hubs](/rest/api/notificationhubs/)
- [Arka uç işlemleri için SDK Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub 'da SDK 'Yı Notification Hubs](https://github.com/Azure/azure-notificationhubs)
- [Uygulama arka ucuna kaydol](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Kayıt yönetimi](notification-hubs-push-notification-registration-management.md)
- [Etiketlerle çalışma](notification-hubs-tags-segment-push-message.md)
- [Özel şablonlarla çalışma](notification-hubs-templates-cross-platform-push-messages.md)
- [Paylaşılan erişim imzaları ile erişim denetimi Service Bus](../service-bus-messaging/service-bus-sas.md)
- [Program aracılığıyla SAS belirteçleri oluşturma](/rest/api/eventhub/generate-sas-token)
- [Apple güvenliği: ortak şifre](https://developer.apple.com/security/)
- [UNIX dönem saati](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)