---
title: Service Bus atılacak ileti sıraları | Microsoft Docs
description: Azure Service Bus içindeki atılacak ileti kuyruklarını açıklar. Service Bus kuyrukları ve konu abonelikleri, atılacak ileti sırası adlı ikincil bir alt sıra sağlar.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 6293a3a9a760ece137644578d8ee7dccebc63d95
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812381"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Service Bus atılacak ileti sıralarına genel bakış

Azure Service Bus kuyrukları ve konu abonelikleri, *atılacak ileti sırası* (DLQ) adlı ikincil bir alt sıra sağlar. Atılacak ileti sırasının açık bir şekilde oluşturulması ve silinemediği veya ana varlıktan bağımsız olarak yönetilebilmesi gerekmez.

Bu makalede Service Bus içindeki atılacak ileti sıraları açıklanmaktadır. Tartışmanın büyük bölümü GitHub 'daki [atılacak ileti sıraları örneğine](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) göre gösterilmiştir.
 
## <a name="the-dead-letter-queue"></a>Atılacak ileti sırası

Atılacak ileti sırasının amacı, hiçbir alıcıya teslim edilemeyen iletileri veya işlenemeyen iletileri tutmaktır. İletiler daha sonra DLQ 'dan kaldırılabilir ve incelenebilir. Bir uygulama, bir operatör yardımıyla, sorunları gidermenize ve iletiyi yeniden gönderebilecek, hatayı bir hata olduğunu günlüğe günlüğünden ve düzeltici bir işlem yapmanıza yardımcı olabilir. 

Bir API ve protokol perspektifinden, DLCı 'ler, yalnızca üst varlığın atılacak ileti işlemi yoluyla gönderilebileceği sürece, genellikle diğer bir sıraya benzer. Ayrıca, yaşam süresi gözlemlenmez ve bir DLQ 'dan bir iletiyi atılacak şekilde görüntüleyemezsiniz. Atılacak ileti kuyruğu, Peek-kilit dağıtımını ve işlem işlemlerini tamamen destekler.

DLCı 'nin otomatik olarak temizlenmesi yoktur. Bu iletiler, DLCı 'lerden açıkça geri alınana ve atılacak ileti iletisini tamamlamadan, DLCı 'de kalır.


## <a name="dlq-message-count"></a>DLQ ileti sayısı
Konu düzeyinde teslim edilemeyen ileti sırasındaki ileti sayısını almak mümkün değildir. Bunun nedeni, Service Bus bir iç hata oluşturulmadığı takdirde iletiler konu düzeyinde yer duymayın. Bunun yerine, gönderen bir konuya bir ileti gönderdiğinde, ileti milisaniye içinde konuya iletilir ve bu nedenle artık konu düzeyinde yer alır. Bu nedenle, konu başlığı için abonelikle ilişkili DLQ içindeki iletileri görebilirsiniz. Aşağıdaki örnekte, **Service Bus Explorer** "test1" ABONELIĞINE ait DLCI 'de şu anda 62 ileti olduğunu gösterir. 

![DLQ ileti sayısı](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Ayrıca, Azure CLı komutunu kullanarak DLQ iletilerinin sayısını alabilirsiniz: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) . 

## <a name="moving-messages-to-the-dlq"></a>İletileri DLQ 'a taşıma
Service Bus, iletilerin ileti altyapısı içinden DLCı 'ye itilmesine neden olan birkaç etkinlik vardır. Bir uygulama Ayrıca iletileri doğrudan DLCı 'ye taşıyabilir. Aşağıdaki iki Özellik (atılacak harf nedeni ve atılacak harf açıklaması), kullanılmayan iletilere eklenir. Uygulamalar, atılacak harf nedeni özelliği için kendi kodlarını tanımlayabilir, ancak sistem aşağıdaki değerleri ayarlar.

| Atılacak mektup nedeni | Atılacak mektup hatası açıklaması |
| --- | --- |
|Headersizeaşıldı |Bu akış için boyut kotası aşıldı. |
|TTLExpiredException |İletinin süresi doldu ve teslim edilmeyenler sırasına eklendi. Ayrıntılar için [yaşam süresi](#time-to-live) bölümüne bakın. |
|Oturum KIMLIĞI null. |Oturumun etkin olduğu varlık, oturum tanımlayıcısı null olan bir iletiye izin vermiyor. |
|Maxtransferhopcountexceden | Kuyruklar arasında iletme sırasında izin verilen en fazla atlama sayısı. Değer 4 ' e ayarlanır. |
| MaxDeliveryCountExceededExceptionMessage | İleti, en fazla teslim denemesinden sonra tüketilmelidir. Ayrıntılar için [en fazla teslimat sayısı](#maximum-delivery-count) bölümüne bakın. |

## <a name="maximum-delivery-count"></a>En fazla teslimat sayısı
Service Bus kuyrukları ve abonelikleri için ileti teslimi denemesi sayısı sınırlıdır. Varsayılan değer 10'dur. Bir ileti bir Özet-kilit altında teslim edildiğinde, ancak açıkça terk edildiğinde ya da kilidin süresi dolmuşsa, iletideki teslim sayısı artırılır. Teslim sayısı sınırı aştığında, ileti DLQ 'a taşınır. DLQ içindeki iletinin atılacak mektup nedeni: Maxdeliverycountexceıda olarak ayarlandı. Bu davranış devre dışı bırakılamaz, ancak en fazla teslim sayısını büyük bir sayı olarak ayarlayabilirsiniz.

## <a name="time-to-live"></a>Yaşam süresi
Kuyruklarda veya aboneliklerde atılacak iletileri etkinleştirdiğinizde, süresi dolan tüm iletiler DLQ 'a taşınır. Atılacak harf nedeni kodu: TTLExpiredException olarak ayarlanır.

Süre dolma iletileri yalnızca, ana kuyruktan veya abonelikte en az bir etkin alıcı çekilerek DLQ öğesine taşınır. Ertelenmiş iletiler de temizlenmez ve süre dolduktan sonra atılacak ileti kuyruğuna taşınamaz. Bu davranış tasarım gereğidir.

## <a name="errors-while-processing-subscription-rules"></a>Abonelik kuralları işlenirken hatalar oluştu
Filtre değerlendirmesi özel durumları üzerinde kullanım dışı bırakırsanız, bir aboneliğin SQL filtre kuralı yürütülürken oluşan tüm hatalar, sorunlu iletiyle birlikte DLCı 'de yakalanır. Bu seçeneği, tüm ileti türlerinin abone olmadığı bir üretim ortamında kullanmayın.

## <a name="application-level-dead-lettering"></a>Uygulama düzeyi atılacak
Sistem tarafından sunulan kullanım dışı kullanım özelliklerine ek olarak, uygulamalar kabul edilmeyen iletileri açıkça reddetmek için DLQ ' ı kullanabilir. Her türlü sistem sorunu, hatalı biçimlendirilmiş yükleri tutan mesajlar veya ileti düzeyindeki bazı güvenlik şemaları kullanılırken kimlik doğrulaması başarısız olan iletiler nedeniyle düzgün işlenemeyecek iletiler içerebilir.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>ForwardTo veya SendVia senaryolarında atılacak
İletiler aşağıdaki koşullarda Aktarım teslim edilemeyen ileti sırasına gönderilir:

- Bir ileti, [birbirine](service-bus-auto-forwarding.md)çok dört kuyruk veya konu başlığı üzerinden geçirilir.
- Hedef kuyruk veya konu devre dışı veya silinmiş.
- Hedef kuyruğu veya konu başlığı, en büyük varlık boyutunu aşıyor.

## <a name="path-to-the-dead-letter-queue"></a>Atılacak ileti sırasının yolu
Aşağıdaki sözdizimini kullanarak atılacak ileti kuyruğuna erişebilirsiniz:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Sonraki adımlar
**İleti süre sonu ayarını geçersiz** kılmak için farklı yollar hakkında bilgi edinmek için, bkz. [bir kuyruk veya abonelik Için atılacak ayarları etkinleştirme](enable-dead-letter.md) .
