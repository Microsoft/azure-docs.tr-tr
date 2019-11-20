---
title: Azure Notification Hubs kullanan Swift iOS uygulamalarına anında iletme bildirimleri gönderme | Microsoft Docs
description: Azure Notification Hubs kullanan Swift iOS uygulamalarına nasıl bildirim göndereceğinizi öğrenin.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 8dae5bcc082ba5dd0953e3e97f609e4031547a35
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030646"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Öğretici: Notification Hubs kullanan Swift iOS uygulamalarına anında Iletme bildirimleri gönderin REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Bu öğreticide, [REST API](/rest/api/notificationhubs/)kullanarak bir Swift tabanlı iOS uygulamasına anında iletme bildirimleri göndermek için Azure Notification Hubs kullanırsınız. Ayrıca, [Apple Anında Iletilen bildirim hizmeti 'ni (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)kullanarak anında iletme bildirimleri alan boş bir iOS uygulaması oluşturursunuz.

Bu öğreticide aşağıdaki adımları izleyebilirsiniz:

> [!div class="checklist"]
> * Sertifika imzalama istek dosyasını oluşturun.
> * Uygulamanızı anında iletme bildirimleri için isteyin.
> * Uygulama için bir sağlama profili oluşturun.
> * Bildirim hub’ı oluşturma.
> * Bildirim Hub 'ını APNs bilgileriyle yapılandırın.
> * İOS uygulamanızı bir Bildirim Hub 'ına bağlayın.
> * Çözümü test edin.

## <a name="prerequisites"></a>Önkoşullar

Bu arada izlemek için şunlar gerekir:

- Hizmeti bilmiyorsanız [Azure Notification Hubs genel bakış](notification-hubs-push-notification-overview.md) bölümüne göz atın.
- [Kayıtlar ve yükleme](notification-hubs-push-notification-registration-management.md)hakkında bilgi edinmek için.
- Etkin bir [Apple geliştirici hesabı](https://developer.apple.com).
- Anahtarınıza yüklenmiş geçerli bir geliştirici sertifikasıyla birlikte Xcode çalıştıran bir Mac.
- Simülatör ile anında iletme bildirimlerini test etmek için, ile çalıştırabilir ve hata ayıklaması yapabilirsiniz.
- Fiziksel iPhone cihazınız [Apple portalına](https://developer.apple.com) kaydedilir ve sertifikanız ile ilişkilendirilir.
- Kaynakları oluşturabileceğiniz ve yönetebileceğiniz bir [Azure aboneliği](https://portal.azure.com) .

İOS geliştirmeyle ilgili önceki bir deneyim olmasa bile, bu ilk ilkeler örneğini oluşturma adımlarını takip edebilmelisiniz. Bununla birlikte, aşağıdaki kavramlarla benzerlik özelliğinden faydalanabilirsiniz:

- Xcode ve Swift ile iOS uygulamaları oluşturma.
- İOS için bir [Azure Notification Hub 'ı](notification-hubs-ios-apple-push-notification-apns-get-started.md) yapılandırma.
- [Apple geliştirici portalı](https://developer.apple.com) ve [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Bildirim Hub 'ı yalnızca **korumalı alan** kimlik doğrulama modunu kullanacak şekilde yapılandırılacak. Bu kimlik doğrulama modunu üretim iş yükleri için kullanmamalısınız.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>İOS uygulamanızı bir Bildirim Hub 'ına bağlama

Bu bölümde, Bildirim Hub 'ına bağlanacak iOS uygulamasını oluşturacaksınız.  

### <a name="create-an-ios-project"></a>İOS projesi oluşturma

1. Xcode'da yeni bir iOS projesi oluşturun ve **Single View Application** (Tek Görünüm Uygulaması) şablonunu seçin.

1. Yeni proje için seçenekleri ayarlarken:

   1. Apple Developer Portal 'da **paket tanımlayıcısını** ayarladığınızda kullandığınız **ürün adını** (Pushdemo) ve **kuruluş tanımlayıcısını** (`com.<organization>`) belirtin.

   1. **Uygulama kimliğinin** ayarlandığı **ekibi** seçin.

   1. **Dili** **Swift**olarak ayarlayın.

   1. **İleri**’yi seçin.

1. **Supportingfiles**adlı yeni bir klasör oluşturun.

1. **Supportingfiles** klasöründe **devsettings. plist** adlı yeni bir p-List dosyası oluşturun. Bu klasörü **gitignore** dosyanıza eklediğinizden emin olun, böylece bir git deposu ile çalışırken uygulanmıyor. Bir üretim uygulamasında, büyük olasılıkla bu gizli dizileri otomatik derleme sürecinin bir parçası olarak ayarlayabilirsiniz. Bu tür ayarlar bu kılavuzda ele alınmıştır.

1. **Devsettings. plist** öğesini, sağladığınız Bildirim Hub 'ından kendi değerlerinizi kullanarak aşağıdaki yapılandırma girdilerini içerecek şekilde güncelleştirin:

   | Anahtar                            | Tür                     | Değer                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | Dize                   | \<hubKey >                  |
   | notificationHubKeyName         | Dize                   | \<hubKeyName >              |
   | notificationHubName            | Dize                   | \<hubName >                 |
   | notificationHubNamespace       | Dize                   | \<hubNamespace >            |

   Gerekli değerleri, Azure portal Notification Hub kaynağına giderek bulabilirsiniz. Özellikle, **Notificationhubname** ve **notificationhubnamespace** değerleri **genel bakış** sayfasındaki **temel** bileşenler özetinin sağ üst köşesinde bulunur.

   ![Notification Hubs Essentials Özeti](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Ayrıca, **erişim ilkelerine** gidip `DefaultFullSharedAccessSignature` gibi Ilgili **erişim Ilkesini**seçerek **Notificationhubkeyname** ve **notificationhubkey** değerlerini de bulabilirsiniz. Bundan sonra, **birincil bağlantı dizesinden** değeri `notificationHubKeyName` için `SharedAccessKeyName=` ve `notificationHubKey` için `SharedAccessKey=` olan değer ile önek olarak kopyalayın.

   Bağlantı dizesi aşağıdaki biçimde olmalıdır:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Basit kalmasını sağlamak için `DefaultFullSharedAccessSignature` belirtin. bu nedenle, bildirim göndermek için belirteci kullanabilirsiniz. Uygulamada `DefaultListenSharedAccessSignature` yalnızca bildirim almak istediğiniz durumlar için daha iyi bir seçimdir.

1. **Proje Gezgini**altında **proje adını** seçin ve ardından **genel** sekmesini seçin.

1. **Kimliği** bulun ve ardından **paket tanımlayıcı** değerini, ÖNCEKI bir adımdan **uygulama kimliği** için kullanılan değer olan `com.<organization>.PushDemo` ile eşleşecek şekilde ayarlayın.

1. **İmzalamayı**bulun ve ardından **Apple geliştirici hesabınız**için uygun **ekibi** seçin. **Takım** değeri, sertifikalarınızı ve profillerinizi oluşturduğunuz ile eşleşmelidir.

1. Xcode, **paket tanımlayıcısına**göre uygun **sağlama profili** değerini otomatik olarak göstermelidir. Yeni **sağlama profili** değerini görmüyorsanız, **Xcode** > **tercihleri** > **Hesap** > **Görünüm ayrıntılarını**seçerek **imzalama kimliği** için profilleri yenilemeyi deneyin. **Imza kimliği**' ni seçin ve sonra profilleri indirmek için sağ alt köşedeki **Yenile** düğmesini seçin.

1. **Yetenekler** sekmesini seçin ve **anında iletme bildirimlerinin** etkinleştirildiğinden emin olun.

1. **Unusernotificationcenterdelegate** protokolünü uygulamak Için **appdelegate. Swift** dosyanızı açın ve sınıfının en üstüne aşağıdaki kodu ekleyin:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    Bu üyeleri daha sonra kullanacaksınız. Özellikle, kaydın bir parçası olarak **Etiketler** ve **generictemplate** üyelerini kullanacaksınız. Etiketler hakkında daha fazla bilgi için bkz. kayıtlar ve [şablon kayıtları](notification-hubs-templates-cross-platform-push-messages.md) [için Etiketler](notification-hubs-tags-segment-push-message.md) .

1. Aynı dosyada, **Didsonlandırhlaunchingwithoptions** işlevine aşağıdaki kodu ekleyin:

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Bu kod, **devsettings. plist**öğesinden ayar değerlerini alır, **Appdelegate** sınıfını **unusernotificationcenter** temsilcisi olarak ayarlar, anında iletme bildirimleri için yetkilendirme ister ve ardından çağırır  **registerForRemoteNotifications**.

    Bu basit tutmak için, kod *yalnızca iOS 10 ve üstünü*destekler. İlgili API 'Leri ve normalde yaptığınız gibi yaklaşımları kullanarak, önceki işletim sistemi sürümleri için destek ekleyebilirsiniz.

1. Aynı dosyada aşağıdaki işlevleri ekleyin:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Kod, Bildirim Hub 'ına kaydolmak için **ınstald ID** ve **pushchannel** değerlerini kullanır. Bu durumda, cihazı tanımlayacak ve sonra istenen **Pushchannel** değerini sağlamak üzere **devicetoken** 'ı biçimlendirirken benzersiz bir değer sağlamak için **UIDevice. Current. ıdentifierforvendor** kullanıyorsunuz. **Showalert** işlevi, Gösterim amacıyla bazı ileti metinlerini göstermek için yeterlidir.

1. **Appdelegate. Swift** dosyasında hala **Unusernotificationcenterdelegate**öğesine **Willvar** ve **didreceive** işlevlerini ekleyin. Bu işlevler, bir uygulamanın ön planda veya arka planda çalıştığı bildirildiklerinde bir uyarı görüntüler.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. **Yüklemekimliği** ve **pushchannel** değerlerinin atandığını doğrulamak Için **Didregisterforremotenocertificate ationswithdevicetoken** işlevinin altına Print deyimlerini ekleyin.

1. Projeye daha sonra ekleyeceğiniz temel bileşenler için **modeller**, **Hizmetler**ve **yardımcı programlar** klasörü oluşturun.

1. Projenin fiziksel bir cihazda oluşturulup çalıştırmalarından emin olun. Anında iletme bildirimleri simülatörü kullanılarak test edilebilir.

### <a name="create-models"></a>Model oluşturma

Bu adımda, [Notification Hubs REST API](/rest/api/notificationhubs/) yüklerini temsil etmek ve gerekli paylaşılan erişim IMZASı (SAS) belirteç verilerini depolamak için bir modeller kümesi oluşturacaksınız.

1. **Modeller** klasörüne **pushtemplate. Swift** adlı yeni bir Swift dosyası ekleyin. Bu model, **Deviceınstallation** yükünün parçası olarak tek bir şablonun **gövdesini** temsil eden bir yapı sağlar.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. **Modeller** klasörüne **deviceınstallation. Swift** adlı yeni bir Swift dosyası ekleyin. Bu dosya, bir **cihaz yüklemesi**oluşturma veya güncelleştirme yükünü temsil eden bir struct tanımlar. Dosyasına aşağıdaki kodu ekleyin:

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. **Modeller** klasörüne **tokendata. Swift** adlı yeni bir Swift dosyası ekleyin. Bu model, bir SAS belirtecini süre sonu ile birlikte depolamak için kullanılacaktır.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>SAS belirteci oluşturma

Notification Hubs Azure Service Bus aynı güvenlik altyapısını kullanın. REST API çağırmak için, isteğin **Yetkilendirme** üst bilgisinde KULLANıLABILECEK [bir SAS belirteci programlı olarak](/rest/api/eventhub/generate-sas-token) oluşturmanız gerekir.  

Elde edilen belirteç aşağıdaki biçimde olacaktır:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

İşlemin kendisi aynı altı ana adımdan oluşur:  

1. Süre sonu, [UNIX dönem saat](https://en.wikipedia.org/wiki/Unix_time) biçiminde hesaplanıyor, yani gece yarısından beri geçen saniye sayısı, 1 Ocak 1970.
1. Erişmeye çalıştığınız kaynağı temsil eden **Resourceurl 'yi** biçimlendirmek, bu nedenle yüzde kodlamalı ve küçük harfle yazılmalıdır. **Resourceurl** 'nin `'https://<namespace>.servicebus.windows.net/<hubName>'` biçimi vardır.
1. @No__t-1 olarak biçimlendirilen **Stringtosign**' ı hazırlama.
1. **Stringtosign** değerinin HMAC-SHA256 karmasını kullanarak, **Imzaları** hesaplama ve Base64 kodlaması. Karma değeri, ilgili **Yetkilendirme kuralı**Için **bağlantı dizesinin** **anahtar** bölümüyle birlikte kullanılır.
1. Base64 kodlu **imza** , yüzde kodlamalı şekilde biçimlendiriliyor.
1. **Urlencodedsignature**, **expiryepoch**, **KeyName**ve **urlencodedresourceurl** değerlerini kullanarak belirteci beklenen biçimde oluşturma.

Paylaşılan erişim imzasına daha kapsamlı bir genel bakış için [Azure Service Bus belgelerine](../service-bus-messaging/service-bus-sas.md) ve Azure Service Bus ve Notification Hubs nasıl kullandığını görün.

Bu Swift örneği amaçları doğrultusunda, imzanın karmasından yararlanmak için Apple 'ın açık kaynaklı **Commonşifre** kitaplığını kullanacaksınız. C Kitaplığı olduğundan, bu, kutudaki Swift 'ta erişilebilir değildir. Bir köprü oluşturma üst bilgisi kullanarak kitaplığı kullanılabilir hale getirebilirsiniz.

Köprü oluşturma üst bilgisini eklemek ve yapılandırmak için:

1. Xcode 'da **dosya** > **Yeni** > **Dosya** > **üstbilgi dosyası**' nı seçin. Üst bilgi dosyasını **Bridgingheader. h**olarak adlandırın.

1. **Commonhmac. h**dosyasını içeri aktarmak için dosyayı düzenleyin:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Hedef **Yapı ayarlarını** , köprü oluşturma başlığına başvuracak şekilde güncelleştirin:

   1. **Oluşturma ayarları** sekmesini açın ve **Swift derleyicisi** bölümüne gidin.

   1. **Install hedefi-C uyumluluk üstbilgisi** seçeneğinin **Yes**olarak ayarlandığından emin olun.

   1. @No__t-0 dosya yolunu, **hedef-C köprüleme üst bilgisi** seçeneğine girin. Bu, köprü oluşturma üst bilgisinin dosya yoludur.

   Bu seçenekleri bulamıyorsanız, **temel** veya **özelleştirilmiş**olmak yerine **Tüm** Görünüm ' ün seçili olduğundan emin olun.

   Birçok üçüncü taraf açık kaynaklı sarmalayıcı kitaplığı vardır ve bu, çok sayıda **Commonşifre** kullanarak biraz daha kolay olabilir. Bununla birlikte, bu kitaplıkların tartışılması Bu makalenin kapsamı dışındadır.

1. **Utilities** klasörü Içinde **tokenutility. Swift** adlı yeni bir Swift dosyası ekleyin ve aşağıdaki kodu ekleyin:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   Bu yardımcı program SAS belirtecini oluşturmaktan sorumlu mantığı kapsüller.

   Daha önce açıklandığı gibi, **Getsastoken** işlevi belirteci hazırlamak için gereken üst düzey adımları düzenler. İşlev, Bu öğreticinin ilerleyen kısımlarında yükleme hizmeti tarafından çağrılır.

   Diğer iki işlev **Getsastoken** işlevi tarafından çağrılır: ilişkili URL dizesini kodlamak için Imza ve **Urlencodedstring** bilgi işlem için **sha256HMac** . **Urlencodedstring** işlevi, yerleşik **Addingyüztenkodlama** işlevini kullanarak gerekli çıktıyı elde etmek mümkün olmadığından gereklidir.

   [Azure Storage IOS SDK 'sı](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) , bu Işlemlere, amaç-C ' d e nasıl yaklaşıma yönelik harika bir örnektir. Azure Service Bus SAS belirteçleri hakkında daha fazla bilgi [Azure Service Bus belgelerinde](../service-bus-messaging/service-bus-sas.md)bulunabilir.

### <a name="verify-the-sas-token"></a>SAS belirtecini doğrulama

Yükleme hizmetini istemcisine uygulamadan önce, uygulamanızın tercih ettiğiniz HTTP yardımcı programını kullanarak SAS belirtecini doğru bir şekilde ürettikten emin olun. Bu öğreticinin amaçları doğrultusunda, tercih ettiğiniz aracımız **Postman**olacaktır.

Uygulama tarafından oluşturulan **Yüklemekimliği** ve **belirteç** değerlerini göz önünde bırakmak için uygun şekilde yerleştirilmiş bir yazdırma ifadesini veya kesme noktasını kullanın.

**Yükleme** API 'sini çağırmak için aşağıdaki adımları izleyin:

1. **Postman**'da yeni bir sekme açın.

1. **Get** için isteği ayarlayın ve şu adresi belirtin:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. İstek üstbilgilerini şu şekilde yapılandırın:

   | Anahtar           | Değer            |
   | ------------- | ---------------- |
   | İçerik türü  | uygulama/json |
   | Yetkilendirme | \<sasToken >       |
   | x-MS-sürümü  | 2015-01          |

1. **Kaydet** düğmesinin altındaki sağ üst köşede görüntülenen **kod** düğmesini seçin. İstek aşağıdaki örneğe benzer şekilde görünmelidir:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. **Gönder** düğmesini seçin.

Bu noktada belirtilen **Yüklemekimliği** için bir kayıt yok. Doğrulamanın "401 Yetkisiz" yanıtı yerine "404 bulunamadı" yanıtı ile sonuçlanmalıdır. Bu sonuç SAS belirtecinin kabul edildiğini onaylamalıdır.

### <a name="implement-the-installation-service-class"></a>Yükleme hizmeti sınıfını uygulama

Daha sonra, [yüklemeler REST API](/rest/api/notificationhubs/create-overwrite-installation)etrafında temel sarmalayıcı uygulayacaksınız.  

**Hizmetler** klasörü altına **notificationregistrationservice. Swift** adlı yeni bir Swift dosyası ekleyin ve bu dosyaya aşağıdaki kodu ekleyin:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil

    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }

    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {

        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)

        if let tags = tags {
            deviceInstallation.tags = tags
        }

        if let templates = templates {
            deviceInstallation.templates = templates
        }

        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"

            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"

            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }

            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)

            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }

    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

Önkoşul ayrıntıları, başlatmanın bir parçası olarak sağlanır. Etiketler ve şablonlar, isteğe bağlı olarak, **cihaz yüklemesi** JSON yükünün bir parçasını oluşturmak için **register** işlevine geçirilir.  

**Register** işlevi, isteği hazırlamak için diğer özel işlevleri çağırır. Yanıt alındıktan sonra, tamamlama çağrılır ve kaydın başarılı olup olmadığını gösterir.  

İstek uç noktası **GetBaseAddress** işlevi tarafından oluşturulur. Yapım, başlatma sırasında sağlanmış olan Notification Hub parametreleri *ad alanını* ve *adını* kullanır.  

**Getsastoken** işlevi, şu anda saklı belirtecin geçerli olup olmadığını denetler. Belirteç geçerli değilse, işlev, yeni bir belirteç oluşturmak için **Tokenutility** yöntemini çağırır ve sonra bir değer döndürmeden önce onu depolar.

Son olarak, **Encodetojson** , istek gövdesinin bir parçası olarak kullanılmak üzere ilgili model nesnelerini JSON 'a dönüştürür.

### <a name="invoke-the-notification-hubs-rest-api"></a>Notification Hubs REST API çağır

Son adım, **Notificationhub**'umuza kaydolmak üzere **Appdelegate** 'i **notificationregistrationservice** kullanacak şekilde güncelleştiriyor.

1. **Appdelegate. Swift** ' i açın ve **notificationregistrationservice**'e bir başvuru depolamak için bir sınıf düzeyi değişkeni ekleyin:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. Aynı dosyada, **Ddregisterforremotenocertificate Ationswithdevicetoken** Işlevini kullanarak **notificationregistrationservice** öğesini önkoşul parametreleriyle başlatın ve ardından **register** işlevini çağırın.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Bu işlemi basit tutmak için, çıkış penceresini **kayıt** işleminin sonucuyla güncelleştirmek üzere birkaç Print deyimi kullanacaksınız.

1. Şimdi uygulamayı bir fiziksel cihazda derleyin ve çalıştırın. Çıkış penceresinde "kayıtlı" görmeniz gerekir.

## <a name="test-the-solution"></a>Çözümü test etme

Uygulamamız bu aşamada **Notificationhub** 'a kaydedilir ve anında iletme bildirimleri alabilir. Xcode 'da, hata ayıklayıcıyı durdurun ve şu anda çalışıyorsa uygulamayı kapatın. Ardından, **Cihaz yükleme** ayrıntılarının beklenildiği ve uygulamamız tarafından artık anında iletme bildirimleri alabilmesine bakın.  

### <a name="verify-the-device-installation"></a>Cihaz yüklemesini doğrulama

Artık, [SAS belirtecini doğrulamak](#verify-the-sas-token)Için **Postman** kullanarak daha önce yaptığınız aynı isteği yapabilirsiniz. SAS belirtecinin süresinin dolmadığı varsayıldığında, yanıt artık, sizin belirttiğiniz şablonlar ve Etiketler gibi yükleme ayrıntılarını içermelidir.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>Test bildirimi (Azure portal) gönder

Artık bildirimleri alabilmeniz için en hızlı yol, Azure portal Bildirim Hub 'ına gözatabiliyor.

1. Azure portal, Bildirim Hub 'ınızdaki **genel bakış** sekmesine göz atın.

1. Portal penceresinin sol üst kısmındaki **temel** bileşenler özetinin üzerinde olan **Test gönder**' i seçin:

    ![Notification Hubs Essentials Özet testi Gönder düğmesi](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. **Platformlar** listesinden **özel şablon** ' ı seçin.

1. **Send to Tag ifadesi**için **12345** girin. Yüklemenizde bu etiketi daha önce belirtmiştiniz.

1. İsteğe bağlı olarak JSON yükünde **iletiyi** düzenleyin:

    ![Notification Hubs testi gönder](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. **Gönder**’i seçin. Portalın, bildirimin cihaza başarıyla gönderilip gönderilmediğini belirtmesi gerekir:

    ![Notification Hubs test gönderme sonuçları](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Uygulamanın ön planda çalışmadığı varsayıldığında, cihazınızdaki **bildirim merkezinde** bir bildirim de görmeniz gerekir. Bildirime dokunduğunuzda uygulamayı açmanız ve uyarının gösterilmesi gerekir.

    ![Bildirim alındı örneği](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Test bildirimi gönderme (posta taşıyıcısı)

Sınama için daha kolay bir yol olabilecek **Postman**kullanarak [REST API](/rest/api/notificationhubs/) aracılığıyla bildirim gönderebilirsiniz.

1. **Postman**'da yeni bir sekme açın.

1. **Gönderi**isteği olarak ayarlayın ve şu adresi girin:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. İstek üstbilgilerini şu şekilde yapılandırın:

   | Anahtar                            | Değer                          |
   | ------------------------------ | ------------------------------ |
   | İçerik türü                   | Uygulama/JSON; charset = UTF-8 |
   | Yetkilendirme                  | \<sasToken >                     |
   | ServiceBusNotification-biçim  | şablon                       |
   | Etiketler                           | "12345"                        |

1. Aşağıdaki JSON yüküyle **Ham JSON (Application. JSON)** kullanmak Için istek **gövdesini** yapılandırın:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Pencerenin sağ üst kısmındaki **Kaydet** düğmesi altında bulunan **kod** düğmesini seçin. İstek aşağıdaki örneğe benzer şekilde görünmelidir:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. **Gönder** düğmesini seçin.

Başarılı bir durum kodu almalısınız ve istemci cihazında bildirimi alacaksınız.

## <a name="next-steps"></a>Sonraki adımlar
Artık [REST API](/rest/api/notificationhubs/) aracılığıyla bir Bildirim Hub 'ına bağlı temel bir iOS Swift uygulamasına sahipsiniz ve bildirim gönderebilir ve alabilir. Daha fazla bilgi için aşağıdaki makalelere bakın:

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
