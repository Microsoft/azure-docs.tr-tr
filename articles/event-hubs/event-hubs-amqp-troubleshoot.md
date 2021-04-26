---
title: Azure Event Hubs AMQP hatalarıyla ilgili sorunları giderin | Microsoft Docs
description: Azure Event Hubs kullanırken alabileceğiniz AMQP hatalarının bir listesini ve bu hataların nedenlerini sağlar.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 51b96792f6921bae9364212c6e5f9c987ff05e2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466074"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>Azure Event Hubs AMQP hataları
Bu makalede, AMQP 'yi Azure Event Hubs kullanırken aldığınız bazı hatalar sunulmaktadır. Bu hizmetler hizmetin tüm standart davranışlarından oluşur. Bağlantıyı/bağlantıyı otomatik olarak yeniden oluşturan bağlantı/bağlantı üzerinde gönderme/alma çağrıları yaparak bu onlardan kaçınabilirsiniz.

## <a name="link-is-closed"></a>Bağlantı kapalı 
AMQP bağlantısı ve bağlantısı etkin olduğunda, ancak bu bağlantı 30 dakika boyunca kullanılarak hiçbir çağrı (örneğin, gönderme veya alma) yapıldığında aşağıdaki hatayı görürsünüz. Bu nedenle bağlantı kapatılır. Bağlantı hala açık.

"AMQP: link: Detach-Zorlanmış: ' G2:7223832: user.tenant0.cud_00000000000-0000-0000-0000-00000000000000 ' bağlantısı, yayımcıda (link164614) oluşan hatalar nedeniyle aracı tarafından ayrılmaya zorlanmıştır. Ayırma kaynağı: AmqpMessagePublisher. ıdlemertimeout: boşta zaman aşımı: 00:30:00. Trackingıd: 00000000000000000000000000000000000000_G2_B3, SystemTracker: MyNamespace: Konu: MyTopic, zaman damgası: 2/16/2018 11:10:40 PM "

## <a name="connection-is-closed"></a>Bağlantı kapalı
Herhangi bir etkinlik (boşta) olmadığından ve 5 dakika içinde yeni bir bağlantı oluşturulmadığı için, bağlantı içindeki tüm bağlantılar kapatıldığında AMQP bağlantısında aşağıdaki hatayı görürsünüz.

"Hata {Condition = AMQP: bağlantı: zorlamalı, açıklama = ' bağlantı, izin verilen 300000 milisaniyelik daha fazla süre için etkin değil ve ' LinkTracker ' kapsayıcısı tarafından kapatılmış. Trackingıd: 00000000000000000000000000000000000_G21, SystemTracker: gateway5, zaman damgası: 2019-03-06T17:32:00 ', Info = null} "

## <a name="link-isnt-created"></a>Bağlantı oluşturulmamış 
Yeni bir AMQP bağlantısı oluşturulduğunda, ancak AMQP bağlantısı oluşturulurken 1 dakika içinde bir bağlantı oluşturulmadığında bu hatayı görürsünüz.

"Hata {Condition = AMQP: bağlantı: zorlamalı, açıklama = ' bağlantı, izin verilen 60000 milisaniyelik daha fazla süre için etkin değil ve ' LinkTracker ' kapsayıcısı tarafından kapatılmış. Trackingıd: 0000000000000000000000000000000000000_G21, SystemTracker: gateway5, zaman damgası: 2019-03-06T18:41:51 ', Info = null} "

## <a name="next-steps"></a>Sonraki adımlar
AMQP hakkında daha fazla bilgi için bkz. [amqp 1,0 protokol Kılavuzu](../service-bus-messaging/service-bus-amqp-protocol-guide.md).
