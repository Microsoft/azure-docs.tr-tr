---
title: Azure Notification Hubs ve Firebase SDK sürümü 1.0.0-preview1 'i kullanarak Android 'e anında iletme bildirimleri gönderme
description: Bu öğreticide, Android cihazlarına anında iletme bildirimleri göndermek için Azure Notification Hubs ve Google Firebase Cloud Messaging 'i kullanmayı öğrenirsiniz (sürüm 1.0.0-preview1 'i).
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 091ada7f099aca17152fc806e61dce6ab979852e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055170"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>Öğretici: Firebase SDK sürümü 1.0.0-preview1 'i kullanarak Android cihazlara anında iletme bildirimleri gönderme

Bu öğreticide, bir Android uygulamasına anında iletme bildirimleri göndermek için Azure Notification Hubs ve Firebase Cloud Messaging (FCM) SDK 'sının (sürüm 1.0.0-preview1 'i) güncelleştirilmiş sürümünün nasıl kullanılacağı gösterilmektedir. Bu öğreticide, Firebase Cloud Messaging (FCM) kullanarak anında iletme bildirimleri alan boş bir Android uygulaması oluşturacaksınız.

Bu öğretici için tamamlanan kodu [GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh)' dan indirebilirsiniz.

Bu öğreticide aşağıdaki adımlar yer almaktadır:

- Android Studio projesi oluşturma.
- Firebase Cloud Messaging’i destekleyen bir Firebase projesi oluşturma.
- Bildirim hub’ı oluşturma.
- Uygulamanızı hub 'a bağlayın.
- Uygulamayı test etme.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için etkin bir Azure hesabınızın olması gerekir. Bir hesabınız yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/free/).

Ayrıca aşağıdaki öğeler de gereklidir:

- [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797) en son sürümü önerilir.
- En düşük destek API düzeyi 19 ' dir.

## <a name="create-an-android-studio-project"></a>Android Studio projesi oluşturma

İlk adım Android Studio bir proje oluşturmaktır:

1. Android Studio başlatın.

2. **Dosya**' yı ve ardından **Yeni**' yi ve ardından **Yeni proje**' yi seçin.

3. **Projenizi seçin** sayfasında **boş etkinlik**' i seçin ve ardından **İleri**' yi seçin.

4. **Projenizi yapılandırın** sayfasında, aşağıdakileri yapın:
   1. Uygulama için bir ad girin.
   2. Proje dosyalarının kaydedileceği bir konum belirtin.
   3. **Son**'u seçin.

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="Projeyi Yapılandır":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>FCM’yi destekleyen bir Firebase projesi oluşturma

1. [Firebase konsolunda](https://firebase.google.com/console/) oturum açın. Henüz bir tane yoksa yeni bir Firebase projesi oluşturun.

2. Projenizi oluşturduktan sonra **Firebase’i Android uygulamanıza ekleyin**’i seçin.

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="Firebase Ekle":::

3. **Android uygulamanıza Firebase ekleme** sayfasında şunları yapın:

   1. **Android paket adı** Için, **ApplicationId** değerini uygulamanızın **Build. Gradle** dosyasına kopyalayın. Bu örnekte, `com.fabrikam.fcmtutorial1app` .

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="Paket adını belirtin":::

   2. **Uygulamayı kaydet**' i seçin.

4. **google-services.jskarşıdan yükle**' yi seçin, dosyayı projenizin **uygulama** klasörüne kaydedin ve sonra **İleri**' yi seçin.

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="Google hizmetini indirin":::

5. Firebase konsolunda projenizin dişli simgesini seçin. Sonra, **Proje Ayarları**’nı seçin.

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="Proje ayarları":::

6. Dosyadaki **google-services.js** Android Studio projenizin **uygulama** klasörüne indirmediyseniz, bu sayfada bunu yapabilirsiniz.

7. **Bulut mesajlaşma** sekmesine geçin.

8. Daha sonra kullanmak üzere **sunucu anahtarını** kopyalayın ve kaydedin. Bu değeri, hub 'ınızı yapılandırmak için kullanırsınız.

## <a name="configure-a-notification-hub"></a>Bildirim hub’ını yapılandırma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. Sol taraftaki menüden **tüm hizmetler** ' i seçin ve ardından **Mobil** bölümünde **Notification Hubs** ' yi seçin. Hizmet adının yanındaki yıldız simgesini seçerek hizmeti Sol menüdeki **Sık Kullanılanlar** bölümüne ekleyin. **Sık kullanılanlara** **Notification Hubs** ekledikten sonra, soldaki menüden seçin.

3. **Notification Hubs** sayfasında, araç çubuğunda **Ekle** ' yi seçin.

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="Hub Ekle":::

4. **Notification Hubs** sayfasında, aşağıdakileri yapın:

   1. **Bildirim Hub 'ına** bir ad girin.

   2. **Yeni ad alanı oluştur**' a bir ad girin. Bir ad alanı bir veya daha fazla hub içerir.

   3. **Konum** açılır listesinden bir değer seçin. Bu değer, hub 'ı oluşturmak istediğiniz konumu belirtir.

   4. **Kaynak grubunda** var olan bir kaynak grubunu seçin veya yeni bir kaynak grubu oluşturun.

   5. **Oluştur**’u seçin.

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="Merkez oluştur":::

5. **Bildirimleri** (zil simgesi) seçin ve ardından **Kaynağa Git**' i seçin. Ayrıca **Notification Hubs** sayfasında listeyi yenileyebilir ve hub 'ınızı seçebilirsiniz.

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="Hub seçin":::

6. Listeden **Erişim İlkeleri**'ni seçin. İki bağlantı dizesinin kullanılabilir olduğunu unutmayın. Anında iletme bildirimlerini işlemek için onlara daha sonra ihtiyacınız olacak.

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="Erişim ilkeleri":::

   > [!IMPORTANT]
   > Uygulamanızda **Defaultfullsharedaccesssignature** ilkesini kullanmayın. Bu ilke yalnızca uygulama arka ucunda kullanılacaktır.

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Hub için Firebase Cloud Messaging ayarlarını yapılandırma

1. Sol bölmede, ayarlar altında **Google (GCM/FCM)** **öğesini** seçin.

2. Daha önce kaydettiğiniz FCM projesinin **sunucu anahtarını** girin.

3. Araç çubuğunda **Kaydet**' i seçin.

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="Sunucu anahtarı":::

4. Azure portal, hub 'ın başarıyla güncelleştirildiğini belirten bir ileti görüntüler. **Kaydet** düğmesi devre dışı bırakıldı.

Bildirim Hub 'ınız şimdi Firebase Cloud Messaging ile çalışacak şekilde yapılandırılmıştır. Ayrıca, bir cihaza bildirim göndermek ve bildirim almak için bir uygulamayı kaydetmek için gerekli olan bağlantı dizelerine sahip olursunuz.

## <a name="connect-your-app-to-the-notification-hub"></a>Uygulamanızı bildirim hub'ına bağlama

### <a name="add-google-play-services-to-the-project"></a>Projeye Google Play hizmetlerini ekleme

1. Android Studio menüsünde **Araçlar** ' ı seçin ve ardından **SDK yöneticisi**' ni seçin.

2. Projenizde kullanılan Android SDK hedef sürümünü seçin. Ardından **Paket ayrıntılarını göster**' i seçin.

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="SDK Yöneticisi":::

3. Zaten yüklenmemişse **Google API 'leri** seçin.

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="API'ler":::

4. **SDK Tools** sekmesine geçin. Google Play Hizmetleri zaten yüklemediyseniz, aşağıdaki görüntüde gösterildiği gibi **Google Play hizmetleri** seçin. Ardından, yüklemek için **Uygula** ' yı seçin. SDK yolunun sonraki bir adım için olduğunu unutmayın.

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Play Services":::

5. **Değişikliği Onayla** iletişim kutusunu görürseniz **Tamam**' ı seçin. Bileşen yükleyicisi istenen bileşenleri yüklüyor. Bileşenler yüklendikten sonra **son** ' u seçin.

6. **Yeni projeler Için ayarlar** iletişim kutusunu kapatmak için **Tamam ' ı** seçin.

### <a name="add-azure-notification-hubs-libraries"></a>Azure Notification Hubs kitaplıklarını ekleme

1. Uygulamanın **Build. Gradle** dosyasında, bağımlılıklar bölümüne aşağıdaki satırları ekleyin:

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk-fcm:1.1.4'
   implementation 'androidx.appcompat:appcompat:1.0.0'
   ```

2. Bağımlılıklar bölümünden sonra aşağıdaki depoyu ekleyin:

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>Google Firebase desteği ekleme

1. Henüz orada değilse, dosyanın sonuna aşağıdaki eklentiyi ekleyin.

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. Araç çubuğunda **Şimdi Eşitle** ' yi seçin.

### <a name="add-code"></a>Kod Ekle

1. Azure Notification Hubs iletileri kesintiye uğratan bir **Notificationhublistener** nesnesi oluşturun.

   ```java
   public class CustomNotificationListener implements NotificationListener {

      @override
      public void onNotificationReceived(Context context, RemoteMessage message) {
    
         /* The following notification properties are available. */
         Notification notification = message.getNotification();
         String title = notification.getTitle();
         String body = notification.getBody();
         Map<String, String> data = message.getData();
    
         if (message != null) {
            Log.d(TAG, "Message Notification Title: " + title);
            Log.d(TAG, "Message Notification Body: " + message);
         }

         if (data != null) {
             for (Map.Entry<String, String> entry : data.entrySet()) {
                 Log.d(TAG, "key, " + entry.getKey() + " value " + entry.getValue());
             }
         }
      }
   }
   ```

2. `OnCreate` `MainActivity` Sınıfının yönteminde, etkinlik oluşturulduğunda Notification Hubs başlatma işlemini başlatmak için aşağıdaki kodu ekleyin:

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.start(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. Android Studio, menü çubuğunda **Oluştur**' u seçin, sonra kodunuzda hata olmadığından emin olmak Için **projeyi yeniden derle** ' yi seçin. **İc_launcher** simgesiyle ilgili bir hata alırsanız, AndroidManifest.xml dosyasından aşağıdaki ifadeyi kaldırın:

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. Uygulamayı çalıştırmak için bir sanal cihazınıza sahip olduğunuzdan emin olun. Yoksa, aşağıdaki gibi bir tane ekleyin:

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="Cihaz Yöneticisi":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="Sanal cihazlar":::
   3. Seçili cihazınızda uygulamayı çalıştırın ve hub 'a başarıyla kaydedildiğinizi doğrulayın.

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="Cihaz kaydı":::

      > [!NOTE]
      > Kayıt, `onTokenRefresh()` örnek kimliği hizmetinin yöntemi çağrılana kadar ilk başlatma sırasında başarısız olabilir. Yenileme işlemi Bildirim Hub 'ına başarılı bir kayıt başlatmalıdır.

## <a name="send-a-test-notification"></a>Test bildirimi gönderme

Aşağıdaki gibi [Azure Portal](https://portal.azure.com/)Bildirim Hub 'ınıza anında iletme bildirimleri gönderebilirsiniz:

1. Azure portal, hub 'ınız için Bildirim Hub 'ı sayfasında, **sorun giderme** bölümünde **Test gönder** ' i seçin.

2. **Platformlar** bölümünde **Android**' i seçin.

3. **Gönder**’i seçin. Mobil uygulamayı üzerinde çalıştırmadığınız için henüz Android cihazında bir bildirim görmezsiniz. Mobil uygulamayı çalıştırdıktan sonra bildirim iletisini görmek için **Gönder** düğmesini tekrar seçin.

4. Portal sayfasının altındaki listede işlemin sonucuna bakın.

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="Test bildirimi gönder":::

5. Uyarı iletisini cihazınızda görürsünüz.

Anında iletme bildirimleri normalde, uyumlu bir kitaplık kullanılarak Mobile Apps veya ASP.NET gibi bir arka uç hizmetinde gönderilir. Arka uçta bir kitaplık yoksa, bildirim iletilerini göndermek için doğrudan REST API de kullanabilirsiniz.

## <a name="run-the-mobile-app-on-emulator"></a>Öykünücü üzerinde mobil uygulamayı çalıştırma

Bir öykünücü içinde anında iletme bildirimlerini test etmeden önce, öykünücü görüntünüzün uygulamanız için seçtiğiniz Google API düzeyini desteklediğinden emin olun. Görüntünüz yerel Google API 'Lerini desteklemiyorsa, **SERVICE_NOT_AVAILABLE** bir özel durum alabilirsiniz.

Ayrıca, Google hesabınızı, **Ayarlar** hesaplar bölümünde çalışan öykünücüsünüzün içine eklediğinizden emin olun  >  . Aksi halde, FCM ile kaydolma denemelerinde **AUTHENTICATION_FAILED** bir özel durum oluşabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, hizmet ile kaydedilmiş tüm Android cihazlara bildirim yayımlamak için Firebase bulut mesajlaşma 'yı kullandınız. Belirli cihazlara nasıl anında iletme bildirimleri gönderileceğini öğrenmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Öğretici: belirli kullanıcılara bildirimler gönderme](push-notifications-android-specific-users-firebase-cloud-messaging.md)

Bildirim göndermek için bazı diğer öğreticiler aşağıda verilmiştir:

- Azure Mobile Apps: Notification Hubs ile tümleştirilmiş Mobile Apps arka uçta bildirimlerin nasıl gönderileceği hakkında bir örnek Için bkz. [iOS uygulamanıza anında Iletme bildirimleri ekleme](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

- ASP.NET: [kullanıcılara anında iletme bildirimleri göndermek için Notification Hubs kullanın](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).

- Azure Notification Hubs Java SDK'sı: Java'dan bildirim göndermek için bkz. [Java'dan Notification Hubs kullanma](notification-hubs-java-push-notification-tutorial.md). Android Geliştirmesi için Eclipse'te test edilmiştir.

- PHP: [PHP'ye ait Notification Hubs nasıl kullanılır](notification-hubs-php-push-notification-tutorial.md).
