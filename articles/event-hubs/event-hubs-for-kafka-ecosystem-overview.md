---
title: Apache Kafka App 'ten Olay Hub 'ı kullanma-Azure Event Hubs | Microsoft Docs
description: Bu makalede, Azure Event Hubs tarafından Apache Kafka desteği hakkında bilgi sağlanır.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d3271d6e8cb7d423e1dccd235b244688e7ab5683
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555782"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Apache Kafka uygulamalardan Azure Event Hubs kullanma
Event Hubs, kendi Kafka kümenizi çalıştırmaya alternatif olarak, var olan Kafka tabanlı uygulamalarınız tarafından kullanılabilecek bir Kafka uç noktası sağlar. Event Hubs, [Apache Kafka protokol 1,0 ve üstünü](https://kafka.apache.org/documentation/)destekler ve mirrormaker da dahil olmak üzere var olan Kafka uygulamalarınızla birlikte kullanılabilir.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Kafka için Event Hubs neler sağlar?

Kafka özelliği için Event Hubs, Kafka konularındaki okuma ve yazma için Kafka sürümleri 1,0 ve üzeri ile ikili uyumlu Azure Event Hubs 'nin en üstünde bir protokol başı sağlar. Kafka uç noktasını, kod değişikliği olmadan uygulamalarınızdan kullanmaya başlayabilir, ancak en az bir yapılandırma değişikliği yapabilirsiniz. Yapılandırmalarda bağlantı dizesini, Kafka kümenize işaret etmek yerine, Olay Hub 'ınız tarafından sunulan Kafka uç noktasını işaret etmek üzere güncelleştirebilirsiniz. Daha sonra, Kafka protokolünü kullanan uygulamalarınızdan akış olaylarını Event Hubs ' a başlatabilirsiniz. Bu tümleştirme, şu anda önizleme aşamasında olan [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect)gibi çerçeveleri de destekler. 

Kavramsal Kafka ve Event Hubs neredeyse aynıdır: akış verileri için oluşturulan bölümlenmiş günlüklerdir. Aşağıdaki tabloda Kafka ve Event Hubs arasındaki kavramlar eşlenir.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka ve Event hub kavramsal eşleme

| Kafka kavram | Event Hubs kavram|
| --- | --- |
| Küme | uzayına |
| Konu | Olay Hub'ı |
| Bölüm | Bölüm|
| Tüketici grubu | Tüketici grubu |
| Uzaklık | Uzaklık|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kafka ve Event Hubs arasındaki temel farklılıklar

[Apache Kafka](https://kafka.apache.org/) , seçtiğiniz her yerde çalıştırabileceğiniz bir yazılımdır, Event Hubs Azure Blob depolama alanına benzer bir bulut hizmetidir. Yönetilecek bir sunucu veya ağ yok ve yapılandırılacak aracılar yok. Başlıklarınızın bulunduğu bir FQDN olan bir ad alanı oluşturun ve ardından bu ad alanı içinde Event Hubs veya konu başlığı oluşturun. Event Hubs ve ad alanları hakkında daha fazla bilgi için bkz. [Event Hubs özellikleri](event-hubs-features.md#namespace). Bulut hizmeti olarak Event Hubs, uç nokta olarak tek bir kararlı sanal IP adresi kullanır, bu nedenle istemcilerin bir küme içindeki aracılar veya makineler hakkında bilmeleri gerekmez. 

Event Hubs ölçek, satın aldığınız kaç üretilen iş birimi ile, her bir üretilen iş birimi için, saniyede 1 MB veya saniyede 1000 olay entitling. Varsayılan olarak, bir [Otomatik şişeden](event-hubs-auto-inflate.md) özelliği ile sınırınıza ulaştığınızda Event Hubs üretilen iş birimlerini ölçeklendirir; Bu özellik ayrıca Event Hubs for Kafka özelliği ile de kullanılabilir. 

### <a name="security-and-authentication"></a>Güvenlik ve kimlik doğrulaması

Azure Event Hubs, tüm iletişimler için SSL veya TLS gerektirir ve kimlik doğrulaması için paylaşılan erişim Imzaları (SAS) kullanır. Bu gereksinim Ayrıca Event Hubs içindeki bir Kafka uç noktası için de geçerlidir. Kafka ile uyumluluk için Event Hubs, aktarım güvenliği için kimlik doğrulaması ve SASL SSL SASL PLAIN kullanır. Event Hubs güvenlik hakkında daha fazla bilgi için bkz. [Event Hubs kimlik doğrulaması ve güvenlik](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Kafka için kullanılabilen diğer Event Hubs özellikleri

Kafka özelliği için Event Hubs, bir protokolle yazmanızı ve diğer kişilerle okumanızı sağlar. böylece, geçerli Kafka üreticileri Kafka aracılığıyla yayımlamaya devam edebilir ve Azure Stream Analytics ya da Azure Işlevleri gibi Event Hubs okuyucular ekleyebilirsiniz. Ayrıca, [yakalama](event-hubs-capture-overview.md) ve [coğrafi olağanüstü durum kurtarma](event-hubs-geo-dr.md) gibi Event Hubs özellikler, ayrıca, Kafka için Event Hubs özelliği ile de çalışır.

## <a name="features-that-are-not-yet-supported"></a>Henüz desteklenmeyen özellikler 

Henüz desteklenmeyen Kafka özelliklerinin listesi aşağıda verilmiştir:

*   Idempotent üreticisi
*   işlem
*   Sıkıştırma
*   Boyut tabanlı bekletme
*   Günlük düzenleme
*   Mevcut bir konuya bölüm ekleme
*   HTTP Kafka API desteği
*   Kafka akışlar

## <a name="next-steps"></a>Sonraki adımlar

Bu makale, Kafka için Event Hubs bir giriş sağlamıştır. Daha fazla bilgi edinmek için aşağıdaki bağlantılara bakın:

- [Kafka özellikli Event Hubs oluşturma](event-hubs-create-kafka-enabled.md)
- [Kafka uygulamalarınızdan Event Hubs'a akış yapma](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka özellikli bir olay hub'ında Kafka aracısını yansıtma](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark'ı Kafka özellikli bir olay hub'ına bağlama](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink'i Kafka özellikli bir olay hub'ına bağlama](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect'i Kafka özellikli olay hub'ıyla tümleştirme](event-hubs-kafka-connect-tutorial.md)
- [Akka Streams’i Kafka özellikli olay hub'ına bağlama](event-hubs-kafka-akka-streams-tutorial.md)
- [GitHub'ımızdaki örnekleri inceleme](https://github.com/Azure/azure-event-hubs-for-kafka)


