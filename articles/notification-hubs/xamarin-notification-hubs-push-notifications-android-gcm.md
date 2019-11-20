---
title: Azure Notification Hubs kullanarak Xamarin. Android uygulamalarına anında iletme bildirimleri gönderin | Microsoft Docs
description: Bu öğreticide, bir Xamarin Android uygulamasına anında iletme bildirimleri göndermek için Azure Notification Hubs'ın nasıl kullanılacağını öğrenirsiniz.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: 0e4354fa7466efcf27f430bbce7edb30bb9a304c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387659"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Öğretici: Notification Hubs kullanarak Xamarin. Android uygulamalarına anında iletme bildirimleri gönderme

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Genel Bakış

Bu öğretici, bir Xamarin.Android uygulamasına anında iletme bildirimleri göndermek için Azure Notification Hubs'ın nasıl kullanılacağını size gösterir. Firebase Cloud Messaging (FCM) kullanarak anında iletme bildirimleri alan boş bir Xamarin.Android uygulaması oluşturursunuz. Uygulamanızı çalıştıran tüm cihazlara anında iletme bildirimleri yayımlamak için bildirim hub’ınızı kullanırsınız. Tamamlanan kod, [Notificationhub uygulama](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid) örneğinde kullanılabilir.

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Firebase projesi oluşturma ve Firebase Cloud Messaging’i etkinleştirme
> * Bildirim hub’ı oluşturma
> * Xamarin.Android uygulaması oluşturma ve bildirim hub'ına bağlama
> * Azure portalından test bildirimleri gönderme

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* Windows'da [Xamarin ile Visual Studio] veya OS X'te [Mac için Visual Studio].
* Etkin Google hesabı

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Firebase projesi oluşturma ve Firebase Cloud Messaging’i etkinleştirme

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Bildirim hub’ı oluşturma

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Bildirim Hub 'ı için GCM/FCM ayarlarını yapılandırma

1. Soldaki menüdeki **Ayarlar** bölümünde **Google (GCM/FCM)/** öğesini seçin.
2. Google Firebase konsolundan not ettiğiniz **sunucu anahtarını** girin.
3. Araç çubuğunda **Kaydet**’i seçin.

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Bildirim hub'ınız FCM ile birlikte çalışmak üzere yapılandırıldı. Ayrıca, uygulamanızı anında iletme bildirimleri alması ve anında iletme bildirimlerini göndermesi amacıyla kaydetmenizi sağlayan bağlantı dizelerine sahipsiniz.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Bir Xamarin. Android uygulaması oluşturma ve Bildirim Hub 'ına bağlama

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Visual Studio projesi oluşturma ve NuGet paketleri ekleme

> [!NOTE]
> Bu öğreticide belgelenen adımlar Visual Studio 2017 içindir. 

1. Visual Studio 'da **Dosya** menüsünü açın, **Yeni**' yi ve ardından **Proje**' yi seçin. **Yeni proje** penceresinde aşağıdaki adımları uygulayın:
    1. **Yüklü**, **görsel C#** ' i genişletin ve ardından **Android**' e tıklayın.
    2. Listeden **Android uygulaması (Xamarin)** öğesini seçin.
    3. Proje için bir **ad** girin.
    4. Proje için bir **konum** seçin.
    5. **Tamam**’ı seçin

        ![Yeni Proje iletişim kutusu](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. **Yeni Android uygulaması** Iletişim kutusunda **boş uygulama**' yı seçin ve **Tamam**' ı seçin.

    ![Yeni Proje iletişim kutusu](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. **Çözüm Gezgini** penceresinde **Özellikler**’i genişletin ve **AndroidManifest.xml** dosyasına tıklayın. Paket adını, Google Firebase Console’da projenize Firebase Cloud Messaging eklerken girdiğiniz paket adıyla eşleşecek şekilde güncelleştirin.

    ![GCM’de paket adı](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Projenin hedef Android sürümünü şu adımları izleyerek **Android 9,0 (pasta)** olarak ayarlayın: 
    1. Projenize sağ tıklayın ve **Özellikler**' i seçin. 
    1. **Android sürümünü kullanarak derle: (hedef çerçeve)** alanı için **Android 9,0 (pasta)** öğesini seçin. 
    1. Hedef Framework 'ü değiştirmeye devam etmek için ileti kutusunda **Evet** ' i seçin.
1. Aşağıdaki adımları izleyerek, gerekli NuGet paketlerini projeye ekleyin:
    1. Projeye sağ tıklayın ve **NuGet Paketlerini Yönet...** seçeneğini belirleyin.
    1. **Yüklü** sekmesine geçiş yapın, **Xamarin. Android. support. Design**' ı seçin ve sağ bölmedeki **Güncelleştir** ' i seçerek paketi en son sürüme güncelleştirin.
    1. **Tarama** sekmesine geçin. **Xamarin. GooglePlayServices. Base**için arama yapın. Sonuç listesinden **Xamarin.GooglePlayServices.Base** öğesini seçin. Ardından **Yükle**’yi seçin.

        ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. **NuGet Package Manager** penceresinde **Xamarin.Firebase.Messaging** öğesini arayın. Sonuç listesinden **Xamarin.Firebase.Messaging** öğesini seçin. Ardından **Yükle**’yi seçin.
    7. Şimdi **Xamarin.Azure.NotificationHubs.Android** araması yapın. Sonuç listesinden **Xamarin.Azure.NotificationHubs.Android** öğesini seçin. Ardından **Yükle**’yi seçin.

### <a name="add-the-google-services-json-file"></a>Google Services JSON Dosyasını ekleme

1. Google Firebase konsolundan indirdiğiniz `google-services.json` dosyasını proje klasörüne kopyalayın.
2. Projeye @no__t ekleyin-0.
3. **Çözüm Gezgini** penceresinde `google-services.json` ' ı seçin.
4. **Özellikler** bölmesinde, Derleme Eylemini **GoogleServicesJson** olarak ayarlayın. **GoogleServicesJson** öğesini görmezseniz, Visual Studio’yu kapatın, yeniden başlatın, projeyi yeniden açın ve yeniden deneyin.

    ![GoogleServicesJson derleme eylemi](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Projenizdeki bildirim hub'larını ayarlama

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging ile kaydolma

1. @No__t-0 dosyasını açın ve aşağıdaki `<receiver>` öğelerini `<application>` öğesine ekleyin:

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

2. **Uygulama öğesinden önce** aşağıdaki deyimlerini ekleyin.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Android uygulamanız ve bildirim hub'ınız için aşağıdaki bilgileri toplayın:

   * **Dinleme bağlantı dizesi**: [Azure portalda] panoda **Bağlantı dizelerini görüntüle**'yi seçin. Bu değer için `DefaultListenSharedAccessSignature` bağlantı dizesini kopyalayın.
   * **Hub adı**: [Azure portalda] hub’ınızın adı. Örneğin, *mynotificationhub2*.
4. **Çözüm Gezgini** penceresinde **projenize**sağ tıklayın, **Ekle**' yi ve ardından **sınıf**' ı seçin.
5. Xamarin projeniz için `Constants.cs` sınıfı oluşturun ve sınıfında aşağıdaki sabit değerleri tanımlayın. Yer tutucuları değerleriniz ile değiştirin.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Aşağıdaki using deyimlerini `MainActivity.cs` ' a ekleyin:

    ```csharp
    using Android.Util;
    using Android.Gms.Common;
    ```

7. Aşağıdaki özellikleri MainActivity sınıfına ekleyin. ETIKET değişkeni, uygulama çalışırken bir uyarı iletişim kutusu göstermek için kullanılacaktır:

    ```csharp
    public const string TAG = "MainActivity";
    internal static readonly string CHANNEL_ID = "my_notification_channel";
    ```

8. Aşağıdaki yöntemi MainActivity sınıfına ekleyin. **Google Play hizmetleri** cihazda kullanılabilir olup olmadığını denetler.

    ```csharp
    public bool IsPlayServicesAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(TAG, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(TAG, "This device is not supported");
                Finish();
            }
            return false;
        }

        Log.Debug(TAG, "Google Play Services is available.");
        return true;
    }
    ```

9. Aşağıdaki yöntemi bir bildirim kanalı oluşturan MainActivity sınıfına ekleyin.

    ```csharp
    private void CreateNotificationChannel()
    {
        if (Build.VERSION.SdkInt < BuildVersionCodes.O)
        {
            // Notification channels are new in API 26 (and not a part of the
            // support library). There is no need to create a notification
            // channel on older versions of Android.
            return;
        }

        var channelName = CHANNEL_ID;
        var channelDescription = string.Empty;
        var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
        {
            Description = channelDescription
        };

        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.CreateNotificationChannel(channel);
    }
    ```

10. @No__t-0 ' da, `base.OnCreate(savedInstanceState)` ' den sonra aşağıdaki kodu `OnCreate` ' e ekleyin:

    ```csharp
    if (Intent.Extras != null)
    {
        foreach (var key in Intent.Extras.KeySet())
        {
            if(key!=null)
            {
                var value = Intent.Extras.GetString(key);
                Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
            }
        }
    }

    IsPlayServicesAvailable();
    CreateNotificationChannel();
    ```

15. Projenize `MyFirebaseMessagingService` adlı bir sınıf ekleyin. 
16. Aşağıdaki using deyimlerini `MyFirebaseMessagingService.cs` ' a ekleyin.

    ```csharp
    using Android.Util;
    using Firebase.Messaging;
    using Android.Support.V4.App;    
    using WindowsAzure.Messaging;
    ```

17. Sınıf bildirimindeki altına aşağıdakileri ekleyin ve sınıfınızın `FirebaseMessagingService` ' dan devralmasını sağlayın:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    ```

18. Alınan iletileri işlemek için aşağıdaki kodu `MyFirebaseMessagingService.cs` ' a ekleyin. 

    ```csharp
        const string TAG = "MyFirebaseMsgService";
        NotificationHub hub;
    
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if (message.GetNotification() != null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());
    
            }
        }
    
        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

19. FCM kayıt belirtecini almak ve Notification Hubs örneğine (hub) göndermek için, MyFirebaseMessagingService sınıfına aşağıdaki yöntemleri ekleyin. 

    ```csharp
        public override void OnNewToken(string token)
        {
            Log.Debug(TAG, "FCM token: " + token);
            SendRegistrationToServer(token);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                        Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
1. Projenizi **derleyin**.
1. Uygulamayı cihazınızda veya yüklü öykünücüde **çalıştırma**

## <a name="send-test-notification-from-the-azure-portal"></a>Azure portalından test bildirimi gönderme

[Azure portalda] **Test Gönderimi** seçeneğini kullanarak uygulamanızda bildirim alma testi gerçekleştirebilirsiniz. Bu, cihazınıza test amaçlı anında iletme bildirimi gönderir.

![Azure portalı - Test Gönderimi](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Anında iletme bildirimleri normalde, uyumlu bir kitaplık aracılığıyla Mobile Services veya ASP.NET gibi bir arka uç hizmetinde gönderilir. Arka uçta bir kitaplık yoksa, bildirim iletilerini göndermek için doğrudan REST API de kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, arka uca kayıtlı olan tüm Android cihazlarınıza yayın bildirimleri gönderdiniz. Belirli Android cihazlara nasıl anında iletme bildirimleri gönderileceğini öğrenmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Belirli cihazlara anında iletme bildirimleri gönderme](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Xamarin ile Visual Studio]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Mac için Visual Studio]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure portalda]: https://portal.azure.com/
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: https://msdn.microsoft.com/library/dn282661.aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android
