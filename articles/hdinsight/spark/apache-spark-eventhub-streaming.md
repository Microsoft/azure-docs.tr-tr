---
title: 'Öğretici: Azure Event Hubs Data & Apache Spark-HDInsight'
description: Öğretici-Azure HDInsight 'ta Apache Spark Azure Event Hubs 'a bağlama ve akış verilerini işleme.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/17/2019
ms.openlocfilehash: 0b24d1b0215564fb9f6063d4a2d091bb7a9a1c3e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494636"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Öğretici: Azure Event Hubs ve HDInsight 'ta Apache Spark ile Işlem Işlemlerini Işleme

Bu öğreticide, bir Azure Olay Hub 'ına fazla kayıt göndermek için bir [Apache Spark](https://spark.apache.org/) akış uygulaması oluşturmayı ve Olay Hub 'ından farklı bir uygulama oluşturmayı öğreneceksiniz. Spark akışının ayrıntılı bir açıklaması için bkz. [Apache Spark akışa genel bakış](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight, Azure 'daki bir Spark kümesine aynı akış özelliklerini getirir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Azure Olay Hub 'ına ileti gönderme
> * Azure Olay Hub 'ından iletileri okuma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Spark kümesi. Bkz. [Apache Spark kümesi oluşturma](./apache-spark-jupyter-spark-sql-use-portal.md).

* HDInsight üzerinde Spark ile Jupyter Notebook kullanma bilgisi. Daha fazla bilgi için bkz. [HDInsight üzerinde verileri yükleme ve sorguları çalıştırma Apache Spark](./apache-spark-load-data-run-query.md).

* Twitter [hesabı](https://twitter.com/i/flow/signup) ve Twitter ile benzerlik.

## <a name="create-a-twitter-application"></a>Twitter uygulaması oluşturma

Tweet’lerin akışını almak için Twitter’da bir uygulama oluşturursunuz. Twitter uygulaması oluşturma yönergelerini izleyin ve bu öğreticiyi tamamlayabilmeniz için ihtiyacınız olan değerleri yazın.

1. [Twitter uygulama yönetimine](https://apps.twitter.com/)gidin.

1. **Uygulama oluştur**' u seçin.

1. Aşağıdaki gerekli değerleri sağlayın:

    |Özellik |Değer |
    |---|---|
    |Uygulama adı|Uygulama adını belirtin. Bu öğretici için kullanılan değer **HDISparkStreamApp0423**. Bu ad benzersiz bir ad olmalıdır.|
    |Uygulama açıklaması|Uygulamanın kısa bir açıklamasını sağlayın. Bu öğretici için kullanılan değer **basit bir HDInsight Spark akış uygulamasıdır**.|
    |Web sitesi URL 'SI|Uygulamanın Web sitesini belirtin. Geçerli bir Web sitesi olması gerekmez.  Bu öğretici için kullanılan değer `http://www.contoso.com`.|
    |Bu uygulamanın nasıl kullanılacağını bize bildirin|Yalnızca test amaçlıdır. Azure Olay Hub 'ına yer aktarmak için Apache Spark bir akış uygulaması oluşturma.|

1. **Oluştur**'u seçin.

1. **Geliştirici koşullarımızı gözden geçirin** açılır penceresinde **Oluştur**' u seçin.

1. **Anahtarlar ve belirteçler** sekmesini seçin.

1. Erişim **belirteci & erişim belirteci parolası**altında **Oluştur**' u seçin.

1. Daha sonra kullanmak üzere sayfada görüntülenen aşağıdaki dört değeri yazın:

    - **Tüketici anahtarı (API anahtarı)**
    - **Tüketici gizli dizisi (API gizli anahtarı)**
    - **Erişim belirteci**
    - **Erişim belirteci gizli dizisi**

## <a name="create-an-azure-event-hubs-namespace"></a>Azure Event Hubs ad alanı oluşturma

Bu olay hub 'ını kullanarak, doldurulabilir bir mağaza yapabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol menüden, **Event Hubs** > nesnelerin **Interneti** > **tüm hizmetlere** gidin.  

    ![Spark akış örneği için Olay Hub 'ı oluşturma](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Spark akış örneği için Olay Hub 'ı oluşturma")

1. **+ Ekle** öğesini seçin.

1. Yeni Event Hubs ad alanı için aşağıdaki değerleri girin:

    |Özellik |Değer |
    |---|---|
    |Ad|Olay Hub 'ı için bir ad girin.  Bu öğretici için kullanılan değer **myeventhubns20180403**.|
    |Fiyatlandırma katmanı|**Standart**' ı seçin.|
    |Abonelik|Uygun aboneliğinizi seçin.|
    |Kaynak grubu|Açılan listeden var olan bir kaynak grubunu seçin veya yeni bir kaynak grubu oluşturmak için **Yeni oluştur** ' u seçin.|
    |Konum|Gecikme ve maliyetleri azaltmak için HDInsight 'ta Apache Spark kümeniz ile aynı **konumu** seçin.|
    |Otomatik Şişir etkinleştir (Isteğe bağlı) |Otomatik şişir, trafiğiniz kendisine atanan üretilen Iş birimi kapasitesini aştığında Event Hubs ad alanına atanan üretilen Iş birimi sayısını otomatik olarak ölçeklendirir.  |
    |Maksimum üretilen Iş birimlerini otomatik Şişir (Isteğe bağlı)|Bu kaydırıcı yalnızca **Otomatik Şişir etkinleştir**' i denetlemeli görünür.  |

    ![Spark akış örneği için bir olay hub 'ı adı sağlayın](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Spark akış örneği için bir olay hub 'ı adı sağlayın")

1. Ad alanını oluşturmak için **Oluştur** ' u seçin.  Dağıtım birkaç dakika içinde tamamlanır.

## <a name="create-an-azure-event-hub"></a>Azure Olay Hub 'ı oluşturma

Event Hubs ad alanı dağıtıldıktan sonra bir olay hub 'ı oluşturun.  Portaldan:

1. Sol menüden, **Event Hubs** > nesnelerin **Interneti** > **tüm hizmetlere** gidin.

1. Listeden Event Hubs ad alanınızı seçin.

1. **Event Hubs ad alanı** sayfasında **+ Event hub**' ı seçin.  

1. **Olay Hub 'ı oluştur** sayfasında aşağıdaki değerleri girin:

    - **Ad**: Olay Hub 'ınız için bir ad verin.

    - **Bölüm sayısı**: 10.  

    - **İleti bekletme**: 1.

      ![Spark akış örneği için Olay Hub 'ı ayrıntılarını sağlama](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Spark akış örneği için Olay Hub 'ı ayrıntılarını sağlama")

1. **Oluştur**'u seçin.  Dağıtım birkaç saniye içinde tamamlanmalı ve Event Hubs ad alanı sayfasına döndürülürsünüz.

1. **Ayarlar**altında, **paylaşılan erişim ilkeleri**' ni seçin.

1. **RootManageSharedAccessKey**öğesini seçin.

     ![Spark akış örneği için Olay Hub 'ı ilkelerini ayarlama](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Spark akış örneği için Olay Hub 'ı ilkelerini ayarlama")

1. **Birincil anahtar** ve **bağlantı dizesi-birincil anahtar** değerlerini daha sonra öğreticide kullanmak üzere kaydedin.

     ![Spark akış örneği için Olay Hub 'ı ilke anahtarlarını görüntüleme](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Spark akış örneği için Olay Hub 'ı ilke anahtarlarını görüntüleme")

## <a name="send-tweets-to-the-event-hub"></a>Olay Hub 'ına fazla doldurulabilir gönder

1. `CLUSTERNAME` Apache Spark Kümenizin adı olduğu `https://CLUSTERNAME.azurehdinsight.net/jupyter` gidin. Bir Jupyter Not defteri oluşturun ve bunu **Sendtweetstoeventhub**olarak adlandırın.

1. Dış Apache Maven kitaplıklarını eklemek için aşağıdaki kodu çalıştırın:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

1. `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`, `<ACCESS TOKEN>`ve `<TOKEN SECRET>` değerlerini uygun değerlerle değiştirerek aşağıdaki kodu düzenleyin. Olay Hub 'ınıza ara kod göndermek için düzenlenmiş kodu çalıştırın:

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._

    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build

    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }

    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"

    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)

    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

1. Olay Hub 'ını Azure portal açın.  **Genel bakışta**, Olay Hub 'ına gönderilen iletileri gösteren bazı grafikler görürsünüz.

## <a name="read-tweets-from-the-event-hub"></a>Olay Hub 'ından daha fazla doldurulabilir

Başka bir Jupyter Not defteri oluşturun ve bunu **ReadTweetsFromEventHub**olarak adlandırın.

1. Bir dış Apache Maven kitaplığı eklemek için aşağıdaki kodu çalıştırın:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. `<Event hub name>`değiştirerek aşağıdaki kodu düzenleyin ve uygun değerlerle `<Event hub namespace connection string>`. Olay Hub 'ınızdan çilleri okumak için düzenlenmiş kodu çalıştırın:

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build

    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

HDInsight ile Verileriniz Azure Storage 'da depolanır veya Azure Data Lake Storage, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmasa bile bir HDInsight kümesi için de ücretlendirilirsiniz. Sonraki öğreticide hemen çalışmayı planlıyorsanız kümeyi tutmak, aksi takdirde devam etmek ve kümeyi silmek isteyebilirsiniz.

Azure portalında kümeyi açıp **Sil**’i seçin.

![HDInsight Azure portal Kümeyi Sil](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight kümesini Sil")

Kaynak grubu adını seçerek de kaynak grubu sayfasını açabilir ve sonra **Kaynak grubunu sil**’i seçebilirsiniz. Kaynak grubunu silerek hem HDInsight Spark kümesini hem de varsayılan depolama hesabını silersiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure Olay Hub 'ına kayıt eklemek için bir Apache Spark akış uygulaması oluşturma ve Olay Hub 'ından farklı bir uygulama oluşturma hakkında bilgi edindiniz.  Bir makine öğrenimi uygulaması oluşturabileceğiniz makaleyi görmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Machine Learning uygulaması oluşturma](./apache-spark-ipython-notebook-machine-learning.md)
