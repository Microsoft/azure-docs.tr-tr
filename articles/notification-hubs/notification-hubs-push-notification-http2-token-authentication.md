---
title: Azure Notification Hubs 'de APNS için belirteç tabanlı (HTTP/2) kimlik doğrulaması | Microsoft Docs
description: APNS için yeni belirteç kimlik doğrulamasını nasıl kullanacağınızı öğrenin.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f8de6389a04448579672b84e91f0bb4dd0f4ce2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460474"
---
# <a name="token-based-http2-authentication-for-apns"></a>APNS için belirteç tabanlı (HTTP/2) kimlik doğrulaması

## <a name="overview"></a>Genel Bakış

Bu makalede, yeni APNS HTTP/2 protokolünün belirteç tabanlı kimlik doğrulamasıyla nasıl kullanılacağı açıklanmaktadır.

Yeni Protokolü kullanmanın başlıca avantajları şunlardır:

* Belirteç oluşturma oldukça basittir (sertifikalarla karşılaştırıldığında)
* Daha fazla sona erme tarihi yok: kimlik doğrulama belirteçlerinizin ve bunların iptallerinin denetimi
* Yük artık en fazla 4 KB olabilir
* Zaman uyumlu geri bildirim
* Apple 'ın en son protokolde kullanıyorsunuz: sertifikalar hala kullanımdan kaldırma için işaretlenen ikili protokolü kullanır

Bu yeni mekanizmanın kullanılması iki adımda gerçekleştirilebilir:

* Apple Geliştirici Hesap portalından gerekli bilgileri alın.
* Bildirim Hub 'ınızı yeni bilgilerle yapılandırın.

Notification Hubs artık APNS ile yeni kimlik doğrulama sistemini kullanacak şekilde ayarlanmıştır.

APNS için sertifika kimlik bilgilerini kullanarak geçiş yaptıysanız, belirteç özellikleri sistemimizde sertifikanızın üzerine yazar, ancak uygulamanız bildirimleri sorunsuz bir şekilde almaya devam eder.

## <a name="obtaining-authentication-information-from-apple"></a>Apple 'dan kimlik doğrulama bilgileri alma

Belirteç tabanlı kimlik doğrulamasını etkinleştirmek için Apple geliştirici hesabınızdan aşağıdaki özellikler gereklidir:

### <a name="key-identifier"></a>Anahtar tanımlayıcı

Anahtar tanımlayıcısı, Apple geliştirici hesabınızdaki **Sertifikalar, tanımlayıcılar & profiller** altında bulunan **anahtarlar** sayfasından elde edilebilir:

![Sertifikalar](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![Tanımlayıcılar](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Uygulama tanımlayıcısı ve uygulama adı

Uygulama adı ve tanımlayıcı, geliştirici hesabındaki **Sertifikalar, tanımlayıcılar & profiller** sayfasında da kullanılabilir:

![Sertifikalar ve kimlikler](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>.NET SDK veya Azure portal aracılığıyla yapılandırın

Hub 'ınızı, [en son istemci SDK 'sını](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)kullanarak veya Azure Portal, belirteç tabanlı kimlik doğrulaması kullanacak şekilde yapılandırabilirsiniz. Portalda belirteç tabanlı kimlik doğrulamasını etkinleştirmek için Azure portal oturum açın ve Bildirim Hub 'ının **ayarlar > Apple (APNs)** paneline gidin. Hub 'ınızı tüm ilgili belirteç özellikleriyle güncelleştirmek için **kimlik doğrulama modu** özelliğinden **belirteç** ' ı seçin.

![Belirteci Yapılandır](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Apple geliştirici hesabınızdan aldığınız özellikleri girin.
* Uygulama modunu (**Üretim** veya **korumalı alan**) seçin.
* APNS kimlik bilgilerinizi güncelleştirmek için **Kaydet** düğmesine tıklayın.

Belirteç tabanlı kimlik bilgileri aşağıdaki alanlardan oluşur:

* **Anahtar kimliği**: Apple geliştirici portalında oluşturulan özel anahtarın tanımlayıcısı; Örneğin, `2USFGKSKLT` .
* **EKIP kimliği**: "önek" veya "uygulama öneki" olarak da bilinir. Bu, Apple geliştirici portalındaki kuruluşun tanımlayıcısıdır; Örneğin, `S4V3D7CHJR` .
* **Paket kimliği**: "uygulama kimliği" olarak da adlandırılır. Bu, uygulamanın paket tanıtıcısıdır; Örneğin, `com.example.myapp` . Yalnızca bir uygulama için tek bir anahtar kullanabileceğinizi unutmayın. Bu değer `apns-topic` bir bildirim gönderilirken http üstbilgisiyle eşlenir ve belirli bir uygulamayı hedeflemek için kullanılır. Açıkça değer ayarlayamazsınız `apns-topic` .
* **Belirteç**: "Key" veya "Private Key" olarak da bilinir. Bu, Apple geliştirici portalında oluşturulan. P8 dosyasından elde edilir. Anahtarın APNS etkin olmalıdır (anahtar oluşturulurken Apple Geliştirici Portalında seçilir). Değer, NH portalına/API 'sine girdiğinizde PEM üst bilgisi/alt bilgisinin bu kümeden çıkarılır.
* **Uç nokta**: bu, Notification Hubs portalı dikey PENCERESINDE ve API 'deki bir dize alanından bir geçiş yapar. Geçerli değerler veya ' dir `https://api.development.push.apple.com:443/3/device` `https://api.sandbox.push.apple.com:443/3/device` . Notification Hubs, bildirim göndermek için üretim veya Sandbox ortamı için bu değeri kullanır. Bu, `aps-environment` uygulamadaki yetkilendirmeler ile eşleşmelidir; Aksi takdirde, oluşturulan APNs cihaz belirteçleri ortamla eşleşmez ve bildirimler gönderilemez.

Doğru kullanımı gösteren bir kod örneği aşağıda verilmiştir:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure portalında Azure bildirim merkezi oluşturma](create-notification-hub-portal.md)
* [Azure portal bir Bildirim Hub 'ı yapılandırma](create-notification-hub-portal.md)
