---
title: İleti süre sonu Azure Service Bus | Microsoft Docs
description: Azure Service Bus iletilerinin yaşam süresi ve yaşam süresi
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 109ecc671b43365c433a626ff8d9fe55a5a626b5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310287"
---
# <a name="message-expiration-time-to-live"></a>İleti süre sonu (Yaşam Süresi)

Bir iletideki yük veya bir iletinin alıcıyı ileten bir komut ya da sorgulama, neredeyse her zaman bir uygulama düzeyi sona erme tarihi biçimine tabidir. Bu süre sonunda, içerik artık teslim edilmemiş veya istenen işlem artık yürütülmemiş.

Kuyrukların ve konuların genellikle uygulamaların veya uygulama bölümlerinin kısmi çalıştırmaları bağlamında kullanıldığı geliştirme ve test ortamları için, bir sonraki Test çalıştırmasının kullanılabilmesi için, yabanlanılan test iletilerinin otomatik olarak atık toplanmasını sağlamak da istenebilir temizlemeyi başlatın.

Her bir ileti için süre sonu, göreli bir süre belirten [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) System özelliği ayarlanarak denetlenebilir. İleti varlığa sıralandığında süre sonu mutlak bir anlık olur. Bu sırada, [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) özelliği değeri alır [(**enqueuedtimeutc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). Aracılı bir iletideki yaşam süresi (TTL) ayarı, etkin bir istemci olmadığında zorunlu değildir.

**ExpiresAtUtc** Instant 'tan geçmiş iletiler, alma işlemi için uygun hale gelir. Süre sonu, şu anda teslim için kilitli iletileri etkilemez; Bu iletiler normal olarak yine de işlenir. Kilidin süresi dolarsa veya ileti durdurulmadığında, süre sonu hemen yürürlüğe girer.

İleti kilit altındayken uygulama, süresi sona ermemiş bir iletinin elinde olabilir. Uygulamanın işleme devam edip etmeyeceğini veya iletiyi bırakmaya izin verip uygulamamayı tercih edin.

## <a name="entity-level-expiration"></a>Varlık düzeyi süre sonu

Bir sıraya veya konuya gönderilen tüm iletiler, [Defaultmessagetimetolive](/azure/templates/microsoft.servicebus/namespaces/queues) özelliğine sahip varlık düzeyinde ayarlanan varsayılan bir süre sonuna tabidir ve oluşturma sırasında portalda ayarlanabilir ve daha sonra ayarlanabilir. Varsayılan süre sonu, [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) 'nin açıkça ayarlanmamasının varlığına gönderilen tüm iletiler için kullanılır. Varsayılan süre sonu Ayrıca **TimeToLive** değeri için tavan olarak çalışır. Varsayılan değerden daha uzun **TimeToLive** süresi dolduktan sonra, kuyruğa alınmadan önce **defaultmessagetimetolive** değerine sessizce ayarlanır.

> [!NOTE]
> Aracılı bir ileti için varsayılan [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) değeri [TimeSpan.](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) aksi belirtilmedikçe Max değeridir.
>
> Mesajlaşma varlıkları (kuyruklar ve konular) için, varsayılan sona erme saati de Service Bus standart ve Premium katmanlar için [TimeSpan. Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) ' dur.  Temel katman için, varsayılan sona erme saati 14 gündür.

Süre dolmayan iletiler, [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) özelliğini ayarlayarak veya portalda ilgili kutusunu denetleyerek, isteğe bağlı olarak bir [atılacak ileti kuyruğuna](service-bus-dead-letter-queues.md) taşınabilir. Seçenek devre dışı bırakılırsa, zaman aşımına uğradı iletileri bırakılır. Teslim edilemeyen ileti kuyruğuna taşınan süre sonu iletileri, kullanıcının Özellikler bölümünde aracı tarafından depolanan [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) özelliği hesaplanarak, diğer kullanılmayan iletilerden ayırt edilebilir. değer, bu durumda [Ttlexpiredexception](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) olur.

Kilit sırasında iletinin süresinin dolma karşı korunduğu ve varlıkta ayarlanmış olması durumunda ileti, kilitlenme terk edildiği veya süresi dolduğunda ileti atılacak ileti kuyruğuna taşınır. Ancak, ileti başarıyla kapatılmışsa taşınmaz ve bu da uygulamanın, kabul edilen süre sonu artma içinde başarıyla işlendiğini varsayar.

[TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) ve otomatik (ve işlem) kullanım süresinin dolma kombinasyonu, bir işleyiciye verilen bir işin veya bir son tarih altındaki bir işleyici grubunun son tarih olarak işlenmesine alınması için değerli bir araçtır ulaşıldı.

Örneğin, işleri ölçeği kısıtlanmış bir arka uçta güvenilir bir şekilde yürütmek için gereken bir Web sitesini göz önünde bulundurun ve zaman zaman trafik artışlarını ve bu arka ucun kullanılabilirlik bölümlerini ayrı olarak ele almak istemektedir. Normal durumda, gönderilen Kullanıcı verilerine yönelik sunucu tarafı işleyicisi bilgileri bir kuyruğa gönderir ve ardından işlemin başarılı bir şekilde işlenmesini onaylayan bir yanıt alır. Bir trafik ani işlemcisi varsa ve arka uç işleyicisi biriktirme listesi öğelerini zamanında işleyemezse, süresi dolmayan işler atılacak ileti kuyruğunda döndürülür. Etkileşimli kullanıcıya, istenen işlemin normalden biraz daha uzun sürmesi ve isteğin nihai işleme sonucunun e-posta ile kullanıcıya gönderildiği bir işleme yolu için farklı bir sıraya konabileceğini görebilirsiniz. 


## <a name="temporary-entities"></a>Geçici varlıklar

Service Bus kuyrukları, konuları ve abonelikleri, belirli bir süre boyunca kullanıldıklarında otomatik olarak kaldırılan geçici varlıklar olarak oluşturulabilir.
 
Otomatik Temizleme, varlıkların dinamik olarak oluşturulduğu ve test ya da hata ayıklama çalıştırmasının kesintiye uğraması nedeniyle, kullanım sonrasında temizlenmeyen geliştirme ve test senaryolarında yararlıdır. Ayrıca, bir uygulama bir yanıt kuyruğu gibi dinamik varlıklar oluştururken, bir Web sunucusu işlemine geri yanıt almak için veya nesne olduğunda bu varlıkları güvenilir bir şekilde temizlemek zor olan başka bir görece kısa süreli nesne olduğunda da yararlıdır. örnek kayboluyor.

Özelliği, [oto Deleteonıdle](/azure/templates/microsoft.servicebus/namespaces/queues) özelliği kullanılarak etkinleştirilir. Bu özellik otomatik olarak silinmeden önce bir varlığın boşta (kullanılmamış) olması gereken süreye ayarlanır. Bu özellik için en düşük değer 5 ' tir.
 
**Oto Deleteonıdle** özelliği bir Azure Resource Manager işlem veya .NET Framework Client [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API 'leri aracılığıyla ayarlanmalıdır. Portal 'da ayarlayamazsınız.

## <a name="idleness"></a>Uyku modu

Varlıkların (kuyruklar, konular ve abonelikler) kullanım dışı olduğu kabul edilir:

- Kuyruklar
    - Gönderme yok  
    - Alma yok  
    - Kuyrukta güncelleştirme yok  
    - Zamanlanmış ileti yok  
    - Göz atma/göz atma yok 
- Konu başlıkları  
    - Gönderme yok  
    - Konuya güncelleştirme yok  
    - Zamanlanmış ileti yok 
- Abonelikler
    - Alma yok  
    - Abonelikte güncelleştirme yok  
    - Aboneliğe yeni kural eklenmedi  
    - Göz atma/göz atma yok  
 


## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşma hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [Service Bus kuyrukları, konu başlıkları ve abonelikleri](service-bus-queues-topics-subscriptions.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus konu başlıklarını ve aboneliklerini kullanma](service-bus-dotnet-how-to-use-topics-subscriptions.md)
