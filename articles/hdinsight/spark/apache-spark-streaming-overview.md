---
title: Azure HDInsight 'ta Spark akışı
description: HDInsight Spark kümelerinde Apache Spark akışlı uygulamalar kullanma.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: e8a9f771827b870f493d6b0d7590feee7fc52b20
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870254"
---
# <a name="overview-of-apache-spark-streaming"></a>Apache Spark akışa genel bakış

[Apache Spark](https://spark.apache.org/) Akış, HDInsight Spark kümelerinde veri akışı işleme sağlar. Herhangi bir giriş olayının, bir düğüm hatası oluşması durumunda bile tam olarak bir kez işlenmesini güvence altına almak. Spark akışı, Azure Event Hubs dahil olmak üzere çok çeşitli kaynaklardan giriş verilerini alan uzun süredir çalışan bir işdir. Ayrıca: Azure IoT Hub, Apache Kafka, Apache flome, Twitter, `ZeroMQ` Ham TCP yuvaları veya izleme Apache Hadoop YARN dosya sistemleri. Yalnızca olay odaklı bir işlemden farklı olarak, bir Spark akışı, verileri zaman Windows 'a girdi olarak işler. Örneğin, 2 saniyelik bir dilim gibi her bir veri kümesini eşleme, azaltma, JOIN ve ayıkla işlemlerini kullanarak dönüştürür. Spark akışı daha sonra dönüştürülen verileri filesystems, veritabanları, panolar ve konsola yazar.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png" alt-text="HDInsight ve Spark streaming ile akış Işleme" border="false":::

Spark akış uygulamaları, `micro-batch` Bu toplu işi işlenmek üzere göndermeden önce olayların her birini toplamasını beklemeniz gerekir. Buna karşılık, olay temelli bir uygulama her olayı hemen işler. Spark akış gecikmesi genellikle birkaç saniye altında. Mikro Batch yaklaşımının avantajları daha verimli veri işleme ve daha basit toplu hesaplamalardır.

## <a name="introducing-the-dstream"></a>DStream 'e giriş

Spark akışı, DStream adlı ayrılmış bir *akış* kullanılarak gelen verilerin sürekli akışını temsil eder. Bir DStream, Event Hubs veya Kafka gibi giriş kaynaklarından oluşturulabilir. Ya da başka bir DStream 'e dönüşümler uygulayarak.

DStream, Ham olay verilerinin üzerine bir soyutlama katmanı sağlar.

Tek bir olayla başlayın ve bağlı bir termostat 'dan bir sıcaklık okuyun. Bu olay Spark akış uygulamanıza ulaştığında, olay, birden çok düğümde çoğaltılan güvenilir bir şekilde depolanır. Bu hata toleransı, tek bir düğümün başarısızlığının olaylarınızın kaybına neden olmamasını sağlar. Spark Core, verileri kümedeki birden çok düğüme dağıtan bir veri yapısı kullanır. Her düğüm, en iyi performans için genellikle kendi verilerini bellek içinde tutar. Bu veri yapısına dayanıklı bir *Dağıtılmış veri kümesi* (RDD) adı verilir.

Her RDD, *toplu iş aralığı* olarak adlandırılan Kullanıcı tanımlı bir zaman diliminde toplanan olayları temsil eder. Her toplu iş aralığı geçtiğinde, bu aralıktaki tüm verileri içeren yeni bir RDD oluşturulur. Sürekli RDDs kümesi bir DStream 'e toplanır. Örneğin, toplu iş aralığı bir ikinci uzunsa DStream, saniye içinde alınan tüm verileri içeren bir RDD 'yi içeren bir toplu işlem yayar. DStream işlenirken, sıcaklık olayı Bu toplu işlemlerden birinde görünür. Spark akış uygulaması olayları içeren toplu işleri işler ve sonunda her bir RDD 'de depolanan veriler üzerinde işlem yapar.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png" alt-text="Sıcaklık olayları ile örnek DStream" border="false":::

## <a name="structure-of-a-spark-streaming-application"></a>Spark akış uygulamasının yapısı

Spark akış uygulaması, alma kaynaklarından veri alan uzun süredir çalışan bir uygulamadır. Verileri işlemek için dönüşümler uygular ve sonra verileri bir veya daha fazla hedefe iter. Spark akış uygulamasının yapısının statik bir bölümü ve dinamik bir bölümü vardır. Statik bölüm, verilerin nereden geldiğini, veriler üzerinde ne kadar işlem yapılacağını tanımlar. Sonuçların nereye gitmesi gerekir. Dinamik bölüm uygulamayı süresiz olarak çalıştırıyor ve durdurma sinyali bekliyor.

Örneğin, aşağıdaki basit uygulama bir TCP yuvası üzerinde bir metin satırı alır ve her sözcüğün kaç kez göründüğünü sayar.

### <a name="define-the-application"></a>Uygulamayı tanımlama

Uygulama mantığı tanımında dört adım vardır:

1. Streammingcontext oluşturun.
2. Streammingcontext öğesinden bir DStream oluşturun.
3. DStream 'e dönüşümler uygulayın.
4. Sonuçların çıkışını yapın.

Bu tanım statiktir ve uygulamayı çalıştırana kadar hiçbir veri işlenmeyecektir.

#### <a name="create-a-streamingcontext"></a>Streammingcontext oluşturma

Kümenizi işaret eden mini olmayan bağlamdan bir StreamingContext oluşturun. Bir StreamingContext oluştururken, toplu işlemin boyutunu saniye cinsinden belirtirsiniz, örneğin:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>DStream oluşturma

StreamingContext örneği ile giriş kaynağınız için bir giriş DStream oluşturun. Bu durumda, uygulama varsayılan bağlı depolama alanındaki yeni dosyaların görünümünü izliyor.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Dönüştürmeleri uygulama

DStream 'e dönüşümler uygulayarak işlemi uygulayabilirsiniz. Bu uygulama, dosyadaki bir seferde bir metin satırı alır, her satırı sözcüklere böler. Ve sonra, her sözcüğün kaç kez göründüğünü saymak için bir eşlem azaltma örüntüsünün kullanımını kullanır.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Çıkış sonuçları

Çıkış işlemleri uygulayarak dönüştürme sonuçlarını hedef sistemlere gönderin. Bu durumda, hesaplama aracılığıyla her bir çalıştırmanın sonucu konsol çıktısında yazdırılır.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Uygulamayı çalıştırma

Akış uygulamasını başlatın ve sonlandırma sinyali alınana kadar çalıştırın.

```
ssc.start()
ssc.awaitTermination()
```

Spark Stream API 'SI ile ilgili ayrıntılar için bkz. [akış programlama kılavuzu Apache Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Aşağıdaki örnek uygulama kendi içinde bulunur, bu nedenle onu bir [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md)içinde çalıştırabilirsiniz. Bu örnek, DummySource sınıfında her beş saniyede bir sayacın değerini ve geçerli saati milisaniye olarak çıkaran bir sahte veri kaynağı oluşturur. Yeni bir StreamingContext nesnesi 30 saniyelik bir toplu iş aralığına sahiptir. Toplu iş her oluşturulduğunda, akış uygulaması üretilen RDD 'yi inceler. Ardından RDD 'yi Spark veri çerçevesine dönüştürür ve DataFrame üzerinde geçici bir tablo oluşturur.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process RDDs in the batch
stream.foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Yukarıdaki uygulamayı başlattıktan yaklaşık 30 saniye bekleyin.  Daha sonra, örneğin bu SQL sorgusunu kullanarak, toplu işte bulunan geçerli değer kümesini görmek için veri çerçevesini düzenli aralıklarla sorgulayabilirsiniz.

```sql
%%sql
SELECT * FROM demo_numbers
```

Elde edilen çıktı aşağıdaki çıktı gibi görünür:

| değer | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

DummySource her 5 saniyede bir değer oluşturduğundan ve uygulama her 30 saniyede bir toplu iş yaydığı için altı değer vardır.

## <a name="sliding-windows"></a>Kayan pencereler

DStream 'de belirli bir süre boyunca toplu hesaplamalar yapmak için örneğin, son iki saniye boyunca ortalama bir sıcaklık almak için `sliding window` Spark akışına dahil edilen işlemleri kullanın. Kayan pencerenin süresi (pencere uzunluğu) ve pencere içeriğinin değerlendirildiği zaman aralığı (Slayt aralığı) vardır.

Kayan pencereler çakışabilir, örneğin, iki saniyelik uzunlukla bir pencere tanımlayabilir ve bu slaytlar her bir saniyede bir yer alabilir. Bu eylem, bir toplama hesaplaması yaptığınız her seferinde, pencerenin önceki pencerenin son bir saniyesden verileri içermeyeceği anlamına gelir. Ve sonraki bir ikinci birindeki tüm yeni verileri.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png" alt-text="Sıcaklık olayları ile örnek Ilk pencere" border="false":::

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png" alt-text="Kayan sonra sıcaklık olayları içeren örnek pencere" border="false":::

Aşağıdaki örnek, toplu işlemleri tek dakikalık bir ve bir dakikalık slaytla bir pencereye toplamak için DummySource kullanan kodu günceller.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

İlk dakika sonra, pencerede toplanan iki toplu işlem için altı giriş olan 12 girdi vardır.

| değer | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

Spark akış API 'SI ile birlikte bulunan kayan pencere işlevleri, pencere, countByWindow, reduceByWindow ve countByValueAndWindow. Bu işlevlerle ilgili ayrıntılar için bkz. [DStreams üzerindeki dönüşümler](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Denetim noktası oluşturma

Spark akışı, dayanıklılık ve hataya dayanıklılık sağlamak için, akış işlemenin düğüm hatası durumunda bile kesintisiz bir şekilde devam edebilmesinin sağlaması için denetim noktası kullanır. Spark, dayanıklı depolama (Azure Storage veya Data Lake Storage) için denetim noktaları oluşturur. Bu denetim noktaları, yapılandırma gibi akış uygulaması meta verilerini ve uygulama tarafından tanımlanan işlemleri depolar. Ayrıca, kuyruğa alınmış ancak henüz işlenmemiş olan tüm işler. Bazen, denetim noktaları, Spark tarafından yönetilen RDDs 'de mevcut olan verilerin durumunu daha hızlı bir şekilde yeniden oluşturmak için, verileri RDDs 'ye kaydetmeyi de kapsar.

## <a name="deploying-spark-streaming-applications"></a>Spark akış uygulamalarını dağıtma

Genellikle bir JAR dosyasına yerel olarak Spark akış uygulaması oluşturursunuz. Ardından JAR dosyasını varsayılan bağlı depolamaya kopyalayarak HDInsight 'ta Spark 'a dağıtın. Bir POST işlemi kullanarak, uygulamanızı kümenizde bulunan LIVY REST API 'Leriyle başlatabilirsiniz. GÖNDERI gövdesi, kasalar yolunu sağlayan bir JSON belgesi içerir. Ve ana yöntemi, akış uygulamasını tanımlayan ve çalıştıran sınıfın adı ve isteğe bağlı olarak işin kaynak gereksinimleri (yürütme sayısı, bellek ve çekirdek sayısı gibi). Ayrıca, uygulama kodunuzun gerektirdiği tüm yapılandırma ayarları.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png" alt-text="Spark akış uygulaması dağıtma" border="false":::

Tüm uygulamaların durumu, bir al uç noktasına karşı bir GET isteğiyle de denetlenebilir. Son olarak, çalışan bir uygulamayı, bir SILME isteğini, LIVY uç noktasına karşı sonlandırarak sonlandırabilir. LIVY API 'SI ile ilgili ayrıntılar için bkz. [Apache Livy Ile uzak işler](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta Apache Spark kümesi oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark akış Programlama Kılavuzu](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Apache Spark yapılandırılmış akışa genel bakış](apache-spark-structured-streaming-overview.md)
