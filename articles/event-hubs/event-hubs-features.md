---
title: Özelliklere genel bakış-Azure Event Hubs | Microsoft Docs
description: Bu makalede, Azure Event Hubs özellikleri ve terminolojisi hakkında ayrıntılar sağlanmaktadır.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: 8ec4b7cdd13c3407747261ef54cb6b1fc58fdb69
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310093"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Azure Event Hubs'ın özellikleri ve terminolojisi

Azure Event Hubs, düşük gecikme süresi ve yüksek güvenilirlikle büyük hacimlerdeki olayları ve verileri işleyen ve işleyen ölçeklenebilir bir olay işleme hizmetidir. Üst düzey genel bakış için bkz. [Event Hubs nedir?](./event-hubs-about.md) .

Bu makale, [genel bakış makalesindeki](./event-hubs-about.md)bilgileri oluşturur ve Event Hubs bileşenleri ve özellikleri hakkında teknik ve uygulama ayrıntıları sağlar.

> [!TIP]
> [ **Apache Kafka** istemcileri için protokol desteği](event-hubs-for-kafka-ecosystem-overview.md)  (sürümler >= 1,0), Event Hubs kullanmak üzere herhangi bir istemciyle Apache Kafka kullanılmak üzere yapılandırılmış uygulamaları etkinleştiren ağ uç noktaları sağlar. Çoğu mevcut Kafka uygulaması yalnızca bir Kafka Cluster önyükleme sunucusu yerine bir olay hub 'ı ad alanı işaret etmek üzere yeniden yapılandırılabilir. 
>
>Azure Event Hubs, maliyet, operasyonel çaba ve güvenilirlik açısından, kendi Kafka ve Zookeeper kümelerini dağıtmaya ve çalıştırmaya yönelik harika bir alternatiftir ve Azure 'da yerel olmayan Kafka servis teklifleri sağlar. 
>
> Apache Kafka aracısından itibaren aynı temel işlevselliği almanın yanı sıra, [Event Hubs yakalama](event-hubs-capture-overview.md), otomatik ölçeklendirme ve dengeleme, olağanüstü durum kurtarma, düşük maliyetli kullanılabilirlik alanı desteği, esnek ve güvenli ağ tümleştirmesi ve güvenlik duvarı kullanımı kolay AMQP-Over-WebSockets protokolü gibi çok protokollü destek aracılığıyla otomatik toplu işlem ve arşivleme gibi Azure Event hub özelliklerine de erişebilirsiniz.


## <a name="namespace"></a>Ad Alanı
Event Hubs ad alanı, DNS tümleşik ağ uç noktaları ve [IP filtrelemesi](event-hubs-ip-filtering.md), [sanal ağ hizmeti uç noktası](event-hubs-service-endpoints.md)ve [özel bağlantı](private-link-service.md) gibi bir dizi erişim denetimi ve ağ tümleştirme yönetimi özellikleri sağlar ve birden çok olay hub 'ının örneklerinden biri (veya Kafka Pari) için yönetim kapsayıcısıdır.

## <a name="event-publishers"></a>Olay yayımcıları

Bir olay hub 'ına veri gönderen herhangi bir varlık bir *olay yayımcısıdır* (terimler *olay üreticisi* ile kullanılır). Olay yayımcıları, HTTPS veya AMQP 1,0 veya Kafka protokolünü kullanarak olayları yayımlayabilir. Olay yayımcıları, OAuth2 tarafından verilen JWT belirteçleri veya bir olay hub 'ına özgü paylaşılan erişim Imzası (SAS) belirteç kazanç yayınlama erişimi ile Azure Active Directory tabanlı yetkilendirme kullanır.

### <a name="publishing-an-event"></a>Olay yayımlama

AMQP 1,0, Kafka Protocol veya HTTPS aracılığıyla bir olay yayımlayabilirsiniz. Event Hubs hizmeti, olayları bir olay hub 'ına yayımlamak için [REST API](/rest/api/eventhub/) ve [.net](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)ve [Go](event-hubs-go-get-started-send.md) istemci kitaplıkları sağlar. Diğer çalışma zamanları ve platformlar için [Apache Qpid](https://qpid.apache.org/) gibi herhangi bir AMQP 1.0 istemcisi kullanabilirsiniz. 

AMQP veya HTTPS kullanma seçimi kullanım senaryosuna bağlıdır. AMQP, taşıma düzeyi güvenliği (TLS) veya SSL/TLS’ye ek olarak kalıcı bir çift yönlü yuva oluşturulmasını gerektirir. AMQP, oturum başlatırken daha yüksek ağ maliyetlerine sahiptir, ancak HTTPS her istek için ek TLS yükü gerektirir. AMQP, sık kullanılan yayımcılar için önemli ölçüde daha yüksek performansa sahiptir ve zaman uyumsuz yayımlama koduyla kullanıldığında çok daha düşük gecikme süreleri elde edebilir

Olayları ayrı ayrı veya toplu şekilde yayımlayabilirsiniz. Tek bir yayının, tek bir olay veya toplu iş olmasına bakılmaksızın 1 MB 'lik bir sınırı vardır. Bu eşikten daha büyük yayımlama olayları reddedilir. 

Event Hubs üretilen iş birimleri, bölümler ve üretilen iş birimi ayırmaları kullanılarak ölçeklendirilir (aşağıya bakın). Yayımcıların bir olay hub 'ı için seçilen belirli bölümlendirme modelinden haberdar olması ve yalnızca ilgili olayları tutarlı olarak aynı bölüme atamak için kullanılan bir *bölüm anahtarı* belirtmesi için en iyi uygulamadır.

![Bölüm anahtarları](./media/event-hubs-features/partition_keys.png)

Event Hubs, bölüm anahtarı değerini paylaşan tüm olayların birlikte depolanmasını ve varış sırasına göre teslim edilmesini sağlar. Bölüm anahtarlarının yayımcı ilkeleriyle birlikte kullanılması durumunda yayımcı kimliğinin ve bölüm anahtarı değerinin eşleşmesi gerekir. Aksi takdirde bir hata oluşur.

### <a name="event-retention"></a>Olay saklama

Yayımlanan olaylar yapılandırılabilir, süreli tabanlı bir bekletme ilkesine göre bir olay hub 'ından kaldırılır. İşte birkaç önemli nokta:

- **Varsayılan** değer ve mümkün olan **en kısa** saklama süresi **1 gündür (24 saat)**.
- Event Hubs **Standart** için maksimum saklama süresi **7 gündür**. 
- **Adanmış** Event Hubs için maksimum saklama süresi **90 gündür**.
- Saklama süresini değiştirirseniz, zaten Olay Hub 'ında bulunan iletiler dahil tüm iletiler için geçerlidir. 

Event Hubs, tüm bölümler için geçerli olan yapılandırılmış bir saklama süresi için olayları tutar. Saklama süresine ulaşıldığında olaylar otomatik olarak kaldırılır. Bir günün bekletme dönemini belirtirseniz olay, kabul edildikten sonra tam 24 saat kullanılamaz hale gelir. Olayları açıkça silemezsiniz. 

Olayları, izin verilen saklama süresi ötesinde arşivlemeniz gerekiyorsa [Azure Storage 'da otomatik olarak depolanabilir veya Event Hubs yakalama özelliğini açıp Azure Data Lake](event-hubs-capture-overview.md), bu tür derinlemesine arşivleri aramanız veya analiz etmeniz gerekiyorsa, [bunları kolayca Azure SYNAPSE](store-captured-data-data-warehouse.md) veya diğer benzer depolar ve analiz platformları içine aktarabilirsiniz. 

Veri saklama sırasında Event Hubs ' sınırının nedeni, zaman damgası tarafından dizine alınmış ve yalnızca sıralı erişime izin veren derin bir depoda, geçmiş müşteri verilerinin büyük hacimlerini önlemektir. Burada, geçmiş verilerin daha zengin dizine sahip olması ve Event Hubs veya Kafka sağladığı gerçek zamanlı olay arabiriminden daha doğrudan erişim sağlaması gerekir. Olay akışı motorları, olay kaynağını belirlemek için veri klarının veya uzun vadeli arşivlerin rolünü yürütmeye uygun değildir. 
 

> [!NOTE]
> Event Hubs gerçek zamanlı bir olay akış altyapısıdır ve bir veritabanı ve/veya sonsuz olay akışları için kalıcı bir mağaza olarak kullanılmak üzere tasarlanmamıştır. 
> 
> Bir olay akışının geçmişi daha derin olduğunda, belirli bir akışın belirli bir geçmiş dilimini bulmak için yardımcı dizinlere ihtiyacınız olacaktır. Olay yüklerini ve Dizin oluşturmayı denetleme Event Hubs (veya Apache Kafka) özellik kapsamında değil. [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md), [Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) ve [Azure SYNAPSE](../synapse-analytics/overview-what-is.md) gibi veritabanları ve özelleştirilmiş analiz depoları ve motorları, geçmiş olayları depolamak için çok daha uygundur.
>
> [Event Hubs yakalama](event-hubs-capture-overview.md) , doğrudan Azure Blob depolama ve Azure Data Lake Storage ile tümleşir ve bu tümleştirme aracılığıyla [doğrudan Azure SYNAPSE 'e akan olayları](store-captured-data-data-warehouse.md)da sunar.
>
> Uygulamanız için [olay](/azure/architecture/patterns/event-sourcing) kaynağını belirleme düzenini kullanmak istiyorsanız, anlık görüntü stratejinizi Event Hubs bekletme limitleriyle hizalamanız gerekir. Ham olaylardaki gerçekleştirilmiş görünümleri, zaman başından başlayarak yeniden oluşturmayı Hedeflemeyin. Uygulamanız bir süredir üretimde olduğunda ve iyi kullanıldığında ve projeksiyon oluşturucunun en son ve devam eden değişikliklere yönelik olarak değişiklik olayları arasında dalgalanmak zorunda kaldıktan sonra bu tür bir stratejiye katılın. 


### <a name="publisher-policy"></a>Yayımcı ilkesi

Event Hubs, *yayımcı ilkeleri* aracılığıyla olay yayımcıları üzerinde ayrıntılı denetim sağlar. Yayımcı ilkeleri çok sayıda bağımsız olay yayımcısını kolaylaştırmak için tasarlanmış çalışma zamanı özellikleridir. Yayımcı ilkeleriyle her yayımcı, olayları aşağıdaki mekanizmayı kullanarak bir olay hub'ında yayımlarken kendi benzersiz tanımlayıcısını kullanır:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

Yayımcı adlarını önceden oluşturmanız gerekli değildir, ancak bunlar bağımsız yayımcı kimlikleri sağlamak amacıyla bir olayı yayımlarken kullanılan SAS belirteci ile eşleşmelidir. Yayımcı ilkelerini kullanırken **PartitionKey** değeri yayımcı adına ayarlanır. Bu hizmetin düzgün çalışması için bu değerlerin eşleşmesi gerekir.

## <a name="capture"></a>Yakalama

[Event Hubs yakalama](event-hubs-capture-overview.md) , akış verilerini Event Hubs otomatik olarak yakalamanızı ve bir BLOB depolama hesabı ya da bir Azure Data Lake hizmet hesabına kaydetmenizi sağlar. Azure portal yakalamayı etkinleştirebilir ve yakalama işlemini gerçekleştirmek için bir minimum boyut ve zaman penceresi belirtebilirsiniz. Event Hubs yakalama 'yı kullanarak, kendi Azure Blob depolama hesabınızı ve kapsayıcınızı ya da yakalanan verileri depolamak için kullanılan bir hizmet hesabı Azure Data Lake belirtirsiniz. Yakalanan veriler Apache avro biçiminde yazılır.

## <a name="partitions"></a>Bölümler
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS belirteçleri

Event Hubs, ad alanı ve Olay Hub 'ı düzeyinde kullanılabilen *paylaşılan erişim imzalarını* kullanır. SAS belirteci bir SAS anahtarından oluşturulur ve belirli bir biçimde kodlanmış bir URL’nin SHA karmasıdır. Event Hubs anahtar (ilke) ve belirtecin adını kullanarak karmayı yeniden oluşturabilir ve böylece gönderenin kimliğini doğrular. Normalde, olay yayımcıları için SAS belirteci yalnızca belirli bir olay hub'ı üzerindeki **gönder** ayrıcalıkları ile oluşturulur. Bu SAS belirteci URL mekanizması, yayımcı ilkesinde sunulan yayımcı kimliğinin temelini oluşturur. SAS ile çalışma hakkında daha fazla bilgi için bkz. [Service Bus ile Paylaşılan Erişim İmzası Kimlik Doğrulaması](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Olay tüketicileri

Bir olay hub'ından olay verilerini okuyan herhangi bir varlık *olay tüketicisidir*. Tüm Event Hubs tüketicileri AMQP 1.0 oturumu üzerinden bağlanır ve olaylar, kullanılabilir olduğu anda oturum üzerinden iletilir. İstemcinin veri kullanılabilirliğini yoklaması gerekmez.

### <a name="consumer-groups"></a>Tüketici grupları

Event Hubs yayımlama/abonelik mekanizması *tüketici grupları* aracılığıyla etkinleştirilir. Tüketici grubu tüm olay hub'ının bir görünümüdür (durum, konum veya uzaklık). Tüketici grupları birden çok tüketen uygulamayı her biri olay akışının ayrı bir görünümüne sahip olacak ve akışı kendi hızlarında ve kendi sapmalarıyla bağımsız bir şekilde okuyacak şekilde etkinleştirir.

Bir akış işleme mimarisinde her bir aşağı akış uygulaması bir tüketici grubuna karşılık gelir. Olay verilerini uzun süreli depolama alanına yazmak isterseniz bu depolama yazma uygulaması bir tüketici grubudur. Bundan sonra karmaşık olay işlemesi başka ve ayrı bir tüketici grubu tarafından gerçekleştirilebilir. Bölümlere yalnızca bir tüketici grubu üzerinden erişebilirsiniz. Bir olay hub'ında her zaman varsayılan bir tüketici grubu vardır ve Standart katmanlı bir olay hub'ı için en fazla 20 tüketici grubu oluşturabilirsiniz.

Tüketici grubu başına bölüm üzerinde en fazla 5 eşzamanlı okuyucu olabilir; Ancak **, Tüketici grubu başına bir bölümde yalnızca bir etkin alıcı olması önerilir**. Tek bir bölüm içinde her okuyucu tüm iletileri alır. Aynı bölümde birden fazla okuyucu varsa, yinelenen iletileri işleyebilirsiniz. Kodunuzda bu, önemsiz olmayan bir işlem yapmanız gerekir. Ancak, bazı senaryolarda bu geçerli bir yaklaşımdır.

Azure SDK 'Ları tarafından sunulan bazı istemciler, her bir bölümün tek bir okuyucu içerdiğinden ve bir olay hub 'ının tüm bölümlerinin okunmakta olduğundan emin olmanın ayrıntılarını otomatik olarak yöneten akıllı tüketici aracılarıdır. Bu, kodunuzun, bölümlerin ayrıntılarının çoğunu yoksayabilmesi için Olay Hub 'ından okunan olayların işlenmesine odaklanmasını sağlar. Daha fazla bilgi için bkz. [bir bölüme bağlanma](#connect-to-a-partition).

Aşağıdaki örneklerde, Tüketici grubu URI kuralı gösterilmektedir:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

Aşağıdaki şekilde Event Hubs akış işleme mimarisi gösterilmektedir:

![Event Hubs mimarisi](./media/event-hubs-about/event_hubs_architecture.svg)

### <a name="stream-offsets"></a>Akış uzaklıkları

*Uzaklık* bir olayın bölüm içindeki konumudur. Uzaklığı istemci tarafındaki bir imleç olarak düşünebilirsiniz. Uzaklık, olayın bayt cinsinden numaralandırılmasıdır. Bu uzaklık, olay tüketicisinin (okuyucu) olay akışında olayları okumaya başlamak istediği bir noktayı belirtmesini sağlar. Uzaklığı bir zaman damgası veya bir uzaklık değeri olarak belirtebilirsiniz. Tüketiciler, kendi uzaklık değerlerini Event Hubs hizmetinin dışında saklamaktan sorumludur. Bir bölüm içinde her olay bir uzaklık içerir.

![Bölüm boşluğu](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Denetim noktası oluşturma

*Denetim noktası oluşturma*, okuyucuların bir bölüm olay dizisindeki konumlarını işaretledikleri veya uyguladıkları bir işlemdir. Denetim noktası oluşturma, tüketicinin sorumluluğundadır ve bir tüketici grubunda bölüm başına temelinde gerçekleşir. Bu sorumluluk, her bir tüketici grubu için her bölüm okuyucusunun geçerli konumunu olay akışında izlemesi gerektiği ve veri akışının tamamlandığını düşündüğünde hizmeti bilgilendirebileceği anlamına gelir.

Bir okuyucunun bölüm bağlantısı kesilirse yeniden bağlandığında ilgili tüketici grubundaki o bölümün son okuyucusu tarafından daha önce gönderilen denetim noktasında okumaya başlar. Okuyucu bağlandığı zaman, okumayı başlatacak konumu belirtmek için Olay Hub 'ına geçişi geçirir. Bu şekilde, denetim noktası oluşturma özelliğini hem aşağı akış uygulamaları ile olayları "tamamlandı" olarak işaretlemek hem de farklı makinelerde çalışan okuyucular arasında bir yük devretme oluşması durumunda esneklik sağlamak amacıyla kullanabilirsiniz. Bu denetim noktası oluşturma işleminden daha düşük bir uzaklık belirterek daha eski verilere geri dönülebilir. Bu mekanizmayla denetim noktası oluşturma özelliği hem yük devretme esnekliği hem de olay akışı yeniden yürütmesi sağlar.

> [!IMPORTANT]
> Uzaklıklar Event Hubs hizmeti tarafından sağlanır. Olay işlendiği için tüketicinin denetim noktası sorumluluğundadır.

> [!NOTE]
> Azure Blob Storage 'ı, Azure 'da bulunan farklı bir Storage blob SDK sürümünü destekleyen bir ortamda denetim noktası deposu olarak kullanıyorsanız, depolama hizmeti API sürümünü bu ortam tarafından desteklenen belirli bir sürümle değiştirmek için kodu kullanmanız gerekir. Örneğin, [Event Hubs bir Azure Stack hub sürümü 2002](/azure-stack/user/event-hubs-overview)çalıştırıyorsanız, depolama hizmeti için en yüksek sürüm 2017-11-09 ' dir. Bu durumda, Storage Service API sürümünü 2017-11-09 'e hedeflemek için kodu kullanmanız gerekir. Belirli bir depolama API sürümünün nasıl hedeflenecek hakkında bir örnek için GitHub 'da şu örneklere bakın: 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) veya  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>Ortak tüketici görevleri

Tüm Event Hubs tüketicileri, durum algılayan çift yönlü iletişim kanalı olan bir AMQP 1,0 oturumu aracılığıyla bağlanır. Her bölümde bölüme göre ayrılmış olayların taşınmasını kolaylaştıran bir AMQP 1.0 oturumu vardır.

#### <a name="connect-to-a-partition"></a>Bir bölüme bağlanma

Bölümlere bağlanırken, okuyucu bağlantılarını belirli bölümlerle koordine etmek için bir kiralama mekanizması kullanılması yaygın bir uygulamadır. Bu şekilde, bir tüketici grubundaki her bölümün yalnızca bir etkin okuyucu olması mümkündür. Akıllı tüketici aracıları işlevi gören Event Hubs SDK 'lar içindeki istemcileri kullanarak denetim noktası oluşturma, kiralama ve yönetme, okuyucuları basitleştirilir. Bunlar:

- .NET için [Eventprocessorclient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient)
- Java için [Eventprocessorclient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient)
- Python için [Eventhubconsumerclient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient)
- JavaScript/TypeScript için [Eventhubconsumerclient](/javascript/api/@azure/event-hubs/eventhubconsumerclient)

#### <a name="read-events"></a>Olayları okuma

Belirli bir bölüm için bir AMQP 1.0 oturumu ve bağlantı açıldıktan sonra olaylar Event Hubs hizmeti tarafından AMQP 1.0 istemcisine teslim edilir. Bu teslim mekanizması, HTTP GET gibi çekme tabanlı mekanizmalardan daha yüksek verimlilik ve daha düşük gecikme sağlar. Olaylar istemciye gönderildiğinde her bir olay verisi örneği, olay dizisinde denetim noktası oluşturmayı kolaylaştırmak için kullanılan uzaklık ve dizi numarası gibi önemli meta veriler içerir.

Olay verileri:
* Uzaklık
* Sıra numarası
* Gövde
* Kullanıcı özellikleri
* Sistem özellikleri

Uzaklığın yönetilmesi sizin sorumluluğunuzdadır.

## <a name="next-steps"></a>Sonraki adımlar

Event Hubs hakkında daha fazla bilgi için şu bağlantıları ziyaret edin:

- Event Hubs kullanmaya başlayın
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Event Hubs programlama kılavuzu](event-hubs-programming-guide.md)
* [Event Hubs’da kullanılabilirlik ve tutarlılık](event-hubs-availability-and-consistency.md)
* [Event Hubs ile ilgili SSS](event-hubs-faq.yml)
* [Event Hubs örnekleri](event-hubs-samples.md)