---
title: Azure Notification Hubs kullanarak Windows Phone uygulamalarına anında iletme bildirimleri gönderme | Microsoft Docs
description: Bu öğreticide, bir Windows Phone 8 veya Windows Phone 8.1 Silverlight uygulamasına anında iletme bildirimleri göndermek için Azure Notification Hubs'ın nasıl kullanılacağını öğrenirsiniz.
services: notification-hubs
documentationcenter: windows
keywords: anında iletme bildirimi,anında iletme bildirimi,windows phone anında iletme
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 7f026dd5953dd233b0183d8ce7978f647fb8c6af
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213464"
---
# <a name="tutorial-push-notifications-to-windows-phone-apps-by-using-azure-notification-hubs"></a>Öğretici: Azure Notification Hubs kullanarak Windows Phone uygulamalara anında iletme bildirimleri gönderin

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Bu öğreticide, bir Windows Phone 8 veya Windows Phone 8.1 Silverlight uygulamalarına anında iletme bildirimleri göndermek için Azure Notification Hubs'ın nasıl kullanılacağı gösterilmektedir. Windows Phone 8.1’i (Silverlight olmayan) hedefliyorsanız bu öğreticinin [Windows Evrensel](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) sürümüne bakın.

Bu öğreticide, Microsoft Anında İletme Bildirimi Hizmeti'ni (MPNS) kullanarak anında iletme bildirimleri alan boş bir Windows Phone 8 uygulaması oluşturursunuz. Uygulamayı oluşturduktan sonra, uygulamanızı çalıştıran tüm cihazlara anında iletme bildirimleri yayımlamak için bildirim hub’ınızı kullanırsınız.

> [!NOTE]
> Notification Hubs Windows Phone SDK'sı, Windows Anında Bildirim Hizmeti'ni (WNS) Windows Phone 8.1 Silverlight uygulamaları ile kullanmayı desteklemez. WNS'yi (MPNS yerine) Windows Phone 8.1 Silverlight uygulamaları ile kullanmak için REST API'ler kullanan [Notification Hubs - Windows Phone Silverlight öğreticisi]'ni izleyin.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bildirim hub’ı oluşturma
> * Windows Phone uygulaması oluşturma
> * Bildirim test gönderimi

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı oluşturun](https://azure.microsoft.com/free/) .
* [Mobil geliştirme bileşenleri ile Visual Studio 2015 Express](https://www.visualstudio.com/vs/older-downloads/)

Bu öğreticiyi tamamlamak Windows Phone 8 uygulamalarına ilişkin diğer tüm Notification Hubs öğreticileri için önkoşuldur.

## <a name="create-your-notification-hub"></a>Bildirim hub'ınızı oluşturma

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-windows-phone-mpns-settings"></a>Windows Phone (MPNS) ayarlarını yapılandırma

1. **BİLDİRİM AYARLARI** bölümünden **Windows Phone (MPNS)** seçeneğini belirleyin.
2. **Kimlik doğrulama anında iletme bildirimini etkinleştir**’i seçin.
3. Araç çubuğunda **Kaydet**’i seçin.

    ![Azure portalı - Kimliği doğrulanmamış anında iletme bildirimlerini etkinleştirme](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

    Hub'ınız şimdi oluşturuldu ve Windows Phone için kimliği doğrulanmamış bildirim göndermek üzere yapılandırıldı.

    > [!NOTE]
    > Bu öğretici, MPNS'yi kimlik doğrulamasız modda kullanır. MPNS kimlik doğrulamasız mod, her bir kanala gönderebileceğiniz bildirimlerle ilgili kısıtlamalara sahiptir. Notification Hubs, sertifikanızı karşıya yüklemenize olanak sağlayarak [MPNS kimlik doğrulamalı mod](https://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx)'u destekler.

## <a name="create-a-windows-phone-application"></a>Windows Phone uygulaması oluşturma

Bu bölümde, bildirim hub’ınıza kendi kendine kaydolan bir Windows Phone uygulaması oluşturursunuz.

1. Visual Studio'da yeni bir Windows Phone 8 uygulaması oluşturun.

    ![Visual Studio - Yeni Proje - Windows Phone Uygulaması][13]

    Visual Studio 2013 Güncelleştirme 2 veya sonrasında, bunun yerine Windows Phone Silverlight uygulaması oluşturursunuz.

    ![Visual Studio - Yeni Proje - Boş Uygulama - Windows Phone Silverlight][11]
2. Visual Studio'da çözüme sağ tıklayın ve ardından **NuGet Paketlerini Yönet**'e tıklayın.
3. `WindowsAzure.Messaging.Managed` için arama yapın ve **Yükle**'ye tıklayın, ardından kullanım koşullarını kabul edin.

    ![Visual Studio - NuGet Paket Yöneticisi][20]
4. App.xaml.cs dosyasını açın ve aşağıdaki `using` deyimlerini ekleyin:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. Aşağıdaki kodu içindeki `Application_Launching` `App.xaml.cs`yönteminin üst kısmına ekleyin:

    ```csharp
    private void Application_Launching(object sender, LaunchingEventArgs e)
    {

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
    }
    ```

   > [!NOTE]
   > Değer `MyPushChannel` , [httpnotificationchannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) koleksiyonundaki mevcut bir kanalı aramak için kullanılan bir dizindir. Bu koleksiyonda bir tane bulunmuyorsa bu adla yeni bir giriş oluşturun.

    Hub 'ınızın adını ve önceki bölümde not ettiğiniz adlı `DefaultListenSharedAccessSignature` bağlantı dizesini ekleyin.
    Bu kod, MPNS'den uygulamanın kanal URI'sini alır ve ardından bu kanal URI'sini bildirim hub'ınıza kaydeder. Bu kod ayrıca uygulama her başlatıldığında kanal URI'sinin bildirim hub'ınıza kaydedilmesini garanti eder.

   > [!NOTE]
   > Bu öğretici cihaza bir bildirim gönderir. Bir kutucuk bildirimi gönderdiğinizde, bunun yerine kanalda `BindToShellTile` yöntemini çağırmanız gerekir. Hem bildirim hem de Kutucuk bildirimlerini desteklemek için hem hem `BindToShellTile` de `BindToShellToast`çağırın.

6. Çözüm Gezgini'nde **Özellikler**'i genişletin, `WMAppManifest.xml` dosyasını açın, **Özellikler** sekmesine tıklayın ve **ID_CAP_PUSH_NOTIFICATION** özelliğinin işaretlendiğinden emin olun. Uygulamanız artık anında iletme bildirimleri alabilir.

    ![Visual Studio - Windows Phone Uygulaması Özellikleri][14]
7. Uygulamayı çalıştırmak için `F5` tuşuna basın. Uygulamada bir kayıt iletisi görüntülenir.
8. Uygulamayı kapatın veya giriş sayfasına geçin.

   > [!NOTE]
   > Bildirim biçiminde bir anında iletme bildirimi almak için uygulamanın ön planda çalışmıyor olması gerekir.

## <a name="test-send-a-notification"></a>Bildirim test gönderimi

1. Azure portalında Genel Bakış sekmesine geçin.
2. **Test Gönderimi**’ni seçin.

    ![Test Gönderimi düğmesi](./media/notification-hubs-windows-phone-get-started/test-send-button.png)
3. **Test Gönderimi** penceresinde aşağıdaki adımları uygulayın:

    1. **Platformlar** için **Windows Phone**’u seçin.
    2. **Bildirim Türü** için **Bildirim**’i seçin.
    3. **Gönder**’i seçin
    4. Pencerenin en altındaki listede**sonucu** görürsünüz.

        ![Test Gönderimi penceresi](./media/notification-hubs-windows-phone-get-started/test-send-window.png)
4. Windows Phone öykünücüsünde veya Windows Phone üzerinde bildirim iletisini gördüğünüzü onaylayın.

    ![Windows Phone üzerinde bildirim](./media/notification-hubs-windows-phone-get-started/notification-on-windows-phone.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu basit örnekte, tüm Windows Phone 8 cihazlarınıza anında iletme bildirimleri yayımladınız. Belirli cihazlara nasıl anında iletme bildirimleri gönderileceğini öğrenmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Belirli cihazlara anında iletme bildirimleri gönderme](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png

<!-- URLs. -->
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: https://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: https://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: https://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs - Windows Phone Silverlight öğreticisi]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp
