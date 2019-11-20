---
title: Azure depolama kuyruklarını ve Service Bus kuyruklarını karşılaştırın
description: Azure tarafından sunulan iki kuyruk türü arasındaki farkları ve benzerlikleri analiz eder.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: a1e75416db34514425436bc3ceae9f27b156b557
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792695"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Depolama kuyrukları ve Service Bus kuyrukları-karşılaştırılan ve değişken maliyetli
Bu makalede, bugün Microsoft Azure tarafından sunulan iki kuyruk türü arasındaki farklar ve benzerlikler analiz edilir: depolama kuyrukları ve Service Bus kuyrukları. Bu bilgileri kullanarak, ilgili teknolojileri karşılaştırabilir ve gereksinimlerinize en uygun çözümü seçerken daha bilinçli kararlar verebilirsiniz.

## <a name="introduction"></a>Tanıtım
Azure iki tür kuyruk mekanizmasını destekler: **depolama kuyrukları** ve **Service Bus kuyrukları**.

[Azure depolama](https://azure.microsoft.com/services/storage/) altyapısının bir parçası olan **depolama kuyrukları**, hizmetler içinde ve arasında güvenilir ve kalıcı mesajlaşma sağlayan basıt bir REST tabanlı Get/put/Peek arabirimi özelliği sunar.

**Service Bus kuyruklar** , kuyruğa almayı ve yayımlamayı/abone olmayı ve daha gelişmiş tümleştirme düzenlerini destekleyen, daha geniş bir [Azure mesajlaşma](https://azure.microsoft.com/services/service-bus/) altyapısının parçasıdır. Service Bus kuyrukları/konuları/abonelikleri hakkında daha fazla bilgi için bkz. [Service Bus genel bakış](service-bus-messaging-overview.md).

Her iki sıraya alma teknolojisi de aynı anda mevcut olsa da, Azure depolama hizmetleri üzerinde oluşturulmuş adanmış bir sıra depolama mekanizması olarak depolama kuyrukları ilk sunulmuştur. Service Bus kuyrukları, birden fazla iletişim kuralını, veri sözleşmelerini, güven etki alanlarını ve/veya ağ ortamlarına yayılabilen uygulamaları veya uygulama bileşenlerini bütünleştirmek için tasarlanan daha geniş mesajlaşma altyapısının üzerine kurulmuştur.

## <a name="technology-selection-considerations"></a>Teknoloji seçimi konuları
Hem depolama kuyrukları hem de Service Bus kuyrukları, şu anda Microsoft Azure tarafından sunulan Message Queuing hizmetinin uygulamalarıdır. Her birinin biraz farklı bir özellik kümesi vardır. Bu, bir ya da diğerini seçebileceğiniz ya da çözmeyeceğiniz iş/teknik sorununuzun gereksinimlerine bağlı olarak her ikisini de kullanabilirsiniz.

Belirli bir çözüm için hangi sıraya alma teknolojisinin amacına uygun olduğunu belirlerken, çözüm mimarları ve geliştiriciler bu önerileri göz önünde bulundurmalıdır. Daha ayrıntılı bilgi için sonraki bölüme bakın.

Çözüm mimarı/geliştiricisi olarak, şu durumlarda **depolama kuyruklarını kullanmayı göz önünde bulundurmanız gerekir** :

* Uygulamanızın bir kuyrukta 80 GB 'den fazla ileti depolaması gerekir.
* Uygulamanız, sıranın içindeki bir iletiyi işlemeye yönelik ilerlemeyi izlemek istiyor. Bu, bir iletinin kilitlenmesinin kilitlenmesi durumunda yararlıdır. Sonraki bir çalışan daha sonra bu bilgileri kullanarak önceki çalışan kaldığınız yerden devam edebilir.
* Kuyruklarınız için yürütülen tüm işlemlerin sunucu tarafında günlüklerine ihtiyacınız vardır.

Çözüm mimarı/geliştiricisi olarak, şu durumlarda **Service Bus kuyrukları kullanmayı göz önünde bulundurmanız gerekir** :

* Çözümünüzün kuyruğu yoklamaya gerek kalmadan ileti alabilmesi gerekir. Service Bus, bu, Service Bus desteklediği TCP tabanlı protokoller kullanılarak uzun yoklama alma işleminin kullanımı aracılığıyla elde edilebilir.
* Çözümünüz için kuyruğun, bir ilk ilk çıkar (FıFO) sıralı teslim sağlaması gerekir.
* Çözümünüzün otomatik yinelenen saptamayı destekleyebilmesi gerekir.
* Uygulamanızın iletileri paralel uzun süreli akışlar olarak işlemesini istiyorsunuz (ileti, iletideki [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) özelliği kullanılarak bir akışla ilişkilendirilir). Bu modelde, tüketim uygulamasındaki her düğüm, iletiler yerine akışlar için bir kullanıcı tarafından kabul edilebilir. Bir akış tüketen bir düğüme verildiğinde, düğüm işlemleri kullanarak uygulama akış durumunun durumunu inceleyebilir.
* Çözümünüz, bir kuyruktan birden çok ileti gönderirken veya alırken işlemsel davranış ve kararlılık gerektirir.
* Uygulamanız 64 KB 'ı aşan iletileri işler, ancak 256 KB sınırına büyük olasılıkla yaklaşımlar.
* Sıralara rol tabanlı erişim modeli sağlamak ve Gönderenler ve alıcılar için farklı haklar/izinler sağlamak üzere bir gereksinimle uğraşmanız gerekir. Daha fazla bilgi için aşağıdaki makalelere bakın:
    - [Yönetilen kimliklerle kimlik doğrulama](service-bus-managed-service-identity.md)
    - [Uygulamadan kimlik doğrulama](authenticate-application.md)
* Sıra boyutunuz 80 GB 'tan büyük büyümez.
* AMQP 1,0 standartları tabanlı mesajlaşma protokolünü kullanmak istiyorsunuz. AMQP hakkında daha fazla bilgi için bkz. [SERVICE Bus AMQP 'ye genel bakış](service-bus-amqp-overview.md).
* Sıra tabanlı noktadan noktaya iletişimden, her biri veya tümünün bağımsız kopyalarını alan ek alıcıların (aboneler) sorunsuz tümleştirilmesine olanak tanıyan bir ileti değişimi düzenine kadar son bir geçiş yapabilirsiniz. sıraya gönderilen iletiler. İkincisi, Service Bus tarafından yerel olarak sağlanmış yayımla/abone olma özelliğine başvurur.
* Mesajlaşma çözümünüz, ek altyapı bileşenleri oluşturmanıza gerek kalmadan "en çok bir kez" teslim garantisi 'nı destekleyebilmelidir.
* İletileri toplu olarak yayımlayabilmek ve tüketmek istiyorsunuz.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Depolama kuyruklarını ve Service Bus sıralarını karşılaştırma
Aşağıdaki bölümlerde yer alarak bulunan tablolar, kuyruk özelliklerinin mantıksal bir gruplandırmasını sağlar ve her iki Azure depolama kuyruğunda ve Service Bus kuyruklarındaki özellikleri bir bakışta karşılaştırmanıza imkan tanır.

## <a name="foundational-capabilities"></a>Temel alınan yetenekler
Bu bölümde, depolama kuyrukları ve Service Bus kuyrukları tarafından sunulan bazı temel sıraya alma özellikleri karşılaştırılır.

| Karşılaştırma ölçütleri | Depolama kuyrukları | Hizmet Veri Yolu kuyrukları |
| --- | --- | --- |
| Sipariş garantisi |**Hayır** <br/><br>Daha fazla bilgi için "ek bilgiler" bölümündeki ilk nota bakın.</br> |**Evet-Ilk çıkar (FıFO)**<br/><br>(mesajlaşma oturumlarının kullanımı aracılığıyla) |
| Teslimat garantisi |**En az bir kez** |En **az bir kez** (PeekLock alma modunu kullanarak-bu varsayılandır) <br/><br/>**En çok bir kez** (ReceiveAndDelete alma modunu kullanarak) <br/> <br/> Çeşitli [alma modları](service-bus-queues-topics-subscriptions.md#receive-modes) hakkında daha fazla bilgi edinin  |
| Atomik işlem desteği |**Hayır** |**Evet**<br/><br/> |
| Alma davranışı |**Engellenmeyen**<br/><br/>(yeni bir ileti bulunmazsa hemen tamamlanır) |**Zaman aşımı ile/olmadan engelleme**<br/><br/>(uzun yoklama sağlar veya ["Comet tekniği"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Engellenmeyen**<br/><br/>(yalnızca .NET yönetilen API kullanımı aracılığıyla) |
| Gönderme stili API 'SI |**Hayır** |**Evet**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) ve **OnMessage** oturumları .NET API 'si. |
| Alma modu |**& Kiralamaya göz atın** |**& Kilidine Gözat**<br/><br/>**& Silme al** |
| Özel erişim modu |**Kira tabanlı** |**Kilit tabanlı** |
| Kira/kilitleme süresi |**30 saniye (varsayılan)**<br/><br/>**7 gün (maksimum)** ( [updatemessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API 'sini kullanarak bir ileti kiralamasını yenileyebilir veya serbest bırakabilirsiniz.) |**60 saniye (varsayılan)**<br/><br/>[Yenilenebilir kilit](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API 'sini kullanarak bir ileti kilidini yenileyebilirsiniz. |
| Kira/kilit hassasiyeti |**İleti düzeyi**<br/><br/>(her ileti farklı bir zaman aşımı değerine sahip olabilir ve bu, daha sonra iletiyi işlerken, [updatemessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API 'sini kullanarak güncelleştirebilirsiniz) |**Sıra düzeyi**<br/><br/>(her kuyruğun tüm iletilerine uygulanan bir kilit duyarlığı vardır, ancak bu kilidi [renewlock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API 'sini kullanarak yenileyebilirsiniz.) |
| Toplu alma |**Evet**<br/><br/>(iletileri alırken ileti sayısını açıkça belirtme, en fazla 32 ileti) |**Evet**<br/><br/>(bir önceden getirme özelliğini örtülü olarak veya işlem kullanımı aracılığıyla açıkça etkinleştirme) |
| Toplu gönder |**Hayır** |**Evet**<br/><br/>(işlemler veya istemci tarafı toplu işlem kullanımı aracılığıyla) |

### <a name="additional-information"></a>Ek Bilgi
* Depolama sıralarındaki iletiler genellikle ilk kez ilk çıkar, ancak bazen sıra dışı olabilir; Örneğin, bir iletinin görünürlük zaman aşımı süresi dolduğunda (örneğin, işleme sırasında kilitlenen bir istemci uygulaması sonucu olarak). Görünürlük zaman aşımı süresi dolduğunda, ileti başka bir çalışanın onu kuyruğa almak için kuyrukta yeniden görünür hale gelir. Bu noktada, önceden sıraya alınmış bir iletiden sonra, yeni görünür ileti sıraya yerleştirilebilir (yeniden kuyruğa alınır).
* Service Bus kuyruklarındaki garantili FıFO deseninin mesajlaşma oturumlarının kullanılması gerekir. Uygulamanın, **Peek & kilit** modunda alınan bir iletiyi işlerken çöktüğü durumda, bir kuyruk alıcısının bir ileti oturumunu kabul ettiği bir sonraki sefer, bu işlem, yaşam SÜRESI (TTL) süresi dolduktan sonra başarısız iletiyle başlar.
* Depolama kuyrukları, hatalara yönelik ölçeklenebilirlik ve toleransı artırmak, yük dengeleme ve işlem iş akışları oluşturmak için uygulama bileşenlerini ayırma gibi standart sıraya alma senaryolarını desteklemek üzere tasarlanmıştır.
* Service Bus oturumlarının bağlamında ileti işlemeye ilişkin tutarsızlıklar, oturum durumu kullanılarak, oturumun ileti sırasını işleme ilerleme durumuyla ilgili olarak uygulamanın durumunu depolamak için ve alınan iletiler kapatılırken ve oturum durumu güncelleştiriliyor. Bu tür bir tutarlılık özelliği bazen diğer satıcının ürünlerinde *tam olarak bir kez Işlem olarak* etiketlidir, ancak işlem sorunları, iletilerin yeniden gönderilmesine neden olur ve bu nedenle terim tam olarak yeterli değildir.
* Depolama kuyrukları, hem geliştiriciler hem de işlem takımları için kuyruklar, tablolar ve Bloblar genelinde Tekdüzen ve tutarlı bir programlama modeli sağlar.
* Service Bus kuyrukları, tek bir sıra bağlamında yerel işlemler için destek sağlar.
* Service Bus tarafından desteklenen **alma ve silme** modu, düşürülen teslim güvencesi için Exchange 'teki mesajlaşma işlem sayısını (ve ilişkili maliyeti) azaltma yeteneği sağlar.
* Depolama kuyrukları, iletiler için kiraları genişletme olanağı sunan kiralamalar sağlar. Bu, çalışanların iletilerde kısa kiralamalar bulundurmasını sağlar. Bu nedenle, bir çalışan kilitlenirse, ileti başka bir çalışan tarafından hızlı bir şekilde yeniden işlenebilir. Ayrıca, bir çalışan bir ileti üzerinde kirayı, geçerli kira süresinden daha uzun süre işlemesi gerekiyorsa genişletebilir.
* Depolama kuyrukları, bir iletinin sıraya alınması veya kuyruğa çıkarılması üzerinde ayarlayabileceğiniz bir görünürlük zaman aşımı sağlar. Ayrıca, çalışma zamanında farklı kira değerleriyle bir ileti güncelleştirebilir ve aynı kuyruktaki iletilerde farklı değerleri güncelleştirebilirsiniz. Service Bus kilit zaman aşımları sıra meta verilerinde tanımlanmıştır; Ancak, [yenilenebilir kilit](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) yöntemini çağırarak kilidi yenileyebilirsiniz.
* Service Bus kuyruklarındaki engelleme alma işlemi için maksimum zaman aşımı 24 gündür. Ancak, REST tabanlı zaman aşımları 55 saniyelik en büyük değere sahiptir.
* Service Bus tarafından sunulan istemci tarafı toplu işleme, bir kuyruk istemcisinin birden çok iletiyi tek bir gönderme işleminde toplu olarak oluşturmasını sağlar. Toplu işleme yalnızca zaman uyumsuz gönderme işlemleri için kullanılabilir.
* 200 TB 'lık depolama sıralarının üst sınırı (hesapları sanallaştırdığınızda daha fazla) ve sınırsız kuyruk, SaaS sağlayıcıları için ideal bir platform haline getirir.
* Depolama kuyrukları, esnek ve performansa sahip bir erişim denetimi mekanizması sağlar.

## <a name="advanced-capabilities"></a>Gelişmiş yetenekler
Bu bölümde, depolama kuyrukları ve Service Bus kuyrukları tarafından sunulan gelişmiş yetenekler karşılaştırılır.

| Karşılaştırma ölçütleri | Depolama kuyrukları | Hizmet Veri Yolu kuyrukları |
| --- | --- | --- |
| Zamanlanmış teslim |**Evet** |**Evet** |
| Otomatik olarak atılacak |**Hayır** |**Evet** |
| Sıra yaşam süresi değerini artırma |**Evet**<br/><br/>(görünürlük zaman aşımı ile yerinde güncelleştirme aracılığıyla) |**Evet**<br/><br/>(özel bir API işlevi ile sunulmaktadır) |
| Zarar iletisi desteği |**Evet** |**Evet** |
| Yerinde güncelleştirme |**Evet** |**Evet** |
| Sunucu tarafı işlem günlüğü |**Evet** |**Hayır** |
| Depolama ölçümleri |**Evet**<br/><br/>**Dakikalık ölçümler**: kullanılabilirlik, TPS, API çağrı sayısı, hata sayısı ve daha fazlası için gerçek zamanlı ölçümler sağlar. her şeyi gerçek zamanlı olarak (dakikada toplanan ve birkaç dakika içinde raporlanır. Daha fazla bilgi için bkz. [depolama Analizi ölçümleri hakkında](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Evet**<br/><br/>( [Getqueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)çağırarak toplu sorgular) |
| Durum yönetimi |**Hayır** |**Evet**<br/><br/>[Microsoft. ServiceBus. Messaging. EntityStatus. ACTIVE](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. DISABLED](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. senddisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [ Microsoft. ServiceBus. Messaging. EntityStatus. ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| İleti otomatik iletme |**Hayır** |**Evet** |
| Temizleme kuyruğu işlevi |**Evet** |**Hayır** |
| İleti grupları |**Hayır** |**Evet**<br/><br/>(mesajlaşma oturumlarının kullanımı aracılığıyla) |
| İleti grubu başına uygulama durumu |**Hayır** |**Evet** |
| Yineleme algılama |**Hayır** |**Evet**<br/><br/>(Gönderen tarafında yapılandırılabilir) |
| İleti gruplarına göz atma |**Hayır** |**Evet** |
| KIMLIĞE göre ileti oturumları getiriliyor |**Hayır** |**Evet** |

### <a name="additional-information"></a>Ek Bilgi
* Her iki sıraya alma teknolojisi de bir iletinin daha sonra teslim edilmek üzere zamanlanmasını sağlar.
* Kuyruk otomatik iletme, binlerce sıranın iletileri, alıcı uygulamanın iletiyi tükettiği tek bir sıraya otomatik olarak iletmesini sağlar. Bu mekanizmayı, güvenlik ve denetim akışı sağlamak ve her bir ileti yayımcısı arasında depolamayı yalıtmak için kullanabilirsiniz.
* Depolama kuyrukları ileti içeriğini güncelleştirmek için destek sağlar. Bu işlevi, kalıcı durum bilgileri ve artımlı ilerleme güncellemeleri için, sıfırdan başlamak yerine, bilinen son denetim noktasından işlenebilmesi için kullanabilirsiniz. Service Bus kuyruklarında, ileti oturumlarının kullanımı aracılığıyla aynı senaryoyu etkinleştirebilirsiniz. Oturumlar, uygulama işleme durumunu kaydetmenizi ve almanızı sağlar ( [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) ve [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)kullanarak).
* Yalnızca Service Bus kuyrukları tarafından desteklenen [yok sayılma](service-bus-dead-letter-queues.md), alıcı uygulama tarafından başarılı bir şekilde işlenemeyen iletileri yalıtmak veya süresi dolan yaşam SÜRESI (TTL) nedeniyle iletiler hedefe ulaşamadığınızda yararlı olabilir özelliði. TTL değeri, bir iletinin kuyrukta ne kadar süreyle kalacağını belirtir. Service Bus, bu ileti TTL süresi sona erdiğinde $DeadLetterQueue adlı özel bir kuyruğa taşınır.
* Depolama sıralarında "Poison" iletileri bulmak için, bir iletiyi sıradan kaldırdığınızda, uygulamanın [Dequeuecount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) özelliğini inceler. **Dequeuecount** verilen eşikten büyükse, uygulama iletiyi uygulama tanımlı "atılacak mektup" kuyruğuna taşımalıdır.
* Depolama kuyrukları, sıraya göre yürütülen tüm işlemlerin yanı sıra toplanan ölçümler elde etmeniz için ayrıntılı bir günlük sağlar. Bu seçeneklerin her ikisi de hata ayıklama ve uygulamanızın depolama kuyruklarını nasıl kullandığını anlamak için yararlıdır. Ayrıca, uygulamanızı performans ayarlaması ve kuyrukları kullanmanın maliyetlerini azaltma için de kullanışlıdır.
* Service Bus tarafından desteklenen "ileti oturumları" kavramı, belirli bir mantıksal gruba ait olan iletilerin belirli bir alıcı ile ilişkilendirilmesini sağlar ve bu da iletiler ve ilgili alıcılar arasında oturum benzeri bir benzeşim oluşturur. Bir iletideki [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) özelliğini ayarlayarak Service Bus bu gelişmiş işlevselliği etkinleştirebilirsiniz. Alıcılar daha sonra belirli bir oturum KIMLIĞI üzerinde dinleme yapabilir ve belirtilen oturum tanımlayıcısını paylaşan iletiler alabilir.
* Service Bus kuyrukları tarafından desteklenen çoğaltma algılama işlevselliği, [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) özelliğinin değerine bağlı olarak bir kuyruğa veya konuya gönderilen yinelenen iletileri otomatik olarak kaldırır.

## <a name="capacity-and-quotas"></a>Kapasite ve Kotalar
Bu bölümde, depolama kuyrukları ve Service Bus kuyrukları, uygulayabilen [Kapasite ve Kotalar](service-bus-quotas.md) açısından karşılaştırılır.

| Karşılaştırma ölçütleri | Depolama kuyrukları | Hizmet Veri Yolu kuyrukları |
| --- | --- | --- |
| En büyük sıra boyutu |**500 TB**<br/><br/>( [tek bir depolama hesabı kapasitesine](../storage/common/storage-introduction.md#queue-storage)sınırlı) |**1 GB ila 80 GB**<br/><br/>(bir kuyruk oluşturulduktan ve [bölümleme etkinleştirildikten](service-bus-partitioning.md) sonra tanımlanır), "ek bilgi" bölümüne bakın |
| En büyük ileti boyutu |**64 KB**<br/><br/>( **Base64** kodlaması KULLANıLıRKEN 48 KB)<br/><br/>Azure, kuyrukları ve Blobları birleştirerek büyük iletileri destekler – bu noktada tek bir öğe için 200 GB 'a kadar sıraya alabilirsiniz. |**256 KB** veya **1 MB**<br/><br/>(başlık ve gövde dahil, en büyük üst bilgi boyutu: 64 KB).<br/><br/>[Hizmet katmanına](service-bus-premium-messaging.md)bağlıdır. |
| En fazla ileti TTL |**Sonsuz** (apı sürümü 2017-07-27 itibariyle) |**TimeSpan. Max** |
| En fazla sıra sayısı |**Sınırsız** |**10.000**<br/><br/>(hizmet ad alanı başına) |
| En fazla eş zamanlı istemci sayısı |**Sınırsız** |**Sınırsız**<br/><br/>(100 eşzamanlı bağlantı sınırı yalnızca TCP protokolü tabanlı iletişim için geçerlidir) |

### <a name="additional-information"></a>Ek Bilgi
* Service Bus kuyruk boyutu sınırlarını zorlar. Sıra boyutu üst sınırı, sıranın oluşturulması üzerine belirtilir ve 1 ile 80 GB arasında bir değere sahip olabilir. Sıra oluşturma sırasında ayarlanan sıra boyutu değerine ulaşıldığında, ek gelen iletiler reddedilir ve çağıran kod tarafından bir özel durum alınır. Service Bus kotaları hakkında daha fazla bilgi için bkz. [Service Bus kotalar](service-bus-quotas.md).
* [Premium katmanda](service-bus-premium-messaging.md)bölümlendirme desteklenmez. Standart katmanda, 1, 2, 3, 4 veya 5 GB boyutunda Service Bus kuyrukları oluşturabilirsiniz (varsayılan değer 1 GB 'tır). Standart katmanda bölümlendirme etkin (varsayılan), Service Bus belirttiğiniz her GB için 16 bölüm oluşturur. Bu nedenle, boyutu 5 GB olan bir kuyruk oluşturursanız, 16 bölümlü en büyük sıra boyutu (5 * 16) = 80 GB olur. Bölümlenmiş kuyruğunuzun veya konusunun en büyük boyutunu [Azure Portal][Azure portal]girişine bakarak görebilirsiniz.
* Depolama kuyrukları ile, iletinin içeriği XML güvenli değilse, **Base64** kodlamalı olmalıdır. İletiyi **Base64**olarak kodlarsanız, kullanıcı yükü 64 kb yerıne 48 KB 'ye kadar olabilir.
* Service Bus kuyruklarında, bir sırada depolanan her ileti iki bölümden oluşur: başlık ve gövde. İletinin toplam boyutu hizmet katmanının desteklediği en büyük ileti boyutunu aşamaz.
* İstemciler TCP protokolü üzerinden Service Bus kuyruklarında iletişim kurduğunda, tek bir Service Bus kuyruğuna en fazla eşzamanlı bağlantı sayısı 100 ile sınırlıdır. Bu sayı, Gönderenler ve alıcılar arasında paylaşılır. Bu kotaya ulaşıldığında, daha sonra ek bağlantı istekleri reddedilir ve çağıran kod tarafından bir özel durum alınır. Bu sınır, REST tabanlı API kullanılarak kuyruklara bağlanan istemcilere uygulanmaz.
* Tek bir Service Bus ad alanında 10.000 ' den fazla kuyruk gerekiyorsa, Azure destek ekibine başvurarak bir artış isteyebilirsiniz. 10.000 kuyruklarının ötesinde Service Bus ölçeklendirmek için [Azure Portal][Azure portal]kullanarak ek ad alanları da oluşturabilirsiniz.

## <a name="management-and-operations"></a>Yönetim ve işlemler
Bu bölümde, depolama kuyrukları ve Service Bus kuyrukları tarafından sunulan yönetim özellikleri karşılaştırılır.

| Karşılaştırma ölçütleri | Depolama kuyrukları | Hizmet Veri Yolu kuyrukları |
| --- | --- | --- |
| Yönetim Protokolü |**HTTP/HTTPS üzerinden REST** |**HTTPS üzerinden GERI dön** |
| Çalışma zamanı Protokolü |**HTTP/HTTPS üzerinden REST** |**HTTPS üzerinden GERI dön**<br/><br/>**AMQP 1,0 standart (TLS ile TCP)** |
| .NET API’si |**Evet**<br/><br/>(.NET Storage Istemci API 'SI) |**Evet**<br/><br/>(.NET Service Bus API) |
| YerelC++ |**Evet** |**Evet** |
| Java API’si |**Evet** |**Evet** |
| PHP APı 'SI |**Evet** |**Evet** |
| Node. js API 'SI |**Evet** |**Evet** |
| Rastgele meta veri desteği |**Evet** |**Hayır** |
| Kuyruk adlandırma kuralları |**En fazla 63 karakter uzunluğunda**<br/><br/>(Bir kuyruk adındaki harfler küçük harfle yazılmalıdır.) |**En fazla 260 karakter uzunluğunda**<br/><br/>(Kuyruk yolları ve adları büyük/küçük harfe duyarlıdır.) |
| Sıra uzunluğu alma işlevi |**Evet**<br/><br/>(İletilerin silinmeksizin TTL 'nin ötesinde yaklaşık değer.) |**Evet**<br/><br/>(Tam, zaman içinde nokta değeri.) |
| Peek işlevi |**Evet** |**Evet** |

### <a name="additional-information"></a>Ek Bilgi
* Depolama kuyrukları, ad/değer çiftleri biçiminde kuyruk açıklamasına uygulanabilen rastgele öznitelikler için destek sağlar.
* Her iki kuyruk teknolojisi de bir iletiyi kilitlemeye gerek kalmadan, bir kuyruk Gezgini/tarayıcı aracı uygularken yararlı olabilecek bir iletiye göz atma özelliği sunar.
* Service Bus .NET Aracılı mesajlaşma API 'Leri, HTTP üzerinden REST ile karşılaştırıldığında gelişmiş performans için tam çift yönlü TCP bağlantılarını kullanır ve AMQP 1,0 standart protokolünü destekler.
* Depolama sıralarının adları 3-63 karakter uzunluğunda olabilir, küçük harf, sayı ve kısa çizgi içerebilir. Daha fazla bilgi için bkz. [ad kuyrukları ve meta verileri](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Service Bus kuyruk adları en fazla 260 karakter uzunluğunda olabilir ve daha az kısıtlayıcı adlandırma kuralına sahip olabilir. Service Bus kuyruk adları harf, sayı, nokta, kısa çizgi ve alt çizgi içerebilir.

## <a name="authentication-and-authorization"></a>Kimlik doğrulama ve yetkilendirme
Bu bölümde, depolama kuyrukları ve Service Bus kuyrukları tarafından desteklenen kimlik doğrulama ve yetkilendirme özellikleri açıklanmaktadır.

| Karşılaştırma ölçütleri | Depolama kuyrukları | Hizmet Veri Yolu kuyrukları |
| --- | --- | --- |
| Kimlik Doğrulaması |**Simetrik anahtar** |**Simetrik anahtar** |
| Güvenlik modeli |SAS belirteçleri aracılığıyla erişim temsilcisi. |'LARıNıN |
| Kimlik sağlayıcısı Federasyonu |**Hayır** |**Evet** |

### <a name="additional-information"></a>Ek Bilgi
* Sıraya alma teknolojilerinin her birine yönelik her bir isteğin kimliğinin doğrulanması gerekir. Anonim erişimi olan genel kuyruklar desteklenmez. [SAS](service-bus-sas.md)kullanarak, bu senaryoya yalnızca bir salt yazılır SAS, salt okunurdur ve hatta tam erişimli SAS yayımlayarak erişebilirsiniz.
* Depolama kuyrukları tarafından sunulan kimlik doğrulama düzeni, SHA-256 algoritması ile hesaplanan ve **Base64** dizesi olarak kodlanan, karma tabanlı İLETI KIMLIK doğrulama kodu (HMAC) olan simetrik anahtar kullanımını içerir. İlgili protokol hakkında daha fazla bilgi için bkz. [Azure depolama hizmetleri Için kimlik doğrulama](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Service Bus kuyrukları, simetrik anahtarlar kullanan benzer bir modeli destekler. Daha fazla bilgi için bkz. [Service Bus Ile paylaşılan erişim Imzası kimlik doğrulaması](service-bus-sas.md).

## <a name="conclusion"></a>Sonuç
İki teknolojiyi daha ayrıntılı bir şekilde öğrenerek hangi kuyruk teknolojisinin kullanılacağı ve ne zaman daha bilinçli bir karar elde edersiniz. Depolama sıralarının veya Service Bus sıralarının ne zaman kullanılacağı kararı, bir dizi etkene bağlıdır. Bu faktörler, uygulamanızın ve mimarinin mimarisine göre büyük ölçüde değişebilir. Uygulamanız Microsoft Azure temel yeteneklerini zaten kullanıyorsa, özellikle hizmetler arasında temel iletişim ve mesajlaşma istiyorsanız veya boyut 80 GB 'tan daha büyük olabilecek kuyruklara ihtiyaç duyuyorsanız, depolama kuyrukları ' nı seçebilirsiniz.

Service Bus kuyrukları, oturumlar, işlemler, yinelenen algılama, otomatik olarak atılacak ve dayanıklı yayımlama/abone olma gibi çeşitli gelişmiş özellikler sağladığından, karma oluşturuyorsanız bu bir tercih edilen seçenek olabilir uygulama ya da uygulamanız Aksi takdirde bu özellikleri gerektirir.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelerde, depolama kuyruklarını veya Service Bus kuyruklarını kullanma hakkında daha fazla rehberlik ve bilgi sağlanmaktadır.

* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Kuyruk depolama hizmetini kullanma](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Service Bus Aracılı mesajlaşma kullanarak performans geliştirmeleri için en iyi uygulamalar](service-bus-performance-improvements.md)
* [Azure Service Bus (blog gönderisi) kuyrukları ve konuları tanıtma](https://www.code-magazine.com/article.aspx?quickid=1112041)
* [Geliştirici Kılavuzu Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Azure 'da sıraya alma hizmetini kullanma](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

