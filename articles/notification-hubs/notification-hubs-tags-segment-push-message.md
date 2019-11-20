---
title: Yönlendirme ve etiket Ifadeleri
description: Bu konuda, Azure Notification Hub 'ları için Yönlendirme ve etiket ifadeleri açıklanmaktadır.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: 66388f139b63c63e1f0f8ee8ee063e0ddd0f9da5
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213043"
---
# <a name="routing-and-tag-expressions"></a>Yönlendirme ve etiket ifadeleri

## <a name="overview"></a>Genel Bakış

Etiket ifadeleri, Notification Hubs aracılığıyla anında iletme bildirimi gönderirken belirli cihaz kümelerini veya daha fazla özel kaydı hedeflemesini sağlar.

## <a name="targeting-specific-registrations"></a>Belirli kayıtları hedefleme

Belirli bildirim kayıtlarını hedeflemek için tek yol, etiketleri bunlarla ilişkilendirmekte ve bu etiketleri hedeflemelidir. [Kayıt yönetiminde](notification-hubs-push-notification-registration-management.md)anlatıldığı gibi, anında iletme bildirimleri almak için bir uygulamanın bir Bildirim Hub 'ına bir cihaz tanıtıcısı kaydetmesi gerekir. Bir Bildirim Hub 'ında kayıt oluşturulduktan sonra, uygulama arka ucu buna anında iletme bildirimleri gönderebilir. Uygulama arka ucu, belirli bir bildirimle hedeflenen kayıtları aşağıdaki yollarla seçebilir:

1. **Yayın**: Bildirim Hub 'ındaki tüm kayıtlar bildirimi alır.
2. **Etiket**: belirtilen etiketi içeren tüm kayıtlar bildirimi alır.
3. **Etiket ifadesi**: etiket kümesi belirtilen ifadeyle eşleşen tüm kayıtlar bildirimi alır.

## <a name="tags"></a>Tags

Etiket, alfasayısal karakterler ve şu alfasayısal olmayan karakterleri içeren, en çok 120 karakter uzunluğunda olabilir: ' _ ', ' @ ', ' # ', '. ', ': ', '-'. Aşağıdaki örnekte, belirli müzik grupları hakkında bildirim almak için kullanabileceğiniz bir uygulama gösterilmektedir. Bu senaryoda, bildirimleri yönlendirmeye yönelik basit bir yol, kayıtları aşağıdaki resimde olduğu gibi farklı bantları temsil eden etiketlerle etiketleyebilir:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Bu resimde, **Beatles** etiketli Ileti yalnızca **Beatles**etiketiyle kayıtlı olan tabletle ulaşır.

Etiketlere yönelik kayıtlar oluşturma hakkında daha fazla bilgi için bkz. [kayıt yönetimi](notification-hubs-push-notification-registration-management.md).

`Microsoft.Azure.NotificationHubs.NotificationHubClient` [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK 'sında sınıfının bildirim gönder yöntemlerini kullanarak etiketlere bildirim gönderebilirsiniz. Node. js veya anında Iletme bildirimleri REST API 'Lerini de kullanabilirsiniz.  SDK kullanarak bir örnek aşağıda verilmiştir.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

Etiketlerin önceden sağlanması gerekmez ve uygulamaya özgü birden çok kavrama başvurabilir. Örneğin, bu örnek uygulamanın kullanıcıları bantlara yorum yapabilir ve yalnızca en sevdiğiniz bantların açıklamalarını değil, kendi Arkadaşlarındaki açıklamaları değil, bunlara yorum eklemek istedikleri bant ne olursa olsun, bunlara ait olan açıklamaları almak isteyebilir. Aşağıdaki resimde bu senaryonun bir örneği gösterilmektedir:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Bu resimde, Gamze, Beatles güncelleştirmeleriyle ilgileniyor ve Bob, Wailers güncelleştirmeleriyle ilgileniyor. Emre Ayrıca, Charlie 'in açıklamalarıyla ve Wailers ile ilgileniyor. Beatles 'de Charlie 'in yorumu için bir bildirim gönderildiğinde, hem gamze hem de Bob bunu alır.

Etiketlerde birden çok endişeleri kodlayabilmeniz (örneğin, "band_Beatles" veya "follows_Charlie"), Etiketler basit dizelerdir ve değerleri olan özellikler değildir. Kayıt yalnızca belirli bir etiketin varlığı veya yokluğu ile eşleştirilir.

İlgi alanı gruplarına göndermek için etiketlerin nasıl kullanılacağına ilişkin bir adım adım öğretici için bkz. [son haberler](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Azure Notification Hubs, kayıt başına en fazla 60 etiketi destekler.

## <a name="using-tags-to-target-users"></a>Kullanıcıları hedeflemek için etiketleri kullanma

Etiketleri kullanmanın başka bir yolu da belirli bir kullanıcının tüm cihazlarını belirlemektir. Kayıtlar, aşağıdaki resimde olduğu gibi bir kullanıcı KIMLIĞI içeren bir etiketle etiketlenebilir:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Bu resimde etiketli UID: Gamze "uid: ali" olarak etiketlenen tüm kayıtlara ulaştığında Bu nedenle, Çiğdem 'in tüm cihazları.

## <a name="tag-expressions"></a>Etiket ifadeleri

Bir bildirimin tek bir etiketle değil tanımlanmış bir kayıt kümesini hedeflemesi gereken, ancak etiketlerindeki Boole ifadesi olan durumlar vardır.

Red Sox ve Cardınals arasındaki bir oyun hakkında Boston 'da herkese anımsatıcı gönderen bir spor uygulaması düşünün. İstemci uygulaması, ekiplerde ve konumda ilgi çekici Etiketler kaydederse, bu bildirimin Red Sox veya Cardınals ile ilgilendiği Boston 'daki herkese hedeflenmiş olması gerekir. Bu durum aşağıdaki Boolean ifadesiyle ifade edilebilir:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Etiket ifadeleri, ve (& &) veya (| |) gibi tüm Boole işleçlerini içerebilir (!). Parantez de içerebilir. Etiket ifadeleri yalnızca ORs içeriyorsa 20 etiketle sınırlıdır; Aksi takdirde 6 etiketle sınırlıdır.

SDK kullanarak etiket ifadeleriyle bildirim göndermek için bir örnek aşağıda verilmiştir.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
