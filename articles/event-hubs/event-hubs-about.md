---
title: Azure Event Hubs nedir? -Büyük veri alma hizmeti | Microsoft Docs
description: Saniyede milyonlarca olayın eklenmesini sağlayan bir Büyük Veri akış hizmeti olan Azure Event Hubs hakkında daha fazla bilgi edinin.
ms.topic: overview
ms.date: 01/13/2021
ms.openlocfilehash: 35e89f60e93ec759d852641653ad138d7672cc37
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303058"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs — büyük bir veri akışı platformu ve olay alma hizmeti
Azure Event Hubs, büyük bir veri akışı platformu ve olay alma hizmetidir. Saniyede milyonlarca olayı alabilir ve işleyebilir. Bir olay hub’ına gönderilen veriler, herhangi bir gerçek zamanlı analiz sağlayıcısı ve işlem grubu oluşturma/depolama bağdaştırıcıları kullanılarak dönüştürülüp depolanabilir.

Aşağıdaki senaryolar Event Hubs kullanabileceğiniz bazı senaryolardır:

- Anomali algılama (sahte/aykırı değer)
- Uygulama günlüğüne kaydetme
- Tıklama dizileri gibi analiz işlem hatları
- Canlı pano oluşturma
- Veri arşivleme
- İşlem gerçekleştirme
- Kullanıcı telemetrisi işleme
- Cihaz telemetrisi akışı

<iframe width="560" height="315" src="https://www.youtube.com/embed/45wgY-VSk9I" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## <a name="why-use-event-hubs"></a>Event Hubs’ı neden kullanmalıyım?

Veriler ancak kolay bir şekilde işlenebildiğinde ve veri kaynaklarından tam zamanında içgörüler alındığında değerlidir. Event Hubs, tüm büyük veri işlem hattınızı oluşturmak için Azure içindeki ve dışındaki veri ve analiz hizmetleriyle, düşük gecikme süresi ve sorunsuz tümleştirme ile dağıtılmış bir akış işleme platformu sağlar.

Event Hubs bir olay işlem hattı için "ön kapı" görevi yapar ve çözüm mimarilerinde genelde *olay alıcı* olarak adlandırılır. Olay yutucu, bir olay akışının üretimini ilgili olayların kullanılmasına ayıran, olay yayımcıları ile olay tüketicileri arasında duran bir bileşen veya hizmettir. Event Hubs, olay tüketicilerinden olay üreticileri bir şekilde ayırarak, zaman saklama arabelleğinin bulunduğu Birleşik bir akış platformu sağlar.

Aşağıdaki bölümlerde Azure Event Hubs hizmetinin temel özellikleri anlatılmaktadır:

## <a name="fully-managed-paas"></a>Tam olarak yönetilen PaaS

Event Hubs, çok az yapılandırma veya yönetim yüküyle birlikte tam olarak yönetilen bir hizmet olarak platform (PaaS), iş çözümlerinize odaklanırsınız. [Apache Kafka ekosistemleri için Event Hubs](event-hubs-for-kafka-ecosystem-overview.md), kümelerinizi yönetmeye, yapılandırmaya veya çalıştırmaya gerek kalmadan PaaS Kafka deneyimi sunar.

## <a name="support-for-real-time-and-batch-processing"></a>Gerçek zamanlı ve toplu işlem desteği

Eyleme dönüştürülebilir içgörüler elde etmek için akışınızı gerçek zamanlı olarak ekleyin, arabelleğe alın, depolayın ve işleyin. Event Hubs, birden fazla uygulamanın akışı eşzamanlı olarak işlemesini ve işleme hızını denetlemenize olanak sağlayan [bölümlenmiş bir tüketici modeli](event-hubs-scalability.md#partitions)kullanır.

Verilerinizi [Azure Blob depolamada](https://azure.microsoft.com/services/storage/blobs/) neredeyse gerçek zamanlı olarak [yakalayın](event-hubs-capture-overview.md) veya [](https://azure.microsoft.com/services/data-lake-store/)   uzun süreli saklama veya mikro toplu işleme için Azure Data Lake Storage. Bu davranışı gerçek zamanlı analiz türetmede kullandığınız akışta elde edebilirsiniz. Olay verilerinin yakalanması hızlı bir şekilde ayarlanır. Çalıştırmak için yönetim maliyeti yoktur ve Event Hubs [üretilen iş birimleriyle](event-hubs-scalability.md#throughput-units)otomatik olarak ölçeklendirilir. Event Hubs veri yakalama yerine veri işlemeye odaklanmanızı sağlar.

Azure Event Hubs, sunucusuz mimari için [Azure İşlevleri](../azure-functions/index.yml) ile de tümleştirilebilir.

## <a name="scalable"></a>Ölçeklenebilir

Event Hubs ile megabayt boyutunda veri akışlarıyla başlayıp gigabayt veya terabayt boyutlarına ulaşabilirsiniz. [Otomatik şişme](event-hubs-auto-inflate.md) özelliği, aktarım hızı birimlerini kullanım ihtiyaçlarınıza göre ölçeklendirmek için kullanabileceğiniz birçok özellikten bir tanesidir.

## <a name="rich-ecosystem"></a>Zengin ekosistem

Endüstri standardı AMQP 1,0 protokolünü temel alan geniş bir ekosistemle ve [.net](https://github.com/Azure/azure-sdk-for-net/), [Java](https://github.com/Azure/azure-sdk-for-java/), [Python](https://github.com/Azure/azure-sdk-for-python/), [JavaScript](https://github.com/Azure/azure-sdk-for-js/)gibi çeşitli dillerde kullanabileceğiniz akışlarınızın Event Hubs kolayca işlenmesine başlayabilirsiniz. Desteklenen tüm istemci dilleri, düşük düzeyde tümleştirme sağlar. Ekosistem Ayrıca, Azure Stream Analytics ve Azure Işlevleri gibi Azure hizmetleriyle sorunsuz bir tümleştirme sağlar ve bu sayede sunucusuz mimariler oluşturmanızı sağlar.

[Apache Kafka ekosistemleri için Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) Ayrıca [Apache Kafka (1,0 ve üzeri)](https://kafka.apache.org/) istemcilerin ve uygulamaların Event Hubs ile iletişim kurmasını sağlar. Kendi Kafka ve Zookeeper kümelerinizi ayarlamanız, yapılandırmanız ve yönetmeniz ya da Azure 'da yerel olmayan bazı Kafka hizmet olarak kullanıma almanız gerekmez.
## <a name="key-architecture-components"></a>Temel mimari bileşenler
Event Hubs şu [temel bileşenleri](event-hubs-features.md) içerir:

- **Olay oluşturucuları**: Olay hub'ına veri gönderen tüm varlıklardır. Olay yayımcıları HTTPS, AMQP 1.0 veya Apache Kafka (1.0 ve üzeri) kullanarak olayları yayımlayabilir
- **Bölümler**: Her tüketici ileti akışının yalnızca belirli bir alt kümesini veya bölümünü okur.
- **Tüketici grupları**: Tüm olay hub'ının bir görünümüdür (durum, konum veya uzaklık). Tüketici grupları, uygulamaların her biri için bir olay akışının ayrı bir görünümüne sahip olmasını sağlar. Akışı kendi hızlarında ve kendi uzaklıklarıyla bağımsız olarak okur.
- **Aktarım hızı birimleri**:Event Hubs'ın aktarım hızı kapasitesini denetleyen önceden satın alınan kapasite birimleridir.
- **Olay alıcıları**: Bir olay hub'ından olay verilerini okuyan tüm varlıklardır. Tüm Event Hubs tüketicileri AMQP 1,0 oturumu aracılığıyla bağlanır. Event Hubs hizmeti, olayları kullanılabilir hale geldiğinde bir oturum üzerinden sunar. Tüm Kafka tüketicileri Kafka protokol 1.0 ve üzeri ile bağlanır.

Aşağıdaki şekilde Event Hubs akış işleme mimarisi gösterilmektedir:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.svg)

## <a name="event-hubs-on-azure-stack-hub"></a>Azure Stack Hub üzerinde Event Hubs
Azure Stack hub 'ında Event Hubs, karma bulut senaryolarına olanak tanır. Hem şirket içi hem de Azure bulut işlemleri için akış ve olay tabanlı çözümler desteklenir. Senaryonuz karma (bağlı) veya bağlantısı kesik olsun, çözümünüz olayları/akışları büyük ölçekte işlemeyi destekleyebilir. Senaryonuz yalnızca gereksinimlerinize göre sağlayabileceğiniz Event Hubs kümesi boyutu ile ilişkilidir. 

Event Hubs sürümleri (Azure Stack hub ve Azure üzerinde), yüksek derecede Özellik eşliği sunmaktadır. Bu eşlik, SDK 'lar, örnekler, PowerShell, CLı ve portalların benzer bir deneyim sunabileceği, birkaç fark sağlar. 

Event Hubs, genel önizleme sırasında yığında ücretsizdir. Daha fazla bilgi için bkz. [Azure Stack hub 'a genel bakış Event Hubs](/azure-stack/user/event-hubs-overview).


## <a name="next-steps"></a>Sonraki adımlar

Event Hubs kullanmaya başlamak için bkz. **olayları gönderme ve alma** öğreticileri:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Git](event-hubs-go-get-started-send.md)
- [C (yalnızca gönderme)](event-hubs-c-getstarted-send.md)
- [Apache Storm (yalnızca alma)](event-hubs-storm-getstarted-receive.md)


Event Hubs hakkında daha fazla bilgi edinmek için şu makalelere göz atın:

- [Event Hubs özelliklerine genel bakış](event-hubs-features.md)
- [Sık sorulan sorular](event-hubs-faq.yml).
