---
title: Azure HDInsight 'ta Spark akışı
description: HDInsight Spark kümelerinde Apache Spark akışlı uygulamalar kullanma.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: f990e5eb2761f1743c2731f499ecc341990edf53
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813985"
---
# <a name="overview-of-apache-spark-streaming"></a>Apache Spark akışa genel bakış

[Apache Spark](https://spark.apache.org/) Akış, HDInsight Spark kümelerinde veri akışı işleme sağlar ve bu da herhangi bir giriş olayının, bir düğüm hatası oluşması durumunda bile tam olarak bir kez işlenmesini güvence altına alır. Spark akışı, Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache flome](https://flume.apache.org/), Twitter, [ZEROMQ](http://zeromq.org/), ham TCP yuvaları veya Apache izleme dahil olmak üzere çok çeşitli kaynaklardan giriş verilerini alan uzun süredir çalışan bir işdir [ Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) dosya sistemleri. Yalnızca olay odaklı bir işlemden farklı olarak, bir Spark akışı, verileri 2 saniyelik bir dilim gibi zaman pencereleri olarak işler ve ardından harita, azaltma, JOIN ve ayıkla işlemlerini kullanarak her bir veri kümesini dönüştürür. Spark akışı daha sonra dönüştürülen verileri filesystems, veritabanları, panolar ve konsola yazar.

![HDInsight ve Spark streaming ile akış Işleme](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark akış uygulamaları, bu toplu işi işlenmek üzere göndermeden önce her bir *mikro-toplu* olay toplamanız gerekir. Buna karşılık, olay temelli bir uygulama her olayı hemen işler. Spark akış gecikmesi genellikle birkaç saniye altında. Mikro Batch yaklaşımının avantajları daha verimli veri işleme ve daha basit toplu hesaplamalardır.

## <a name="introducing-the-dstream"></a>DStream 'e giriş

Spark akışı, DStream adlı ayrılmış bir *akış* kullanılarak gelen verilerin sürekli akışını temsil eder. Event Hubs veya Kafka gibi giriş kaynaklarından bir DStream oluşturulabilir veya başka bir DStream 'e dönüşümler uygulayabilirsiniz.

DStream, Ham olay verilerinin üzerine bir soyutlama katmanı sağlar. 

Tek bir olayla başlayın ve bağlı bir termostat 'dan bir sıcaklık okuyun. Bu olay Spark akış uygulamanıza ulaştığında, olay, birden çok düğümde çoğaltılan güvenilir bir şekilde depolanır. Bu hata toleransı, tek bir düğümün başarısızlığının olaylarınızın kaybına neden olmamasını sağlar. Spark Core, kümedeki birden çok düğüme veri dağıtan bir veri yapısı kullanır ve bu durumda her düğüm en iyi performans için genellikle kendi verilerini bellek içinde tutar. Bu veri yapısına dayanıklı bir *Dağıtılmış veri kümesi* (RDD) adı verilir.

Her RDD, *toplu iş aralığı*olarak adlandırılan Kullanıcı tanımlı bir zaman diliminde toplanan olayları temsil eder. Her toplu iş aralığı geçtiğinde, bu aralıktaki tüm verileri içeren yeni bir RDD oluşturulur. Sürekli RDDs kümesi bir DStream 'e toplanır. Örneğin, toplu iş aralığı bir ikinci uzunsa DStream, saniye içinde alınan tüm verileri içeren bir RDD 'yi içeren bir toplu işlem yayar. DStream işlenirken, sıcaklık olayı Bu toplu işlemlerden birinde görünür. Spark akış uygulaması olayları içeren toplu işleri işler ve sonunda her bir RDD 'de depolanan veriler üzerinde işlem yapar.

![Sıcaklık olayları ile örnek DStream](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Spark akış uygulamasının yapısı

Spark akış uygulaması, alma kaynaklarından veri alan uzun süredir çalışan bir uygulamadır, verileri işlemek için dönüşümler uygular ve ardından verileri bir veya daha fazla hedefe iter. Spark akış uygulamasının yapısının statik bir bölümü ve dinamik bir bölümü vardır. Statik bölüm, verilerin nereden geldiğini, verilerde ne tür işlemleri yapılacağını ve sonuçların nereye gidebileceğini tanımlar. Dinamik bölüm uygulamayı süresiz olarak çalıştırıyor ve durdurma sinyali bekliyor.

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

StreamingContext örneği ile giriş kaynağınız için bir giriş DStream oluşturun. Bu durumda, uygulama HDInsight kümesine eklenen varsayılan depolama alanındaki yeni dosyaların görünümünü izliyor.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Dönüşümleri Uygula

DStream 'e dönüşümler uygulayarak işlemi uygulayabilirsiniz. Bu uygulama, dosyadaki bir seferde bir metin satırı alır, her satırı sözcüklere böler ve ardından bir eşlem azaltma deseninin her sözcüğün kaç kez görüneceğini saymasını sağlar.

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

Spark Stream API 'SI ile birlikte, desteklediği olay kaynakları, dönüştürmeler ve çıkış işlemleri ile ilgili ayrıntılar için bkz. [akış programlama kılavuzu Apache Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Aşağıdaki örnek uygulama kendi içinde bulunur, bu nedenle onu bir [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md)içinde çalıştırabilirsiniz. Bu örnek, DummySource sınıfında her beş saniyede bir sayacın değerini ve geçerli saati milisaniye olarak çıkaran bir sahte veri kaynağı oluşturur. Yeni bir StreamingContext nesnesi 30 saniyelik bir toplu iş aralığına sahiptir. Bir toplu iş her oluşturulduğunda, akış uygulaması oluşturulan RDD 'yi inceler, RDD 'yi Spark veri çerçevesine dönüştürür ve DataFrame üzerinde geçici bir tablo oluşturur.

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

Elde edilen çıktı aşağıdakine benzer:

| value | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

DummySource her 5 saniyede bir değer oluşturduğundan ve uygulama her 30 saniyede bir toplu iş yaydığı için altı değer vardır.

## <a name="sliding-windows"></a>Kayan pencereler

DStream 'de belirli bir süre içinde toplu hesaplamalar gerçekleştirmek için örneğin, son iki saniye boyunca ortalama bir sıcaklık almak için Spark akışı ile birlikte gelen *kayan pencere* işlemlerini kullanabilirsiniz. Kayan pencerenin süresi (pencere uzunluğu) ve pencere içeriğinin değerlendirildiği zaman aralığı (Slayt aralığı) vardır.

Kayan pencereler çakışabilir, örneğin, iki saniyelik uzunlukla bir pencere tanımlayabilir ve bu slaytlar her bir saniyede bir yer alabilir. Bu, bir toplama hesaplaması gerçekleştirdiğiniz her seferinde, bir önceki pencerenin son bir saniyesinin yanı sıra bir sonraki ikinci birindeki tüm yeni verileri de içerecektir.

![Sıcaklık olayları ile örnek Ilk pencere](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Kayan sonra sıcaklık olayları içeren örnek pencere](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

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

| value | time |
| --- | --- |
| 1\. | 1497316294139 |
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

Spark akışı, dayanıklılık ve hataya dayanıklılık sağlamak için, akış işlemenin düğüm hatası durumunda bile kesintisiz bir şekilde devam edebilmesinin sağlaması için denetim noktası kullanır. HDInsight 'ta Spark, dayanıklı depolama (Azure Storage veya Data Lake Storage) için denetim noktaları oluşturur. Bu kontrol noktaları, akış uygulaması hakkında yapılandırma, uygulama tarafından tanımlanan işlemler ve kuyruğa alınan ancak henüz işlenmemiş tüm toplu işler hakkında meta verileri depolar. Bazı durumlarda, denetim noktaları, Spark tarafından yönetilen RDDs 'de mevcut olan verilerin durumunu daha hızlı bir şekilde yeniden oluşturmak için, verileri RDDs 'ye kaydetmeyi de kapsar.

## <a name="deploying-spark-streaming-applications"></a>Spark akış uygulamalarını dağıtma

Genellikle bir JAR dosyasına yerel olarak bir Spark akış uygulaması derleyin ve ardından JAR dosyasını HDInsight üzerinde Spark 'a dağıtarak, JAR dosyasını HDInsight kümenize bağlı varsayılan depolama alanına kopyalarsınız. Bir POST işlemi kullanarak, uygulamanızı kümenizde bulunan LIVY REST API 'Leriyle başlatabilirsiniz. GÖNDERI gövdesi, JAR 'nizin yolunu sağlayan bir JSON belgesi, ana yöntemi, akış uygulamasını tanımlayan ve çalıştıran sınıfın adı ve isteğe bağlı olarak işin kaynak gereksinimleri (yürütme sayısı, bellek ve çekirdek sayısı gibi) içerir ve uygulama kodunuzun gerektirdiği tüm yapılandırma ayarlarını.

![Spark akış uygulaması dağıtma](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Tüm uygulamaların durumu, bir al uç noktasına karşı bir GET isteğiyle de denetlenebilir. Son olarak, çalışan bir uygulamayı, LIVY uç noktasına karşı SILME isteği vererek sonlandırabilirsiniz. LIVY API 'SI ile ilgili ayrıntılar için bkz. [Apache Livy Ile uzak işler](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta Apache Spark kümesi oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark akış Programlama Kılavuzu](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Apache LIVY ile Apache Spark işleri uzaktan başlatın](apache-spark-livy-rest-interface.md)
