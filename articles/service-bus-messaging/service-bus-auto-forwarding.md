---
title: Azure Service Bus mesajlaşma varlıklarını otomatik iletme
description: Bu makalede bir Azure Service Bus kuyruğu veya aboneliği başka bir kuyruğa veya konuya nasıl zincirleyen açıklanır.
ms.topic: article
ms.date: 01/20/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 80bef52d568130fa800a1da661f4867abb3df02c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678997"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Oto Service Bus varlıkları tekrar yönlendirme ile zincirle

Service Bus *oto iletme* özelliği, bir kuyruğu veya aboneliği aynı ad alanının parçası olan başka bir kuyruğa veya konuya zincirlemenize olanak sağlar. Otomatik iletme etkinleştirildiğinde Service Bus, ilk sıraya veya aboneliğe (kaynak) yerleştirilmiş iletileri otomatik olarak kaldırır ve bunları ikinci kuyruğa veya konuya (hedef) koyar. Hedef varlığa doğrudan bir ileti göndermek yine de mümkündür.

> [!NOTE]
> Service Bus temel katmanı, oto iletme özelliğini desteklemez. Standart ve Premium katmanlar özelliği destekler. Bu katmanlar arasındaki farklar için bkz. [Service Bus fiyatlandırması](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="using-autoforwarding"></a>Oto iletmeyi kullanma

Aşağıdaki örnekte olduğu gibi, kaynak [için queuedescription veya][QueueDescription] [Subscriptiondescription][SubscriptionDescription] nesnelerindeki [queuedescription. ForwardTo][QueueDescription.ForwardTo] veya [subscriptiondescription. ForwardTo][SubscriptionDescription.ForwardTo] özelliklerini ayarlayarak, oto iletmeyi etkinleştirebilirsiniz:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Hedef varlık, kaynak varlığın oluşturulduğu sırada mevcut olmalıdır. Hedef varlık yoksa, Service Bus kaynak varlığı oluşturmanız istendiğinde bir özel durum döndürür.

Tek bir konuyu ölçeklendirmek için, oto iletmeyi kullanabilirsiniz. Service Bus, [belirli bir konudaki aboneliklerin sayısını](service-bus-quotas.md) 2.000 olarak sınırlandırır. İkinci düzey konuları oluşturarak ek aboneliklere uyum sağlayabilirsiniz. Abonelik sayısında Service Bus sınırlaması ile bağlı olmasanız bile, ikinci bir konu seviyesi eklemek, konunun genel verimini iyileştirebilirler.

![Üç adet ikinci düzey sipariş konusunun herhangi birine dalla bir Siparişler konusu aracılığıyla işlenen bir iletiyi gösteren bir oto iletme senaryosunun diyagramı.][0]

Ayrıca, alıcıların ileti gönderenleri ayırmak için de oto iletmeyi kullanabilirsiniz. Örneğin, üç modülden oluşan bir ERP sistemi düşünün: sipariş işleme, stok yönetimi ve müşteri ilişkileri yönetimi. Bu modüllerin her biri, ilgili bir konuya sıraya alınan iletileri oluşturur. Gamze ve Bob, müşterileri ile ilgili tüm iletilerle ilgilenen satış temsilcilerdir. Bu iletileri almak için, Gamze ve Bob her biri, tüm iletileri kuyruğuna otomatik olarak ileten ERP konularındaki her bir kişisel kuyruk ve bir abonelik oluşturur.

![İki ayrı sıraya karşılık gelen üç konuyla ileti gönderen üç işleme modülünü gösteren bir oto iletme senaryosunun diyagramı.][1]

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

4 sıçramaları aşan bir zincir oluşturmayın. 4 atlamanın aşıldığı iletiler yok edilir.

## <a name="next-steps"></a>Sonraki adımlar

Oto iletme hakkında ayrıntılı bilgi için aşağıdaki başvuru konularına bakın:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [Abonelik açıklaması][SubscriptionDescription]

Service Bus performans iyileştirmeleri hakkında daha fazla bilgi edinmek için bkz. 

* [Service Bus mesajlaşma kullanarak performans iyileştirmeleri için en iyi uygulamalar](service-bus-performance-improvements.md)
* [Bölümlenmiş mesajlaşma varlıkları][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
