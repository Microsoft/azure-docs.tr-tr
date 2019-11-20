---
title: Azure Event Hubs nedir? -Büyük veri alma hizmeti | Microsoft Docs
description: Saniyede milyonlarca olayın eklenmesini sağlayan bir Büyük Veri akış hizmeti olan Azure Event Hubs hakkında daha fazla bilgi edinin.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: overview
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 242f2fa9885f3f85439caddd061f650baafb8df4
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314409"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs - Büyük veri akışı platformu ve olay alımı hizmeti
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

Verilerinizi [Azure Blob depolamada](https://azure.microsoft.com/services/storage/blobs/) neredeyse gerçek zamanlı olarak [yakalayın](event-hubs-capture-overview.md) veya uzun süreli saklama veya mikro toplu işleme için [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) . Bu davranışı gerçek zamanlı analiz türetmede kullandığınız akışta elde edebilirsiniz. Olay verilerinin yakalanması hızlı bir şekilde ayarlanır. Çalıştırmak için yönetim maliyeti yoktur ve Event Hubs [üretilen iş birimleriyle](event-hubs-scalability.md#throughput-units)otomatik olarak ölçeklendirilir. Event Hubs veri yakalama yerine veri işlemeye odaklanmanızı sağlar.

Azure Event Hubs, sunucusuz mimari için [Azure İşlevleri](/azure/azure-functions/) ile de tümleştirilebilir.

## <a name="scalable"></a>Ölçeklenebilir

Event Hubs ile megabayt boyutunda veri akışlarıyla başlayıp gigabayt veya terabayt boyutlarına ulaşabilirsiniz. [Otomatik şişme](event-hubs-auto-inflate.md) özelliği, aktarım hızı birimlerini kullanım ihtiyaçlarınıza göre ölçeklendirmek için kullanabileceğiniz birçok özellikten bir tanesidir.

## <a name="rich-ecosystem"></a>Zengin ekosistem

[Apache Kafka ekosistemleri için Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) [, Apache Kafka (1,0 ve üzeri)](https://kafka.apache.org/) istemcilerin ve uygulamaların Event Hubs ile iletişim kurmasını sağlar. Kendi Kafka kümelerinizi ayarlamanız, yapılandırmanız ve yönetmeniz gerekmez.

Çeşitli [dillerde (.NET, Java, Python, Go, Node.js)](https://github.com/Azure/azure-event-hubs) kullanılabilen geniş ekosistem sayesinde akışlarınızı Event Hubs'dan işlemeye kolayca başlayabilirsiniz. Desteklenen tüm istemci dilleri, düşük düzeyde tümleştirme sağlar. Ekosistem Ayrıca, Azure Stream Analytics ve Azure Işlevleri gibi Azure hizmetleriyle sorunsuz bir tümleştirme sağlar ve bu sayede sunucusuz mimariler oluşturmanızı sağlar.

## <a name="key-architecture-components"></a>Temel mimari bileşenler
Event Hubs şu [temel bileşenleri](event-hubs-features.md) içerir:

- **Event üreticileri**: Bir olay hub 'ına veri gönderen herhangi bir varlık. Olay yayımcıları HTTPS, AMQP 1.0 veya Apache Kafka (1.0 ve üzeri) kullanarak olayları yayımlayabilir
- **Bölümler**: Her tüketici ileti akışının yalnızca belirli bir alt kümesini veya bölümünü okur.
- **Tüketici grupları**: Tüm Olay Hub 'ının bir görünümü (durum, konum veya konum). Tüketici grupları, uygulamaların her biri için bir olay akışının ayrı bir görünümüne sahip olmasını sağlar. Akışı kendi hızlarında ve kendi uzaklıklarıyla bağımsız olarak okur.
- **Üretilen iş birimleri**: Event Hubs üretilen iş kapasitesini denetleyen, önceden satın alınan kapasite birimleri.
- **Olay alıcıları**: Olay Hub 'ından olay verilerini okuyan herhangi bir varlık. Tüm Event Hubs tüketicileri AMQP 1,0 oturumu aracılığıyla bağlanır. Event Hubs hizmeti, olayları kullanılabilir hale geldiğinde bir oturum üzerinden sunar. Tüm Kafka tüketicileri Kafka protokol 1.0 ve üzeri ile bağlanır.

Aşağıdaki şekilde Event Hubs akış işleme mimarisi gösterilmektedir:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>Sonraki adımlar

Event Hubs kullanmaya başlamak için bkz. **olayları gönderme ve alma** öğreticileri:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (yalnızca gönderme)](event-hubs-c-getstarted-send.md)
- [Apache Storm (yalnızca alma)](event-hubs-storm-getstarted-receive.md)


Event Hubs hakkında daha fazla bilgi edinmek için şu makalelere göz atın:

- [Event Hubs özelliklerine genel bakış](event-hubs-features.md)
- [Sık sorulan sorular](event-hubs-faq.md).


