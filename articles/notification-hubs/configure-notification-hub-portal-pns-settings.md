---
title: Azure Notification Hubs anında iletme bildirimleri ayarlama | Microsoft Docs
description: Platform bildirim sistemi (PNS) ayarlarını kullanarak Azure portal Azure Notification Hubs ayarlamayı öğrenin.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 31d915cd44bcf60f3515eb1a84309980f45d40b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868312"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Hızlı başlangıç: Bildirim Hub 'ında anında iletme bildirimleri ayarlama

Azure Notification Hubs, kolayca kullanılan ve ölçeklendirilen bir gönderim altyapısı sağlar. Herhangi bir platforma (iOS, Android, Windows, Baidu) ve herhangi bir arka uçtan (bulutta veya şirket içi) bildirim göndermek için Notification Hubs kullanın. Daha fazla bilgi için bkz. [Azure Notification Hubs nedir?](notification-hubs-push-notification-overview.md).

Bu hızlı başlangıçta, birden çok platformda anında iletme bildirimleri ayarlamak için Notification Hubs platform bildirim sistemi (PNS) ayarlarını kullanacaksınız. Hızlı başlangıç, Azure portal alma adımlarını gösterir. [Google Firebase Cloud Messaging](?tabs=azure-cli#google-firebase-cloud-messaging-fcm) , Azure CLI kullanma yönergelerini içerir.

Henüz bir Bildirim Hub 'ı oluşturmadıysanız, şimdi bir tane oluşturun. Daha fazla bilgi için bkz. [Azure Portal Azure Notification Hub 'ı oluşturma](create-notification-hub-portal.md) veya Azure [CLI kullanarak bir Azure Notification Hub 'ı oluşturma](create-notification-hub-azure-cli.md).

## <a name="apple-push-notification-service"></a>Apple Anında İletilen Bildirim Servisi

Apple Anında İletilen Bildirim Servisi (APNS) ayarlamak için:

1. Azure portal, **Bildirim Hub** 'ı sayfasında, sol taraftaki menüden **Apple (APNs)** öğesini seçin.

1. **Kimlik doğrulama modu** Için, **sertifika** ya da **belirteç** seçin.

   a. **Sertifika** seçerseniz:
   * Dosya simgesini seçin ve ardından karşıya yüklemek istediğiniz *. p12* dosyasını seçin.
   * Bir parola girin.
   * **Korumalı alan** modunu seçin. Ya da uygulamanızı mağazadan satın alan kullanıcılara anında iletme bildirimleri göndermek için **Üretim** modu ' nu seçin.

     ![Azure portal bir APNS sertifika yapılandırmasının ekran görüntüsü](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. **Belirteç**' yi seçerseniz:

   * **Anahtar kimliği**, **paket KIMLIĞI**, **Takım Kimliği** ve **belirteç** değerlerini girin.
   * **Korumalı alan** modunu seçin. Ya da uygulamanızı mağazadan satın alan kullanıcılara anında iletme bildirimleri göndermek için **Üretim** modu ' nu seçin.

     ![Azure portal bir APNS belirteç yapılandırması ekran görüntüsü](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Daha fazla bilgi için bkz. [Azure Notification Hubs kullanarak iOS uygulamalarına anında iletme bildirimleri gönderme](ios-sdk-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Google FCM için anında iletme bildirimleri ayarlamak için:

1. Azure portal, **Bildirim Hub** 'ı sayfasında, soldaki menüden **Google (GCM/FCM)** öğesini seçin.
2. Daha önce kaydettiğiniz Google FCM projesi için **API anahtarını** yapıştırın.
3. **Kaydet**’i seçin.

   ![Google FCM için Notification Hubs yapılandırmayı gösteren ekran görüntüsü](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Bu adımları tamamladığınızda, bir uyarı bildirim hub 'ının başarıyla güncelleştirildiğini gösterir. **Kaydet** düğmesi devre dışı bırakıldı.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Google Firebase Cloud Messaging (FCM) projeniz için **API anahtarına** ihtiyacınız olacak.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.67 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

### <a name="set-up-push-notifications-for-google-fcm"></a>Google FCM için anında iletme bildirimleri ayarlama

1. Google API anahtarınızı Notification Hub 'ınıza eklemek için [az Notification-Hub Credential GCM Update](/cli/azure/notification-hub/credential/gcm#az_notification_hub_credential_gcm_update) komutunu kullanın.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Android uygulamasının Bildirim Hub 'ına bağlanmak için bir bağlantı dizesi olması gerekir.  Kullanılabilir erişim ilkelerini listelemek için [az Notification-Hub Authorization-Rule List](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list) komutunu kullanın.  Erişim ilkesi bağlantı dizelerini almak için [az Notification-Hub Authorization-Rule List-Keys](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list_keys) komutunu kullanın.  Birincil bağlantı dizesini doğrudan almak için parametresinde **primaryconnectionstring** veya **secondaryconnectionstring** öğesini belirtin `--query` .

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Android uygulamasına ileti göndermeyi sınamak için [az Notification-Hub test-Send](/cli/azure/notification-hub#az_notification_hub_test_send) komutunu kullanın.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

[Az Notification-Hub Credential](/cli/azure/notification-hub/credential) komutuyla diğer platformlar IÇIN Azure CLI başvuruları alın.

Android uygulamasına bildirim gönderme hakkında daha fazla bilgi için bkz. [Firebase kullanarak Android cihazlarına anında iletme bildirimleri gönderme](notification-hubs-android-push-notification-google-fcm-get-started.md).

---

## <a name="windows-push-notification-service"></a>Windows anında Iletme bildirimi hizmeti

Windows anında bildirim hizmeti 'ni (WNS) ayarlamak için:

1. Azure portal, **Bildirim Hub** 'ı sayfasında, sol menüden **Windows (WNS)** seçeneğini belirleyin.
2. **Paket SID 'si** ve **güvenlik anahtarı** için değerler girin.
3. **Kaydet**’i seçin.

   ![Paket SID ve güvenlik anahtarı kutularını gösteren ekran görüntüsü](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Bilgi için bkz. [Azure Notification Hubs kullanarak UWP uygulamalarına bildirim gönderme](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Windows Phone için Microsoft anında Iletme bildirimi hizmeti

Windows Phone için Microsoft anında Iletme bildirimi hizmeti 'ni (MPNS) ayarlamak için:

1. Azure portal, **Bildirim Hub** 'ı sayfasında sol menüden **Windows Phone (MPNS)** öğesini seçin.
1. Kimliği doğrulanmamış veya kimliği doğrulanmış anında iletme bildirimlerini etkinleştir:

   a. Kimliği doğrulanmamış anında iletme bildirimlerini etkinleştirmek için, **kimliği doğrulanmamış gönderimi etkinleştir**' i seçin  >  .

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

* [Azure Notification Hubs kullanarak iOS uygulamalarına anında iletme bildirimleri gönderme](ios-sdk-get-started.md)
* [Notification Hubs ve Google FCM kullanarak Android cihazlara bildirimler gönderme](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Bir Windows cihazında çalışan UWP uygulamasına bildirim gönderme](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [MPNS kullanarak Windows Phone 8 uygulamasına bildirimler gönderme](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Notification Hubs ve Baidu bulut anında iletme kullanarak bildirim gönderin](notification-hubs-baidu-china-android-notifications-get-started.md)
