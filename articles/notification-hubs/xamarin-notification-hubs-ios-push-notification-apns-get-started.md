---
title: Azure Notification Hubs kullanarak Xamarin 'e anında iletme bildirimleri gönderin | Microsoft Docs
description: Bu öğreticide, bir Xamarin.iOS uygulamasına anında iletme bildirimleri göndermek için Azure Notification Hubs'ın nasıl kullanılacağını öğrenirsiniz.
services: notification-hubs
keywords: ios anında iletme bildirimleri,anında iletme iletileri,anında iletme bildirimleri,anında iletme iletisi
documentationcenter: xamarin
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: ff1e5edad05ebd7157f71ad2e099ea88905be4f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221145"
---
# <a name="tutorial-send-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Öğretici: Azure Notification Hubs kullanarak Xamarin. iOS uygulamalarına anında iletme bildirimleri gönderme

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Genel Bakış

Bu öğretici, bir iOS uygulamasına anında iletme bildirimleri göndermek için Azure Notification Hubs'ın nasıl kullanılacağını size gösterir. [Apple Anında İletilen Bildirim Servisi'ni (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) kullanarak anında iletme bildirimleri alan boş bir Xamarin.iOS uygulaması oluşturursunuz.

İşiniz bittiğinde, uygulamanızı çalıştıran tüm cihazlara anında iletme bildirimleri yayımlamak için bildirim hub’ınızı kullanabilirsiniz. Tamamlanan kodu [NotificationHubs uygulaması][GitHub] örneğinde bulabilirsiniz.

Bu öğreticide, aşağıdaki görevleri gerçekleştirmek için kod oluşturur/güncelleştirirsiniz:

> [!div class="checklist"]
> * Sertifika imzalama isteği dosyası oluşturma
> * Anında iletme bildirimleri için uygulamanızı kaydetme
> * Uygulama için bir sağlama profili oluşturun
> * iOS anında iletme bildirimleri için bildirim hub’ınızı yapılandırma
> * Test amaçlı anında iletme bildirimleri gönderme

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* [Xcode][Install Xcode]'un en son sürümü
* iOS 10 (veya sonraki bir sürümü) uyumlu bir cihaz
* [Apple Developer Program](https://developer.apple.com/programs/) üyeliği.
* [Mac için Visual Studio]
  
  > [!NOTE]
  > iOS anında iletme bildirimlerinin yapılandırma gereksinimleri nedeniyle, örnek uygulamayı benzetici yerine fiziksel bir iOS cihazında (iPhone veya iPad) dağıtmanız ve test etmeniz gerekir.

Bu öğreticiyi tamamlamak Xamarin.iOS uygulamalarına ilişkin diğer tüm Notification Hubs öğreticileri için önkoşuldur.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>Uygulamanızı bildirim hub'ına bağlama

### <a name="create-a-new-project"></a>Yeni proje oluşturma

1. Visual Studio'da yeni bir iOS projesi oluşturup **Tek Görünüm Uygulaması** şablonunu seçin ve **İleri**'ye tıklayın

     ![Visual Studio - Uygulama Türünü Seçme][31]

2. Uygulama adınızı ve kuruluş tanımlarınızı girip **İleri**' ye ve ardından **Oluştur** ' a tıklayın.

3. Çözüm görünümünde **Kimlik** bölümündeki *Info.plist* dosyasına çift tıklayarak Paket Tanımlayıcısının sağlama profili oluştururken kullandığınızla eşleştiğinden emin olun. **İmzalama** bölümünde **Ekip** altında Geliştirici hesabınızın seçili olduğundan, "İmzalamayı otomatik olarak yönet" seçeneğinin belirlendiğinden ve İmza Sertifikası ile Sağlama Profili bilgilerinizin otomatik olarak seçildiğinden emin olun.

    ![Visual Studio- iOS Uygulaması Yapılandırması][32]

4. Çözüm görünümünden öğesine çift tıklayın `Entitlements.plist` ve **anında Iletme bildirimlerini etkinleştir** ' in işaretli olduğundan emin olun.

    ![Visual Studio- iOS Destek Hakları Yapılandırması][33]

5. Azure Messaging paketini ekleyin. Çözüm görünümünde projeye sağ tıklayın ve Add   >  **NuGet paketleri** Ekle ' yi seçin. **Xamarin.Azure.NotificationHubs.iOS** araması yapıp projeyi pakete ekleyin.

6. Sınıfınıza yeni bir dosya ekleyin, bu dosyayı adlandırın `Constants.cs` ve aşağıdaki değişkenleri ekleyin ve dize değişmez yer tutucularını `hubname` ve `DefaultListenSharedAccessSignature` daha önce belirtilen ile değiştirin.

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. İçinde `AppDelegate.cs` , aşağıdaki using ifadesini ekleyin:

    ```csharp
    using WindowsAzure.Messaging.NotificationHubs;
    using UserNotifications
    ```

8. İçinde uygulamasının bir uygulamasını oluşturun `MSNotificationHubDelegate` `AppDelegate.cs` :

    ```csharp
    public class AzureNotificationHubListener : MSNotificationHubDelegate
    {
        public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
        {

        }
    }
    ```

9. `AppDelegate.cs`' De, `FinishedLaunching()` aşağıdaki kodla eşleşecek şekilde güncelleştirin:

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // Set the Message listener
        MSNotificationHub.SetDelegate(new AzureNotificationHubListener());
        
        // Start the SDK
        MSNotificationHub.Start(ListenConnectionString, NotificationHubName);

        return true;
    }
    ```

10. İçinde `AppDelegate.cs` , `DidReceivePushNotification` sınıfı için yöntemini uygulayın `AzureNotificationHubListener` :

    ```csharp
    public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
    {
        // This sample assumes { aps: { alert: { title: "Hello", body: "World" } } }
        var alertTitle = message.Title ?? "Notification";
        var alertBody = message.Body;

        var myAlert = UIAlertController.Create(alertTitle, alertBody, UIAlertControllerStyle.Alert);
        myAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(myAlert, true, null);
    }
    ```

11. Cihazınızda uygulamayı çalıştırın.

## <a name="send-test-push-notifications"></a>Test amaçlı anında iletme bildirimleri gönderme

[Azure Portal]*Test Gönderimi* seçeneğini kullanarak uygulamanızda bildirim alma testi gerçekleştirebilirsiniz. Bu, cihazınıza test amaçlı anında iletme bildirimi gönderir.

![Azure portalı - Test Gönderimi][30]

Anında iletme bildirimleri normal olarak, uyumlu bir kitaplık kullanılarak Mobile Apps veya ASP.NET gibi bir arka uç hizmetine gönderilir. Arka ucunuz için uygun bir kitaplık yoksa bildirim iletileri göndermek için doğrudan REST API de kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, arka uca kayıtlı olan tüm iOS cihazlarınıza yayın bildirimleri gönderdiniz. Belirli iOS cihazlarına nasıl anında iletme bildirimleri gönderileceğini öğrenmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Belirli cihazlara anında iletme bildirimleri gönderme](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Mac için Visual Studio]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure portalı]: https://portal.azure.com
