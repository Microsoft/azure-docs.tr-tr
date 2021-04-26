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
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: matthewp
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: c97da77619a395a8e2839ea672fe7a78f0501c47
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575862"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Öğretici: Notification Hubs kullanarak Xamarin. Android uygulamalarına anında iletme bildirimleri gönderme

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Genel Bakış

Bu öğretici, bir Xamarin.Android uygulamasına anında iletme bildirimleri göndermek için Azure Notification Hubs'ın nasıl kullanılacağını size gösterir. Firebase Cloud Messaging (FCM) kullanarak anında iletme bildirimleri alan boş bir Xamarin.Android uygulaması oluşturursunuz. Uygulamanızı çalıştıran tüm cihazlara anında iletme bildirimleri yayımlamak için bildirim hub’ınızı kullanırsınız. Tamamlanan kodu [NotificationHubs uygulaması](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid) örneğinde bulabilirsiniz.

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

    ![Google G C M F C M seçeneği vurgulanmış ve kırmızı renkle özetlenen Azure portalındaki Bildirim Hub 'ının ekran görüntüsü.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Bildirim hub'ınız FCM ile birlikte çalışmak üzere yapılandırıldı. Ayrıca, uygulamanızı anında iletme bildirimleri alması ve anında iletme bildirimlerini göndermesi amacıyla kaydetmenizi sağlayan bağlantı dizelerine sahipsiniz.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Bir Xamarin. Android uygulaması oluşturma ve Bildirim Hub 'ına bağlama

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Visual Studio projesi oluşturma ve NuGet paketleri ekleme

> [!NOTE]
> Bu öğreticide belgelenen adımlar Visual Studio 2017 içindir. 

1. Visual Studio 'da **Dosya** menüsünü açın, **Yeni**' yi ve ardından **Proje**' yi seçin. **Yeni proje** penceresinde aşağıdaki adımları uygulayın:
    1. **Yüklü** ve **Visual C#**' yi genişletin ve ardından **Android**' e tıklayın.
    2. Listeden **Android uygulaması (Xamarin)** öğesini seçin.
    3. Proje için bir **ad** girin.
    4. Proje için bir **konum** seçin.
    5. **Tamam**'ı seçin

        ![Yeni Proje iletişim kutusu](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. **Yeni Android uygulaması** Iletişim kutusunda **boş uygulama**' yı seçin ve **Tamam**' ı seçin.

    ![Boş uygulama şablonunu vurgulayan ekran görüntüsü.](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. **Çözüm Gezgini** penceresinde **Özellikler**’i genişletin ve **AndroidManifest.xml** dosyasına tıklayın. Paket adını, Google Firebase Console’da projenize Firebase Cloud Messaging eklerken girdiğiniz paket adıyla eşleşecek şekilde güncelleştirin.

    ![GCM’de paket adı](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Aşağıdaki adımları izleyerek projenin hedef Android sürümünü **Android 10,0** olarak ayarlayın: 
    1. Projenize sağ tıklayın ve **Özellikler**' i seçin. 
    1. **Android sürümünü kullanarak derle: (hedef çerçeve)** alanı için **Android 10,0**' i seçin. 
    1. Hedef Framework 'ü değiştirmeye devam etmek için ileti kutusunda **Evet** ' i seçin.
1. Aşağıdaki adımları izleyerek, gerekli NuGet paketlerini projeye ekleyin:
    1. Projeye sağ tıklayın ve **NuGet Paketlerini Yönet...** seçeneğini belirleyin.
    1. **Yüklü** sekmesine geçin, **Xamarin.Android.support.Design**' yi seçin ve sağ bölmedeki **Güncelleştir** ' i seçerek paketi en son sürüme güncelleştirin.
    1. **Tarama** sekmesine geçin. **Xamarin. GooglePlayServices. Base** için arama yapın. Sonuç listesinden **Xamarin.GooglePlayServices.Base** öğesini seçin. Ardından, **Install**' ı seçin.

        ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. **NuGet Package Manager** penceresinde **Xamarin.Firebase.Messaging** öğesini arayın. Sonuç listesinden **Xamarin.Firebase.Messaging** öğesini seçin. Ardından, **Install**' ı seçin.
    7. Şimdi **Xamarin.Azure.NotificationHubs.Android** araması yapın. Sonuç listesinden **Xamarin.Azure.NotificationHubs.Android** öğesini seçin. Ardından, **Install**' ı seçin.

### <a name="add-the-google-services-json-file"></a>Google Services JSON Dosyasını ekleme

1. `google-services.json`Google Firebase konsolundan indirdiğiniz dosyayı proje klasörüne kopyalayın.
2. `google-services.json`Projeye ekleyin.
3. `google-services.json` **Çözüm Gezgini** penceresinde öğesini seçin.
4. **Özellikler** bölmesinde, Derleme Eylemini **GoogleServicesJson** olarak ayarlayın. **GoogleServicesJson** öğesini görmezseniz, Visual Studio’yu kapatın, yeniden başlatın, projeyi yeniden açın ve yeniden deneyin.

    ![GoogleServicesJson derleme eylemi](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Projenizdeki bildirim hub'larını ayarlama

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging ile kaydolma

1. Google Cloud Messaging 'ten Firebase 'e geçiş yapıyorsanız, projenizin `AndroidManifest.xml` dosyası geçmiş BIR GCM yapılandırması içerebilir ve bu durum bildirim çoğaltmasına neden olabilir. Dosyayı düzenleyin ve varsa bölümünde aşağıdaki satırları kaldırın `<application>` :

    ```xml
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
        android:exported="false" />
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
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

   * **Dinleme bağlantı dizesi**: [Azure portalındaki] panoda **Bağlantı dizelerini görüntüle**'yi seçin. `DefaultListenSharedAccessSignature`Bu değer için bağlantı dizesini kopyalayın.
   * **Hub adı**: [Azure portalındaki] hub’ınızın adı. Örneğin, *mynotificationhub2*.
4. **Çözüm Gezgini** penceresinde **projenize** sağ tıklayın, **Ekle**' yi ve ardından **sınıf**' ı seçin.
5. `Constants.cs`Xamarin projeniz için bir sınıf oluşturun ve sınıfında aşağıdaki sabit değerleri tanımlayın. Yer tutucuları değerleriniz ile değiştirin.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Aşağıdaki using deyimlerini öğesine ekleyin `MainActivity.cs` :

    ```csharp
    using WindowsAzure.Messaging.NotificationHubs;
    ```

7. Aşağıdaki özellikleri MainActivity sınıfına ekleyin:

    ```csharp
    internal static readonly string CHANNEL_ID = "my_notification_channel";

8. In `MainActivity.cs`, add the following code to `OnCreate` after `base.OnCreate(savedInstanceState)`:

    ```csharp
    // Listen for push notifications
    NotificationHub.SetListener(new AzureListener());

    // Start the SDK
    NotificationHub.Start(this.Application, HubName, ConnectionString);
    ```

9. Projenize adlı bir sınıf ekleyin `AzureListener` .
10. Aşağıdaki using deyimlerini öğesine ekleyin `AzureListener.cs` .

    ```csharp
    using Android.Content;
    using WindowsAzure.Messaging.NotificationHubs;
    ```

11. Aşağıdaki, sınıf bildirimidir ' yi ekleyin ve sınıfınızın kaynağından devralmasını ve uygulamasını `Java.Lang.Object` uygulamanız gerekir `INotificationListener` :

    ```csharp
    public class AzureListener : Java.Lang.Object, INotificationListener
    ```

12. `MyFirebaseMessagingService`Alınan iletileri işlemek için aşağıdaki kodu sınıfına ekleyin.

    ```csharp
        public void OnPushNotificationReceived(Context context, INotificationMessage message)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle(message.Title)
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(message.Body)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

1. Projenizi **derleyin**.
1. Uygulamayı cihazınızda veya yüklü öykünücüde **çalıştırma**

## <a name="send-test-notification-from-the-azure-portal"></a>Azure portalından test bildirimi gönderme

[Azure Portal]**Test Gönderimi** seçeneğini kullanarak uygulamanızda bildirim alma testi gerçekleştirebilirsiniz. Bu, cihazınıza test amaçlı anında iletme bildirimi gönderir.

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
[Xamarin ile Visual Studio]: /visualstudio/install/install-visual-studio
[Mac için Visual Studio]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure portalı]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android
