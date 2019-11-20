---
title: Evrensel Windows Platformu (UWP) uygulamanıza anında iletme bildirimleri ekleme | Microsoft Docs
description: Evrensel Windows Platformu (UWP) uygulamanıza anında iletme bildirimleri göndermek için Azure App Service Mobile Apps ve Azure Notification Hubs kullanmayı öğrenin.
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 80723bc8e7685c66117e71d87cbf64710ab4507a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388623"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Windows uygulamanıza anında iletme bildirimleri ekleme

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış

Bu öğreticide, bir kayıt her eklendiğinde cihaza anında iletme bildirimi gönderilmesi için [Windows hızlı başlangıç](app-service-mobile-windows-store-dotnet-get-started.md) projesine anında iletme bildirimleri eklersiniz.

İndirilen hızlı başlangıç sunucusu projesini kullanmıyorsanız, anında iletme bildirimi uzantı paketine ihtiyacınız olacaktır. Daha fazla bilgi için bkz. [Azure için .net arka uç sunucu SDK 'sı Mobile Apps çalışma](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

## <a name="configure-hub"></a>Bildirim Hub 'ı yapılandırma

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Anında iletme bildirimleri için uygulamanızı kaydetme

Uygulamanızı Microsoft Store göndermeniz ve sonra sunucu projenizi, push göndermek için [Windows bildirim hizmetleri (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) ile tümleştirilecek şekilde yapılandırmanız gerekir.

1. Visual Studio Çözüm Gezgini, UWP uygulama projesine sağ tıklayın, **depola** > **uygulamayı mağaza ile ilişkilendir...** seçeneğini tıklayın.

    ![Uygulamayı Microsoft Store ilişkilendir](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. Sihirbazda, **İleri**' ye tıklayın, Microsoft hesabı ile oturum açın, **Yeni bir uygulama adı**ayırarak uygulamanız için bir ad yazın ve ardından **Ayır**' a tıklayın.
3. Uygulama kaydı başarıyla oluşturulduktan sonra, yeni uygulama adını seçin, **İleri**' ye tıklayın ve ardından **ilişkilendir**' e tıklayın. Bu, gerekli Microsoft Store kayıt bilgilerini uygulama bildirimine ekler.
4. [Uygulama kayıt portalı](https://apps.dev.microsoft.com/) ' na gidin ve Microsoft hesabı oturum açın. Önceki adımda ilişkilendirdiğiniz Windows Mağazası uygulaması ' na tıklayın.
5. Kayıt sayfasında, mobil uygulama arka ucunuzu yapılandırmak için kullanacağınız **uygulama gizli** dizileri ve **paket SID 'si**altındaki değeri bir yere göz önünde bulabilirsiniz.

    ![Uygulamayı Microsoft Store ilişkilendir](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Gizli anahtar ve paket SID'si önemli güvenlik kimlik bilgileridir. Bu değerleri kimseyle paylaşmayın veya uygulamanızla birlikte dağıtmayın. **Uygulama kimliği** , Microsoft hesabı kimlik doğrulamasını yapılandırmak için gizli anahtar ile birlikte kullanılır.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) Ayrıca, çekme BILDIRIMLERI için UWP uygulamalarını yapılandırma yönergelerine sahiptir.

## <a name="configure-the-backend-to-send-push-notifications"></a>Arka ucunu anında iletme bildirimleri gönderecek şekilde yapılandırma

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Sunucuyu anında iletme bildirimleri gönderecek şekilde Güncelleştir

Aşağıdaki yordamı kullanarak, arka uç proje türü olan @ no__t-0, [.net arka ucu](#dotnet) veya [Node. js arka ucu](#nodejs)ile eşleşen prosedürü kullanın.

### <a name="dotnet"></a>.NET arka uç projesi

1. Visual Studio 'da sunucu projesine sağ tıklayın ve **NuGet Paketlerini Yönet**' e tıklayın, Microsoft. Azure. notificationhub 'ları arayın ve ardından **Install**' a tıklayın. Bu, Notification Hubs istemci kitaplığını kurar.
2. **Denetleyiciler**' i genişletin, TodoItemController.cs açın ve aşağıdaki using deyimlerini ekleyin:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. **PostTodoItem** yönteminde, **ınsertasync**çağrısından sonra aşağıdaki kodu ekleyin:

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the Mobile App.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create the notification hub client.
    NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Define a WNS payload
    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                            + item.Text + @"</text></binding></visual></toast>";
    try
    {
        // Send the push notification.
        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    Bu kod, Bildirim Hub 'ına yeni bir öğe eklendikten sonra anında iletme bildirimi göndermesini söyler.

4. Sunucu projesini yeniden yayımlayın.

### <a name="nodejs"></a>Node. js arka uç projesi
1. Arka uç projenizi ayarlayın.
2. TodoItem. js dosyasındaki mevcut kodu aşağıdaki kodla değiştirin:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs
    logger.info('Running TodoItem.insert');

    // Define the WNS payload that contains the new item Text.
    var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                + context.item.text + "</text></binding></visual></toast>";

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a WNS native toast notification.
                context.push.wns.sendToast(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute()
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;
    ```

    Bu, yeni bir Todo öğesi eklendiğinde Item. Text öğesini içeren bir WNS bildirim bildirimi gönderir.

3. Yerel bilgisayarınızda dosyayı düzenlediğinizde, sunucu projesini yeniden yayımlayın.

## <a id="update-app"></a>Uygulamanıza anında iletme bildirimleri ekleyin
Ardından, uygulamanız başlangıç sırasında anında iletme bildirimleri için kaydolmanız gerekir. Kimlik doğrulamasını zaten etkinleştirdiyseniz, Kullanıcı tarafından anında iletme bildirimlerine kaydolmayı denemeden önce oturum açtığından emin olun.

1. **App.xaml.cs** proje dosyasını açın ve aşağıdaki `using` deyimlerini ekleyin:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Aynı dosyada, **uygulama** sınıfına aşağıdaki **ınitnotificationsasync** yöntem tanımını ekleyin:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        // Get a channel URI from WNS.
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        // Register the channel URI with Notification Hubs.
        await App.MobileService.GetPush().RegisterAsync(channel.Uri);
    }
    ```

    Bu kod, WNS adresinden uygulama için ChannelURI 'yi alır ve ardından bu Channelurı 'yi App Service mobil uygulamanıza kaydeder.

3. **App.xaml.cs**' de **onbaşlatılmış** olay işleyicisinin üst kısmında, **zaman uyumsuz** değiştiricisini Yöntem tanımına ekleyin ve aşağıdaki örnekte gösterildiği gibi New **initnotificationsasync** yöntemine aşağıdaki çağrıyı ekleyin:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Bu, uygulama her başlatıldığında kısa süreli ChannelURI 'nin kaydettirilmesini güvence altına alır.

4. UWP uygulama projenizi yeniden derleyin. Uygulamanız şimdi bildirim almaya hazırdır.

## <a id="test"></a>Uygulamanızda anında iletme bildirimlerini test etme

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Sonraki adımlar

Anında iletme bildirimleri hakkında daha fazla bilgi edinin:

* [Azure için yönetilen istemciyi kullanma Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Şablonlar, platformlar arası gönderimleri ve yerelleştirilmiş gönderimleri gönderme esnekliği sunar. Şablonların nasıl kaydedileceği hakkında bilgi edinin.
* [Anında iletme bildirimi sorunlarını tanılama](../notification-hubs/notification-hubs-push-notification-fixer.md) Bildirimlerin bırakılmış olmasının çeşitli nedenleri vardır veya cihazlar üzerinde bitmeyebilir. Bu konuda, anında iletme bildirimi hatalarının asıl nedenini çözümleme ve oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.

Aşağıdaki öğreticilerden birine devam etmeyi göz önünde bulundurun:

* [Uygulamanıza kimlik doğrulaması ekleme](app-service-mobile-windows-store-dotnet-get-started-users.md) Uygulamanızdaki kullanıcıların kimliklerini bir kimlik sağlayıcısı ile nasıl doğrulayacağınızı öğrenin.
* [Uygulamanız için çevrimdışı eşitlemeyi etkinleştirin](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Mobil uygulama arka ucu kullanarak uygulamanıza çevrimdışı destek eklemeyi öğrenin. Çevrimdışı eşitleme son kullanıcıların, ağ bağlantısı yokken dahi, mobil uygulama ile etkileşim kurmalarına &mdash;veri görüntüleme, ekleme ya da değiştirme&mdash; olanak tanır.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
