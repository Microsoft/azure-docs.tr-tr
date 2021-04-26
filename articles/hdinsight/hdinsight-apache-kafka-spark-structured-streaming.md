---
title: 'Öğretici: Apache Spark akış & Apache Kafka-Azure HDInsight'
description: Apache Spark akışını kullanarak Apache Kafka içine veya dışına veri almayı öğrenin. Bu öğreticide, HDInsight üzerinde Spark 'tan bir Jupyter Notebook kullanarak veri akışı yapabilirsiniz.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,seodec18,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 72c82e8f425b05dde37352225dd7167b089ba48a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868469"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Öğretici: HDInsight üzerinde Apache Spark Yapılandırılmış Akışını Apache Kafka ile kullanma

Bu öğreticide, Azure HDInsight üzerinde [Apache Kafka](./kafka/apache-kafka-introduction.md) ile verileri okumak ve yazmak Için [Apache Spark yapılandırılmış akışın](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) nasıl kullanılacağı gösterilmektedir.

Spark yapılandırılmış akış, Spark SQL üzerinde oluşturulmuş bir akış işleme motorudur. Bu altyapıyı kullanarak, statik veriler üzerinde toplu hesaplamayla aynı şekilde akış hesaplamalarını ifade edebilirsiniz.  

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Küme oluşturmak için bir Azure Resource Manager şablonu kullanma
> * Spark yapısal akışını Kafka ile kullanma

Bu belgedeki adımlarla işiniz bittiğinde, fazla ücretlerden kaçınmak için kümeleri silmeyi unutmayın.

## <a name="prerequisites"></a>Önkoşullar

* bir komut satırı JSON işlemcisi olan JQ.  Bkz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) ..

* HDInsight 'ta Spark ile [Jupyıter not defterlerini](https://jupyter.org/) kullanma hakkında benzerlik. Daha fazla bilgi için bkz. [HDInsight 'ta verileri yükleme ve sorguları çalıştırma Apache Spark](spark/apache-spark-load-data-run-query.md) .

* Scala programlama dilini bilme. Bu öğreticide kullanılan kod, Scala dilinde yazılmıştır.

* Kafka konuları oluşturmayı bilme. Daha fazla bilgi için bkz. [HDInsight hızlı başlangıç belgesi Apache Kafka](kafka/apache-kafka-get-started.md) .

> [!IMPORTANT]  
> Bu belgede yer alan adımlar hem HDInsight üzerinde Spark hem de HDInsight kümesi üzerinde Kafka içeren bir Azure kaynak grubu gerektirir. Bu kümelerin her ikisi de Spark kümesinin Kafka kümesiyle doğrudan iletişim kurmasına olanak tanıyan bir Azure Sanal Ağı içinde bulunur.
>
> Size kolaylık sağlamak için bu belgede, tüm gerekli Azure kaynaklarını oluşturabilecek bir şablonun bağlantıları sağlanır.
>
> Bir sanal ağda HDInsight kullanma hakkında daha fazla bilgi için bkz. [HDInsight için sanal ağ planı](hdinsight-plan-virtual-network-deployment.md) belgesi.

## <a name="structured-streaming-with-apache-kafka"></a>Apache Kafka ile yapılandırılmış akış

Spark Yapılandırılmış Akışı, Spark SQL altyapısı üzerinde derlenen bir akış işleme altyapısıdır. Yapılandırılmış akış kullanırken, akış sorgularını toplu sorguları yazarken aynı şekilde yazabilirsiniz.

Aşağıdaki kod parçacıkları, Kafka’dan okuma ve dosyaya depolama işlemlerini gösterir. Birincisi bir toplu iş işlemi, ikincisiyse bir akış işlemidir:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

Her iki kod parçacığında Kafka’dan veriler okunur ve dosyaya yazılır. Örnekler arasındaki farklar şunlardır:

| Batch | Akış |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Akış işlemi de kullanır `awaitTermination(30000)` ve bu da akışı 30.000 MS 'den sonra sonlandırır.

Kafka ile Yapılandırılmış Akışı kullanmak için projenizin `org.apache.spark : spark-sql-kafka-0-10_2.11` paketinde bir bağımlılığı olmalıdır. Bu paketin sürümü, HDInsight üzerinde Spark sürümüyle eşleşmelidir. Spark 2.2.0 için (HDInsight 3,6 ' de mevcuttur), üzerinde farklı proje türleri için bağımlılık bilgilerini bulabilirsiniz [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar) .

Bu öğreticide kullanılan Jupyter Notebook için aşağıdaki hücre bu paket bağımlılığını yükler:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Kümeleri oluşturma

HDInsight üzerinde Apache Kafka, genel İnternet üzerinden Kafka aracıları için erişim sağlamaz. Kafka kullanan her özellik aynı Azure sanal ağı içinde olmalıdır. Bu öğreticide hem Kafka hem de Spark kümeleri aynı Azure sanal ağı içinde yer alır.

Aşağıdaki diyagramda Spark ile Kafka arasındaki iletişimin nasıl aktığı gösterilmektedir:

:::image type="content" source="./media/hdinsight-apache-kafka-spark-structured-streaming/apache-spark-kafka-vnet.png" alt-text="Bir Azure sanal ağında Spark ve Kafka kümeleri diyagramı" border="false":::

> [!NOTE]  
> Kafka hizmeti, sanal ağ içindeki iletişimle sınırlıdır. SSH ve Ambari gibi küme üzerindeki diğer hizmetlere internet üzerinden erişilebilir. HDInsight üzerinde kullanılabilir olan genel bağlantı noktaları hakkında daha fazla bilgi için bkz. [HDInsight Tarafından Kullanılan Bağlantı Noktaları ve URI’ler](hdinsight-hadoop-port-settings-for-services.md).

Bir Azure Sanal Ağı oluşturmak ve sonra bunun içinde Kafka ve Spark kümeleri oluşturmak için aşağıdaki adımları kullanın:

1. Aşağıdaki düğmeyi kullanarak Azure'da oturum açın ve şablonu Azure portalında açın.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-kafka-spark-structured-streaming/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Azure Resource Manager şablonu konumunda bulunur **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** .

    Bu şablon aşağıdaki kaynakları oluşturur:

   * HDInsight 3.6 kümesi üzerinde bir Kafka.
   * HDInsight 3.6 kümesi üzerinde bir Spark 2.2.0.
   * HDInsight kümeleri içeren bir Azure Sanal Ağı.

     > [!IMPORTANT]  
     > Bu öğreticide kullanılan yapılandırılmış akış not defteri için HDInsight 3.6 üzerinde Spark 2.2.0 gerekir. HDInsight üzerinde Spark’ın daha önceki bir sürümünü kullanıyorsanız, not defterini kullanırken hatalarla karşılaşırsınız.

2. **Özelleştirilmiş şablon** bölümündeki girişleri doldurmak için aşağıdaki bilgileri kullanın:

    | Ayar | Değer |
    | --- | --- |
    | Abonelik | Azure aboneliğiniz |
    | Kaynak grubu | Kaynakları içeren kaynak grubu. |
    | Konum | İçinde kaynakların oluşturulduğu Azure bölgesi. |
    | Spark Kümesi Adı | Spark kümesinin adı. İlk altı karakter Kafka küme adından farklı olmalıdır. |
    | Kafka Kümesi Adı | Kafka kümesinin adı. İlk altı karakter Spark küme adından farklı olmalıdır. |
    | Küme Oturum Açma Kullanıcı Adı | Kümeler için yönetici kullanıcı adı. |
    | Küme Oturum Açma Parolası | Kümeler için yönetici kullanıcı parolası. |
    | SSH Kullanıcı Adı | Kümeler için oluşturulacak SSH kullanıcısı. |
    | SSH Parolası | SSH kullanıcısı için parola. |

    :::image type="content" source="./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png" alt-text="Özelleştirilmiş şablonun ekran görüntüsü":::

3. **Hüküm ve koşulları** okuyun, ardından **yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u seçin.

4. **Satın al**'ı seçin.

> [!NOTE]  
> Kümelerin oluşturulması 20 dakikaya kadar sürebilir.

## <a name="use-spark-structured-streaming"></a>Spark yapılandırılmış akışını kullanma

Bu örnek, HDInsight üzerinde Kafka ile Spark yapısal akışının nasıl kullanılacağını gösterir. Bu, New York City tarafından sunulan TAXI seyahatlerinde verileri kullanır.  Bu not defteri tarafından kullanılan veri kümesi [2016 yeşil TAXI veri yolculuğuna](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb)ait.

1. Konak bilgilerini toplayın. Kafka ZooKeeper ve Broker Konakları bilgilerini almak için aşağıdaki kıvrımlı ve [JQ](https://stedolan.github.io/jq/) komutlarını kullanın. Komutlar bir Windows komut istemi için tasarlanan diğer ortamlar için hafif Çeşitlemeler gerekecektir. `KafkaCluster`Kafka Kümenizin adı ve `KafkaPassword` küme oturum açma parolası ile değiştirin. Ayrıca, `C:\HDI\jq-win64.exe` JQ yüklemenizin gerçek yoluyla değiştirin. Komutları bir Windows komut istemine girin ve sonraki adımlarda kullanmak için çıktıyı kaydedin.

    ```cmd
    REM Enter cluster name in lowercase

    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

1. Bir Web tarayıcısından, `https://CLUSTERNAME.azurehdinsight.net/jupyter` , `CLUSTERNAME` Kümenizin adı olan ' a gidin. Sorulduğunda, kümeyi oluştururken kullanılan küme kullanıcı adı (yönetici) ve parolasını girin.

1. **Yeni > Spark** ' ı seçerek bir not defteri oluşturun.

1. Spark akışında mikro işleme vardır. Bu, verilerin toplu işler ve yürüticiler veri yığınlarıyla birlikte çalıştırılmasıdır. Yürütücü, toplu işi işlemek için gereken süreden daha düşük bir süre zaman aşımına uğramışsa, yürütmeleri sürekli olarak eklenir ve kaldırılır. Yürütme boşta kalma zaman aşımı, toplu iş süresinden fazlaysa, yürütücü hiçbir zaman kaldırılmaz. **Bu nedenle, akış uygulamalarını çalıştırırken spark. dynamicAllocation. Enabled ayarını false olarak ayarlayarak dinamik ayırmayı devre dışı bırakmanızı öneririz.**

    Aşağıdaki bilgileri bir not defteri hücresine girerek Not defteri tarafından kullanılan paketleri yükleyin. **CTRL + ENTER** kullanarak komutu çalıştırın.

    ```configuration
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11",
            "spark.dynamicAllocation.enabled": false
        }
    }
    ```

1. Kafka konusunu oluşturun. `YOUR_ZOOKEEPER_HOSTS`İlk adımda ayıklanan Zookeeper ana bilgisayar bilgileriyle değiştirerek aşağıdaki komutu düzenleyin. Konuyu oluşturmak için Jupyter Notebook düzenlenen komutu girin `tripdata` .

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

1. Vergi seyahatlerinde veri alın. Yeni York şehrinde vergi seyahatleri üzerine veri yüklemek için sonraki hücreye komutu girin. Veriler bir veri çerçevesine yüklenir ve ardından dataframe hücre çıktısı olarak görüntülenir.

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString

    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)

    // Display the dataframe containing trip data
    taxiDF.show()
    ```

1. Kafka Broker ana bilgisayar bilgilerini ayarlayın. `YOUR_KAFKA_BROKER_HOSTS`Adım 1 ' de ayıkladığınız aracı ana bilgisayar bilgileriyle değiştirin.  Sonraki Jupyter Notebook hücresinde düzenlenen komutunu girin.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"

    println("Finished setting Kafka broker and topic configuration.")
    ```

1. Verileri Kafka 'ye gönderin. Aşağıdaki komutta, `vendorid` alanı Kafka iletisi için anahtar değer olarak kullanılır. Anahtar, verileri Bölümlemede Kafka tarafından kullanılır. Tüm alanlar, Kafka iletisinde JSON dizesi değeri olarak depolanır. Bir Batch sorgusu kullanarak verileri Kafka 'e kaydetmek için jupi 'de aşağıdaki komutu girin.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

1. Bir şema bildirin. Aşağıdaki komut, Kafka öğesinden JSON verileri okurken bir şemanın nasıl kullanılacağını göstermektedir. Sonraki Jupyıter hücresindeki komutu girin.

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)

    println("Schema declared")
    ```

1. Verileri seçin ve akışı başlatın. Aşağıdaki komut, Batch sorgusu kullanarak Kafka 'tan nasıl veri alınacağını gösterir. Daha sonra Spark kümesinde, bu sonuçları bir. Bu örnekte, `select` Kafka adresinden iletiyi (değer alanı) alır ve şemayı buna uygular. Daha sonra bu veriler, Parquet biçiminde bir. Sonraki Jupyıter hücresindeki komutu girin.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()

    println("Wrote data to file")
    ```

1. Sonraki jupi hücresine komutu girerek dosyaların oluşturulduğunu doğrulayabilirsiniz. Dizindeki dosyaları listeler `/example/batchtripdata` .

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

1. Önceki örnekte bir toplu iş sorgusu kullanılırken aşağıdaki komut, akış sorgusu kullanarak aynı şeyi nasıl yapabileceğinizi gösterir. Sonraki Jupyıter hücresindeki komutu girin.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

1. Dosyaların akış sorgusu tarafından yazıldığını doğrulamak için aşağıdaki hücreyi çalıştırın.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici ile oluşturulan kaynakları temizlemek için kaynak grubunu silebilirsiniz. Kaynak grubu silindiğinde, ilişkili HDInsight kümesi de silinir. Ve kaynak grubuyla ilişkili diğer kaynaklar.

Azure portalını kullanarak kaynak grubunu kaldırmak için:

1. [Azure Portal](https://portal.azure.com/), sol taraftaki menüyü genişleterek hizmet menüsünü açın ve kaynak __grupları__ ' nı seçerek kaynak gruplarınızın listesini görüntüleyin.
2. Silinecek kaynak grubunu bulun ve sonra listenin sağ tarafındaki __Daha fazla__ düğmesine (...) sağ tıklayın.
3. __Kaynak grubunu sil__'i seçip onaylayın.

> [!WARNING]  
> HDInsight kümesi faturalandırması küme oluşturulduğunda başlar ve küme silindiğinde sona erer. Fatura dakikalara eşit olarak dağıtıldığından, kullanılmayan kümelerinizi mutlaka silmelisiniz.
>
> HDInsight üzerinde Kafka kümesinin silinmesi Kafka’da depolanmış tüm verileri siler.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Apache Spark yapısal akışı kullanmayı öğrendiniz. HDInsight üzerinde Apache Kafka verileri yazmak ve okumak için. Kafka ile Apache Storm nasıl kullanacağınızı öğrenmek için aşağıdaki bağlantıyı kullanın.

> [!div class="nextstepaction"]
> [Apache Kafka ile Apache Storm kullanma](hdinsight-apache-storm-with-kafka.md)
