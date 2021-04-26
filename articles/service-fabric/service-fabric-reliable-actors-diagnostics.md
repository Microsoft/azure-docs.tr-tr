---
title: Aktör tanılama ve izleme
description: Bu makalede, tarafından oluşturulan olaylar ve performans sayaçları da dahil olmak üzere Service Fabric Reliable Actors çalışma zamanındaki tanılama ve performans izleme özellikleri açıklanmaktadır.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: ebaedb5369f3b39372262bfde526706e8d069418
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789625"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Reliable Actors için tanılama ve performans izlemesi
Reliable Actors Runtime, [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) olaylarını ve [performans sayaçlarını](/dotnet/api/system.diagnostics.performancecounter)yayar. Bunlar, çalışma zamanının nasıl çalıştığı hakkında Öngörüler ve sorun giderme ve performans izleme konularında yardım sağlar.

## <a name="eventsource-events"></a>EventSource olayları
Reliable Actors Runtime için EventSource sağlayıcı adı "Microsoft-ServiceFabric-aktör" ' dir. Bu olay kaynağının olayları, aktör uygulamasının [Visual Studio 'da hata ayıklaması](service-fabric-debugging-your-application.md)yapıldığında [Tanılama olayları](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) penceresinde görüntülenir.

EventSource olaylarını toplama ve/veya görüntüleme konusunda yardımcı olan araç ve teknolojilerin örnekleri şunlardır. [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure tanılama](../cloud-services/cloud-services-dotnet-diagnostics.md), [anlam günlüğü](/previous-versions/msp-n-p/dn774980(v=pandp.10))ve [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Anahtar sözcükler
Reliable Actors EventSource 'e ait olan tüm olaylar bir veya daha fazla anahtar sözcük ile ilişkilendirilir. Bu, toplanan olayların filtrelenmesini mümkün bir şekilde sunar. Aşağıdaki anahtar sözcük bitleri tanımlanmıştır.

| Sürümleri | Description |
| --- | --- |
| 0x1 |Yapı aktör çalışma zamanının işlemini özetleyen önemli olaylar kümesi. |
| 0x2 |Aktör yöntemi çağrılarını tanımlayan olay kümesi. Daha fazla bilgi için [aktörlerin giriş konusuna](service-fabric-reliable-actors-introduction.md)bakın. |
| 4, |Aktör durumuyla ilgili olay kümesi. Daha fazla bilgi için [aktör durum yönetimi](service-fabric-reliable-actors-state-management.md)konusuna bakın. |
| 0x8 |Aktör içinde çift tabanlı eşzamanlılık ile ilgili olay kümesi. Daha fazla bilgi için [eşzamanlılık](service-fabric-reliable-actors-introduction.md#concurrency)sayfasındaki konusuna bakın. |

## <a name="performance-counters"></a>Performans sayaçları
Reliable Actors çalışma zamanı aşağıdaki performans sayacı kategorilerini tanımlar.

| Kategori | Açıklama |
| --- | --- |
| Aktör Service Fabric |Azure Service Fabric aktörlerini özel sayaçlar, ör. aktör durumunu kaydetmek için geçen süre |
| Service Fabric aktör yöntemi |Service Fabric aktörleri tarafından uygulanan yöntemlere özgü sayaçlar, ör. aktör yöntemi ne sıklıkla çağrılır |

Yukarıdaki kategorilerin her birinde bir veya daha fazla sayaç bulunur.

Windows işletim sisteminde varsayılan olarak kullanılabilir olan [Windows Performans İzleyicisi](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) uygulaması, performans sayacı verilerini toplamak ve görüntülemek için kullanılabilir. [Azure tanılama](../cloud-services/cloud-services-dotnet-diagnostics.md) , performans sayacı verilerini toplamaya ve Azure tablolarına yüklemeye yönelik başka bir seçenektir.

### <a name="performance-counter-instance-names"></a>Performans sayacı örnek adları
Çok sayıda aktör hizmeti veya aktör hizmeti bölümü olan bir küme, çok sayıda aktör performans sayacı örneğine sahip olacaktır. Performans sayacı örneği adları, performans sayacı örneğinin ilişkilendirildiği belirli [bölümü](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) ve aktör yöntemini (varsa) tanımlamaya yardımcı olabilir.

#### <a name="service-fabric-actor-category"></a>Aktör kategorisi Service Fabric
Kategori için `Service Fabric Actor` , sayaç örneği adları aşağıdaki biçimdedir:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*Servicefabricpartitionıd* , performans sayacı örneğinin ilişkilendirildiği SERVICE fabrıc bölüm kimliğinin dize gösterimidir. Bölüm KIMLIĞI bir GUID 'dir ve dize temsili, [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) Biçim belirleyicisi "D" olan yöntem aracılığıyla oluşturulur.

*Actorruntimeınternalıd* , iç kullanımı Için yapı aktörleri çalışma zamanı tarafından oluşturulan 64 bitlik bir tamsayının dize gösterimidir. Bu, benzersizlik sağlamak ve diğer performans sayacı örneği adlarıyla çakışmamak için performans sayacı örneği adına dahildir. Kullanıcılar, performans sayacı örneği adının bu bölümünü yorumlama denememelidir.

Aşağıda, kategoriye ait bir sayaca yönelik sayaç örneği adının bir örneği verilmiştir `Service Fabric Actor` :

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

Yukarıdaki örnekte, `2740af29-78aa-44bc-a20b-7e60fb783264` Service Fabric bölüm kimliğinin dize gösterimidir ve `635650083799324046` çalışma zamanının iç kullanımı için oluşturulan 64 bitlik kimliğidir.

#### <a name="service-fabric-actor-method-category"></a>Service Fabric aktör yöntemi kategorisi
Kategori için `Service Fabric Actor Method` , sayaç örneği adları aşağıdaki biçimdedir:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* , performans sayacı örneğinin ilişkilendirildiği aktör yönteminin adıdır. Yöntem adının biçimi, yapı aktör çalışma zamanındaki bir mantığa göre belirlenir. Bu ad, adın okunabilirliğini Windows üzerinde performans sayacı örnek adlarının en büyük uzunluğuna göre dengeler.

*Actorsruntimemethodıd* , iç kullanımı Için yapı aktör çalışma zamanı tarafından oluşturulan 32 bitlik bir tamsayının dize gösterimidir. Bu, benzersizlik sağlamak ve diğer performans sayacı örneği adlarıyla çakışmamak için performans sayacı örneği adına dahildir. Kullanıcılar, performans sayacı örneği adının bu bölümünü yorumlama denememelidir.

*Servicefabricpartitionıd* , performans sayacı örneğinin ilişkilendirildiği SERVICE fabrıc bölüm kimliğinin dize gösterimidir. Bölüm KIMLIĞI bir GUID 'dir ve dize temsili, [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) Biçim belirleyicisi "D" olan yöntem aracılığıyla oluşturulur.

*Actorruntimeınternalıd* , iç kullanımı Için yapı aktörleri çalışma zamanı tarafından oluşturulan 64 bitlik bir tamsayının dize gösterimidir. Bu, benzersizlik sağlamak ve diğer performans sayacı örneği adlarıyla çakışmamak için performans sayacı örneği adına dahildir. Kullanıcılar, performans sayacı örneği adının bu bölümünü yorumlama denememelidir.

Aşağıda, kategoriye ait bir sayaca yönelik sayaç örneği adının bir örneği verilmiştir `Service Fabric Actor Method` :

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

Yukarıdaki örnekte, `ivoicemailboxactor.leavemessageasync` Yöntem adı, `2` çalışma zamanının iç kullanımı için oluşturulan 32 bitlik kimlik, `89383d32-e57e-4a9b-a6ad-57c6792aa521` Service Fabric bölüm kimliğinin dize gösterimidir ve `635650083804480486` çalışma zamanının iç kullanımı IÇIN oluşturulan 64 bit kimliğidir.

## <a name="list-of-events-and-performance-counters"></a>Olaylar ve performans sayaçları listesi
### <a name="actor-method-events-and-performance-counters"></a>Aktör yöntemi olayları ve performans sayaçları
Reliable Actors Runtime, [aktör yöntemleriyle](service-fabric-reliable-actors-introduction.md)ilgili aşağıdaki olayları yayar.

| Olay adı | Olay Kimliği | Level | Sözcükle | Description |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Ayrıntılı |0x2 |Aktör çalışma zamanı bir aktör yöntemi çağırmak için. |
| ActorMethodStop |8 |Ayrıntılı |0x2 |Aktör yöntemi yürütmeyi bitirdi. Diğer bir deyişle, çalışma zamanının aktör metoduna zaman uyumsuz çağrısı döndürülür ve aktör yöntemi tarafından döndürülen görev tamamlanmıştır. |
| ActorMethodThrewException |9 |Uyarı |0x3 |Bir aktör yönteminin yürütülmesi sırasında, çalışma zamanının aktör yöntemine yönelik zaman uyumsuz çağrı sırasında veya aktör yöntemi tarafından döndürülen görevin yürütülmesi sırasında bir özel durum oluştu. Bu olay, araştırma gerektiren aktör kodundaki bir hata sıralamasını gösterir. |

Reliable Actors Runtime, aktör yöntemlerinin yürütülmesi ile ilgili aşağıdaki performans sayaçlarını yayımlar.

| Kategori adı | Sayaç adı | Description |
| --- | --- | --- |
| Service Fabric aktör yöntemi |Saniyedeki çağırma sayısı |Aktör hizmeti metodunun saniye başına çağrılme sayısı |
| Service Fabric aktör yöntemi |Çağrı başına ortalama milisaniye |Aktör hizmeti yönteminin yürütülmesi için harcanan süre (milisaniye) |
| Service Fabric aktör yöntemi |Oluşturulan özel durumlar/sn |Aktör hizmeti metodunun saniye başına özel durum kaç kez aldığı |

### <a name="concurrency-events-and-performance-counters"></a>Eşzamanlılık olayları ve performans sayaçları
Reliable Actors Runtime [eşzamanlılık](service-fabric-reliable-actors-introduction.md#concurrency)ile ilgili aşağıdaki olayları yayar.

| Olay adı | Olay Kimliği | Level | Sözcükle | Description |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Ayrıntılı |0x8 |Bu olay, bir aktör içindeki her yeni bir öğenin başlangıcında yazılır. Bu, çift tabanlı eşzamanlılık uygulayan aktör başına kilidi almak için bekleyen, bekleyen aktör çağrılarının sayısını içerir. |

Reliable Actors Runtime eşzamanlılık ile ilgili aşağıdaki performans sayaçlarını yayınlar.

| Kategori adı | Sayaç adı | Description |
| --- | --- | --- |
| Aktör Service Fabric |aktör kilidi için bekleyen aktör çağrısı sayısı |Etkinleştirme başına eşzamanlılık uygulayan, aktör başına kilit elde etmek için bekleyen aktör çağrısı sayısı |
| Aktör Service Fabric |Kilit bekleme başına ortalama milisaniye |Çift tabanlı eşzamanlılık uygulayan aktör başına kilidi almak için geçen süre (milisaniye cinsinden) |
| Aktör Service Fabric |Ortalama milisaniye aktör kilidi tutuldu |Aktör kilidi 'nin tutulduğu süre (milisaniye cinsinden) |

### <a name="actor-state-management-events-and-performance-counters"></a>Aktör durumu yönetim olayları ve performans sayaçları
Reliable Actors Runtime, [aktör durumu yönetimiyle](service-fabric-reliable-actors-state-management.md)ilgili aşağıdaki olayları yayar.

| Olay adı | Olay Kimliği | Level | Sözcükle | Description |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Ayrıntılı |4, |Aktör çalışma zamanı aktör durumunu kaydetmek için kullanılır. |
| ActorSaveStateStop |11 |Ayrıntılı |4, |Aktör çalışma zamanı aktör durumunu kaydetmeyi tamamladı. |

Reliable Actors Runtime, aktör durumu yönetimiyle ilgili aşağıdaki performans sayaçlarını yayınlar.

| Kategori adı | Sayaç adı | Description |
| --- | --- | --- |
| Aktör Service Fabric |Durum kaydetme işlemi başına ortalama milisaniye |Aktör durumunu milisaniye olarak kaydetmek için geçen süre |
| Aktör Service Fabric |Yük durumu işlemi başına ortalama milisaniye |Aktör durumu yükleme için harcanan süre (milisaniye) |

### <a name="events-related-to-actor-replicas"></a>Aktör çoğaltmalarıyla ilgili olaylar
Reliable Actors Runtime, [aktör çoğaltmalarıyla](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)ilgili aşağıdaki olayları yayar.

| Olay adı | Olay Kimliği | Level | Sözcükle | Description |
| --- | --- | --- | --- | --- |
| Replicachangeroletoprımary |1 |Bilgilendirici |0x1 |Aktör çoğaltma rolü birincil olarak değiştirildi. Bu, bu bölümün aktörleri bu çoğaltma içinde oluşturulacak anlamına gelir. |
| ReplicaChangeRoleFromPrimary |2 |Bilgilendirici |0x1 |Aktör çoğaltma rolü birincil olmayan olarak değiştirildi. Bu, bu bölümün aktörlerin artık bu çoğaltma içinde oluşturulmayacağı anlamına gelir. Bu çoğaltma içinde önceden oluşturulmuş aktörler için yeni istek teslim edilecek. Tüm sürmekte olan istekler tamamlandıktan sonra aktörler yok edilir. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Aktör etkinleştirme ve devre dışı bırakma olayları ve performans sayaçları
Reliable Actors Runtime, [aktör etkinleştirme ve devre dışı bırakma](service-fabric-reliable-actors-lifecycle.md)ile ilgili aşağıdaki olayları yayar.

| Olay adı | Olay Kimliği | Level | Sözcükle | Description |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Bilgilendirici |0x1 |Aktör etkinleştirildi. |
| Actora devre dışı |6 |Bilgilendirici |0x1 |Aktör devre dışı bırakıldı. |

Reliable Actors Runtime, aktör etkinleştirme ve devre dışı bırakma ile ilgili aşağıdaki performans sayaçlarını yayınlar.

| Kategori adı | Sayaç adı | Description |
| --- | --- | --- |
| Aktör Service Fabric |Ortalama OnActivateAsync milisaniyesi |OnActivateAsync yönteminin yürütülmesi için harcanan süre (milisaniye) |

### <a name="actor-request-processing-performance-counters"></a>Aktör isteği işleme performans sayaçları
İstemci bir aktör proxy nesnesi aracılığıyla bir yöntemi çağırdığında, ağ üzerinden aktör hizmetine bir istek iletisi gönderilmesine neden olur. Hizmet, istek iletisini işler ve istemciye geri yanıt gönderir. Reliable Actors Runtime, aktör isteği işlemeyle ilgili aşağıdaki performans sayaçlarını yayınlar.

| Kategori adı | Sayaç adı | Description |
| --- | --- | --- |
| Aktör Service Fabric |bekleyen istek sayısı |Hizmette işlenmekte olan istek sayısı |
| Aktör Service Fabric |İstek başına ortalama milisaniye |Bir isteği işlemek için hizmete göre geçen süre (milisaniye cinsinden) |
| Aktör Service Fabric |İstek seri durumundan çıkarma için ortalama milisaniye |Hizmette alındığı sırada aktör isteği iletisinin serisini kaldırmak için geçen süre (milisaniye cinsinden) |
| Aktör Service Fabric |Yanıt serileştirme ortalama milisaniyesi |Yanıt istemciye gönderilmeden önce hizmette aktör yanıt iletisini serileştirmek için geçen süre (milisaniye cinsinden) |

## <a name="next-steps"></a>Sonraki adımlar
* [Reliable Actors Service Fabric platformunu kullanma](service-fabric-reliable-actors-platform.md)
* [Aktör API 'SI başvuru belgeleri](/previous-versions/azure/dn971626(v=azure.100))
* [Örnek kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [PerfView 'da EventSource sağlayıcıları](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
