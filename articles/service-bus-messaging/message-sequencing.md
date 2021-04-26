---
title: Azure Service Bus ileti sıralaması ve zaman damgaları | Microsoft Docs
description: Bu makalede, Azure Service Bus iletilerinin sıralama ve sıralamayı (zaman damgalarına) nasıl koruyabileceğiniz açıklanır.
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 3d5300568232afae1238445113d60eda8cdb2f1b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497107"
---
# <a name="message-sequencing-and-timestamps"></a>İleti sıralama ve zaman damgaları

Sıralama ve zaman damgalama, tüm Service Bus varlıklarda her zaman etkin olan ve `SequenceNumber` `EnqueuedTimeUtc` alınan veya taranan iletilerin özellikleri aracılığıyla yüzey olan iki özellikten oluşur.

İletilerin mutlak sırasının önemli olduğu ve/veya bir tüketicinin iletiler için güvenilir benzersiz bir tanımlayıcıya ihtiyacı olan bu durumlar için, aracı, bir boşluk içermeyen iletileri damgalarına ve sıra veya konuya göre artan sıra numarasına sahip olur. Bölümlenmiş varlıklar için sıra numarası bölüme göre verilir.

**SequenceNumber** değeri, aracı ve işlevleri tarafından iç tanımlayıcı olarak kabul edildiği ve depolandığı için bir iletiye atanan benzersiz bir 64 bitlik tamsayıdır. Bölümlenmiş varlıklarda en üstteki 16 bit, Bölüm tanımlayıcısını yansıtır. 48/64 bit Aralık tükendiğinde sıra numaraları sıfıra geçer.

Sıra numarası, istemcilere göre değil, merkezi ve bağımsız bir yetkili tarafından atandığı için benzersiz bir tanımlayıcı olarak güvenilir olabilir. Ayrıca, aynı zamanda gerçek bir zaman damgasına da sahiptir ve zaman damgalarının, yoğun ileti fiyatları üzerinde yüksek düzeyde çözünürlüğü olmaması ve bu durum, aracı sahipliğinin düğümler arasında geçiş yaptığı durumlarda (ancak en düşük düzeyde) saat çarpıklığı tabi olabilmesinden kaynaklanır.

Mutlak varış siparişi, örneğin, en son tedarik edilirken sınırlı sayıda sunulan malları ilk kez sunulan bir kez sunulan iş senaryolarında önemli, konser bilet satışları bir örnektir.

Zaman damgalama özelliği, bir iletinin, **Enqueuedtimeutc** ÖZELLIĞINDE yansıtılan UTC saatini doğru bir şekilde yakalayan, nötr ve güvenilir bir yetkili olarak davranır. Bir iş senaryosu, gece yarısından önce belirli bir tarihte gönderilip gönderilmediğine karşın işlem sıranın biriktirme listesinin en altında olduğundan, bu değer yararlı olur.

## <a name="scheduled-messages"></a>Zamanlanmış iletiler

Bir kuyruğa veya konu başlığına daha sonra işlenmek; örneğin belirli bir işi belirli bir zamanda sistem tarafından işlenmeye uygun hale gelmesi için zamanlamak üzere ileti gönderebilirsiniz. Bu yetenek, güvenilir bir dağıtılmış zaman tabanlı Scheduler 'ı daha gerçekçi hale getirir.

Zamanlanan iletiler, tanımlanan sıraya alma zamanına kadar sırada bir şekilde çalışmaz. Bu süreden önce zamanlanan iletiler iptal edilebilir. İptal etme iletiyi siler.

İstemcilerimizden herhangi birini kullanarak, iki şekilde ileti zamanlayabilirsiniz:
- Normal gönderme API 'sini kullanın, ancak `ScheduledEnqueueTimeUtc` göndermeden önce iletideki özelliği ayarlayın.
- İleti zamanlama API 'sini kullanın, hem normal iletiyi hem de zamanlanan saati geçirin. Bu işlem, daha sonra gerekirse zamanlanmış iletiyi iptal etmek için kullanabileceğiniz zamanlanan ileti **SequenceNumber** döndürür. 

Zamanlanan iletiler ve sıra numaraları [ileti tarama](message-browsing.md)kullanılarak da bulunabilir.

Zamanlanmış bir ileti için **SequenceNumber** yalnızca ileti bu durumda olduğunda geçerlidir. İleti etkin duruma geçiş yaparken, ileti geçerli anında sıraya alınmış gibi sıraya eklenir, bu da yeni bir **SequenceNumber** atamayı içerir.

Özelliği tek tek iletilere sabitlenmiş olduğundan ve mesajlar yalnızca bir kez sıraya alınmışsa Service Bus iletiler için yinelenen zamanlamaları desteklemez.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşma hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [Service Bus kuyrukları, konu başlıkları ve abonelikleri](service-bus-queues-topics-subscriptions.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus konu başlıklarını ve aboneliklerini kullanma](service-bus-dotnet-how-to-use-topics-subscriptions.md)
