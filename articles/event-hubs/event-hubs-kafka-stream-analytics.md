---
title: 'Öğretici: Stream Analytics kullanarak Apache Kafka olaylarını Işleme-Azure Event Hubs'
description: "Öğretici: Bu makalede Azure Stream Analytics kullanılarak Olay Hub 'ları aracılığıyla alınan Kafka olaylarının nasıl işlenmesi gösterilmektedir."
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 7801b3252ab13df1f92e7aa5e0eba071195cb76c
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720621"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Öğretici: Stream Analytics kullanarak Event Hubs olaylar için Işlem Apache Kafka 
Bu makalede, verilerin Kafka özellikli Event Hubs akışını ve Azure Stream Analytics nasıl işleyeceğini gösterir. Aşağıdaki adımlarda size yol gösterir: 

1. Kafka etkin bir Event Hubs ad alanı oluşturun.
2. Olay Hub 'ına ileti gönderen bir Kafka istemcisi oluşturun.
3. Olay Hub 'ından Azure Blob depolama alanına veri kopyalayan bir Stream Analytics iş oluşturun. 

Bir olay hub 'ı tarafından kullanıma sunulan Kafka uç noktasını kullandığınızda protokol istemcilerinizi değiştirmeniz veya kendi kümelerinizi çalıştırmanız gerekmez. Azure Event Hubs [Apache Kafka sürüm 1.0](https://kafka.apache.org/10/documentation.html)’ı destekler. ve üzeri. 


## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıcı tamamlamak için aşağıdaki önkoşulların karşılandığından emin olun:

* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* Bir Maven ikili arşivini [indirin](https://maven.apache.org/download.cgi) ve [yükleyin](https://maven.apache.org/install.html).
* [Git](https://www.git-scm.com/)
* Bir **Azure depolama hesabı**. Yoksa, devam etmeden önce [bir tane oluşturun](../storage/common/storage-quickstart-create-account.md) . Bu izlenecek yolda Stream Analytics işi, çıktı verilerini bir Azure Blob depolama alanında depolar. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka etkin Event Hubs ad alanı oluşturma

1. [Azure Portal](https://portal.azure.com)oturum açın ve ekranın sol üst kısmında bulunan **kaynak oluştur ' a** tıklayın.
2. **Event Hubs** arayın ve burada gösterilen seçenekleri seçin:
    
    ![Portalda Event Hubs arama](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. **Event Hubs** sayfasında **Oluştur**' u seçin.
4. **Ad alanı oluştur** sayfasında, aşağıdaki işlemleri yapın: 
    1. Ad alanı için benzersiz bir **ad** sağlayın. 
    2. Bir **fiyatlandırma katmanı**seçin. 
    3. **Kafka etkinleştir**' i seçin. Bu adım **önemli** bir adımdır. 
    4. Olay Hub 'ı ad alanının oluşturulmasını istediğiniz **aboneliğinizi** seçin. 
    5. Yeni bir **kaynak grubu** oluşturun veya var olan bir kaynak grubunu seçin. 
    6. Bir **konum**seçin. 
    7. **Oluştur**'a tıklayın.
    
        ![Ad alanı oluşturma](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. **Bildirim iletisinde**, **kaynak grubu adını**seçin. 

    ![Ad alanı oluşturma](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Kaynak grubunda **Olay Hub 'ı ad alanını** seçin. 
2. Ad alanı oluşturulduktan sonra **Ayarlar**altında **paylaşılan erişim ilkeleri** ' ni seçin.

    ![Paylaşılan erişim ilkeleri’ne tıklayın.](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. Varsayılan **RootManageSharedAccessKey** ilkesini seçebilir veya yeni bir ilke ekleyebilirsiniz. İlke adına tıklayın ve **bağlantı dizesini**kopyalayın. Kafka istemcisini yapılandırmak için bağlantı dizesini kullanın. 
    
    ![İlke seçme](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

Artık Kafka protokolünü kullanan uygulamalarınızdaki olayların akışını Event Hubs'a yapabilirsiniz.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Kafka ile ileti gönderme Event Hubs

1. [Azure Event Hubs for Kafka Repository](https://github.com/Azure/azure-event-hubs-for-kafka) 'yi makinenize kopyalayın.
2. Şu klasöre gidin: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. `src/main/resources/producer.config`' de üreticinin yapılandırma ayrıntılarını güncelleştirin. **Olay Hub 'ı ad alanı**için **adı** ve **bağlantı dizesini** belirtin. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`gidin ve **Testdatareporter. Java** dosyasını seçtiğiniz bir düzenleyicide açın. 
6. Aşağıdaki kod satırını açıklama:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Açıklamalı kodun yerine aşağıdaki kod satırını ekleyin: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Bu kod, olay verilerini **JSON** biçiminde gönderir. Stream Analytics bir iş için giriş yapılandırdığınızda, JSON öğesini giriş verilerinin biçimi olarak belirtirsiniz. 
7. **Producer** ve Stream 'i Kafka özellikli Event Hubs olarak çalıştırın. Bir Windows makinesinde, **Node. js komut istemi**kullanırken, bu komutları çalıştırmadan önce `azure-event-hubs-for-kafka/quickstart/java/producer` klasörüne geçin. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Olay Hub 'ının verileri aldığını doğrulama

1. **Varlıklar**altında **Event Hubs** seçin. **Test**adlı bir olay hub 'ı görtığınızdan emin olun. 

    ![Olay Hub 'ı-test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Olay Hub 'ına gelen iletileri görtığınızdan emin olun. 

    ![Olay Hub 'ı-iletiler](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Stream Analytics işini kullanarak olay verilerini işleme
Bu bölümde bir Azure Stream Analytics işi oluşturursunuz. Kafka istemcisi olayları Olay Hub 'ına gönderir. Olay verilerini giriş olarak alan ve bunu bir Azure Blob depolama alanına çıkaran bir Stream Analytics işi oluşturursunuz. **Azure depolama hesabınız**yoksa [bir tane oluşturun](../storage/common/storage-quickstart-create-account.md).

Stream Analytics işteki sorgu, herhangi bir analiz yapmadan verileri geçirir. Giriş verilerini, farklı bir biçimde veya elde edilen öngörülerle çıkış verileri oluşturacak şekilde dönüştüren bir sorgu oluşturabilirsiniz.  

### <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma 

1. Azure portal **+ kaynak oluştur** ' u seçin [](https://portal.azure.com).
2. **Azure Marketi** menüsünde **analiz** ' i seçin ve **Stream Analytics iş**' ı seçin. 
3. **Yeni Stream Analytics** sayfasında aşağıdaki işlemleri yapın: 
    1. İş için bir **ad** girin. 
    2. **Aboneliğinizi**seçin.
    3. **Kaynak grubu** Için **Yeni oluştur** ' u seçin ve adı girin. **Var olan bir** kaynak grubunu da kullanabilirsiniz. 
    4. İş için bir **konum** seçin.
    5. İşi oluşturmak için **Oluştur** ' u seçin. 

        ![Yeni Stream Analytics işi](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>İş girişi yapılandırma

1. Bildirim iletisinde, **Stream Analytics işi** sayfasını görmek Için **Kaynağa Git** ' i seçin. 
2. Sol menüdeki **Iş topolojisi** bölümünde **girişler** ' i seçin.
3. **Akış girişi Ekle**' yi ve ardından **Olay Hub**'ı ' nı seçin. 

    ![Giriş olarak olay hub 'ı ekleme](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. **Olay Hub 'ı giriş** yapılandırması sayfasında, aşağıdaki işlemleri yapın: 

    1. Giriş için bir **diğer ad** belirtin. 
    2. Azure **aboneliğinizi** seçin.
    3. Daha önce oluşturduğunuz **Olay Hub 'ı ad alanını** seçin. 
    4. **Olay Hub 'ı**için **Test** ' i seçin. 
    5. **Kaydet**’i seçin. 

        ![Olay Hub 'ı giriş yapılandırması](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>İş çıkışını yapılandırma 

1. Menüdeki **Iş topolojisi** bölümünde yer alan **çıktılar** ' i seçin. 
2. Araç çubuğunda **+ Ekle** ' yi seçin ve **BLOB depolama** ' yı seçin.
3. BLOB depolama çıkış ayarları sayfasında, aşağıdaki işlemleri yapın: 
    1. Çıkış için bir **diğer ad** belirtin. 
    2. Azure **aboneliğinizi** seçin. 
    3. **Azure depolama hesabınızı**seçin. 
    4. Stream Analytics sorgusundan çıkış verilerini depolayan **kapsayıcı için bir ad** girin.
    5. **Kaydet**’i seçin.

        ![BLOB depolama çıkış yapılandırması](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Sorgu tanımlama
Gelen bir veri akışını okumak için bir Stream Analytics işi ayarladıktan sonraki adım, verileri gerçek zamanlı olarak analiz eden bir dönüştürme oluşturmaktır. Dönüştürme sorgusunu [Stream Analytics sorgu dilini](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) kullanarak tanımlarsınız. Bu kılavuzda, herhangi bir dönüşüm yapmadan verilerden geçen bir sorgu tanımlarsınız.

1. **Sorgu**seçin.
2. Sorgu penceresinde, `[YourOutputAlias]` daha önce oluşturduğunuz çıktı diğer adıyla değiştirin.
3. `[YourInputAlias]`, daha önce oluşturduğunuz giriş diğer adıyla değiştirin. 
4. Araç çubuğunda **Kaydet**’i seçin. 

    ![Sorgu](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Stream Analytics işini çalıştırma

1. Sol menüdeki **Genel Bakış ' ı** seçin. 
2. **Başlat**' ı seçin. 

    ![Başlat menüsü](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. **Işi Başlat** sayfasında **Başlat**' ı seçin. 

    ![İşi Başlat sayfası](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. İşin durumunun **çalışmaya** **başlamasını** bekleyin. 

    ![İş durumu-çalışıyor](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Senaryoyu test etme
1. Olayları Olay Hub 'ına göndermek için **Kafka üreticisi** 'ni yeniden çalıştırın. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. **Azure Blob depolamada** **çıktı verilerinin** oluşturulduğunu görtığınızdan emin olun. Kapsayıcıda aşağıdaki örnek satırlara benzeyen 100 satırlık bir JSON dosyası görürsünüz: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Azure Stream Analytics işi, Olay Hub 'ından giriş verileri aldı ve bu senaryodaki Azure Blob depolama alanında depolandı. 



## <a name="next-steps"></a>Sonraki adımlar
Bu makalede protokol istemcilerinizi değiştirmenize veya kendi kümelerinizi çalıştırmanıza gerek kalmadan Kafka etkin Event Hubs’a nasıl akış oluşturacağınızı öğrendiniz. Event Hubs ve Kafka için Event Hubs hakkında daha fazla bilgi edinmek için aşağıdaki konuya bakın:  

- [Event Hubs hakkında bilgi edinin](event-hubs-what-is-event-hubs.md)
- [Apache Kafka için Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka özellikli Event Hubs oluşturma](event-hubs-create-kafka-enabled.md)
- [Kafka uygulamalarınızdan Event Hubs'a akış yapma](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka özellikli bir olay hub'ında Kafka aracısını yansıtma](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark'ı Kafka özellikli bir olay hub'ına bağlama](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink'i Kafka özellikli bir olay hub'ına bağlama](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect'i Kafka özellikli olay hub'ıyla tümleştirme](event-hubs-kafka-connect-tutorial.md)
- [Akka Streams’i Kafka özellikli olay hub'ına bağlama](event-hubs-kafka-akka-streams-tutorial.md)
- [GitHub'ımızdaki örnekleri inceleme](https://github.com/Azure/azure-event-hubs-for-kafka) 
