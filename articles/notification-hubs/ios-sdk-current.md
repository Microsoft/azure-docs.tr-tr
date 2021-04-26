---
title: Azure Notification Hubs ve iOS SDK sürümü 3.0.0 Preview 1 kullanarak iOS 'a anında iletme bildirimleri gönderin
description: Bu öğreticide, iOS cihazlarına (sürüm 3.0.0-preview1 'i) anında iletme bildirimleri göndermek için Azure Notification Hubs ve Apple Anında Iletilen bildirim hizmetini nasıl kullanacağınızı öğreneceksiniz.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: f905bfa830bfc78caa6ebb02ae49d4839168367b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100628326"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-sdk-for-apple"></a>Öğretici: Apple için Azure Notification Hubs SDK 'sını kullanarak iOS uygulamalarına anında iletme bildirimleri gönderme

Bu öğreticide, Apple için Azure Notification Hubs SDK 'sını kullanarak bir iOS uygulamasına anında iletme bildirimleri göndermek için Azure Notification Hubs 'nin nasıl kullanılacağı gösterilmektedir.

Bu öğreticide aşağıdaki adımlar yer almaktadır:

- Örnek bir iOS uygulaması oluşturun.
- İOS uygulamanızı Azure Notification Hubs bağlayın.
- Test anında iletme bildirimleri gönderin.
- Uygulamanızın bildirimleri aldığını doğrulayın.

Bu öğreticinin tüm kodunu [GitHub 'dan](https://github.com/Azure/azure-notificationhubs-ios/tree/main/SampleNHAppObjC)indirebilirsiniz.

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

   - İkililerini projenize kopyalayarak tümleştirme:

      İkililerini projenize kopyalayarak aşağıdaki gibi tümleştirebilirsiniz:

        - Zip dosyası olarak sunulan [Azure NOTIFICATION HUBS SDK](https://github.com/Azure/azure-notificationhubs-iOS/releases/) çerçevesini indirin ve sıkıştırmayı açın.

        - Xcode'da projenize sağ tıklayın ve **WindowsAzureMessaging.framework** klasörünü Xcode projenize eklemek için **Add Files to** (Dosyaları Şuraya Ekle) seçeneğine tıklayın. **Options** (Seçenekler) seçeneğine tıklayıp **Copy items if needed** (Gerekirse verileri kopyala) öğesinin seçili olduğundan emin olduktan sonra **Add** (Ekle) öğesine tıklayın.

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Çerçeve Ekle":::

6.  `CONNECTION_STRING` Azure Notification Hub 'ına yönelik bağlantı dizesi ve `HUB_NAME` Azure Notification Hub adı Için Iki özellik içeren devsettings. plist adlı bir dosya ekleyin veya düzenleyin.

7. Azure Notification Hubs 'a bağlanma bilgilerini uygun `<string></string>` bölümde ekleyin.  `--HUB-NAME--` `--CONNECTION-STRING--` Daha önce portaldan elde ettiğiniz gibi, dize değişmez yer tutucuları ve hub adı ve **Defaultlistensharedaccesssignature** ile değiştirin:

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

8. Aynı **Appdelegate. d** dosyasında aşağıdaki kodla sonraki tüm kodu değiştirin `didFinishLaunchingWithOptions` :

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>

    // Extend the AppDelegate to listen for messages using MSNotificationHubDelegate and User Notification Center
    @interface AppDelegate () <MSNotificationHubDelegate>

    @end

    @implementation AppDelegate
    
    @synthesize notificationPresentationCompletionHandler;
    @synthesize notificationResponseCompletionHandler;

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

        NSString *path = [[NSBundle mainBundle] pathForResource:@"DevSettings" ofType:@"plist"];
        NSDictionary *configValues = [NSDictionary dictionaryWithContentsOfFile:path];
        
        NSString *connectionString = [configValues objectForKey:@"CONNECTION_STRING"];
        NSString *hubName = [configValues objectForKey:@"HUB_NAME"];

        if([connectionString length] != 0 && [hubName length] != 0) {
            [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
            [MSNotificationHub setDelegate:self];
            [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];
    
            return YES;
        }

        NSLog(@"Please setup CONNECTION_STRING and HUB_NAME in DevSettings.plist and restart application");

        exit(-1);
    }

    - (void)notificationHub:(MSNotificationHub *)notificationHub didReceivePushNotification:(MSNotificationHubMessage *)message {
        // Send message using NSNotificationCenter with the message
        NSDictionary *userInfo = [NSDictionary dictionaryWithObject:message forKey:@"message"];
        [[NSNotificationCenter defaultCenter] postNotificationName:@"MessageReceived" object:nil userInfo:userInfo];
    }

    @end
    ```

    Bu kod, **Devsettings. plist** dosyasında belirttiğiniz bağlantı bilgilerini kullanarak Bildirim Hub 'ına bağlanır. Daha sonra, hub 'ın bildirim gönderebilmesi için cihaz belirtecini Bildirim Hub 'ına verir.

### <a name="create-notificationdetailviewcontroller-header-file"></a>NotificationDetailViewController üst bilgi dosyası oluştur

1. Önceki yönergelere benzer şekilde, **Setupviewcontroller. h** adlı başka bir üst bilgi dosyası ekleyin. Yeni üstbilgi dosyasının içeriğini aşağıdaki kodla değiştirin:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface SetupViewController : UIViewController

   @end

   NS_ASSUME_NONNULL_END
   ```

2. **Setupviewcontroller. d** uygulama dosyasını ekleyin. Dosya içeriğini, UIViewController yöntemlerini uygulayan aşağıdaki kodla değiştirin:

   ```objc
   #import "SetupViewController.h"

    static NSString *const kNHMessageReceived = @"MessageReceived";
    
    @interface SetupViewController ()
    
    @end

    @implementation SetupViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        
        // Listen for messages using NSNotificationCenter
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceivePushNotification:) name:kNHMessageReceived object:nil];
    }

    - (void)dealloc {
        // Clean up subscription to NSNotificationCenter
        [[NSNotificationCenter defaultCenter] removeObserver:self name:kNHMessageReceived object:nil];
    }

    - (void)didReceivePushNotification:(NSNotification *)notification {
        MSNotificationHubMessage *message = [notification.userInfo objectForKey:@"message"];

        // Create UI Alert controller with message title and body
        UIAlertController *alertController = [UIAlertController alertControllerWithTitle:message.title
                             message:message.body
                      preferredStyle:UIAlertControllerStyleAlert];
        [alertController addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleCancel handler:nil]];
        [self presentViewController:alertController animated:YES completion:nil];
        
        // Dismiss after 2 seconds
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            [alertController dismissViewControllerAnimated:YES completion: nil];
        });

    }

    @end
   ```

3. Herhangi bir hata olmadığını doğrulamak için cihazınızda uygulamayı derleyin ve çalıştırın.

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