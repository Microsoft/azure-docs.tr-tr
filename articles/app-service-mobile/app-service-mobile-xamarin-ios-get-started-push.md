---
title: Azure App Service ile Xamarin. iOS uygulamanıza anında iletme bildirimleri ekleme
description: Xamarin. iOS uygulamanıza anında iletme bildirimleri göndermek için Azure App Service kullanmayı öğrenin
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 5f968de25226528cb7aa016a4c301d07e53f2695
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388382"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Xamarin. iOS uygulamanıza anında iletme bildirimleri ekleme

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış

Bu öğreticide, bir kayıt her eklendiğinde cihaza anında iletme bildirimi gönderilmesi için [Xamarin. iOS hızlı başlangıç](app-service-mobile-xamarin-ios-get-started.md) projesine anında iletme bildirimleri eklersiniz.

İndirilen hızlı başlangıç sunucusu projesini kullanmıyorsanız, anında iletme bildirimi uzantı paketine ihtiyacınız olacaktır. Daha fazla bilgi için bkz. [Azure için .net arka uç sunucu SDK 'sı Mobile Apps çalışma](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

## <a name="prerequisites"></a>Önkoşullar

* [Xamarin. iOS hızlı başlangıç](app-service-mobile-xamarin-ios-get-started.md) öğreticisini doldurun.
* Fiziksel bir iOS cihazı. Anında iletme bildirimleri iOS simülatörü tarafından desteklenmez.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Apple 'ın Geliştirici Portalında anında iletme bildirimleri için uygulamayı kaydetme

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Mobil uygulamanızı anında iletme bildirimleri gönderecek şekilde yapılandırma

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Sunucu projesini anında iletme bildirimleri gönderecek şekilde Güncelleştir

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Xamarin. iOS projenizi yapılandırma

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Uygulamanıza anında iletme bildirimleri ekleyin

1. **Qstodoservice**Içinde, **appdelegate** 'in Mobil istemciyi edinebilmesi için aşağıdaki özelliği ekleyin:

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Aşağıdaki `using` ifadesini **AppDelegate.cs** dosyasının en üstüne ekleyin.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. **Appdelegate**Içinde, **sonlandırmandbaşlatırken** olayını geçersiz kılın:

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. Aynı dosyada `RegisteredForRemoteNotifications` olayını geçersiz kılın. Bu kodda, sunucu tarafından desteklenen tüm platformlarda gönderilecek basit bir şablon bildirimi için kayıt yaptırmış olursunuz.

    Notification Hubs olan şablonlar hakkında daha fazla bilgi için bkz. [Şablonlar](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Ardından, **DidReceivedRemoteNotification** olayını geçersiz kılın:

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

Uygulamanız artık anında iletme bildirimlerini destekleyecek şekilde güncelleştirildi.

## <a name="test"></a>Uygulamanızda anında iletme bildirimlerini test etme

1. **Çalıştır** düğmesine basarak projeyi oluşturun ve uygulamayı iOS özellikli bir cihazda başlatın, sonra anında iletme bildirimlerini kabul etmek için **Tamam** ' ı tıklatın.

   > [!NOTE]
   > Uygulamanızın anında iletme bildirimlerini kabul etmelisiniz. Bu istek yalnızca uygulamanın ilk kez çalıştırıldığı zaman gerçekleşir.

2. Uygulamada bir görev yazın ve ardından artı ( **+** ) simgesine tıklayın.
3. Bildirimin alındığını doğrulayın ve sonra bildirimi kapatmak için **Tamam** ' ı tıklatın.
4. 2\. adımı tekrarlayın ve uygulamayı hemen kapatın, sonra bir bildirimin gösterildiğini doğrulayın.

Bu öğreticiyi başarıyla tamamladınız.
