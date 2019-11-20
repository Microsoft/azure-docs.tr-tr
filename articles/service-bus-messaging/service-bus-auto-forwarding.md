---
title: Azure Service Bus mesajlaşma varlıklarını otomatik iletme | Microsoft Docs
description: Service Bus kuyruğu veya aboneliğini başka bir kuyruğa veya konuya nasıl zincirleyebilirsiniz.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 1d7b76a58a427b687d0dc36d13cfc00f32196853
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390141"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Oto Service Bus varlıkları tekrar yönlendirme ile zincirle

Service Bus *oto iletme* özelliği, bir kuyruğu veya aboneliği aynı ad alanının parçası olan başka bir kuyruğa veya konuya zincirlemenize olanak sağlar. Otomatik iletme etkinleştirildiğinde Service Bus, ilk sıraya veya aboneliğe (kaynak) yerleştirilmiş iletileri otomatik olarak kaldırır ve bunları ikinci kuyruğa veya konuya (hedef) koyar. Hedef varlığa doğrudan bir ileti göndermek yine de mümkündür.

## <a name="using-autoforwarding"></a>Oto iletmeyi kullanma

Queuedescription [. ForwardTo][QueueDescription.ForwardTo] veya [Subscriptiondescription. ForwardTo][SubscriptionDescription.ForwardTo] ayarlarını, kaynak Için [queuedescription][QueueDescription] veya [subscriptiondescription][SubscriptionDescription] nesnelerinde Aşağıdaki örnek:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Hedef varlık, kaynak varlığın oluşturulduğu sırada mevcut olmalıdır. Hedef varlık yoksa, Service Bus kaynak varlığı oluşturmanız istendiğinde bir özel durum döndürür.

Tek bir konuyu ölçeklendirmek için, oto iletmeyi kullanabilirsiniz. Service Bus, [belirli bir konudaki aboneliklerin sayısını](service-bus-quotas.md) 2.000 olarak sınırlandırır. İkinci düzey konuları oluşturarak ek aboneliklere uyum sağlayabilirsiniz. Abonelik sayısında Service Bus sınırlaması ile bağlı olmasanız bile, ikinci bir konu seviyesi eklemek, konunun genel verimini iyileştirebilirler.

![Otomatik iletme senaryosu][0]

Ayrıca, alıcıların ileti gönderenleri ayırmak için de oto iletmeyi kullanabilirsiniz. Örneğin, üç modülden oluşan bir ERP sistemi düşünün: sipariş işleme, stok yönetimi ve müşteri ilişkileri yönetimi. Bu modüllerin her biri, ilgili bir konuya sıraya alınan iletileri oluşturur. Gamze ve Bob, müşterileri ile ilgili tüm iletilerle ilgilenen satış temsilcilerdir. Bu iletileri almak için, Gamze ve Bob her biri, tüm iletileri kuyruğuna otomatik olarak ileten ERP konularındaki her bir kişisel kuyruk ve bir abonelik oluşturur.

![Otomatik iletme senaryosu][1]

Gamze,, ERP konusu yerine kişisel kuyruğu tatilden gittiğinde, doldurur. Bu senaryoda, bir satış temsilcisi herhangi bir ileti almadığı için kotadan hiçbirine hiçbir zaman ulaşmaz.

> [!NOTE]
> Otomatik iletme ayarı ayarlandığında, **hem kaynak hem de hedef** üzerinde otomatik deleteonıdle değeri otomatik olarak veri türünün en büyük değerine ayarlanır.
> 
>   - Kaynak tarafında, oto iletimi bir alma işlemi olarak davranır. Bu nedenle, ileri iletme kurulumuna sahip olan kaynak hiçbir şekilde gerçekten "boşta" değildir.
>   - Hedef tarafta bu, iletinin iletmekte olduğu her zaman bir hedefin olduğundan emin olmak için yapılır.

## <a name="autoforwarding-considerations"></a>Oto iletme konuları

Hedef varlık çok fazla ileti birikiyor ve kotayı aşarsa ya da hedef varlık devre dışıysa, hedefte alan alana kadar, kaynak varlık iletileri teslim edilemeyen ileti [kuyruğuna](service-bus-dead-letter-queues.md) ekler (veya varlık yeniden etkin olur). Bu iletiler, atılacak ileti kuyruğunda canlı olmaya devam eder, bu nedenle bunları teslim edilemeyen ileti sırasından açıkça alıp işlemeli.

Birçok abonelikle Birleşik bir konu almak için bireysel konular ile zincirleme yaparken, birinci düzey konu üzerinde ve ikinci düzey konularda çok sayıda aboneliğe sahip olan orta sayıda aboneliğe sahip olmanız önerilir. Örneğin, her biri 200 abonelik ile ikinci düzey bir konuya zincirleme olan 20 abonelikli ilk düzey bir konu, her biri 20 abonelikle ikinci düzey bir konuya zincirleme olan 200 abonelikleriyle ilk düzey bir konudan daha yüksek aktarım hızı sağlar.

Her iletilen ileti için bir işlem Service Bus. Örneğin, 20 abonelikle bir konuya ileti göndermek, her birinin iletileri başka bir kuyruğa veya konuya otomatik olarak iletecek şekilde yapılandırmaları, tüm ilk düzey abonelikler iletinin bir kopyasını alıyorsa 21 işlem olarak faturalandırılır.

Başka bir kuyruğa veya konuya zincirleme bir abonelik oluşturmak için, aboneliğin oluşturucusunun hem kaynak hem de hedef varlık üzerinde **yönetme** izinlerine sahip olması gerekir. Kaynak konuya ileti gönderimi yalnızca kaynak konu üzerinde **gönderme** izinleri gerektirir.

## <a name="next-steps"></a>Sonraki adımlar

Oto iletme hakkında ayrıntılı bilgi için aşağıdaki başvuru konularına bakın:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [Abonelik açıklaması][SubscriptionDescription]

Service Bus performans iyileştirmeleri hakkında daha fazla bilgi edinmek için bkz. 

* [Service Bus Mesajlaşması kullanarak performans geliştirmek için en iyi yöntemler](service-bus-performance-improvements.md)
* [Bölümlenmiş mesajlaşma varlıkları][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
