---
title: Xamarin. Forms uygulamanıza anında iletme bildirimleri ekleme | Microsoft Docs
description: Azure hizmetlerini kullanarak Xamarin. Forms uygulamalarınıza çok platformlu anında iletme bildirimleri gönderme hakkında bilgi edinin.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: ce5750a5071ee0dfc257498f83f41b6d59c99a8b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388526"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Xamarin. Forms uygulamanıza anında iletme bildirimleri ekleme

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış

Bu öğreticide, [Xamarin. Forms hızlı başlatmasından](app-service-mobile-xamarin-forms-get-started.md)kaynaklanan tüm projelere anında iletme bildirimleri eklersiniz. Bu, bir kayıt her eklendiğinde bir anında iletme bildiriminin tüm platformlar arası istemcilere gönderilmesi anlamına gelir.

İndirilen hızlı başlangıç sunucusu projesini kullanmıyorsanız, anında iletme bildirimi uzantı paketine ihtiyacınız olacaktır. Daha fazla bilgi için bkz. [Azure için .net arka uç sunucu SDK 'sı Mobile Apps çalışma](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Önkoşullar

İOS için bir [Apple Geliştirici Programı üyeliğine](https://developer.apple.com/programs/ios/) ve fiziksel bir iOS cihazına ihtiyacınız olacaktır. [İOS simülatörü anında iletme bildirimlerini desteklemez](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Bildirim Hub 'ı yapılandırma

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Sunucu projesini anında iletme bildirimleri gönderecek şekilde Güncelleştir

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Android projesini yapılandırma ve çalıştırma (isteğe bağlı)

Android için Xamarin. Forms DROID projesi anında iletme bildirimlerini etkinleştirmek için bu bölümü doldurun.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Firebase Cloud Messaging (FCM) özelliğini etkinleştir

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>FCM kullanarak anında iletme istekleri gönderecek Mobile Apps arka ucu yapılandırın

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Android projesine anında iletme bildirimleri ekleme

Arka ucu FCM ile yapılandırılmışsa, FCM ile kaydolmak için istemciye bileşen ve kod ekleyebilirsiniz. Ayrıca, Azure Notification Hubs ile anında iletme bildirimlerini Mobile Apps arka ucu aracılığıyla kaydedebilir ve bildirim alabilirsiniz.

1. **DROID** projesinde,, **NuGet Paketlerini Yönet > başvurular**' a sağ tıklayın....
1. NuGet Paket Yöneticisi penceresinde, **Xamarin. Firebase. Messaging** paketini arayın ve projeye ekleyin.
1. **DROID** projesinin proje özellikleri ' nde, uygulamayı Android sürüm 7,0 veya üstünü kullanarak derlemek üzere ayarlayın.
1. Firebase konsolundan indirilen **Google-Services. JSON** dosyasını, **DROID** projesinin köküne ekleyin ve Build eylemini **GoogleServicesJson**olarak ayarlayın. Daha fazla bilgi için bkz. [Google SERVICES JSON dosyası ekleme](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging ile kaydolma

1. **AndroidManifest.xml** dosyasını açın ve aşağıdaki `<receiver>` öğelerini `<application>` öğesine ekleyin:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

#### <a name="implementing-the-firebase-instance-id-service"></a>Firebase örnek KIMLIĞI hizmetini uygulama

1. @No__t-1 adlı **DROID** projesine yeni bir sınıf ekleyin ve aşağıdaki `using` deyimlerinin dosyanın en üstünde bulunduğundan emin olun:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Boş `FirebaseRegistrationService` sınıfını şu kodla değiştirin:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class FirebaseRegistrationService : FirebaseInstanceIdService
    {
        const string TAG = "FirebaseRegistrationService";

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationTokenToAzureNotificationHub(refreshedToken);
        }

        void SendRegistrationTokenToAzureNotificationHub(string token)
        {
            // Update notification hub registration
            Task.Run(async () =>
            {
                await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
            });
        }
    }
    ```

    @No__t-0 sınıfı, uygulamanın FCM 'ye erişmesini yetkilendirecek güvenlik belirteçleri oluşturmaktan sorumludur. @No__t-0 yöntemi, uygulama FCM 'den bir kayıt belirteci aldığında çağrılır. Yöntemi, FCM tarafından zaman uyumsuz olarak güncellenen `FirebaseInstanceId.Instance.Token` özelliğinden belirteci alır. @No__t-0 yöntemi seyrek çağrılır, çünkü belirteç yalnızca uygulama yüklendiğinde veya kaldırıldığında, Kullanıcı uygulama verilerini sildiğinde, uygulama örnek KIMLIĞINI sildiğinde veya belirtecin güvenliği olduğunda barýn. Ayrıca, FCM örnek KIMLIĞI hizmeti uygulamanın belirtecini düzenli aralıklarla ve genellikle 6 ayda bir yenilemelerini ister.

    @No__t-0 Yöntemi ayrıca kullanıcının kayıt belirtecini Azure Bildirim Hub 'ı ile ilişkilendirmek için kullanılan `SendRegistrationTokenToAzureNotificationHub` yöntemini çağırır.

#### <a name="registering-with-the-azure-notification-hub"></a>Azure Notification Hub 'ına kaydolma

1. @No__t-1 adlı **DROID** projesine yeni bir sınıf ekleyin ve aşağıdaki `using` deyimlerinin dosyanın en üstünde bulunduğundan emin olun:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Boş `AzureNotificationHubService` sınıfını şu kodla değiştirin:

    ```csharp
    public class AzureNotificationHubService
    {
        const string TAG = "AzureNotificationHubService";

        public static async Task RegisterAsync(Push push, string token)
        {
            try
            {
                const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBody}
                };

                await push.RegisterAsync(token, templates);
                Log.Info("Push Installation Id: ", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
            }
        }
    }
    ```

    @No__t-0 yöntemi JSON olarak basit bir bildirim iletisi şablonu oluşturur ve Firebase kayıt belirtecini kullanarak Bildirim Hub 'ından şablon bildirimleri almak için kayıt yapar. Bu, Azure Bildirim Hub 'ından gönderilen tüm bildirimlerin kayıt belirteci tarafından temsil edilen cihazı hedeflemesini sağlar.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Anında Iletme bildiriminin Içeriğini görüntüleme

1. @No__t-1 adlı **DROID** projesine yeni bir sınıf ekleyin ve aşağıdaki `using` deyimlerinin dosyanın en üstünde bulunduğundan emin olun:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Boş `FirebaseNotificationService` sınıfını şu kodla değiştirin:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class FirebaseNotificationService : FirebaseMessagingService
    {
        const string TAG = "FirebaseNotificationService";

        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);

            // Pull message body out of the template
            var messageBody = message.Data["message"];
            if (string.IsNullOrWhiteSpace(messageBody))
                return;

            Log.Debug(TAG, "Notification message body: " + messageBody);
            SendNotification(messageBody);
        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new NotificationCompat.Builder(this)
                .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle("New Todo Item")
                .SetContentText(messageBody)
                .SetContentIntent(pendingIntent)
                .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                .SetAutoCancel(true);

            var notificationManager = NotificationManager.FromContext(this);
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    }
    ```

    Bir uygulama FCM 'den bildirim aldığında çağrılan `OnMessageReceived` yöntemi, ileti içeriğini ayıklar ve `SendNotification` yöntemini çağırır. Bu yöntem ileti içeriğini, bildirim alanında görüntülenen bildirim ile uygulama çalışırken başlatılan bir yerel bildirime dönüştürür.

Şimdi, Android cihazda veya Öykünücüde çalışan uygulamada test anında iletme bildirimleri hazır olursunuz.

### <a name="test-push-notifications-in-your-android-app"></a>Android uygulamanızda anında iletme bildirimlerini test etme

İlk iki adım yalnızca bir öykünücü üzerinde test edilirken gereklidir.

1. Google Play Hizmetleri ile yapılandırılmış bir cihaza veya öykünücüye dağıtım veya hata ayıklama yaptığınızdan emin olun. Bu, **Play** uygulamalarının cihaza veya öykünücüsünde yüklü olup olmadığını denetleyerek doğrulanabilir.
2. **Uygulamalar** > **Ayarlar** > **Hesap Ekle**' ye tıklayarak Android cihazına bir Google hesabı ekleyin. Ardından, var olan bir Google hesabını cihaza eklemek veya yeni bir tane oluşturmak için istemleri izleyin.
3. Visual Studio veya Xamarin Studio ' de, **DROID** projesine sağ tıklayın ve **Başlangıç projesi olarak ayarla**' ya tıklayın.
4. Projeyi derlemek ve uygulamayı Android cihazınızda veya öykünücüsünde başlatmak için **Çalıştır** ' a tıklayın.
5. Uygulamada bir görev yazın ve ardından artı ( **+** ) simgesine tıklayın.
6. Bir öğe eklendiğinde bildirim alındığını doğrulayın.

## <a name="configure-and-run-the-ios-project-optional"></a>İOS projesini yapılandırma ve çalıştırma (isteğe bağlı)

Bu bölüm iOS cihazları için Xamarin iOS projesi çalıştırmaya yöneliktir. iOS cihazlarıyla çalışmıyorsanız, bu bölümü atlayabilirsiniz.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>APNS için Bildirim Hub 'ını yapılandırma

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Daha sonra, Xamarin Studio veya Visual Studio 'da iOS proje ayarını yapılandıracaksınız.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>İOS uygulamanıza anında iletme bildirimleri ekleme

1. **İOS** projesinde, AppDelegate.cs açın ve aşağıdaki ifadeyi kod dosyasının en üstüne ekleyin.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. **Appdelegate** sınıfında, bildirimlere kaydolmak için **RegisteredForRemoteNotifications** olayı için bir geçersiz kılma ekleyin:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application,
        NSData deviceToken)
    {
        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

        // Register for push with your mobile app
        Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

3. **Appdelegate**Içinde, **DidReceiveRemoteNotification** olay işleyicisi için aşağıdaki geçersiz kılmayı da ekleyin:

    ```csharp
    public override void DidReceiveRemoteNotification(UIApplication application,
        NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps[new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

    Bu yöntem, uygulama çalışırken gelen bildirimleri işler.

4. **Appdelegate** sınıfında, aşağıdaki kodu **Sonlandırhedbaşlatırken** yöntemine ekleyin:

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    Bu, uzak bildirimler ve istekleri anında kaydetme desteği sunar.

Uygulamanız artık anında iletme bildirimlerini destekleyecek şekilde güncelleştirildi.

#### <a name="test-push-notifications-in-your-ios-app"></a>İOS uygulamanızda anında iletme bildirimlerini test etme

1. İOS projesine sağ tıklayın ve **Başlangıç projesi olarak ayarla**' ya tıklayın.
2. Projeyi derlemek ve uygulamayı bir iOS cihazında başlatmak için, Visual Studio 'da **Çalıştır** düğmesine veya **F5** tuşuna basın. Sonra anında iletme bildirimlerini kabul etmek için **Tamam** ' ı tıklatın.

   > [!NOTE]
   > Uygulamanızın anında iletme bildirimlerini kabul etmelisiniz. Bu istek yalnızca uygulamanın ilk kez çalıştırıldığı zaman gerçekleşir.

3. Uygulamada bir görev yazın ve ardından artı ( **+** ) simgesine tıklayın.
4. Bildirimin alındığını doğrulayın ve sonra bildirimi kapatmak için **Tamam** ' ı tıklatın.

## <a name="configure-and-run-windows-projects-optional"></a>Windows projelerini yapılandırma ve çalıştırma (isteğe bağlı)

Bu bölüm, Windows cihazları için Xamarin. Forms WinApp ve WinPhone81 projelerini çalıştırmaya yöneliktir. Bu adımlar Evrensel Windows Platformu (UWP) projelerini da destekler. Windows cihazlarıyla çalışmıyorsanız, bu bölümü atlayabilirsiniz.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Windows Notification Service ile Windows uygulamanızı anında iletme bildirimleri için kaydetme (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>WNS için Bildirim Hub 'ını yapılandırma

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Windows uygulamanıza anında iletme bildirimleri ekleme

1. Visual Studio 'da bir Windows projesinde **app.xaml.cs** açın ve aşağıdaki deyimleri ekleyin.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    @No__t-0 ' i, `TodoItemManager` sınıfını içeren taşınabilir projenizin ad alanı ile değiştirin.

2. App.xaml.cs ' de, aşağıdaki **ınitnotificationsasync** yöntemini ekleyin:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        const string templateBodyWNS =
            "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        JObject headers = new JObject();
        headers["X-WNS-Type"] = "wns/toast";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyWNS},
            {"headers", headers} // Needed for WNS.
        };

        await TodoItemManager.DefaultManager.CurrentClient.GetPush()
            .RegisterAsync(channel.Uri, templates);
    }
    ```

    Bu yöntem, anında iletme bildirimi kanalını alır ve Bildirim Hub 'ınızdan şablon bildirimleri almak için bir şablon kaydeder. *Messageparam* destekleyen bir şablon bildirimi bu istemciye teslim edilecek.

3. App.xaml.cs ' de, `async` değiştiricisini ekleyerek **Onbaşlatılan** olay işleyicisi yöntem tanımını güncelleştirin. Ardından, yönteminin sonuna aşağıdaki kod satırını ekleyin:

    ```csharp
    await InitNotificationsAsync();
    ```

    Bu, uygulama her başlatıldığında anında iletme bildirimi kaydının oluşturulmasını veya yenilenmesini sağlar. WNS Push kanalının her zaman etkin olduğunu garantilemek için bunu yapmanız önemlidir.  

4. Visual Studio için Çözüm Gezgini ' de **Package. appxmanifest** dosyasını açın ve **Bildirimler**altında **bildirim özellikli** ' i **Evet** olarak ayarlayın.
5. Uygulamayı derleyin ve hata olmadığını doğrulayın. İstemci uygulamanız artık Mobile Apps arka uçtaki şablon bildirimlerine kaydolmalıdır. Çözümünüzde her Windows projesi için bu bölümü tekrarlayın.

#### <a name="test-push-notifications-in-your-windows-app"></a>Windows uygulamanızda anında iletme bildirimlerini test etme

1. Visual Studio 'da bir Windows projesine sağ tıklayın ve **Başlangıç projesi olarak ayarla**' ya tıklayın.
2. Projeyi oluşturmak ve uygulamayı başlatmak için **Çalıştır** düğmesine basın.
3. Uygulamada, yeni bir TodoItem için bir ad yazın ve ardından artı ( **+** ) simgesine tıklayarak ekleyin.
4. Öğe eklendiğinde bir bildirimin alındığını doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Anında iletme bildirimleri hakkında daha fazla bilgi edinebilirsiniz:

* [Azure Mobile Apps anında Iletme bildirimleri gönderme](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase bulut mesajlaşma](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Firebase Cloud Messaging ile uzak bildirimler](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Anında iletme bildirimi sorunlarını tanılama](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Bildirimlerin bırakılmış olmasının çeşitli nedenleri vardır veya cihazlar üzerinde bitmeyebilir. Bu konuda, anında iletme bildirimi hatalarının asıl nedenini çözümleme ve oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.

Aşağıdaki öğreticilerden birine de devam edebilirsiniz:

* [Uygulamanıza kimlik doğrulaması ekleme](app-service-mobile-xamarin-forms-get-started-users.md)  
  Uygulamanızdaki kullanıcıların kimliklerini bir kimlik sağlayıcısı ile nasıl doğrulayacağınızı öğrenin.
* [Uygulamanız için çevrimdışı eşitlemeyi etkinleştirme](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Mobile Apps arka ucu kullanarak uygulamanıza çevrimdışı destek eklemeyi öğrenin. Çevrimdışı eşitleme sayesinde, kullanıcılar bir mobil uygulama olan @ no__t-0görüntüleme, ekleme veya değiştirme, ağ bağlantısı olmadığında bile @ no__t-1verileri ile etkileşime geçebilir.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
