---
title: Azure App Service Mobile Apps özellikli bir Apache Cordova uygulamasına anında iletme bildirimleri ekleyin | Microsoft Docs
description: Apache Cordova uygulamanıza anında iletme bildirimleri göndermek için Mobile Apps nasıl kullanacağınızı öğrenin.
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: elamalani
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 7a7e64b32a084075fe96cbd4ab7a0d28fc0f8e19
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388863"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Apache Cordova uygulamanıza anında iletme bildirimleri ekleme

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış

Bu öğreticide, bir kayıt her eklendiğinde cihaza anında iletme bildirimi gönderilmesi için [Apache Cordova hızlı başlangıç][5] projesine anında iletme bildirimleri eklersiniz.

İndirilen hızlı başlangıç sunucusu projesini kullanmıyorsanız, anında iletme bildirimi uzantı paketine ihtiyacınız vardır. Daha fazla bilgi için bkz. [Mobile Apps için .net arka uç sunucu SDK 'sı Ile çalışma][1].

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici, Visual Studio 2015 ile geliştirilen bir Apache Cordova uygulamasına sahip olduğunuzu varsayar. Bu cihazın Google Android Emulator, bir Android cihazında, bir Windows cihazında veya iOS cihazında çalışması gerekir.

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* [Visual Studio Community 2015][2] veya ÜZERI bir bilgisayar
* [Apache Cordova için Visual Studio Araçları][4]
* [Etkin bir Azure hesabı][3]
* Tamamlanmış bir [Apache Cordova hızlı başlangıç][5] projesi
* Android Doğrulanmış bir e-posta adresine sahip bir [Google hesabı][6]
* Işlemine [Apple geliştirici programı üyeliği][7] ve iOS cihazı (IOS simülatörü anında iletme bildirimlerini desteklemez)
* Pencerelerin [Microsoft Store Geliştirici hesabı][8] ve Windows 10 cihazı

## <a name="configure-hub"></a>Bildirim Hub 'ı yapılandırma

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Bu bölümdeki adımları gösteren bir video izleyin][9].

## <a name="update-the-server-project"></a>Sunucu projesini güncelleştir

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Cordova uygulamanızı değiştirme

Apache Cordova uygulama projenizin anında iletme bildirimlerini işlemeye hazırlanmaya çalıştığından emin olmak için Cordova Push eklentisini ve platforma özgü tüm gönderme hizmetlerini yükleme.

#### <a name="update-the-cordova-version-in-your-project"></a>Projenizdeki Cordova sürümünü güncelleştirin.

Projeniz sürüm 6.1.1 ' den önceki bir Apache Cordova sürümünü kullanıyorsa, istemci projesini güncelleştirin. Projeyi güncelleştirmek için aşağıdaki adımları uygulayın:

* Yapılandırma tasarımcısını açmak için `config.xml` ' a sağ tıklayın.
* **Platformlar** sekmesini seçin.
* **Cordova CLI** metin kutusunda **6.1.1**' yi seçin. 
* Projeyi güncelleştirmek için **Oluştur**' u seçin ve ardından **Build Solution**' ı seçin.

#### <a name="install-the-push-plugin"></a>İtme eklentisini yükleme

Apache Cordova uygulamalar, cihaz veya ağ yeteneklerini yerel olarak işlemez.  Bu yetenekler, [NPM][10] veya GitHub üzerinde yayınlanan eklentiler tarafından sağlanır. @No__t-0 eklentisi ağ anında iletme bildirimlerini işler.

İtme eklentisini aşağıdaki yöntemlerle yükleyebilirsiniz:

**Komut isteminden:**

Şu komutu çalıştırın:

    cordova plugin add phonegap-plugin-push

**Visual Studio içinden:**

1. Çözüm Gezgini ' de, `config.xml` dosyasını açın. Sonra, eklentiler **@no__t-** 1**özel**' i seçin. Ardından yükleme kaynağı olarak **Git** ' i seçin.

2. Kaynak olarak `https://github.com/phonegap/phonegap-plugin-push` girin.

    ![Config. xml dosyasını Çözüm Gezgini açın][img1]

3. Yükleme kaynağının yanındaki oku seçin.

4. **SENDER_ID**' de, Google Geliştirici konsol projesi için bir sayısal proje kimliğiniz zaten varsa, buradan ekleyebilirsiniz. Aksi takdirde, 777777 gibi bir yer tutucu değeri girin. Android 'i hedefliyorsanız, bu değeri config. xml dosyasında daha sonra güncelleştirebilirsiniz.

    >[!NOTE]
    >Sürüm 2.0.0 itibariyle, Google-Services. json ' ın, gönderenin KIMLIĞINI yapılandırmak için projenizin kök klasörüne yüklenmesi gerekir. Daha fazla bilgi için [yükleme belgelerine bakın.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. **Add (Ekle)** seçeneğini belirleyin.

Gönderme eklentisi artık yüklendi.

#### <a name="install-the-device-plugin"></a>Cihaz eklentisini yükler

Push eklentisini yüklerken kullandığınız yordamın aynısını izleyin. Çekirdek eklentiler listesinden cihaz eklentisini ekleyin. (Bulmak için **eklentiler @no__t-** 1**çekirdek**' u seçin.) Platform adını almak için bu eklentiye ihtiyacınız vardır.

#### <a name="register-your-device-when-the-application-starts"></a>Uygulama başladığında cihazınızı kaydetme 

Başlangıçta Android için çok az kod ekledik. Daha sonra, uygulamayı iOS veya Windows 10 ' da çalışacak şekilde değiştirebilirsiniz.

1. Oturum açma işlemi için geri çağırma sırasında **Registerforpushnotifications** öğesine bir çağrı ekleyin. Alternatif olarak, onu **Ondeviceready** yönteminin altına ekleyebilirsiniz:

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    Bu örnekte, kimlik doğrulaması başarılı olduktan sonra **Registerforpushnotifications** çağrısı gösterilmektedir. Gereken sıklıkta `registerForPushNotifications()` ' yı çağırabilirsiniz.

2. Yeni **Registerforpushnotifications** yöntemini aşağıdaki şekilde ekleyin:

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

    // Handle the registration event.
    pushRegistration.on('registration', function (data) {
        // Get the native platform of the device.
        var platform = device.platform;
        // Get the handle returned during registration.
        var handle = data.registrationId;
        // Set the device-specific message template.
        if (platform == 'android' || platform == 'Android') {
            // Register for GCM notifications.
            client.push.register('gcm', handle, {
                mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'iOS') {
            // Register for notifications.
            client.push.register('apns', handle, {
                mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'windows') {
            // Register for WNS notifications.
            client.push.register('wns', handle, {
                myTemplate: {
                    body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                    headers: { 'X-WNS-Type': 'wns/toast' } }
            });
        }
    });

    pushRegistration.on('notification', function (data, d2) {
        alert('Push Received: ' + data.message);
    });

    pushRegistration.on('error', handleError);
    }
    ```
3. Android Yukarıdaki kodda `Your_Project_ID` ' ı, [Google Developer konsolundan][18]uygulamanızın sayısal proje kimliğiyle değiştirin.

## <a name="optional-configure-and-run-the-app-on-android"></a>Seçim Android 'de uygulamayı yapılandırma ve çalıştırma

Android 'e yönelik anında iletme bildirimlerini etkinleştirmek için bu bölümü doldurun.

#### <a name="enable-gcm"></a>Firebase bulut Iletilerini etkinleştir

Google Android platformunu başlangıçta hedeflediğinden, Firebase Cloud Messaging 'i etkinleştirmeniz gerekir.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Mobil uygulama arka ucu 'nı, FCM kullanarak anında iletme istekleri gönderecek şekilde yapılandırma

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Android için Cordova uygulamanızı yapılandırma

Cordova uygulamanızda, **config. xml**dosyasını açın. Sonra, [Google Geliştirici konsolundan][18]`Your_Project_ID` ' ı uygulamanızın sayısal proje kimliğiyle değiştirin.

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

**İndex. js**' ye açın. Ardından kodu sayısal proje KIMLIĞINIZI kullanacak şekilde güncelleştirin.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Android cihazınızı USB hata ayıklama için yapılandırma

Uygulamanızı Android cihazınıza dağıtabilmeniz için önce USB hata ayıklamayı etkinleştirmeniz gerekir. Android telefonunuzda aşağıdaki adımları uygulayın:

1. **Telefon hakkında**@no__t **Ayarlar**' a gidin. Ardından, geliştirici modu etkinleştirilene kadar (yaklaşık yedi kez) **yapı numarasına** dokunun.
2. **Ayarlar** > **Geliştirici seçenekleri**' ne geri döndüğünüzde, **USB hata ayıklamayı**etkinleştirin. Ardından Android telefonunuzu bir USB kablosuyla geliştirme PC 'nize bağlayın.

Bunu, Android 6,0 (sıralamada Mallow) çalıştıran bir Google Nexus 5X cihazını kullanarak test ettik. Ancak, teknikler tüm modern Android sürümler üzerinde ortaktır.

#### <a name="install-google-play-services"></a>Google Play Hizmetleri yüklensin

Gönderme eklentisi, anında iletme bildirimleri için Android Google Play Hizmetleri kullanır.

1. Visual Studio 'da **araçlar** > **Android** > **Android SDK Manager**' ı seçin. Ardından **ekstralar** klasörünü genişletin. Aşağıdaki SDK 'ların her birinin yüklü olduğundan emin olmak için ilgili kutuları işaretleyin:

   * Android 2,3 veya üzeri
   * Google Repository düzeltmesi 27 veya üzeri
   * Google Play Hizmetleri 9.0.2 veya üzeri

2. **Paketleri yüklemeyi**seçin. Sonra yüklemenin bitmesini bekleyin.

Geçerli gerekli kitaplıklar [PhoneGap-plug-push yükleme belgelerinde][19]listelenmiştir.

#### <a name="test-push-notifications-in-the-app-on-android"></a>Android 'de uygulamada anında iletme bildirimlerini test etme

Artık uygulamayı çalıştırarak ve TodoItem tablosuna öğeler ekleyerek anında iletme bildirimlerini test edebilirsiniz. Aynı arka ucu kullandığınız sürece aynı cihazdan veya ikinci bir cihazdan test edebilirsiniz. Cordova uygulamanızı Android platformunda aşağıdaki yollarla test edin:

* *Fiziksel bir cihazda:* Android cihazınızı, USB kablosuyla geliştirme bilgisayarınıza bağlayın.  **Google Android Emulator**yerine **cihaz**' ı seçin. Visual Studio, uygulamayı cihaza dağıtır ve uygulamayı çalıştırır. Bundan sonra cihazdaki uygulamayla etkileşime geçebilirsiniz.

  [Mobizen][20] gibi ekran paylaşım uygulamaları, Android uygulamaları geliştirmeye yardımcı olabilir. Android ekranınızı bilgisayarınızdaki bir Web tarayıcısına aktarmak için Mobizen projelerini yapın.

* *Android öykünücüsünde:* Öykünücü kullanırken gerekli olan ek yapılandırma adımları vardır.

    Android sanal cihaz (AVD) yöneticisinde gösterildiği gibi, hedef olarak ayarlanmış Google API 'Leri olan sanal bir cihaza dağıttığınızdan emin olun.

    ![Android sanal Aygıt Yöneticisi](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Daha hızlı bir x86 öykünücüsü kullanmak istiyorsanız [HAXM sürücüsünü yükleyip][11]öykünücü 'yı kullanmak üzere yapılandırın.

    **Uygulamalar** > **Ayarlar** > **Hesap Ekle**' i seçerek Android cihazına bir Google hesabı ekleyin. Ardından istemleri izleyin.

    ![Android cihazına bir Google hesabı ekleme](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    ToDoList uygulamasını daha önce olduğu gibi çalıştırın ve yeni bir Todo öğesi ekleyin. Bu kez, bildirim alanında bir bildirim simgesi görüntülenir. Bildirimin tam metnini görüntülemek için bildirim çekmecesini açabilirsiniz.

    ![Bildirimi görüntüle](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>Seçim İOS üzerinde yapılandırma ve çalıştırma

Bu bölüm, iOS cihazlarında Cordova projesini çalıştırmak içindir. İOS cihazlarıyla çalışmıyorsanız, bu bölümü atlayabilirsiniz.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Bir Mac veya bulut hizmetine iOS uzak derleme Aracısı 'nı yükleyip çalıştırma

Visual Studio kullanarak iOS 'ta bir Cordova uygulamasını çalıştırmadan önce, uzaktan derleme Aracısı 'nı yüklemek ve çalıştırmak için [iOS Kurulum kılavuzundaki][12] adımları izleyin.

İOS için uygulamayı derlediğinizden emin olun. Kurulum kılavuzundaki adımlar, Visual Studio 'dan iOS için uygulama oluşturmak üzere gereklidir. Mac yoksa, Macincm gibi bir hizmette uzak derleme Aracısı 'nı kullanarak iOS için derleme yapabilirsiniz. Daha fazla bilgi için bkz. [iOS uygulamanızı bulutta çalıştırma][21].

> [!NOTE]
> İOS üzerinde Push eklentisini kullanmak için Xcode 7 veya üzeri gereklidir.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Uygulama KIMLIĞINIZ olarak kullanılacak KIMLIĞI bulun

Uygulamanızı anında iletme bildirimleri için kaydetmeden önce, Cordova uygulamanızda config. xml dosyasını açın, pencere öğesi öğesinde `id` öznitelik değerini bulun ve daha sonra kullanmak üzere kopyalayın. Aşağıdaki XML 'de, KIMLIK `io.cordova.myapp7777777` ' dır.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Daha sonra, Apple 'ın Geliştirici Portalında bir uygulama KIMLIĞI oluşturduğunuzda bu tanımlayıcıyı kullanın. Geliştirici portalında farklı bir uygulama KIMLIĞI oluşturursanız, bu öğreticide daha sonra birkaç ek adım uygulamanız gerekir. Pencere öğesi öğesindeki KIMLIğIN geliştirici portalındaki uygulama KIMLIĞIYLE eşleşmesi gerekir.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Apple 'ın Geliştirici Portalında anında iletme bildirimleri için uygulamayı kaydetme

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Benzer adımları gösteren bir video izleyin](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Azure 'ı anında iletme bildirimleri gönderecek şekilde yapılandırma

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Uygulama KIMLIĞINIZIN Cordova uygulamanız ile eşleştiğini doğrulayın

Apple geliştirici hesabınızda oluşturduğunuz uygulama KIMLIĞI, config. xml dosyasındaki pencere öğesi öğesinin KIMLIĞIYLE zaten eşleşiyorsa, bu adımı atlayabilirsiniz. Ancak, kimlikler eşleşmiyorsa, aşağıdaki adımları uygulayın:

1. Projenizden platformlar klasörünü silin.
2. Eklentiler klasörünü projenizden silin.
3. Node_modules klasörünü projenizden silin.
4. Apple geliştirici hesabınızda oluşturduğunuz uygulama KIMLIĞINI kullanmak için config. xml dosyasındaki pencere öğesi öğesinin ID özniteliğini güncelleştirin.
5. Projenizi yeniden derleyin.

##### <a name="test-push-notifications-in-your-ios-app"></a>İOS uygulamanızda anında iletme bildirimlerini test etme

1. Visual Studio 'da **iOS** 'un dağıtım hedefi olarak seçildiğinden emin olun. Ardından, bağlı iOS cihazınızda anında iletme bildirimlerini çalıştırmak için **cihaz** ' ı seçin.

    Anında iletme bildirimlerini, PC 'nize iTunes ile bağlanmış bir iOS cihazında çalıştırabilirsiniz. İOS simülatörü anında iletme bildirimlerini desteklemez.

2. Projeyi derlemek ve uygulamayı bir iOS cihazında başlatmak üzere Visual Studio 'da **Çalıştır** düğmesini veya **F5** ' i seçin. Sonra anında iletme bildirimlerini kabul etmek için **Tamam** ' ı seçin.

   > [!NOTE]
   > Uygulama, ilk çalıştırma sırasında anında iletme bildirimleri için onay ister.

3. Uygulamada bir görev yazın ve ardından artı **(+)** simgesini seçin.
4. Bir bildirimin alındığını doğrulayın. Ardından, bildirimi kapatmak için **Tamam** ' ı seçin.

## <a name="optional-configure-and-run-on-windows"></a>Seçim Windows üzerinde yapılandırma ve çalıştırma

Bu bölümde, Apache Cordova uygulama projesinin Windows 10 cihazlarında nasıl çalıştırılacağı açıklanmaktadır (PhoneGap gönderme eklentisi Windows 10 ' da desteklenir). Windows cihazlarıyla çalışmıyorsanız, bu bölümü atlayabilirsiniz.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>WNS ile Windows uygulamanızı anında iletme bildirimleri için kaydedin

Visual Studio 'daki depolama seçeneklerini kullanmak için **Windows-x64** veya **Windows-x86**gibi çözüm platformları listesinden bir Windows hedefi seçin. (Anında iletme bildirimleri için **Windows-anycpu** 'dan kaçının.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Benzer adımları gösteren bir video izleyin][13]

#### <a name="configure-the-notification-hub-for-wns"></a>WNS için Bildirim Hub 'ını yapılandırma

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Cordova uygulamanızı Windows anında iletme bildirimlerini destekleyecek şekilde yapılandırma

**Config. xml**' ye sağ tıklayarak yapılandırma tasarımcısını açın. Ardından **Görünüm Tasarımcısı**' nı seçin. Sonra, **Windows** sekmesini seçin ve Windows **hedef sürümü**altında **Windows 10** ' u seçin.

Varsayılan (hata ayıklama) derlemelerinizin anında iletme bildirimlerini desteklemek için **Build. JSON** dosyasını açın. Ardından "yayın" yapılandırmasını hata ayıklama yapılandırmanıza kopyalayın.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Güncelleştirmeden sonra **Build. JSON** dosyası aşağıdaki kodu içermelidir:

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        },
    "debug": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        }
    }
```

Uygulamayı derleyin ve hata olmadığını doğrulayın. İstemci uygulamanız artık Mobile Apps arka uçtaki bildirimlere kaydolmalıdır. Çözümünüzde her Windows projesi için bu bölümü tekrarlayın.

#### <a name="test-push-notifications-in-your-windows-app"></a>Windows uygulamanızda anında iletme bildirimlerini test etme

Visual Studio 'da, Windows **-x64** veya **Windows-x86**gibi bir Windows platformunun dağıtım hedefi olarak seçildiğinden emin olun. Uygulamayı Visual Studio barındıran bir Windows 10 bilgisayarında çalıştırmak için **yerel makine**' yi seçin.

1. Projeyi derlemek ve uygulamayı başlatmak için **Çalıştır** düğmesini seçin.

2. Uygulamada, yeni bir TodoItem için bir ad yazın ve ardından eklemek için artı **(+)** simgesini seçin.

Öğe eklendiğinde bir bildirimin alındığını doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

* Anında iletme bildirimleri hakkında bilgi edinmek için [Notification Hubs][17] okuyun.
* Daha önce yapmadıysanız, Apache Cordova uygulamanıza [kimlik doğrulaması ekleyerek][14] öğreticiye devam edin.

Aşağıdaki SDK 'Ları kullanmayı öğrenin:

* [Apache Cordova SDK][15]
* [ASP.NET Sunucusu SDK][1]
* [Node.js Sunucusu SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
