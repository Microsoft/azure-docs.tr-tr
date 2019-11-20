---
title: Özellikler - Azure Event Hubs'a genel bakış | Microsoft Docs
description: Bu makalede, özellikleri ve Azure Event Hubs'ın terminolojisi hakkında ayrıntılar sağlar.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e0505960a413308283c4e67e33ec495eedd3b092
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827718"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Özellikler ve Azure Event Hubs terminolojisinde

Azure Event Hubs, ölçeklenebilir bir olay işleme alır ve büyük hacimli olayları ve verileri, düşük gecikme süresi ve yüksek güvenilirlikle işler hizmetidir. Bkz: [Event Hubs nedir?](event-hubs-what-is-event-hubs.md) üst düzey bir genel bakış.

Bu makalede yer alan bilgiler geliştirir [genel bakış makalesi](event-hubs-what-is-event-hubs.md)ve Event Hubs bileşenler ve özellikler hakkında teknik ve uygulama ayrıntılarını sağlar.

## <a name="namespace"></a>Ad Alanı
Bir Event Hubs ad alanı tarafından başvurulan bir benzersiz bir kapsam kapsayıcı sağlar. kendi [tam etki alanı adı](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), içinde bir veya daha fazla event hubs'ı veya Kafka konularını oluşturma. 

## <a name="event-hubs-for-apache-kafka"></a>Apache Kafka için Event Hubs

[Bu özellik](event-hubs-for-kafka-ecosystem-overview.md) müşterilerin Event Hubs'a Kafka protokolünü kullanarak iletişim kurmasına olanak tanır bir uç nokta sağlar. Bu tümleştirme, müşterilere bir Kafka uç noktası sağlar. Bu, müşterilerin kendi Kafka kümelerini çalıştırmak için bir alternatif sunar, Event Hubs konuşabilir mevcut Kafka uygulamalarını yapılandırmak sağlar. Event Hubs için Apache Kafka, Kafka protokolü 1.0 ve üzeri destekler. 

Bu tümleştirme sayesinde Kafka kümelerini çalıştırmak veya bunları içeren Zookeeper yönetmeniz gerekmez. Bu ayrıca, En zorlu olay hub'ları gibi yakalama, otomatik şişme ve coğrafi olağanüstü durum kurtarma özelliklerinin bazılarını ile çalışmanıza olanak sağlar.

Bu tümleştirme de yansıtma Oluşturucu gibi uygulamalar veya framework Kafka bağlanma gibi çalışması yalnızca yapılandırma değişikliğiyle clusterless sağlar. 

## <a name="event-publishers"></a>Olay yayımcıları

Olay hub'ına veri gönderen herhangi bir olay üretici varlıktır veya *olay yayımcısı*. Olay yayımcıları HTTPS veya AMQP 1.0 veya Kafka 1.0 ve üzeri kullanarak olayları yayımlayabilir. Olay yayımcıları kendilerini bir olay hub'ına tanıtmak için Paylaşılan Erişim İmzası (SAS) belirteci kullanır ve benzersiz bir kimliğe sahip olabilir ya da ortak bir SAS belirteci kullanabilir.

### <a name="publishing-an-event"></a>Olay yayımlama

Bir olayı AMQP 1.0, 1.0 (ve üzeri) Kafka veya HTTPS üzerinden yayımlayabilirsiniz. Event Hubs sağlar [istemci kitaplıkları ve sınıfları](event-hubs-dotnet-framework-api-overview.md) olayları, .NET istemcilerinden bir olay hub'ına yayımlama. Diğer çalışma zamanları ve platformlar için [Apache Qpid](https://qpid.apache.org/) gibi herhangi bir AMQP 1.0 istemcisi kullanabilirsiniz. Olayları ayrı ayrı veya toplu olarak yayımlayabilirsiniz. Tek bir yayın (Olay verileri örneği), tek bir olay ya da toplu işlem olmasına bakılmaksızın 1 MB sınırı vardır. Bu hata eşiği sonuçlarında daha büyük olaylar yayımlama. Yayımcıların olay hub'ındaki bölümleri bilmemesi ve yalnızca bir *bölüm anahtarı* (sonraki bölümde açıklanmıştır) ya da kimliklerini SAS belirteci üzerinden belirtmeleri en iyi yöntemdir.

AMQP veya HTTPS kullanma seçimi kullanım senaryosuna bağlıdır. AMQP, taşıma düzeyi güvenliği (TLS) veya SSL/TLS’ye ek olarak kalıcı bir çift yönlü yuva oluşturulmasını gerektirir. Oturum başlatılırken AMQP’nin ağ maliyetleri daha yüksektir, ancak HTTPS her istek için ek SSL yükü gerektirir. Daha sık yayımcılar için AMQP daha yüksek performans sunar.

![Event Hubs](./media/event-hubs-features/partition_keys.png)

Event Hubs aynı bölüm anahtarı değerini paylaşan tüm olayların sırayla ve aynı bölüme iletilmesini sağlar. Bölüm anahtarlarının yayımcı ilkeleriyle birlikte kullanılması durumunda yayımcı kimliğinin ve bölüm anahtarı değerinin eşleşmesi gerekir. Aksi takdirde bir hata oluşur.

### <a name="publisher-policy"></a>Yayımcı ilkesi

Event Hubs, *yayımcı ilkeleri* aracılığıyla olay yayımcıları üzerinde ayrıntılı denetim sağlar. Yayımcı ilkeleri çok sayıda bağımsız olay yayımcısını kolaylaştırmak için tasarlanmış çalışma zamanı özellikleridir. Yayımcı ilkeleriyle her yayımcı, olayları aşağıdaki mekanizmayı kullanarak bir olay hub'ında yayımlarken kendi benzersiz tanımlayıcısını kullanır:

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Yayımcı adlarını önceden oluşturmanız gerekli değildir, ancak bunlar bağımsız yayımcı kimlikleri sağlamak amacıyla bir olayı yayımlarken kullanılan SAS belirteci ile eşleşmelidir. Yayımcı ilkelerini kullanırken **PartitionKey** değeri yayımcı adına ayarlanır. Bu hizmetin düzgün çalışması için bu değerlerin eşleşmesi gerekir.

## <a name="capture"></a>Capture

[Event Hubs yakalama](event-hubs-capture-overview.md) otomatik olarak Event Hubs, akış verilerini yakalamanıza ve seçtiğiniz bir Blob Depolama hesabı veya bir Azure veri Gölü hizmeti hesabı için kaydetmeden olanak tanır. Azure portalından yakalamayı etkinleştirme ve en küçük boyut ve yakalama gerçekleştirmek için zaman penceresi belirtin. Event Hubs yakalama özelliğini kullanarak, kendi Azure Blob Depolama hesabı ve kapsayıcı ya da bunlardan biri yakalanan verileri depolamak için kullanılan Azure veri Gölü hizmeti hesabı belirtin. Yakalanan veriler Apache Avro biçiminde yazılır.

## <a name="partitions"></a>Bölümler
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS belirteçleri

Event Hubs, ad alanında ve olay hub’ı düzeyinde kullanılabilen *Paylaşılan Erişim İmzaları* kullanır. SAS belirteci bir SAS anahtarından oluşturulur ve belirli bir biçimde kodlanmış bir URL’nin SHA karmasıdır. Event Hubs anahtar (ilke) ve belirtecin adını kullanarak karmayı yeniden oluşturabilir ve böylece gönderenin kimliğini doğrular. Normalde, olay yayımcıları için SAS belirteci yalnızca belirli bir olay hub'ı üzerindeki **gönder** ayrıcalıkları ile oluşturulur. Bu SAS belirteci URL mekanizması, yayımcı ilkesinde sunulan yayımcı kimliğinin temelini oluşturur. SAS ile çalışma hakkında daha fazla bilgi için bkz. [Service Bus ile Paylaşılan Erişim İmzası Kimlik Doğrulaması](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Olay tüketicileri

Bir olay hub'ından olay verilerini okuyan herhangi bir varlık *olay tüketicisidir*. Tüm Event Hubs tüketicileri AMQP 1.0 oturumu üzerinden bağlanır ve olaylar, kullanılabilir olduğu anda oturum üzerinden iletilir. İstemcinin veri kullanılabilirliğini yoklaması gerekmez.

### <a name="consumer-groups"></a>Tüketici grupları

Event Hubs yayımlama/abonelik mekanizması *tüketici grupları* aracılığıyla etkinleştirilir. Tüketici grubu tüm olay hub'ının bir görünümüdür (durum, konum veya uzaklık). Tüketici grupları birden çok tüketen uygulamayı her biri olay akışının ayrı bir görünümüne sahip olacak ve akışı kendi hızlarında ve kendi sapmalarıyla bağımsız bir şekilde okuyacak şekilde etkinleştirir.

Bir akış işleme mimarisinde her bir aşağı akış uygulaması bir tüketici grubuna karşılık gelir. Olay verilerini uzun süreli depolama alanına yazmak isterseniz bu depolama yazma uygulaması bir tüketici grubudur. Bundan sonra karmaşık olay işlemesi başka ve ayrı bir tüketici grubu tarafından gerçekleştirilebilir. Bölümlere yalnızca bir tüketici grubu üzerinden erişebilirsiniz. Bir olay hub'ında her zaman varsayılan bir tüketici grubu vardır ve Standart katmanlı bir olay hub'ı için en fazla 20 tüketici grubu oluşturabilirsiniz.

Olabilir en fazla 5 eşzamanlı okuyucu tüketici grubu başına bir bölüme; ancak **olduğunu yalnızca bir etkin alıcı tüketici grubu başına bir bölüme önerilir**. Tek bir bölüm içinde her Okuyucu tüm iletileri alır. Ardından aynı bölüme birden fazla okuyucuyu kapsayacak varsa, yinelenen iletileri işler. Bu Önemsiz olmayabilir, kodunuzda ele almanız gerekir. Ancak, bazı senaryolarda geçerli bir yaklaşım değildir.


Tüketici grubu URI kuralının örnekleri aşağıda verilmiştir:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

Aşağıdaki şekilde Event Hubs akış işleme mimarisi gösterilmektedir:

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Akış uzaklıkları

*Uzaklık* bir olayın bölüm içindeki konumudur. Uzaklığı istemci tarafındaki bir imleç olarak düşünebilirsiniz. Uzaklık, olayın bayt cinsinden numaralandırılmasıdır. Bu uzaklık, olay tüketicisinin (okuyucu) olay akışında olayları okumaya başlamak istediği bir noktayı belirtmesini sağlar. Uzaklığı bir zaman damgası veya bir uzaklık değeri olarak belirtebilirsiniz. Tüketiciler, kendi uzaklık değerlerini Event Hubs hizmetinin dışında saklamaktan sorumludur. Bir bölüm içinde her olay bir uzaklık içerir.

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Denetim noktası oluşturma

*Denetim noktası oluşturma*, okuyucuların bir bölüm olay dizisindeki konumlarını işaretledikleri veya uyguladıkları bir işlemdir. Denetim noktası oluşturma, tüketicinin sorumluluğundadır ve bir tüketici grubunda bölüm başına temelinde gerçekleşir. Bu sorumluluk, her bir tüketici grubu için her bölüm okuyucusunun geçerli konumunu olay akışında izlemesi gerektiği ve veri akışının tamamlandığını düşündüğünde hizmeti bilgilendirebileceği anlamına gelir.

Bir okuyucunun bölüm bağlantısı kesilirse yeniden bağlandığında ilgili tüketici grubundaki o bölümün son okuyucusu tarafından daha önce gönderilen denetim noktasında okumaya başlar. Okuyucu bağlandığında okumaya başlayacağı konumu belirtmek için olay hub'ına uzaklığı geçirir. Bu şekilde, denetim noktası oluşturma özelliğini hem aşağı akış uygulamaları ile olayları "tamamlandı" olarak işaretlemek hem de farklı makinelerde çalışan okuyucular arasında bir yük devretme oluşması durumunda esneklik sağlamak amacıyla kullanabilirsiniz. Bu denetim noktası oluşturma işleminden daha düşük bir uzaklık belirterek daha eski verilere geri dönülebilir. Bu mekanizmayla denetim noktası oluşturma özelliği hem yük devretme esnekliği hem de olay akışı yeniden yürütmesi sağlar.

### <a name="common-consumer-tasks"></a>Ortak tüketici görevleri

Tüm Event Hubs tüketicileri bir durumu algılayan çift yönlü iletişim kanalı bir AMQP 1.0 oturumu üzerinden bağlanır. Her bölümde bölüme göre ayrılmış olayların taşınmasını kolaylaştıran bir AMQP 1.0 oturumu vardır.

#### <a name="connect-to-a-partition"></a>Bir bölüme bağlanma

Bölümlere doğrudan bağlanırken okuyucu bağlantılarının belirli bölümlerle koordine edilmesi için bir kiralama mekanizmasının kullanılması yaygın bir uygulamadır. Bu şekilde, bir tüketici grubundaki her bölümün yalnızca bir etkin okuyucuya sahip olması mümkündür. Denetim noktası oluşturma, kiralama ve okuyucuları yönetme işlemleri, .NET istemcileri için [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) sınıfı kullanılarak basitleştirilir. Event Processor Host, akıllı bir tüketici aracısıdır.

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

* [Event Hubs öğreticisi][Event Hubs tutorial] ile çalışmaya başlayın
* [Event Hubs programlama kılavuzu](event-hubs-programming-guide.md)
* [Event Hubs’da kullanılabilirlik ve tutarlılık](event-hubs-availability-and-consistency.md)
* [Event Hubs ile ilgili SSS](event-hubs-faq.md)
* [Event Hubs örnekleri][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Event Hubs örnekleri]: https://github.com/Azure/azure-event-hubs/tree/master/samples
