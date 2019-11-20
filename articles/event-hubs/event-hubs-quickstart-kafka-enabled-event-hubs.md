---
title: 'Hızlı başlangıç: Kafka protokolünü kullanarak Azure Event Hubs ile veri akışı'
description: "Hızlı başlangıç: Bu makalede, Kafka Protokolü ve API 'Leri kullanılarak Azure Event Hubs 'da nasıl akış yapılacağı hakkında bilgiler sağlanmaktadır."
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.openlocfilehash: 2222345054982799f9f9e0b84961271a3cc04ddf
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717811"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Hızlı başlangıç: Kafka protokolünü kullanarak Event Hubs veri akışı
Bu hızlı başlangıçta, protokol istemcilerinizi değiştirmenize veya kendi kümelerinizi çalıştırmanıza gerek kalmadan Kafka etkin Event Hubs’a nasıl akış oluşturulacağı gösterilir. Yalnızca uygulamalarınızdaki bir yapılandırma değişikliğiyle Kafka etkin Event Hubs ile konuşmak için üreticilerinizi ve tüketicilerinizi nasıl kullanacağınızı öğrenirsiniz. Azure Event Hubs [Apache Kafka sürüm 1.0](https://kafka.apache.org/10/documentation.html)’ı destekler.

> [!NOTE]
> Bu örnek [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)'da sağlanır

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıcı tamamlamak için aşağıdaki önkoşulların karşılandığından emin olun:

* [Apache Kafka için Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) makalesini okuyun.
* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* Bir Maven ikili arşivini [indirin](https://maven.apache.org/download.cgi) ve [yükleyin](https://maven.apache.org/install.html).
* [Git](https://www.git-scm.com/)
* [Kafka etkin Event Hubs ad alanı](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka etkin Event Hubs ad alanı oluşturma

1. [Azure Portal](https://portal.azure.com)oturum açın ve ekranın sol üst kısmında bulunan **kaynak oluştur ' a** tıklayın.

2. Event Hubs araması yapın ve burada gösterilen seçenekleri belirleyin:
    
    ![Portalda Event Hubs arama](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Benzersiz bir ad belirtin ve ad alanında Kafka'yı etkinleştirin. **Oluştur**'a tıklayın. Note: Kafka için Event Hubs yalnızca standart ve adanmış katman Event Hubs tarafından desteklenir. Temel katman Event Hubs, herhangi bir Kafka işlemine yanıt olarak bir konu yetkilendirme hatası döndürür.
    
    ![Ad alanı oluşturma](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Ad alanı oluşturulduktan sonra **Ayarlar** sekmesinde **Paylaşılan erişim ilkeleri**'ne tıklayarak bağlantı dizesini alın.

    ![Paylaşılan erişim ilkeleri’ne tıklayın.](./media/event-hubs-create/create-event-hub7.png)

5. Varsayılan **RootManageSharedAccessKey** ilkesini seçebilir veya yeni bir ilke ekleyebilirsiniz. İlke adına tıklayın ve bağlantı dizesini kopyalayın. 
    
    ![İlke seçme](./media/event-hubs-create/create-event-hub8.png)
 
6. Bu bağlantı dizesini Kafka uygulaması yapılandırmanıza ekleyin.

Artık Kafka protokolünü kullanan uygulamalarınızdaki olayların akışını Event Hubs'a yapabilirsiniz.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Event Hubs’da Kafka ile ileti gönderme ve alma

1. [Kafka için Azure Event Hubs deposunu](https://github.com/Azure/azure-event-hubs-for-kafka) kopyalayın.

2. `azure-event-hubs-for-kafka/quickstart/java/producer` sayfasına gidin.

3. `src/main/resources/producer.config` konumundaki üreticinin yapılandırma ayrıntılarını aşağıdaki şekilde güncelleştirin:

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    
4. Üretici kodunu çalıştırın ve Kafka etkin Event Hubs’a akış oluşturun:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. `azure-event-hubs-for-kafka/quickstart/java/consumer` sayfasına gidin.

6. `src/main/resources/consumer.config` konumundaki tüketicinin yapılandırma ayrıntılarını aşağıdaki şekilde güncelleştirin:
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Tüketici kodunu çalıştırın ve Kafka istemcilerinizi kullanarak Kafka etkin Event Hubs’tan işleyin:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Event Hubs Kafka kümenizin olayları varsa, bu olayları artık tüketiciden almaya başlarsınız.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede protokol istemcilerinizi değiştirmenize veya kendi kümelerinizi çalıştırmanıza gerek kalmadan Kafka etkin Event Hubs’a nasıl akış oluşturacağınızı öğrendiniz. Daha fazla bilgi edinmek için aşağıdaki öğreticiyle devam edin:

* [Event Hubs hakkında bilgi edinin](event-hubs-what-is-event-hubs.md)
* [Kafka için Event Hubs hakkında bilgi edinin](event-hubs-for-kafka-ecosystem-overview.md)
* [Kafka için Event Hubs GitHub'ındaki diğer örnekleri keşfedin](https://github.com/Azure/azure-event-hubs-for-kafka)
* [Kafka 'ten Şirket içindeki olayları, bulutta Kafka etkin Event Hubs akışa](event-hubs-kafka-mirror-maker-tutorial.md) almak Için [mirrormaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 'ı kullanın.
* [Apache Flink](event-hubs-kafka-flink-tutorial.md) ya da [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) kullanarak Kafka etkin Event Hubs’a nasıl akış oluşturacağınızı öğrenin.
