---
title: YARN 'de yüksek oranda kullanılabilir Spark akış işleri-Azure HDInsight
description: Azure HDInsight 'ta yüksek kullanılabilirliğe sahip bir senaryo için Apache Spark akışı ayarlama
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 602fa1cab71a797dd25aca263e0c6a9f2aa616bb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870237"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>YARN ile yüksek kullanılabilirliğe sahip Apache Spark akışı işleri oluşturma

[Apache Spark](https://spark.apache.org/) Akış, veri akışları işleme için ölçeklenebilir, yüksek performanslı, hataya dayanıklı uygulamalar uygulamanıza olanak sağlar. Bir HDInsight Spark kümesinde Spark akış uygulamalarını Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache flome](https://flume.apache.org/), Twitter, [ZEROMQ](http://zeromq.org/), ham TCP yuvaları gibi farklı türlerde veri kaynaklarına bağlayabilirsiniz veya [Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) bir dosya sistemini değişiklikler için izleyerek izleyebilirsiniz. Spark akışı, belirli bir olayın bir düğüm hatası ile birlikte tam olarak bir kez işlendiği garantisi ile hata toleransını destekler.

Spark akışı, verilere dönüşümler uygulayabileceğiniz ve ardından sonuçları filesystems, veritabanları, panolar ve konsola gönderebilecek uzun süre çalışan işler oluşturur. Spark akışı, önce tanımlı bir zaman aralığı boyunca bir olay toplu işi toplayarak mikro-veri yığınlarını işler. Ardından, bu toplu işlem, işleme ve çıkış için tarihinde gönderilir. Toplu iş zaman aralıkları genellikle saniyenin kesirleri olarak tanımlanır.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-streaming.png" alt-text="Spark akışı" border="false":::

## <a name="dstreams"></a>DStreams

Spark akışı, *ayrık bir akış* (dstream) kullanarak sürekli bir veri akışını temsil eder. Bu DStream, Event Hubs veya Kafka gibi giriş kaynaklarından ya da başka bir DStream 'e dönüşümler uygulanarak oluşturulabilir. Spark akış uygulamanıza bir olay ulaştığında, olay güvenilir bir şekilde depolanır. Diğer bir deyişle, olay verileri birden çok düğümün bir kopyasına sahip olacak şekilde çoğaltılır. Bu, tek bir düğümün başarısızlığının olaylarınızın kaybına neden olmamasını sağlar.

Spark Core, *Esnek dağıtılmış veri kümeleri* (rdds) kullanır. RDDs, verileri kümedeki birden çok düğüme dağıtarak, her düğümün en iyi performansı elde etmek için verileri tamamen bellek içinde tutar. Her RDD bir toplu iş aralığı üzerinden toplanan olayları temsil eder. Toplu iş aralığı geçtiğinde, Spark akışı bu aralıktaki tüm verileri içeren yeni bir RDD oluşturur. Bu sürekli RDDs kümesi bir DStream 'e toplanır. Spark akış uygulaması, her bir Batch 'in RDD ' de depolanan verileri işler.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-dstream.png" alt-text="Spark DStream" border="false":::

## <a name="spark-structured-streaming-jobs"></a>Spark yapılandırılmış akış işleri

Spark yapılandırılmış akış, Spark 2,0 ' de, yapılandırılmış veri akışı için kullanılmak üzere analitik bir altyapı olarak sunulmuştur. Spark yapısal akışı, Mini SQL toplu işlem altyapısı API 'Lerini kullanır. Spark akışında olduğu gibi, Spark yapılandırılmış akış, zaman içinde sürekli olarak gelen mikro-veri yığınlarıyla ilgili hesaplamalar çalıştırır. Spark yapılandırılmış akış, bir veri akışını sınırsız satır içeren bir giriş tablosu olarak temsil eder. Diğer bir deyişle, giriş tablosu yeni veri geldiğinde büyümeye devam eder. Bu giriş tablosu, uzun süre çalışan bir sorgu tarafından sürekli işlenir ve sonuçlar bir çıktı tablosuna yazılır.

:::image type="content" source="./media/apache-spark-streaming-high-availability/structured-streaming.png" alt-text="Spark yapılandırılmış akışı" border="false":::

Yapılandırılmış akışta, veriler sisteme ulaşır ve hemen giriş tablosuna alınır. Bu giriş tablosuna karşı işlem gerçekleştiren sorgular yazarsınız. Sorgu çıktısı, sonuçlar tablosu olarak adlandırılan başka bir tablo oluşturur. Sonuçlar tablosu, bir ilişkisel veritabanı gibi bir dış veri deposuna gönderilmek üzere veri çizeceğiniz sorgunun sonuçlarını içerir. *Tetikleyici aralığı* , giriş tablosundan verilerin işlendiği zaman için zamanlamayı ayarlar. Varsayılan olarak, yapılandırılmış akış, verileri ulaştığı anda işler. Ancak, tetikleyiciyi daha uzun bir aralıkta çalışacak şekilde de yapılandırabilirsiniz, böylece akış verileri zaman tabanlı toplu işlerle işlenir. Sonuçlar tablosundaki veriler, akış sorgusunun başlamasından bu yana tüm çıkış verilerini içermesi (*tam mod*) ve yalnızca sorgunun işlendiği son tarihten bu yana yalnızca yeni olan verileri (*ekleme modu*) içermesi için her yeni veri bir kez yenilenebilir.

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Hataya dayanıklı Spark akış işleri oluşturma

Spark akış işleriniz için yüksek oranda kullanılabilir bir ortam oluşturmak üzere, hata durumunda bireysel işlerinizi kurtarma için kodlayarak başlayın. Bu tür otomatik kurtarma işleri hataya dayanıklıdır.

RDDs, yüksek oranda kullanılabilir ve hataya dayanıklı Spark akış işlerine yardımcı olan çeşitli özelliklere sahiptir:

* RDD 'de depolanan giriş verilerinin toplu işleri, hata toleransı için otomatik olarak bellekte çoğaltılır.
* Bu çalışan düğümleri kullanılabilir olduğu sürece, çalışan arızası farklı çalışanlar üzerindeki çoğaltılan giriş verilerinden yeniden hesaplanabileceğinden veriler kayboldu.
* Hataların/stratablıların bellek içindeki hesaplama yoluyla meydana gelen kurtarması durumunda hızlı hata kurtarma bir saniye içinde gerçekleşebilir.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Spark akışı ile tam bir kez semantiği

Her bir olayı bir kez (ve yalnızca bir kez) işleyen bir uygulama oluşturmak için, bir sorunla karşılaşdıktan sonra tüm sistem hatası ve veri kaybını nasıl önleyebileceğiniz hakkında düşünün. Tam bir kez semantiği herhangi bir noktada hiçbir veri kaybolmamasını ve bu ileti işlemenin hatanın nerede gerçekleştiğine bakılmaksızın yeniden başlatılabilir olmasını gerektirir. Bkz. [tek bir olay işleme Ile Spark akış Işleri oluşturma](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark akışı ve Apache Hadoop YARN

HDInsight 'ta küme çalışması, *henüz başka bir kaynak Negotiator* (Yarn) tarafından koordine edilir. Spark akışı için yüksek kullanılabilirlik tasarlama, Spark akışı ve ayrıca YARN bileşenleri için teknikler içerir.  YARN kullanan örnek bir yapılandırma aşağıda gösterilmiştir.

:::image type="content" source="./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png" alt-text="YARN mimarisi" border="false":::

Aşağıdaki bölümlerde bu yapılandırmaya ilişkin tasarım konuları açıklanır.

### <a name="plan-for-failures"></a>Başarısızlık planı

Yüksek kullanılabilirliğe sahip bir YARN yapılandırması oluşturmak için olası bir yürütücü veya sürücü hatası planlaması yapmanız gerekir. Bazı Spark akış işleri, ek yapılandırma ve kurulum gerektiren veri garantisi gereksinimlerini de kapsar. Örneğin, bir akış uygulaması, barındırma Akış sisteminde veya HDInsight kümesinde oluşan herhangi bir hataya rağmen sıfır veri kaybı garantisi için bir iş gereksinimine sahip olabilir.

Bir **yürütücü** başarısız olursa, görevleri ve alıcıları Spark tarafından otomatik olarak yeniden başlatılır, bu nedenle yapılandırma değişikliği gerekli değildir.

Ancak, bir **sürücü** başarısız olursa, tüm ilişkili yürüticileri başarısız olur ve tüm alınan bloklar ve hesaplama sonuçları kaybolur. Bir sürücü hatasından kurtarmak için, [tam olarak bir kez olay işleme Ile Spark akış Işleri oluşturma](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers)bölümünde açıklandığı gibi *dstream checksize* seçeneğini kullanın. DStream checkişaretleyici, Azure depolama gibi hataya dayanıklı depolamaya, DStreams için *yönlendirilmiş çevrimsiz grafiği* (DAG) düzenli aralıklarla kaydeder.  Checkişaret, Spark yapılandırılmış akışının, denetim noktası bilgilerinde başarısız olan sürücüyü yeniden başlatmasını sağlar.  Bu sürücü yeniden başlatması yeni yürüticileri başlatır ve ayrıca alıcıları yeniden başlatır.

DStream checkişaret ile sürücüleri kurtarmak için:

* Bu yapılandırma ayarıyla YARN 'de otomatik sürücü yeniden başlatmayı yapılandırın `yarn.resourcemanager.am.max-attempts` .
* İle uyumlu bir dosya sisteminde denetim noktası dizini ayarlayın `streamingContext.checkpoint(hdfsDirectory)` .
* Kurtarma için denetim noktaları kullanmak üzere kaynak kodunu yeniden yapılandır, örneğin:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Üzerine yazma öncesi günlüğü (WAL) etkinleştirerek kayıp veri kurtarmayı yapılandırın `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` ve ile giriş DStreams için bellek içi çoğaltmayı devre dışı bırakın `StorageLevel.MEMORY_AND_DISK_SER` .

Denetim noktası + WAL + güvenilir alıcıları kullanarak özetlemek için, "en az bir kez" veri kurtarma sağlayabilirsiniz:

* Tam olarak bir kez, alınan veriler kaybolmadığından ve çıktılar ıdempotent ya da işlemsel olduğu sürece.
* Tek bir kez, yeni Kafka doğrudan yaklaşımıyla, alıcı veya WALs kullanmak yerine Kafka ' ı çoğaltılan bir günlük olarak kullanır.

### <a name="typical-concerns-for-high-availability"></a>Yüksek kullanılabilirlik için tipik sorunlar

* Toplu işlerin dışında akış işlerini izlemek daha zordur. Spark akış işleri genellikle uzun süredir çalışır ve bir iş bitene kadar YARN günlükleri yığmaz.  Spark denetim noktaları uygulama veya Spark yükseltmeleri sırasında kaybolur ve bir yükseltme sırasında denetim noktası dizinini temizlemeniz gerekir.

* İstemci başarısız olsa bile, YARN küme modlarınızı sürücüleri çalıştıracak şekilde yapılandırın. Sürücüler için otomatik yeniden başlatmayı ayarlamak için:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark ve Spark akış Kullanıcı arabirimi, yapılandırılabilir bir ölçüm sistemine sahiptir. Ayrıca, Graphite/Grafana gibi ek kitaplıkları da kullanabilirsiniz; örneğin ' sayı kaydı işlendi ', ' sürücü & yürüticilerine bellek/GC kullanımı ', ' Toplam gecikme ', ' küme kullanımı ' vb. Yapılandırılmış akış sürümü 2,1 veya üzeri bir sürümde `StreamingQueryListener` ek ölçümler toplamak için kullanabilirsiniz.

* Uzun süre çalışan işleri segmentleyebilirsiniz.  Kümeye bir Spark akış uygulaması gönderildiğinde, işin çalıştırıldığı YARN kuyruğu tanımlanmalıdır. Yarı çalışan işleri ayrı sıralara göndermek için [Yarn kapasite zamanlayıcısını](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) kullanabilirsiniz.

* Akış uygulamanızı sorunsuz bir şekilde kapatın. Uzaklıklarınız biliniyorsa ve tüm uygulama durumu dışarıdan depolanıyorsa, akış uygulamanızı uygun yerde program aracılığıyla durdurabilirsiniz. Bir teknik, her *n* saniyede bir dış bayrak denetleyerek Spark 'ta "iş parçacığı kancaları" kullanmaktır. Ayrıca, uygulamayı başlatırken de bir *işaretleyici dosyası* kullanabilir, sonra da durdurmak istediğinizde kaldırılır. Bir işaret dosyası yaklaşımı için, Spark uygulamanızda şuna benzer bir kod çağıran ayrı bir iş parçacığı kullanın:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark akışa genel bakış](apache-spark-streaming-overview.md)
* [Tam olarak bir kez olay işleme ile Apache Spark akışı işleri oluşturma](apache-spark-streaming-exactly-once.md)
* [YARN 'de uzun süre çalışan Apache Spark akışı Işleri](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Yapılandırılmış akış: hataya dayanıklı anlambilim](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Ayrık akışlar: ölçeklenebilir akış Işleme için bir Fault-Tolerant modeli](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
