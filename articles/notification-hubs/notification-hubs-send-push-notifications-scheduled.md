---
title: Zamanlanmış bildirimler gönderme | Microsoft Docs
description: Bu konuda Azure Notification Hubs ile zamanlanmış bildirimlerin kullanılması açıklanmaktadır.
services: notification-hubs
documentationcenter: .net
keywords: anında iletme bildirimleri, anında iletme bildirimi, anında iletme bildirimleri zamanlama
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ef5eedaa903480ae670f9bc48d0af89744a99d22
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213008"
---
# <a name="how-to-send-scheduled-notifications"></a>Nasıl Yapılır: Planlanmış bildirimler gönderme

Gelecekte bir noktada bildirim göndermek istediğiniz bir senaryonuz varsa, ancak bildirimi göndermek için arka uç kodunuzu uyandırmanın kolay bir yolu yoktur. Standart katman Bildirim Hub 'ları, gelecekteki yedi güne kadar bildirim zamanlamanıza olanak tanıyan bir özelliği destekler.


## <a name="schedule-your-notifications"></a>Bildirimlerinizi zamanlayın
Bir bildirim gönderilirken, aşağıdaki örnekte gösterildiği gibi Notification Hubs SDK içindeki [ `ScheduledNotification` sınıfını](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) kullanmanız yeterlidir:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Zamanlanan bildirimleri iptal et
Ayrıca, önceden zamanlanmış bir bildirimi Notificationıd kullanarak iptal edebilirsiniz:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Gönderebilmeniz için zamanlanmış bildirim sayısı sınırı yoktur.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki öğreticilere bakın:

 - [Tüm kayıtlı cihazlara anında iletme bildirimleri gönderme](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Belirli cihazlara anında iletme bildirimleri gönderme](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Yerelleştirilmiş anında iletme bildirimleri gönderme](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Belirli kullanıcılara anında iletme bildirimleri gönderme](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Konum temelli anında iletme bildirimleri gönderme](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
