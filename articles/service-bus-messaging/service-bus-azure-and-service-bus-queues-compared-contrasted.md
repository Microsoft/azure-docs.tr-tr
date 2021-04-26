---
title: Azure Depolama kuyruklarıyla Service Bus kuyruklarını karşılaştırma
description: Azure tarafından sunulan iki kuyruk türü arasındaki farkları ve benzerlikleri analiz eder.
ms.topic: article
ms.date: 04/12/2021
ms.openlocfilehash: 1c3b0fda12d5e301b17a342c5d5ed11ab76c76da
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304367"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Depolama kuyrukları ve Service Bus kuyrukları-karşılaştırılan ve değişken maliyetli
Bu makalede, Microsoft Azure tarafından sunulan iki kuyruk türü arasındaki farklar ve benzerlikler analiz edilir: depolama kuyrukları ve Service Bus kuyrukları. Bu bilgileri kullanarak, gereksinimlerinizi en iyi şekilde karşılayan çözüm hakkında daha bilinçli bir karar elde edebilirsiniz.

## <a name="introduction"></a>Giriş
Azure iki tür kuyruk mekanizmasını destekler: **depolama kuyrukları** ve **Service Bus kuyrukları**.

**Depolama kuyrukları** , [Azure depolama](https://azure.microsoft.com/services/storage/) altyapısının bir parçasıdır. Bunlar, çok sayıda iletiyi depolamanıza olanak tanır. HTTP veya HTTPS kullanarak kimliği doğrulanmış çağrılar aracılığıyla dünyanın her yerinden iletilere erişirsiniz. Kuyruk iletisi boyutu 64 KB 'ye kadar olabilir. Bir kuyruk, depolama hesabının toplam kapasite sınırına kadar milyonlarca ileti içerebilir. Kuyruklar genellikle zaman uyumsuz olarak işlenecek iş biriktirme listesi oluşturmak için kullanılır. Daha fazla bilgi için bkz. [Azure depolama kuyrukları nelerdir](../storage/queues/storage-queues-introduction.md).

**Service Bus kuyrukları** , sıraya alma, yayımlama/abone olma ve daha gelişmiş tümleştirme düzenlerini destekleyen daha geniş bir [Azure mesajlaşma](https://azure.microsoft.com/services/service-bus/) altyapısının parçasıdır. Birden çok iletişim protokollerine, veri sözleşmelerine, güven etki alanlarına veya ağ ortamlarına yayılabilen uygulamaları veya uygulama bileşenlerini bütünleştirmek üzere tasarlanmıştır. Service Bus kuyrukları/konuları/abonelikleri hakkında daha fazla bilgi için bkz. [Service Bus kuyruklar, konular ve abonelikler](service-bus-queues-topics-subscriptions.md).


## <a name="technology-selection-considerations"></a>Teknoloji seçimi konuları
Depolama kuyrukları ve Service Bus kuyrukları biraz farklı bir özellik kümesine sahiptir. Belirli çözümünüzün ihtiyaçlarına bağlı olarak, birini veya her ikisini de seçebilirsiniz.

Hangi kuyruğa alma teknolojisinin belirli bir çözümün amacına uygun olduğu belirlenirken, çözüm mimarları ve geliştiriciler bu önerileri göz önünde bulundurmalıdır. 

### <a name="consider-using-storage-queues"></a>Depolama kuyruklarını kullanmayı düşünün
Çözüm mimarı/geliştiricisi olarak, şu durumlarda **depolama kuyruklarını kullanmayı göz önünde bulundurmanız gerekir** :

* Uygulamanızın bir kuyruktaki 80 gigabayt 'tan fazla ileti depolaması gerekir.
* Uygulamanız kuyruktaki bir iletiyi işlemeye yönelik ilerlemeyi izlemek istiyor. Bir iletinin kilitlenmesinin kilitlenmesi durumunda bu faydalıdır. Daha sonra başka bir çalışan, bu bilgileri kullanarak önceki çalışan kaldığınız yerden devam edebilir.
* Kuyruklarınız için yürütülen tüm işlemlerin sunucu tarafında günlüklerine ihtiyacınız vardır.

### <a name="consider-using-service-bus-queues"></a>Service Bus kuyruklarını kullanmayı düşünün
Çözüm mimarı/geliştiricisi olarak, şu durumlarda **Service Bus kuyrukları kullanmayı göz önünde bulundurmanız gerekir** :

* Çözümünüzün kuyruğu yoklamaya gerek kalmadan ileti alması gerekir. Service Bus, Service Bus desteklediği TCP tabanlı protokolleri kullanarak uzun süreli bir alma işlemi kullanarak elde edebilirsiniz.
* Çözümünüz için kuyruğun, bir ilk ilk çıkar (FıFO) sıralı teslim sağlaması gerekir.
* Çözümünüzün otomatik yinelenen saptamayı desteklemesi gerekir.
* Uygulamanızın iletileri paralel uzun süreli akışlar olarak işlemesini istiyorsunuz (ileti, iletideki **oturum kimliği** özelliği kullanılarak bir akış ile ilişkilendirilir). Bu modelde, tüketim uygulamasındaki her düğüm, iletiler yerine akışlar için bir kullanıcı tarafından kabul edilebilir. Bir akış tüketen bir düğüme verildiğinde, düğüm işlemleri kullanarak uygulama akış durumunun durumunu inceleyebilir.
* Çözümünüz, bir kuyruktan birden çok ileti gönderirken veya alırken işlemsel davranış ve kararlılık gerektirir.
* Uygulamanız 64 KB 'ı aşan iletileri işler, ancak büyük olasılıkla 256 KB 'lik sınıra yaklaşımlar.
* Sıralara rol tabanlı erişim modeli sağlamak ve Gönderenler ve alıcılar için farklı haklar/izinler sağlamak üzere bir gereksinimle uğraşmanız gerekir. Daha fazla bilgi için aşağıdaki makaleleri inceleyin:
    - [Yönetilen kimliklerle kimlik doğrulaması](service-bus-managed-service-identity.md)
    - [Uygulamadan kimlik doğrulaması yapma](authenticate-application.md)
* Sıra boyutunuz 80 GB 'tan büyük büyümez.
* AMQP 1,0 standartları tabanlı mesajlaşma protokolünü kullanmak istiyorsunuz. AMQP hakkında daha fazla bilgi için bkz. [SERVICE Bus AMQP 'ye genel bakış](service-bus-amqp-overview.md).
* Kuyruk tabanlı noktadan noktaya iletişimden bir yayımlama-abone olma mesajlaşma düzenine son bir geçişi adım adım zenginleştecaksınız. Bu model ek alıcıların (aboneler) tümleştirilmesini mümkün. Her alıcı, bir veya daha fazla iletinin sıraya gönderildiği bağımsız kopyalarını alır. 
* İleti çözümünüz, ek altyapı bileşenleri oluşturmanıza gerek kalmadan "en çok bir kez" teslim garantisi 'nı desteklemelidir.
* Çözümünüzün, toplu iletileri yayımlaması ve kullanması gerekir.

## <a name="compare-storage-queues-and-service-bus-queues"></a>Depolama kuyruklarını ve Service Bus kuyruklarını karşılaştırın
Aşağıdaki bölümlerde bulunan tablolar, kuyruk özelliklerinin mantıksal bir gruplandırmasını sağlar. Her iki Azure depolama sıralarında ve Service Bus kuyruklarındaki özellikleri bir bakışta karşılaştırmanıza olanak sağlar.

## <a name="foundational-capabilities"></a>Temel alınan yetenekler
Bu bölümde, depolama kuyrukları ve Service Bus kuyrukları tarafından sunulan bazı temel sıraya alma özellikleri karşılaştırılır.

| Karşılaştırma ölçütleri | Depolama kuyrukları | Service Bus kuyrukları |
| --- | --- | --- |
| Sipariş garantisi |**Hayır** <br/><br>Daha fazla bilgi için [ek bilgi](#additional-information) bölümündeki ilk nota bakın.</br> | **Evet-Ilk çıkar (FıFO)**<br/><br>( [ileti oturumlarını](message-sessions.md)kullanarak) |
| Teslimat garantisi |**En az bir kez** |En **az bir kez** (PeekLock alma modunu kullanarak). Varsayılan değer) <br/><br/>**En çok bir kez** (ReceiveAndDelete alma modunu kullanarak) <br/> <br/> Çeşitli [alma modları](service-bus-queues-topics-subscriptions.md#receive-modes) hakkında daha fazla bilgi edinin  |
| Atomik işlem desteği |**Hayır** |**Evet**<br/><br/> |
| Alma davranışı |**Engellenmeyen**<br/><br/>(yeni bir ileti bulunmazsa hemen tamamlanır) |**Zaman aşımı ile veya olmadan engelleme**<br/><br/>(uzun yoklama sağlar veya ["Comet tekniği"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Engellenmeyen**<br/><br/>(yalnızca .NET yönetilen API kullanarak) |
| Gönderme stili API 'SI |**Hayır** |**Evet**<br/><br/>.NET, Java, JavaScript ve go SDK 'larımız, gönderim stili API 'SI sağlar. |
| Alma modu |**& kiralamaya göz atın** |**& kilidine Gözat**<br/><br/>**& silme al** |
| Özel erişim modu |**Kira tabanlı** |**Kilit tabanlı** |
| Kira/kilitleme süresi |**30 saniye (varsayılan)**<br/><br/>**7 gün (maksimum)** ( [updatemessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API 'sini kullanarak bir ileti kiralamasını yenileyebilir veya serbest bırakabilirsiniz.) |**30 saniye (varsayılan)**<br/><br/>Her seferinde aynı kilit süresine ait ileti kilitlemeyi yenileyebilir veya otomatik kilit yenileme özelliğini kullanarak istemcinin kilit yenilemesini sizin için yönettiğini sağlayabilirsiniz. |
| Kira/kilit hassasiyeti |**İleti düzeyi**<br/><br/>Her ileti farklı bir zaman aşımı değerine sahip olabilir. Bu, daha sonra iletiyi işlerken [updatemessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API 'sini kullanarak güncelleştirebilirsiniz. |**Sıra düzeyi**<br/><br/>(her kuyruğun tüm iletilerine uygulanan bir kilit duyarlığı vardır, ancak kilit önceki satırda açıklandığı şekilde yenilenebilir) |
| Toplu alma |**Evet**<br/><br/>(iletileri alırken ileti sayısını açıkça belirtme, en fazla 32 ileti) |**Evet**<br/><br/>(bir önceden getirme özelliğini örtülü olarak veya işlemler kullanılarak açıkça etkinleştirme) |
| Toplu gönder |**Hayır** |**Evet**<br/><br/>(işlemler veya istemci tarafı toplu işlem kullanarak) |

### <a name="additional-information"></a>Ek bilgiler
* Depolama sıralarındaki iletiler genellikle ilk kez ilk çıkar, ancak bazen sıra dışı olabilir. Örneğin, bir iletinin görünürlük zaman aşımı süresi dolduğunda bir istemci uygulama bir iletiyi işlerken çökirken. Görünürlük zaman aşımı süresi dolduğunda, ileti başka bir çalışanın onu kuyruğa almak için kuyrukta yeniden görünür hale gelir. Bu noktada, yeni görünür ileti sıraya alınmış olarak yeniden kuyruğa alınır.
* Service Bus kuyruklarındaki garantili FıFO deseninin mesajlaşma oturumlarının kullanılması gerekir. Uygulama, **Peek & kilit** modunda alınan bir iletiyi işlerken kilitlenirse, bir kuyruk alıcısı bir ileti oturumu kabul ettiğinde, iletinin yaşam SÜRESI (TTL) döneminin süresi dolduktan sonra başarısız iletisiyle başlar.
* Depolama kuyrukları, aşağıdakiler gibi standart sıraya alma senaryolarını destekleyecek şekilde tasarlanmıştır:
    - Hatalara karşı ölçeklenebilirlik ve toleransı artırmak için uygulama bileşenlerini Ayrıştır
    - Yük Dengeleme
    - İşlem iş akışları oluşturuluyor.
* Service Bus oturumlarının bağlamındaki ileti işleme ile ilgili tutarsızlıklar, oturum durumu kullanılarak, oturumun ileti sırasını işleme ilerleme durumuyla ilgili olarak uygulamanın durumunu depolamak için ve alınan iletilerin ve oturum durumunu güncelleştiren işlemler kullanılarak kaçınılabilir. Bu tür bir tutarlılık özelliği bazen diğer satıcının ürünlerinde *işleme olarak bir kez* etiketlidir. Tüm işlem sorunları, iletilerin yeniden gönderilmesine neden olur ve bu nedenle terim tam olarak yeterli değildir.
* Depolama kuyrukları, hem geliştiriciler hem de işlem takımları için kuyruklar, tablolar ve Bloblar genelinde Tekdüzen ve tutarlı bir programlama modeli sağlar.
* Service Bus kuyrukları, tek bir sıra bağlamında yerel işlemler için destek sağlar.
* Service Bus tarafından desteklenen **alma ve silme** modu, düşürülen teslim güvencesi için Exchange 'teki mesajlaşma işlem sayısını (ve ilişkili maliyeti) azaltma yeteneği sağlar.
* Depolama kuyrukları, iletiler için kiraları genişletme olanağı sunan kiralamalar sağlar. Bu özellik, çalışan işlemlerinin iletilerde kısa kiraları korumasına olanak tanır. Bu nedenle, bir çalışan kilitlenirse, ileti başka bir çalışan tarafından hızla yeniden işlenebilir. Ayrıca, bir çalışan bir ileti üzerinde kirayı, geçerli kira süresinden daha uzun süre işlemesi gerekiyorsa genişletebilir.
* Depolama kuyrukları, bir iletinin sıraya alınması veya kuyruğa çıkarılması üzerinde ayarlayabileceğiniz bir görünürlük zaman aşımı sağlar. Ayrıca, çalışma zamanında farklı kira değerleriyle bir ileti güncelleştirebilir ve aynı kuyruktaki iletiler arasında farklı değerleri güncelleştirebilirsiniz. Service Bus kilit zaman aşımları sıra meta verilerinde tanımlanmıştır. Ancak, önceden tanımlı kilit süresi için ileti kilitlemeyi el ile yenileyebilir veya istemcinin kilit yenilemesini sizin için yönettiği otomatik kilit yenileme özelliğini kullanabilirsiniz.
* Service Bus kuyruklarındaki engelleme alma işlemi için maksimum zaman aşımı 24 gündür. Ancak, REST tabanlı zaman aşımları 55 saniyelik en büyük değere sahiptir.
* Service Bus tarafından sunulan istemci tarafı toplu işleme, bir kuyruk istemcisinin birden çok iletiyi tek bir gönderme işleminde toplu olarak oluşturmasını sağlar. Toplu işleme yalnızca zaman uyumsuz gönderme işlemleri için kullanılabilir.
* 200 TB 'lık depolama sıralarının üst sınırı (hesapları sanallaştırdığınızda daha fazla) ve sınırsız kuyruk, SaaS sağlayıcıları için ideal bir platform haline getirir.
* Depolama kuyrukları, esnek ve performansa sahip bir erişim denetimi mekanizması sağlar.

## <a name="advanced-capabilities"></a>Gelişmiş yetenekler
Bu bölümde, depolama kuyrukları ve Service Bus kuyrukları tarafından sunulan gelişmiş yetenekler karşılaştırılır.

| Karşılaştırma ölçütleri | Depolama kuyrukları | Service Bus kuyrukları |
| --- | --- | --- |
| Zamanlanmış teslim |**Evet** |**Evet** |
| Otomatik olarak atılacak |**Hayır** |**Evet** |
| Sıra yaşam süresi değerini artırma |**Evet**<br/><br/>(görünürlük zaman aşımı ile yerinde güncelleştirme aracılığıyla) |**Evet**<br/><br/>(özel bir API işlevi ile sunulmaktadır) |
| Zarar iletisi desteği |**Evet** |**Evet** |
| Yerinde güncelleştirme |**Evet** |**Evet** |
| Sunucu tarafı işlem günlüğü |**Evet** |**Hayır** |
| Depolama ölçümleri |**Evet**<br/><br/>**Dakikalık ölçümler** kullanılabilirlik, TPS, API çağrı sayıları, hata sayıları ve daha fazlası için gerçek zamanlı ölçümler sağlar. Bunlar gerçek zamanlı olarak, dakikada toplanan ve birkaç dakika içinde, üretimde gerçekleşen süreden itibaren raporlanır. Daha fazla bilgi için bkz. [depolama Analizi ölçümleri hakkında](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Evet**<br/><br/>Azure Service Bus tarafından desteklenen ölçümler hakkında daha fazla bilgi için bkz. [ileti ölçümleri](service-bus-metrics-azure-monitor.md#message-metrics). |
| Durum yönetimi |**Hayır** |**Evet** (etkin, devre dışı, Senddisabled, receivedisabled. Bu durumlarıyla ilgili ayrıntılar için, bkz. [sıra durumu](entity-suspend.md#queue-status)) |
| İletiyi oto iletme |**Hayır** |**Evet** |
| Temizleme kuyruğu işlevi |**Evet** |**Hayır** |
| İleti grupları |**Hayır** |**Evet**<br/><br/>(mesajlaşma oturumlarını kullanarak) |
| İleti grubu başına uygulama durumu |**Hayır** |**Evet** |
| Yineleme algılama |**Hayır** |**Evet**<br/><br/>(Gönderen tarafında yapılandırılabilir) |
| İleti gruplarına göz atma |**Hayır** |**Evet** |
| KIMLIĞE göre ileti oturumları getiriliyor |**Hayır** |**Evet** |

### <a name="additional-information"></a>Ek bilgiler
* Her iki sıraya alma teknolojisi de bir iletinin daha sonra teslim edilmek üzere zamanlanmasını sağlar.
* Sıra oto iletme, binlerce sıranın iletileri, alıcı uygulamanın iletiyi tükettiği tek bir kuyruğa iletmesini sağlar. Bu mekanizmayı, güvenlik ve denetim akışı sağlamak ve her bir ileti yayımcısı arasında depolamayı yalıtmak için kullanabilirsiniz.
* Depolama kuyrukları ileti içeriğini güncelleştirmek için destek sağlar. Bu işlevi, kalıcı durum bilgileri ve artımlı ilerleme güncellemeleri için, sıfırdan başlamak yerine, bilinen son denetim noktasından işlenebilmesi için kullanabilirsiniz. Service Bus kuyruklarında, ileti oturumlarını kullanarak aynı senaryoya izin verebilirsiniz. Daha fazla bilgi için bkz. [ileti oturumu durumu](message-sessions.md#message-session-state).
* Service Bus kuyrukları, [atılacak](service-bus-dead-letter-queues.md)araçları destekler. Aşağıdaki ölçütlere uyan iletileri yalıtmak yararlı olabilir:
    - İletiler, alıcı uygulama tarafından başarıyla işlenemiyor 
    - Süresi biten yaşam süresi (TTL) özelliği nedeniyle iletiler hedefine ulaşamamanıza izin vermez. TTL değeri, bir iletinin kuyrukta ne kadar süreyle kalacağını belirtir. Service Bus, bu ileti TTL süresi sona erdiğinde $DeadLetterQueue adlı özel bir kuyruğa taşınır.
* Depolama sıralarında "Poison" iletileri bulmak için, bir iletiyi sıradan kaldırdığınızda, uygulamanın [Dequeuecount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) özelliğini inceler. **Dequeuecount** verilen eşikten büyükse, uygulama iletiyi uygulama tanımlı "atılacak mektup" kuyruğuna taşımalıdır.
* Depolama kuyrukları, sıraya göre yürütülen tüm işlemlerin ayrıntılı bir günlüğünü ve toplu ölçümleri elde etmeniz sağlar. Bu seçeneklerin her ikisi de hata ayıklama ve uygulamanızın depolama kuyruklarını nasıl kullandığını anlamak için yararlıdır. Ayrıca, uygulamanızı performans ayarlaması ve kuyrukları kullanmanın maliyetlerini azaltma için de kullanışlıdır.
* Service Bus tarafından desteklenen [ileti oturumları](message-sessions.md) bir mantıksal gruba ait olan iletileri bir alıcı ile ilişkilendirilecek şekilde etkinleştirir. İletiler ve ilgili alıcılar arasında oturum benzeri bir benzeşim oluşturur. İleti üzerinde oturum KIMLIĞI özelliğini ayarlayarak Service Bus bu gelişmiş işlevselliği etkinleştirebilirsiniz. Alıcılar daha sonra belirli bir oturum KIMLIĞI üzerinde dinleme yapabilir ve belirtilen oturum tanımlayıcısını paylaşan iletiler alabilir.
* Service Bus kuyruklarının çoğaltma algılaması özelliği, ileti KIMLIĞI özelliğinin değerine bağlı olarak bir kuyruğa veya konuya gönderilen yinelenen iletileri otomatik olarak kaldırır.

## <a name="capacity-and-quotas"></a>Kapasite ve Kotalar
Bu bölümde, depolama kuyrukları ve Service Bus kuyrukları, uygulayabilen [Kapasite ve Kotalar](service-bus-quotas.md) açısından karşılaştırılır.

| Karşılaştırma ölçütleri | Depolama kuyrukları | Service Bus kuyrukları |
| --- | --- | --- |
| En büyük sıra boyutu |**500 TB**<br/><br/>( [tek bir depolama hesabı kapasitesine](../storage/common/storage-introduction.md#queue-storage)sınırlı) |**1 GB ila 80 GB**<br/><br/>(bir kuyruk oluşturulduktan ve [bölümleme etkinleştirildikten](service-bus-partitioning.md) sonra tanımlanır), "ek bilgi" bölümüne bakın |
| En büyük ileti boyutu |**64 KB**<br/><br/>( **Base64** kodlaması KULLANıLıRKEN 48 KB)<br/><br/>Azure, kuyrukları ve Blobları birleştirerek büyük iletileri destekler – bu noktada tek bir öğe için 200 GB 'a kadar sıraya alabilirsiniz. |**256 KB** veya **1 MB**<br/><br/>(başlık ve gövde dahil, en büyük üst bilgi boyutu: 64 KB).<br/><br/>[Hizmet katmanına](service-bus-premium-messaging.md)bağlıdır. |
| En fazla ileti TTL |**Sonsuz** (API-sürüm 2017-07-27 veya üzeri) |**TimeSpan. Max** |
| En fazla sıra sayısı |**Sınırsız** |**10.000**<br/><br/>(hizmet ad alanı başına) |
| En fazla eş zamanlı istemci sayısı |**Sınırsız** |**5.000** |

### <a name="additional-information"></a>Ek bilgiler
* Service Bus kuyruk boyutu sınırlarını zorlar. Kuyruk oluşturulurken en büyük sıra boyutu belirtilir. 1 GB ile 80 GB arasında olabilir. Kuyruğun boyutu bu sınıra ulaşırsa, ek gelen iletiler reddedilir ve çağıran bir özel durum alır. Service Bus kotaları hakkında daha fazla bilgi için bkz. [Service Bus kotalar](service-bus-quotas.md).
* Bölüm, [Premium katmanda](service-bus-premium-messaging.md)desteklenmez. Standart Mesajlaşma katmanında, 1 (varsayılan), 2, 3, 4 veya 5 GB boyutunda Service Bus kuyruklar ve konular oluşturabilirsiniz. Bölümlendirme etkinken, Service Bus aynı boyuttaki her biri varlığın 16 kopyasını (16 Bölüm) oluşturur. Bu nedenle, boyutu 5 GB olan ve 16 bölümlü bir sıra oluşturursanız, en büyük sıra boyutu (5 * 16) = 80 GB olur.  [Azure Portal][Azure portal]bölümlenmiş kuyruğun veya konusunun en büyük boyutunu görebilirsiniz.
* Depolama kuyrukları ile, iletinin içeriği XML güvenli değilse, **Base64** kodlamalı olmalıdır. İletiyi **Base64** olarak kodlarsanız, kullanıcı yükü 64 kb yerıne 48 KB 'ye kadar olabilir.
* Service Bus kuyruklarında, bir sırada depolanan her ileti iki bölümden oluşur: başlık ve gövde. İletinin toplam boyutu hizmet katmanının desteklediği en büyük ileti boyutunu aşamaz.
* İstemciler TCP protokolü üzerinden Service Bus kuyruklarında iletişim kurduğunda, tek bir Service Bus kuyruğuna en fazla eşzamanlı bağlantı sayısı 100 ile sınırlıdır. Bu sayı, Gönderenler ve alıcılar arasında paylaşılır. Bu kotaya ulaşıldığında, ek bağlantı istekleri reddedilir ve çağıran kod tarafından bir özel durum alınır. Bu sınır, REST tabanlı API kullanılarak kuyruklara bağlanan istemcilere uygulanmaz.
* Tek bir Service Bus ad alanında 10.000 ' den fazla kuyruk gerekiyorsa, Azure destek ekibine başvurarak bir artış isteyebilirsiniz. 10.000 kuyruklarının ötesinde Service Bus ölçeklendirmek için [Azure Portal][Azure portal]kullanarak ek ad alanları da oluşturabilirsiniz.

## <a name="management-and-operations"></a>Yönetim ve işlemler
Bu bölümde, depolama kuyrukları ve Service Bus kuyrukları tarafından sunulan yönetim özellikleri karşılaştırılır.

| Karşılaştırma ölçütleri | Depolama kuyrukları | Service Bus kuyrukları |
| --- | --- | --- |
| Yönetim Protokolü |**HTTP/HTTPS üzerinden REST** |**HTTPS üzerinden GERI dön** |
| Çalışma zamanı Protokolü |**HTTP/HTTPS üzerinden REST** |**HTTPS üzerinden GERI dön**<br/><br/>**AMQP 1,0 standart (TLS ile TCP)** |
| .NET API’si |**Evet**<br/><br/>(.NET Storage Istemci API 'SI) |**Evet**<br/><br/>(.NET Service Bus API) |
| Yerel C++ |**Evet** |**Evet** |
| Java API’si |**Evet** |**Evet** |
| PHP APı 'SI |**Evet** |**Evet** |
| Node.js API’si |**Evet** |**Evet** |
| Rastgele meta veri desteği |**Evet** |**Hayır** |
| Kuyruk adlandırma kuralları |**En fazla 63 karakter uzunluğunda**<br/><br/>(Bir kuyruk adındaki harfler küçük harfle yazılmalıdır.) |**En fazla 260 karakter uzunluğunda**<br/><br/>(Kuyruk yolları ve adları büyük/küçük harfe duyarlıdır.) |
| Sıra uzunluğu alma işlevi |**Evet**<br/><br/>(İletilerin silinmeksizin TTL 'nin ötesinde yaklaşık değer.) |**Evet**<br/><br/>(Tam, zaman içinde nokta değeri.) |
| Peek işlevi |**Evet** |**Evet** |

### <a name="additional-information"></a>Ek bilgiler
* Depolama kuyrukları, ad/değer çiftleri biçiminde kuyruk açıklamasına uygulanabilen rastgele öznitelikler için destek sağlar.
* Her iki kuyruk teknolojisi de bir iletiyi kilitlemeye gerek kalmadan, bir kuyruk Gezgini/tarayıcı aracı uygularken yararlı olabilecek bir iletiye göz atma özelliği sunar.
* Service Bus .NET Aracılı mesajlaşma API 'Leri, HTTP üzerinden REST ile karşılaştırıldığında gelişmiş performans için tam çift yönlü TCP bağlantıları kullanır ve AMQP 1,0 standart protokolünü destekler.
* Depolama sıralarının adları 3-63 karakter uzunluğunda olabilir, küçük harf, sayı ve kısa çizgi içerebilir. Daha fazla bilgi için bkz. [ad kuyrukları ve meta verileri](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Service Bus kuyruk adları en fazla 260 karakter uzunluğunda olabilir ve daha az kısıtlayıcı adlandırma kuralına sahip olabilir. Service Bus kuyruk adları harf, sayı, nokta, kısa çizgi ve alt çizgi içerebilir.

## <a name="authentication-and-authorization"></a>Kimlik doğrulaması ve yetkilendirme
Bu bölümde, depolama kuyrukları ve Service Bus kuyrukları tarafından desteklenen kimlik doğrulama ve yetkilendirme özellikleri açıklanmaktadır.

| Karşılaştırma ölçütleri | Depolama kuyrukları | Service Bus kuyrukları |
| --- | --- | --- |
| Kimlik Doğrulaması |**Simetrik anahtar** |**Simetrik anahtar** |
| Güvenlik modeli |SAS belirteçleri aracılığıyla erişim temsilcisi. |SAS |
| Kimlik sağlayıcısı Federasyonu |**Evet** |**Evet** |

### <a name="additional-information"></a>Ek bilgiler
* Sıraya alma teknolojilerinin her birine yönelik her bir isteğin kimliğinin doğrulanması gerekir. Anonim erişimi olan genel kuyruklar desteklenmez. [SAS](service-bus-sas.md)kullanarak, bu senaryoya yalnızca bir salt yazılır SAS, salt okunurdur ve hatta tam erişimli SAS yayımlayarak erişebilirsiniz.
* Depolama kuyrukları tarafından sunulan kimlik doğrulama düzeni, simetrik anahtar kullanımını içerir. Bu anahtar, SHA-256 algoritması ile hesaplanan ve **Base64** dizesi olarak kodlanan, karma tabanlı bir İLETI KIMLIK doğrulama kodu (HMAC). İlgili protokol hakkında daha fazla bilgi için bkz. [Azure depolama hizmetleri Için kimlik doğrulama](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Service Bus kuyrukları, simetrik anahtarlar kullanan benzer bir modeli destekler. Daha fazla bilgi için bkz. [Service Bus Ile paylaşılan erişim Imzası kimlik doğrulaması](service-bus-sas.md).

## <a name="conclusion"></a>Sonuç
İki teknolojiyi daha ayrıntılı bir şekilde öğrenerek hangi kuyruk teknolojisinin kullanılacağı ve ne zaman daha bilinçli bir karar elde edebilirsiniz. Depolama sıralarının veya Service Bus sıralarının ne zaman kullanılacağı kararı, birçok faktöre bağlıdır. Bu faktörler, uygulamanızın ve mimarinin mimarisine göre büyük ölçüde değişebilir. 

Aşağıdakiler gibi nedenlerle depolama kuyrukları arasından seçim yapmayı tercih edebilirsiniz:

- Uygulamanız Microsoft Azure temel yeteneklerini zaten kullanıyorsa 
- Hizmetler arasında temel iletişim ve mesajlaşma istemek istiyorsanız 
- Boyut 80 GB 'tan daha büyük olabilecek kuyruklar gerekir

Service Bus kuyrukları, aşağıdakiler gibi birçok gelişmiş özellik sağlar. Bu nedenle, karma bir uygulama oluşturuyorsanız veya uygulamanız Aksi takdirde bu özellikleri gerektiriyorsa, tercih edilen bir seçenek olabilir.

- [Oturumlar](message-sessions.md)
- [İşlemler](service-bus-transactions.md)
- [Yineleme algılama](duplicate-detection.md)
- [Otomatik atılacak](service-bus-dead-letter-queues.md)
- [Dayanıklı yayımlama ve abone olma özellikleri](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 



## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelerde, depolama kuyruklarını veya Service Bus kuyruklarını kullanma hakkında daha fazla rehberlik ve bilgi sağlanmaktadır.

* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Kuyruk depolama hizmetini kullanma](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Service Bus Aracılı mesajlaşma kullanarak performans geliştirmeleri için en iyi uygulamalar](service-bus-performance-improvements.md)
* [Azure Service Bus (blog gönderisi) kuyrukları ve konuları tanıtma](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Geliştirici Kılavuzu Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Azure 'da sıraya alma hizmetini kullanma](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

