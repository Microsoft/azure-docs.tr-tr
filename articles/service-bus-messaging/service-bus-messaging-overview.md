---
title: Azure Service Bus mesajlaşmasına genel bakış | Microsoft Belgeleri
description: Bu makalede, tam olarak yönetilen bir kurumsal tümleştirme ileti Aracısı olan Azure Service Bus üst düzey bir genel bakış sunulmaktadır.
ms.topic: overview
ms.date: 02/16/2021
ms.openlocfilehash: 897729b9748d69ad3c6de507e800dbb3a1a3619c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570462"
---
# <a name="what-is-azure-service-bus"></a>Azure Service Bus nedir?
Microsoft Azure Service Bus, ileti kuyrukları ve yayımlama-abone ol konuları ile tam olarak yönetilen bir kurumsal ileti aracısıdır. Service Bus, uygulamaları ve hizmetleri birbirinden ayırmak için kullanılır ve aşağıdaki avantajları sağlar:

- Rekabet çalışanları arasında yük dengeleme çalışması
- Hizmet ve uygulama sınırları genelinde verileri ve denetimi güvenle yönlendirme ve aktarma
- Yüksek derecede güvenilirlik gerektiren işlem işlerini koordine etme 

## <a name="overview"></a>Genel Bakış
Veriler, farklı uygulamalar ve hizmetler arasında *iletiler* kullanılarak aktarılır. İleti, meta verilerle düzenlenmiş ve veri içeren bir kapsayıcıdır. Veriler, aşağıdakiler gibi ortak biçimlerin kodlandığı yapılandırılmış veriler de dahil olmak üzere herhangi bir tür bilgi olabilir: JSON, XML, Apache avro, düz metin.

Yaygın olarak kullanılan bazı mesajlaşma senaryoları:

* *Mesajlaşma*. Satış veya satın alma siparişleri, Günlükler veya stok hareketleri gibi iş verilerini aktarın.
* *Uygulamaları ayrıştı*. Uygulama ve hizmetlerin güvenilirliğini ve ölçeklenebilirliğini geliştirme. Üretici ve tüketicinin aynı anda çevrimiçi veya kullanıma hazır olması gerekmez. Yük, trafik yoğunluğu bir hizmetin [yükünü fazla değil olarak seviyelendirilir](/azure/architecture/patterns/queue-based-load-leveling) . 
* *Yük Dengeleme*. Birden çok [rekabet tüketicilerinin](/azure/architecture/patterns/competing-consumers) aynı anda bir kuyruktan okumasına izin verin, her biri belirli iletilere güvenli bir şekilde özel sahiplik elde edin. 
* *Konular ve abonelikler*. [Yayımcılar ve aboneler](/azure/architecture/patterns/publisher-subscriber)arasındaki 1:*n* ilişkilerini etkinleştirerek abonelerin yayınlanmış bir ileti akışından gelen belirli iletileri seçmesini sağlar.
* *İşlemler*. Atomik bir işlemin kapsamındaki birkaç işlemi yapmanıza olanak sağlar. Örneğin, aşağıdaki işlemler bir işlem kapsamında yapılabilir.  

    1. Bir kuyruktan bir ileti alın.
    2. İşleme sonuçlarını bir veya daha fazla farklı kuyruğa gönderin.
    3. Giriş iletisini özgün kuyruktan taşıyın. 
    
    Sonuçlar, yalnızca bir kez işleme semantiklerine izin veren, başarıyla giriş iletisinin başarıyla kapatılması dahil olmak üzere, yalnızca başarılı olduğunda aşağı akış tüketicilerine görünür hale gelir. Bu işlem modeli, daha fazla çözüm bağlamındaki [telafi işlemleri](/azure/architecture/patterns/compensating-transaction) deseninin sağlam bir temelidir. 
* *İleti oturumları*. Katı ileti sıralaması veya ileti erteleme gerektiren iş akışlarının ve çoklu aktarımların yüksek ölçekli bir şekilde koordine edilmesini uygulayın.

Apache ActiveMQ gibi diğer ileti aracıları hakkında bilginiz varsa Service Bus kavramlar, bildiğiniz işe benzer. Service Bus bir hizmet olarak platform (PaaS) teklifi olduğundan, önemli bir farklılık aşağıdaki eylemler hakkında endişelenmenize gerek kalmaz. Azure, sizin için bu ilgili bu nesnelerin kullanımını üstlenir. 

- Günlükleri yerleştirme ve disk alanını yönetme
- Yedeklemeleri işleme
- İşletim sistemlerini veya ürünleri düzeltme eki uygulanmış olarak tutma
- Donanım arızaları hakkında endişelenme 
- Yedek makineye yük devrediliyor

## <a name="compliance-with-standards-and-protocols"></a>Standartlarla ve protokollerle uyumluluk

Service Bus için birincil hat protokolü, açık bir ISO/ıEC standardı olan [Gelişmiş mesajlaşma sıraya alma Protokolü (AMQP) 1,0](service-bus-amqp-overview.md)' dir. Müşterilerin Service Bus ve ActiveMQ ya da Kbbitmq gibi şirket içi aracılarından dolayı çalışan uygulamalar yazmasını sağlar. [AMQP protokol Kılavuzu](service-bus-amqp-protocol-guide.md) , böyle bir soyutlama oluşturmak istediğiniz durumlarda ayrıntılı bilgiler sağlar.

[Service Bus Premium](service-bus-premium-messaging.md) , Java/Jakarta Ee [Java ileti hizmeti (jms) 2,0](how-to-use-java-message-service-20.md) API 'siyle tamamen uyumludur. Ve, Service Bus standart sıralara odaklanan JMS 1,1 alt kümesini destekler. JMS, ileti aracıları için ortak bir soyutlamadır ve popüler yay çerçevesi dahil olmak üzere birçok uygulama ve çerçeve ile tümleşir. Diğer aracılardan Azure Service Bus geçiş yapmak için, yalnızca sıraların ve konuların topolojisini yeniden oluşturmanız ve istemci sağlayıcısı bağımlılıklarını ve yapılandırmasını değiştirmeniz gerekir. Örneğin, [ActiveMQ geçiş kılavuzu](migrate-jms-activemq-to-servicebus.md)' na bakın.

## <a name="concepts-and-terminology"></a>Kavramlar ve terminoloji 
Bu bölümde Service Bus kavramlar ve terminoloji açıklanmaktadır.

### <a name="namespaces"></a>Ad alanları
Ad alanı, tüm mesajlaşma bileşenleri için bir kapsayıcıdır. Tek bir ad alanında birden fazla kuyruk ve konu bulunabilir ve ad alanları genellikle uygulama kapsayıcıları olarak görev yapar. 

Bir ad alanı diğer aracıların terminolojisinde bir "sunucu" ile karşılaştırılabilir, ancak kavramlar doğrudan eşdeğer değildir. Service Bus ad alanı, her türlü onlarca etkin sanal makineden oluşan büyük bir kümenin kendi kapasite dilim'unuzda bulunur. İsteğe bağlı olarak üç [Azure kullanılabilirlik bölgesine](../availability-zones/az-overview.md)yayılabilir. Bu nedenle, ileti aracısını en büyük ölçekte çalıştırmanın tüm kullanılabilirlik ve sağlamlık avantajlarını elde edersiniz. Ve temel karmaşıklık konusunda endişelenmeniz gerekmez. "Sunucusuz" mesajlaşması Service Bus.

### <a name="queues"></a>Kuyruklar
İletiler *kuyruklara* gönderilir ve bunlardan alınır. Kuyruklar, alıcı uygulama tarafından alınması ve işlenmesi için kullanılabilir olana kadar iletileri depolar.

![Kuyruk](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Kuyruklarda bulunan iletiler, varış noktasında sıralanır ve zaman damgalanır. Aracı tarafından kabul edildiğinde, ileti her zaman üçlü olarak yedekli depolamada tutulur, ad alanı bölge özellikli ise kullanılabilirlik bölgelerine yayılır. Service Bus istemciye, kabul edildiği şekilde bildirildikten sonra iletileri bellekte veya geçici depolamada hiçbir şekilde terk etmez.

İletiler *çekme* moduna alınır ve yalnızca istendiğinde iletileri teslim edilir. Diğer bazı bulut sıralarının meşgul yoklama modelinden farklı olarak, çekme işlemi uzun süreli olabilir ve yalnızca bir ileti kullanılabilir olduğunda tamamlanabilir. 

### <a name="topics"></a>Konu başlıkları

İleti göndermek ve almak için *konu başlıklarını* da kullanabilirsiniz. Bir kuyruk genellikle noktadan noktaya iletişim için kullanılır, buna karşın konu başlıkları yayımlama/abone olma senaryolarında yararlıdır.

![Konu](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Konular, konuya iliştirilecek ve başka bir şekilde alıcı tarafındaki kuyruklar gibi çalışan birden fazla bağımsız aboneliğe sahip olabilir. Bir konu başlığının bir abonesi konu başlığına gönderilen her iletinin bir kopyasını alabilir. Abonelikler, varlıklar olarak adlandırılır. Abonelikler varsayılan olarak dayanıklı olur, ancak sona ermek üzere yapılandırılabilir ve sonra otomatik olarak silinir. JMS API 'SI aracılığıyla Service Bus Premium, bağlantı süresince mevcut olan geçici abonelikler oluşturmanıza de olanak tanır.

Bir abonelikte kurallar tanımlayabilirsiniz. Abonelik kuralında, ileti için aboneliğe kopyalanacak bir koşul ve ileti meta verilerini değiştirebilecek isteğe bağlı bir *eylem* tanımlamak için bir *filtre* vardır. Daha fazla bilgi için bkz. [Konu filtreleri ve eylemleri](topic-filters.md). Bu özellik aşağıdaki senaryolarda faydalıdır:

- Bir aboneliğin bir konuya gönderilen tüm iletileri almasını istemezsiniz.
- Bir abonelikten geçediklerinde iletileri ek meta verilerle işaretlemek istiyorsunuz.

## <a name="advanced-features"></a>Gelişmiş özellikler

Service Bus, daha karmaşık Mesajlaşma sorunlarını çözmenize olanak sağlayan gelişmiş özellikler içerir. Aşağıdaki bölümlerde bu özelliklerden bazıları açıklanır.

### <a name="message-sessions"></a>İleti oturumları

Service Bus ' de ilk kez ilk çıkar (FıFO) garantisi oluşturmak için oturumları kullanın. İleti oturumları, sınırlandırılmamış ilişkili ileti sıralarının özel ve sıralı işlenmesini etkinleştirir. Yüksek ölçekli, yüksek kullanılabilirliğe sahip sistemlerde oturum işleme izni vermek için oturum özelliği, oturumların işleyiciler arasında güvenle hareket etmesine olanak tanıyan oturum durumunu depolamayı da sağlar. Daha fazla bilgi için bkz. [ileti oturumları: ilk gelen, ilk çıkar (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Oto iletme

Oto iletme özelliği bir kuyruğu veya aboneliği aynı ad alanı içindeki başka bir kuyruğa veya konuya zincirler. Bu özelliği kullandığınızda Service Bus, iletileri bir kuyruktan veya abonelikten hedef kuyruğa veya konuya otomatik olarak taşıtir. Tüm bu taşınmaların işlem temelli olarak yapılır. Daha fazla bilgi için bkz. [Service Bus varlıklarını oto iletme Ile zincirleme](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Atılacak ileti sırası

Tüm Service Bus kuyrukları ve konu abonelikleri, ilişkili bir atılacak ileti kuyruğuna (DLQ) sahiptir. Bir DLQ aşağıdaki ölçütlere uyan iletileri barındırır: 

- Bunlar herhangi bir alıcıya başarıyla teslim ediamazlar.
- Zaman aşımına uğrar.
- Alıcı uygulama tarafından açıkça bir şekilde hizalanırlar. 

Atılacak ileti sırasındaki iletiler, neden yerleştirildikleri nedene göre açıklanırlar. Atılacak ileti sırası özel bir uç noktaya sahiptir, aksi takdirde normal sıra gibi davranır. Bir uygulama veya araç, bir DLCı 'ye veya sıradan bir sıradan çıkar. Ayrıca, bir atılacak ileti sırasının dışına da ileri sarabilirsiniz. Daha fazla bilgi için bkz. [Service Bus atılacak ileti sıralarına genel bakış](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Zamanlanmış teslim

Gecikmeli işleme için iletileri bir kuyruğa veya konuya gönderebilirsiniz ve ileti tüketim için kullanılabilir hale gelir. Zamanlanmış iletiler de iptal edilebilir. Daha fazla bilgi için bkz. [zamanlanmış iletiler](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>İleti erteleme

Bir kuyruk veya abonelik istemcisi daha sonra alınan bir iletinin alınmasını erteedebilir. İleti beklenen bir siparişten alınmış olabilir ve istemcinin başka bir ileti almasını beklemek istiyor. Ertelenmiş iletiler kuyrukta veya abonelikte kalır ve hizmet tarafından atanan sıra numarası kullanılarak açık bir şekilde yeniden etkinleştirilmelidir. Daha fazla bilgi için bkz. [ileti erteleme](message-deferral.md).

### <a name="batching"></a>Toplu İşleme

İstemci tarafı toplu işlem, bir kuyruk veya konu istemcisinin bir ileti kümesini birikmesini ve bunları birlikte aktarmasını sağlar. Bant genişliğini kaydetmek veya aktarım hızını artırmak için genellikle yapılır. Daha fazla bilgi için bkz. [istemci tarafı toplu işleme](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>İşlemler

İşlem iki veya daha fazla işlemi bir *yürütme kapsamında* gruplandırır. Service Bus, tek bir işlemin kapsamındaki birden çok mesajlaşma varlıklarına karşı işlemleri gruplamaya olanak sağlar. İleti varlığı bir kuyruk, konu veya abonelik olabilir. Daha fazla bilgi için bkz. [Service Bus işlem Işlemeye genel bakış](service-bus-transactions.md).

### <a name="autodelete-on-idle"></a>Boşta üzerinde oto silme
Boştayken otomatik silme, bir kuyruk veya konu aboneliğinin otomatik olarak silineceği bir boşta kalma aralığı belirtmenize olanak sağlar. En düşük süre 5 dakikadır. 

### <a name="duplicate-detection"></a>Yineleme algılama
Yinelenen saptama özelliği, gönderenin aynı iletiyi yeniden göndermesini ve aracının olası bir yinelemeyi bırakması için izin sağlar. Daha fazla bilgi için bkz. [yinelenen algılama](duplicate-detection.md).

### <a name="geo-disaster-recovery"></a>Coğrafi olağanüstü durum kurtarma

Bir Azure bölgesi kesinti yaşdığında, olağanüstü durum kurtarma özelliği veri işlemenin farklı bir bölgede veya veri merkezinde çalışmaya devam etmesine olanak sağlar. Özelliği, ikincil bölgede bulunan bir ad alanının yapısal yansıtmasını tutar ve ad alanı kimliğinin ikincil ad alanına geçiş yapmasına olanak tanır. Önceden gönderilmiş iletiler, kullanılabilirlik bölümü alt taraflarından sonra kurtarma için önceki birincil ad alanında kalır. Daha fazla bilgi için bkz. [Azure Service Bus coğrafi olağanüstü durum kurtarma](service-bus-geo-dr.md).

### <a name="security"></a>Güvenlik

Service Bus, standart [AMQP 1,0](service-bus-amqp-overview.md) ve [http ya da rest](/rest/api/servicebus/) protokollerini ve aktarım düzeyi güvenlik (TLS) dahil olmak üzere ilgili güvenlik tesislerini destekler. İstemciler, [paylaşılan erişim imzası](service-bus-sas.md) veya rol tabanlı güvenlik [Azure Active Directory](service-bus-authentication-and-authorization.md) kullanarak erişim için yetkilendirilir. 

İstenmeyen trafiğe karşı koruma için Service Bus, IP güvenlik duvarı ve sanal ağlarla tümleştirme gibi [güvenlik özellikleri](network-security.md) sağlar. 

## <a name="client-libraries"></a>İstemci kitaplıkları

Tam olarak desteklenen Service Bus istemci kitaplıkları Azure SDK ile kullanılabilir.

- [.NET için Azure Service Bus](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Java için Azure Service Bus kitaplıkları](/java/api/overview/azure/servicebus?preserve-view=true)
- [Java JMS 2,0 için Azure Service Bus sağlayıcısı](how-to-use-java-message-service-20.md)
- [JavaScript ve TypeScript için Azure Service Bus modülleri](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Python için Azure Service Bus kitaplıkları](/python/api/overview/azure/servicebus?preserve-view=true)

[Azure Service Bus ' birincil protokol amqp 1,0](service-bus-amqp-overview.md) ve herhangi bir amqp 1,0 uyumlu protokol istemcisinden kullanılabilir. Çeşitli açık kaynaklı AMQP istemcilerinin, Service Bus birlikte çalışabilirliği açıkça gösteren örnekleri vardır. AMQP 1,0 istemcilerinin doğrudan Service Bus ' özelliklerini kullanmayı öğrenmek için [amqp 1,0 protokol kılavuzunu](service-bus-amqp-protocol-guide.md) gözden geçirin.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Tümleştirme

Service Bus, birçok Microsoft ve Azure hizmeti ile tam olarak tümleştirilir, örneğin:

* [Event Grid](service-bus-to-event-grid-integration-example.md)
* [Logic Apps](../connectors/connectors-create-api-servicebus.md)
* [Azure İşlevleri](../azure-functions/functions-bindings-service-bus.md)
* [Power Platform](../connectors/connectors-create-api-servicebus.md)
* [Dynamics 365](/dynamics365/fin-ops-core/dev-itpro/business-events/how-to/how-to-servicebus)
* [Azure Akış Analizi](../stream-analytics/stream-analytics-define-outputs.md)

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşmasını kullanmaya başlamak için aşağıdaki makalelere bakın:

* Azure mesajlaşma hizmetlerini karşılaştırmak için bkz. [Hizmetleri karşılaştırma](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* [.Net](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md)veya [JMS](service-bus-java-how-to-use-jms-api-amqp.md)için hızlı başlangıç yapmayı deneyin.
* Service Bus kaynaklarını yönetmek için, bkz. [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Standart ve Premium katmanları ve fiyatları hakkında daha fazla bilgi edinmek için bkz. [Service Bus fiyatlandırması](https://azure.microsoft.com/pricing/details/service-bus/).
* Premium katmanı için performans ve gecikme hakkında bilgi edinmek için bkz. [Premium mesajlaşma](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).