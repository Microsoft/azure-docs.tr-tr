---
title: Azure Notification Hubs anında iletme bildirimleri ayarlama | Microsoft Docs
description: Platform bildirim sistemi (PNS) ayarlarını kullanarak Azure portal Azure Notification Hubs ayarlamayı öğrenin.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 9a17b96da376843c07a4324856bf8943aa385cac
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212458"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Azure portal Bildirim Hub 'ında anında iletme bildirimleri ayarlama

Azure Notification Hubs, kolayca kullanılan ve ölçeklendirilen bir gönderim altyapısı sağlar. Herhangi bir platforma (iOS, Android, Windows, Baidu) ve herhangi bir arka uçtan (bulutta veya şirket içi) bildirim göndermek için Notification Hubs kullanın. Daha fazla bilgi için bkz. [Azure Notification Hubs nedir?](notification-hubs-push-notification-overview.md).

Bu hızlı başlangıçta, birden çok platformda anında iletme bildirimleri ayarlamak için Notification Hubs platform bildirim sistemi (PNS) ayarlarını kullanacaksınız. Hızlı başlangıç, Azure portal alma adımlarını gösterir.

Henüz bir Bildirim Hub 'ı oluşturmadıysanız, şimdi bir tane oluşturun. Daha fazla bilgi için [Azure Portal Azure Notification Hub 'ı oluşturma](create-notification-hub-portal.md)konusuna bakın. 

## <a name="apple-push-notification-service"></a>Apple Anında İletilen Bildirim Servisi

Apple Anında İletilen Bildirim Servisi (APNS) ayarlamak için:

1. Azure portal, **Bildirim Hub** 'ı sayfasında, sol taraftaki menüden **Apple (APNs)** öğesini seçin.

1. **Kimlik doğrulama modu**Için, **sertifika** ya da **belirteç**seçin.

   a. **Sertifika**seçerseniz:
   * Dosya simgesini seçin ve ardından karşıya yüklemek istediğiniz *. p12* dosyasını seçin.
   * Bir parola girin.
   * **Korumalı alan** modunu seçin. Ya da uygulamanızı mağazadan satın alan kullanıcılara anında iletme bildirimleri göndermek için **Üretim** modu ' nu seçin.

     ![Azure portal bir APNS sertifika yapılandırmasının ekran görüntüsü](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. **Belirteç**' yi seçerseniz:

   * **Anahtar kimliği**, **paket KIMLIĞI**, **Takım Kimliği**ve **belirteç**değerlerini girin.
   * **Korumalı alan** modunu seçin. Ya da uygulamanızı mağazadan satın alan kullanıcılara anında iletme bildirimleri göndermek için **Üretim** modu ' nu seçin.

     ![Azure portal bir APNS belirteç yapılandırması ekran görüntüsü](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Daha fazla bilgi için bkz. [Azure Notification Hubs kullanarak iOS 'A anında iletme bildirimleri gönderme](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Google Firebase bulutu Iletisi

Google Firebase Cloud Messaging (FCM) için anında iletme bildirimleri ayarlamak için:

1. Azure portal, **Bildirim Hub** 'ı sayfasında, soldaki menüden **Google (GCM/FCM)** öğesini seçin. 
2. Daha önce kaydettiğiniz FCM projesi için **API anahtarını** yapıştırın. 
3. **Kaydet**’i seçin. 

   ![Google FCM için Notification Hubs yapılandırmayı gösteren ekran görüntüsü](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Bu adımları tamamladığınızda, bir uyarı bildirim hub 'ının başarıyla güncelleştirildiğini gösterir. **Kaydet** düğmesi devre dışı bırakıldı. 

Daha fazla bilgi için bkz. [Notification Hubs ve Google FCM kullanarak Android cihazlara anında iletme bildirimleri gönderme](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Windows anında Iletme bildirimi hizmeti

Windows anında bildirim hizmeti 'ni (WNS) ayarlamak için:

1. Azure portal, **Bildirim Hub** 'ı sayfasında, sol menüden **Windows (WNS)** seçeneğini belirleyin.
2. **Paket SID 'si** ve **güvenlik anahtarı**için değerler girin.
3. **Kaydet**’i seçin.

   ![Paket SID ve güvenlik anahtarı kutularını gösteren ekran görüntüsü](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Bilgi için bkz. [Azure Notification Hubs kullanarak UWP uygulamalarına bildirim gönderme](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Windows Phone için Microsoft anında Iletme bildirimi hizmeti

Windows Phone için Microsoft anında Iletme bildirimi hizmeti 'ni (MPNS) ayarlamak için: 

1. Azure portal, **Bildirim Hub** 'ı sayfasında sol menüden **Windows Phone (MPNS)** öğesini seçin.
1. Kimliği doğrulanmamış veya kimliği doğrulanmış anında iletme bildirimlerini etkinleştir:

   a. Kimliği doğrulanmamış**anında iletme bildirimlerini**etkinleştirmek için, **kimliği doğrulanmamış gönderimi** > etkinleştir ' i seçin.

      ![Kimliği doğrulanmamış anında iletme bildirimlerinin nasıl etkinleştirileceğini gösteren ekran görüntüsü](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Kimliği doğrulanmış anında iletme bildirimlerini etkinleştirmek için:
      * Araç çubuğunda **sertifikayı karşıya yükle**' yi seçin.
      * Dosya simgesini seçin ve ardından sertifika dosyasını seçin.
      * Sertifika için parola belirtin.
      * **Tamam**’ı seçin.
      * **Windows Phone (MPNS)** sayfasında **Kaydet**' i seçin.

Daha fazla bilgi için bkz. [Notification Hubs kullanarak Windows Phone uygulamalara anında iletme bildirimleri gönderme](notification-hubs-windows-mobile-push-notifications-mpns.md).
      

## <a name="baidu-android-china"></a>Baidu (Android China)

Baidu için anında iletme bildirimleri ayarlamak için:

1. Azure portal, **Bildirim Hub** 'ı sayfasında sol menüden **Baidu (Android China)** öğesini seçin. 
2. Baidu bulut anında iletme projesindeki Baidu konsolundan edindiğiniz **API anahtarını** girin. 
3. Baidu bulut anında iletme projesindeki Baidu konsolundan edindiğiniz **gizli anahtarı** girin. 
4. **Kaydet**’i seçin. 

    ![Anında iletme bildirimleri için Baidu (Android China) yapılandırmasını gösteren Notification Hubs ekran görüntüsü](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Bu adımları tamamladığınızda, bir uyarı bildirim hub 'ının başarıyla güncelleştirildiğini gösterir. **Kaydet** düğmesi devre dışı bırakıldı. 

Daha fazla bilgi için bkz. [Baidu kullanarak Notification Hubs kullanmaya başlama](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta, Azure portal bir Bildirim Hub 'ı için platform bildirim sistemi ayarlarını yapılandırmayı öğrendiniz. 

Çeşitli platformlara bildirim gönderme hakkında daha fazla bilgi edinmek için şu öğreticilere bakın:

- [Notification Hubs ve APNS kullanarak iOS cihazlarına anında iletme bildirimleri gönderme](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Notification Hubs ve Google FCM kullanarak Android cihazlarına anında iletme bildirimleri gönderin](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Windows cihazında çalışan bir UWP uygulamasına anında iletme bildirimleri gönderme](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [MPNS kullanarak Windows Phone 8 uygulamasına anında iletme bildirimleri gönderme](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Notification Hubs ve Baidu bulut anında iletme kullanarak anında iletme bildirimleri](notification-hubs-baidu-china-android-notifications-get-started.md)
