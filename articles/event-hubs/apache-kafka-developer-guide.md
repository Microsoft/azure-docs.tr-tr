---
title: Event Hubs için Apache Kafka Geliştirici Kılavuzu
description: Bu makalede, Kafka uygulamalarınızı Azure Event Hubs ile tümleştirmeyi anlatan makalelerin bağlantıları sağlanmaktadır.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 84fa40d7cc4b09f31d78665f901b49adacf3267f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303092"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Azure Event Hubs için Apache Kafka Geliştirici Kılavuzu
Bu makale, Apache Kafka uygulamalarınızın Azure Event Hubs ile nasıl tümleştirileceğini betimleyen makalelere bağlantılar sağlar. 

## <a name="overview"></a>Genel Bakış
Event Hubs, kendi Kafka kümenizi çalıştırmaya alternatif olarak, var olan Kafka tabanlı uygulamalarınız tarafından kullanılabilecek bir Kafka uç noktası sağlar. Event Hubs, mevcut Kafka uygulamalarınızın birçoğu ile birlikte kullanılabilir. Daha fazla bilgi için bkz. [Event Hubs Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Hızlı Başlangıçlar
Hızlı başlangıçlara GitHub 'da ve Kafka için Event Hubs hızlı bir şekilde yararlanmanıza yardımcı olan bu içerik kümesinde bulabilirsiniz.

### <a name="quickstarts-in-github"></a>GitHub 'da hızlı başlangıç
**Azure-Event-hub 'lar-for-Kafka** depolarındaki şu hızlı başlangıçlara bakın: 

| İstemci dili/çerçevesi | Açıklama | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Bu hızlı başlangıçta, .NET Core 2,0 kullanılarak C# dilinde yazılmış örnek bir üretici ve tüketici kullanarak bir Event Hubs Kafka uç noktası oluşturma ve buna bağlanma gösterilmektedir.</p><p>Bu örnek, Kafka için Event Hubs birlikte kullanılmak üzere değiştirilmiş olan, [confluent 'in Apache Kafka .net Client](https://github.com/confluentinc/confluent-kafka-dotnet)tabanlıdır.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Bu hızlı başlangıçta, Java 'da yazılan örnek bir üretici ve tüketici kullanarak bir Event Hubs Kafka uç noktası oluşturma ve buna bağlanma gösterilmektedir. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Bu hızlı başlangıçta, bir örnek üretici ve düğüm içinde yazılmış bir tüketici kullanarak bir Event Hubs Kafka uç noktası oluşturma ve buna bağlanma gösterilmektedir.</p><p>Bu örnek, [node-rdkafka](https://github.com/Blizzard/node-rdkafka) kitaplığını kullanır. </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Bu hızlı başlangıçta, Python 'da yazılmış örnek bir üretici ve tüketici kullanarak bir Event Hubs Kafka uç noktası oluşturma ve buna bağlanma gösterilmektedir.</p><p>Bu örnek, Kafka için Event Hubs birlikte kullanılmak üzere değiştirilmiş olan, [confluent 'ın Apache Kafka Python istemcisini](https://github.com/confluentinc/confluent-kafka-python)temel alır.</p>|
| [Git](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Bu hızlı başlangıçta, go 'da yazılan örnek bir üretici ve tüketici kullanarak bir Event Hubs Kafka uç noktası oluşturma ve buna bağlanma gösterilmektedir.</p><p>Bu örnek, Kafka için Event Hubs birlikte kullanılmak üzere değiştirilmiş olan, [confluent Apache Kafka Golang istemcisini](https://github.com/confluentinc/confluent-kafka-go)temel alır.</p>| 
| [Sarama Kafka go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Bu hızlı başlangıçta, [Sarama Kafka istemci](https://github.com/Shopify/sarama) kitaplığı kullanılarak Go ile yazılan örnek bir üretici ve tüketici kullanarak bir Event Hubs Kafka uç noktası oluşturma ve buna bağlanma gösterilmektedir. |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Bu hızlı başlangıçta, Apache Kafka dağıtımı ile paketlenmiş CLı kullanarak bir Event Hubs Kafka uç noktası oluşturma ve buna bağlanma gösterilmektedir.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat, hızı ve küçük ayak izi nedeniyle popüler olan ve librdkafka temel olmayan bir JVM komut satırı tüketicisidir. Bu hızlı başlangıçta örnek bir yapılandırma ve birkaç basit örnek kafkacat komutu bulunur. | 
 
### <a name="quickstarts-in-docs"></a>DOCS 'ta hızlı başlangıç
Bkz. hızlı başlangıç: Bu içerik kümesindeki [Kafka protokolünü kullanarak Event Hubs veri akışı](event-hubs-quickstart-kafka-enabled-event-hubs.md) Event Hubs, nasıl akış yapılacağı hakkında adım adım yönergeler sağlar. Uygulamalarınızda yalnızca bir yapılandırma değişikliği ile Event Hubs konuşabilmek için üreticileri ve tüketicilerinizi nasıl kullanacağınızı öğrenirsiniz. 


## <a name="tutorials"></a>Öğreticiler 

### <a name="tutorials-in-github"></a>GitHub 'daki öğreticiler
GitHub 'da aşağıdaki öğreticilere bakın:

| Öğretici | Açıklama | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Bu öğreticide, protokol istemcilerinizi değiştirmeden veya kendi kümelerinizi çalıştırmadan Akka akışlarının Kafka özellikli Event Hubs nasıl bağlanacağı gösterilmektedir. **Java** ve **Scala** programlama dillerini kullanan iki ayrı öğretici vardır. | 
| [Bağlan](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Bu belge, Kafka Connect 'i Azure Event Hubs tümleştirirken ve temel FileStreamSource ve FileStreamSink bağlayıcılarını dağıtırken size yol gösterecektir. Bu bağlayıcılar üretim kullanımına yönelik değildir, ancak Azure Event Hubs bir Kafka Aracısı olarak kendini gizleyen bir uçtan uca Kafka Connect senaryosu gösterir.| 
| [Dosya](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Bu belge, dosya ve Event Hubs dosya ve Kafka çıkışı aracılığıyla tümleştirerek size yol gösterecektir. | 
| [Flınk](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Bu öğreticide, protokol istemcilerinizi değiştirmeden veya kendi kümelerinizi çalıştırmadan Apache flink 'in Kafka özellikli Event Hubs nasıl bağlanacağı gösterilmektedir. | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Bu belge, floentd ve Event Hubs akıcı entd için çıkış eklentisini kullanarak size kılavuzluk eder `out_kafka` . |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | Bu öğreticide, farklı protokoller kullanılarak tüketiciler ve üreticileri arasında nasıl olay alışverişi yapılacağı gösterilmektedir. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Bu öğretici logstash Kafka giriş/çıkış eklentilerini kullanarak Logstash 'i Kafka özellikli Event Hubs tümleştirirken size yol gösterecektir. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Bu öğreticide, bir olay hub 'ı ve Kafka MirrorMaker 'ın, Event Hubs hizmetinde Kafka giriş akışını yansıtarak mevcut bir Kafka işlem hattını Azure 'a nasıl tümleştirebileceğinizi gösterir. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | Bu öğreticide, Apache nBir Event Hubs ad alanına nasıl bağlanacağı gösterilmektedir. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Hızlı başlangıç, Go ve Java programlama dillerinde yazılmış örnek bir üretici ve tüketici kullanarak bir Event Hubs Kafka uç noktası oluşturma ve bu noktaya bağlanma şeklini gösterir. |
| [Confluent 'in şema kayıt defteri](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Bu öğretici, şema kayıt defterini tümleştirirken ve Kafka için Event Hubs size yol gösterecektir. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Bu öğreticide, protokol istemcilerinizi değiştirmeden veya kendi Kafka kümelerinizi çalıştırmadan Spark uygulamanızın bir olay hub 'ına nasıl bağlanacağı gösterilmektedir. | 

### <a name="tutorials-in-docs"></a>DOCS 'ta öğreticiler
Ayrıca bkz. öğreticiye bakın: Bu içerik kümesindeki [Stream Analytics 'i kullanarak Event Hubs olaylar Için işleme Apache Kafka](event-hubs-kafka-stream-analytics.md) , verileri Event Hubs ve Azure Stream Analytics ile işleme işlemlerini gösterir.

## <a name="how-to-guides"></a>Nasıl yapılır kılavuzları
Belgelerimizde aşağıdaki nasıl yapılır kılavuzlarını inceleyin:

| Makale | Açıklama | 
| ------- | ----------- | 
| [Bir olay hub'ında Kafka aracısı yansıtma](event-hubs-kafka-mirror-maker-tutorial.md) | Kafka MirrorMaker kullanarak bir olay hub 'ında Kafka aracısının nasıl yansıtmasının gösterir. |
| [Apache Spark'ı bir olay hub'ına bağlama](event-hubs-kafka-spark-tutorial.md) | Spark uygulamanızı gerçek zamanlı akış için Event Hubs bağlama konusunda size kılavuzluk eder. |
| [Apache Flink'i bir olay hub'ına bağlama](event-hubs-kafka-flink-tutorial.md) | Protokol istemcilerinizi değiştirmeden veya kendi kümelerinizi çalıştırmadan Apache flink 'i bir olay hub 'ına nasıl bağlayabileceğiniz gösterilir. |
| [Apache Kafka Connect 'i bir olay hub 'ı ile tümleştirme (Önizleme)](event-hubs-kafka-connect-tutorial.md) | Kafka Connect 'i bir olay hub 'ı ile tümleştirerek temel FileStreamSource ve FileStreamSink bağlayıcılarını dağıtmanıza yardımcı olur. |
| [Akka Streams’i bir olay hub’ına bağlama](event-hubs-kafka-akka-streams-tutorial.md) | Protokol istemcilerinizi değiştirmeden veya kendi kümelerinizi çalıştırmadan, Akka akışlarını bir olay hub 'ına bağlamayı gösterir. |
| [Azure ile Apache Kafka için Spring Boot Starter 'ı kullanın Event Hubs](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Azure Event Hubs ile Apache Kafka kullanmak üzere Spring Boot başlatıcısıyla oluşturulmuş bir Java tabanlı yay bulut akışı bağlayıcısının nasıl yapılandırılacağını gösterir. |

## <a name="next-steps"></a>Sonraki adımlar
Hızlı başlangıç ve öğreticiler klasörleri altındaki GitHub deposu [Azure-Event-hub-for-Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) örneklerini gözden geçirin.

Ayrıca, aşağıdaki makalelere bakın:

- [Event Hubs için sorun giderme kılavuzu Apache Kafka](apache-kafka-troubleshooting-guide.md)
- [Sık Sorulan Sorular-Apache Kafka için Event Hubs](apache-kafka-frequently-asked-questions.yml)
- [Event Hubs için Apache Kafka geçiş kılavuzu](apache-kafka-migration-guide.md)



