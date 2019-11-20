---
title: Azure Notification Hubs iOS 13 güncelleştirmeleri | Microsoft Docs
description: Azure Notification Hubs iOS 13 ile ilgili son değişiklikler hakkında bilgi edinin
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: e493ac10858aa374362d25f1467ded237b30ca44
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177411"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>iOS 13 için Azure Notification Hubs güncelleştirmeleri

Apple yakın zamanda genel gönderim hizmetinde bazı değişiklikler yaptı; değişiklikler genellikle iOS 13 ve Xcode sürümleriyle hizalanır. Bu makalede, bu değişikliklerin Azure Notification Hubs üzerindeki etkileri açıklanmaktadır.

## <a name="apns-push-payload-changes"></a>APNS itme yükü değişiklikleri

### <a name="apns-push-type"></a>APNS gönderim türü

Apple artık geliştiricilerin, APNS API 'sindeki yeni `apns-push-type` üst bilgisi aracılığıyla bildirimleri bir uyarı veya arka plan bildirimleri olarak belirlemesini gerektirir. [Apple belgelerine](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)göre: "bu üstbilginin değeri, bildirimin yükünün içeriğini doğru şekilde yansıtmalıdır. Bir uyumsuzluk varsa ya da gerekli sistemlerde üst bilgi yoksa APN’ler hata döndürebilir, bildirimi geç teslim edebilir veya tamamen bırakabilir."

Geliştiricilerin artık bu üstbilgiyi Azure Notification Hubs aracılığıyla bildirim gönderen uygulamalarda ayarlaması gerekir. Teknik bir sınırlama nedeniyle, müşteriler bu özniteliği içeren isteklerle APNS kimlik bilgileri için belirteç tabanlı kimlik doğrulaması kullanmalıdır. APNS kimlik bilgileriniz için sertifika tabanlı kimlik doğrulaması kullanıyorsanız, belirteç tabanlı kimlik doğrulaması ile geçiş yapmanız gerekir.

Aşağıdaki kod örnekleri, Azure Notification Hubs aracılığıyla gönderilen bildirim isteklerinde bu üstbilgi özniteliğinin nasıl ayarlanacağını göstermektedir.

#### <a name="template-notifications---net-sdk"></a>Şablon bildirimleri-.NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type",
"alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Yerel bildirimler-.NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Doğrudan REST çağrıları

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Bu geçiş sırasında size yardımcı olmak için, Azure Notification Hubs `apns-push-type` ayarlanmış olmayan bir bildirim algıladığında, hizmet bildirim isteğinden gönderim türünü bulur ve değeri otomatik olarak ayarlar. Gerekli üstbilgiyi ayarlamak için Azure Notification Hubs belirteç tabanlı kimlik doğrulaması kullanacak şekilde yapılandırmanız gerektiğini unutmayın; daha fazla bilgi için bkz. [APNs Için belirteç tabanlı (http/2) kimlik doğrulaması](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>APNS önceliği

Başka bir küçük değişiklik, ancak bildirim gönderen arka uç uygulamasında değişiklik gerektiren bir tane, arka plan bildirimleri için `apns-priority` üst bilgisinin 5 olarak ayarlanması gerekir. Birçok uygulama `apns-priority` üst bilgisini 10 ' a (hemen tesliyi gösterir) ayarlar veya bunu ayarlayıp varsayılan değeri (Ayrıca 10) alır.

Arka plan bildirimleri için bu değerin 10 olarak ayarlanmasına artık izin verilmez ve her istek için değeri ayarlamanız gerekir. Bu değer eksikse Apple, arka plan bildirimleri teslim etmez. Örnek:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK değişiklikleri

İOS geliştiricileri, bir arka uç uygulamasının cihaza bildirim göndermek için kullandığı anında iletme belirtecini ayıklamak üzere, `deviceToken` verilerin `description` özniteliğini göndererek, bir arka uç uygulamanın tarafından cihaza bildirim göndermek için kullanılır. Xcode 11 ile bu `description` özniteliği farklı bir biçimde değiştirilir. Geliştiricilerin bu öznitelik için kullandığı mevcut kod artık kopuk. Azure Notification Hubs SDK 'sını bu değişikliğe uyum sağlayacak şekilde güncelleştirdik, bu nedenle lütfen uygulamalarınız tarafından kullanılan SDK 'Yı [azure Notification Hubs IOS SDK 'sının](https://github.com/Azure/azure-notificationhubs-ios)2.0.4 veya daha yeni bir sürümüne güncelleştirin.
