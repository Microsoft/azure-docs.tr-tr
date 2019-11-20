---
title: Spark akış & tam bir kez olay işleme-Azure HDInsight
description: Bir olayı bir kez ve yalnızca bir kez işlemek için Apache Spark akışı ayarlama.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 34cb3f4cdcc5bfc11bba300ff1aa04422e0fcc57
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241139"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Tam bir kez olay işleme ile Apache Spark akış işleri oluşturma

Akış işleme uygulamaları, sistemdeki bazı arızalardan sonra iletileri yeniden işlemeyi nasıl işleydikleri konusunda farklı yaklaşımlar alır:

* En az bir kez: her ileti işlenmek üzere garanti edilir, ancak birden çok kez işlenebilir.
* En fazla bir kez: her ileti işlenmeyebilir veya işlenmeyebilir. Bir ileti işlenirse, yalnızca bir kez işlenir.
* Tam olarak bir kez: her iletinin bir kez ve yalnızca bir kez işlenmesi garanti edilir.

Bu makalede, Spark akışının tam bir kez işleme sağlamak için nasıl yapılandırılacağı gösterilir.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Apache Spark akışı ile tam bir kez semantiği

İlk olarak, bir sorunla karşılaşdıktan sonra tüm sistem hatası ve veri kaybını nasıl önleyebileceğiniz hakkında düşünün. Spark akış uygulaması şunları içerir:

* Bir giriş kaynağı.
* Bir veya daha fazla alıcı, giriş kaynağından veri çekmesini sağlayan işlem.
* Verileri işleyen görevler.
* Çıkış Havuzu.
* Uzun süre çalışan işi yöneten bir sürücü işlemi.

Tam bir kez semantiği, herhangi bir noktada hiçbir veri kaybolmamasını ve bu ileti işlemenin hatanın nerede gerçekleştiğine bakılmaksızın yeniden başlatılabilir olmasını gerektirir.

### <a name="replayable-sources"></a>Replayable kaynaklar

Spark akış uygulamanızın olaylarınızın okuduğu kaynak *replayable*olmalıdır. Bu, iletinin alındığı, ancak iletinin kalıcı hale getirilmesi veya işlenebilmesi için sistemin başarısız olduğu durumlarda, kaynağın aynı iletiyi yeniden sağlaması gerektiği anlamına gelir.

Azure 'da, HDInsight üzerinde hem Azure Event Hubs hem de [Apache Kafka](https://kafka.apache.org/) replayable kaynakları sağlar. Bir replayable kaynağına ait başka bir örnek, [Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), Azure depolama Blobları veya Azure Data Lake Storage gibi hataya dayanıklı bir dosya sistemidir. Bu durumda, tüm veriler süresiz olarak ve tüm durumlarda verileri tamamen yeniden okuyabilirsiniz.

### <a name="reliable-receivers"></a>Güvenilir alıcılar

Spark akışında, Event Hubs ve Kafka gibi kaynaklarda *güvenilir alıcılar*vardır; burada her alıcı, kaynağı okurken ilerleme durumunu izler. Güvenilir bir alıcı, [Apache ZooKeeper](https://zookeeper.apache.org/) içinde ya da "' ye yazılan Spark akış denetim noktalarında veya hata toleranslı depolama alanında durumunu devam ettirir. Böyle bir alıcı başarısız olursa ve daha sonra yeniden başlatılırsa, kaldığınız yerden devam edebilir.

### <a name="use-the-write-ahead-log"></a>Sonradan yazma günlüğünü kullanın

Spark akışı, her alınan olayın hata toleranslı depolamada ilk olarak Spark 'ın denetim noktası dizinine yazıldığı ve dayanıklı bir dağıtılmış veri kümesinde (RDD) depolandığı bir yazma öncesi günlüğü kullanımını destekler. Azure 'da, hataya dayanıklı depolama, Azure Storage veya Azure Data Lake Storage tarafından desteklenir. Spark akış uygulamanızda, `spark.streaming.receiver.writeAheadLog.enable` yapılandırma ayarı `true`olarak ayarlanarak tüm alıcılar için yazma günlüğü etkinleştirilir. Sonradan yazma günlüğü, sürücü ve yürütmelerinden oluşan hatalara karşı hata toleransı sağlar.

Olay verilerine karşı görevleri çalıştıran çalışanlar için her bir RDD, her ikisi de birden fazla çalışan arasında çoğaltılır ve dağıtılır. Bir görev çöktüğü çalıştığı için başarısız olursa, görev olay verilerinin bir çoğaltmasını içeren başka bir çalışan üzerinde yeniden başlatılır, bu nedenle olay kaybedilmez.

### <a name="use-checkpoints-for-drivers"></a>Sürücüler için denetim noktaları kullanma

İş sürücülerinin yeniden başlatılabilir olması gerekir. Spark akış uygulamanızı çalıştıran sürücü kilitlenirse, çalışan tüm alıcılar, görevler ve olay verilerini depolayan tüm RDDs ile birlikte çalışır. Bu durumda, daha sonra devam edebilmeniz için işin ilerlemesini kaydedebilmeniz gerekir. Bu, hata toleranslı depolamada, DStream 'in yönlendirilmiş çevrimsiz grafiği (DAG) için denetim noktası kullanılarak gerçekleştirilir. DAG meta verileri, akış uygulaması oluşturmak için kullanılan yapılandırmayı, uygulamayı tanımlayan işlemleri ve kuyruğa alınmış ancak henüz tamamlanmamış tüm toplu işleri içerir. Bu meta veriler, başarısız bir sürücünün denetim noktası bilgilerinde yeniden başlatılmasını sağlar. Sürücü yeniden başlatıldığında, olay verilerini doğrudan yazma günlüğünden geri yükleyen yeni alıcıları başlatır.

Denetim noktaları, Spark akışında iki adımda etkinleştirilir. 

1. Streammingcontext nesnesinde, denetim noktaları için depolama yolunu yapılandırın:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    HDInsight 'ta bu kontrol noktalarının, Azure Storage veya Azure Data Lake Storage kümenize bağlı olan varsayılan depolamaya kaydedilmesi gerekir.

2. Ardından, DStream 'de bir kontrol noktası aralığı (saniye cinsinden) belirtin. Her aralıkta, giriş olayından türetilen durum verileri depolama alanına kalıcıdır. Kalıcı durum verileri, kaynak olaydan durumu yeniden oluştururken gereken hesaplamayı azaltabilir.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Idempotent havuzlarını kullanma

İşinizin sonuçları yazdığı hedef havuz, aynı sonucun birden çok kez verildiği durumu işleyebilmelidir. Havuz, bu tür yinelenen sonuçları algılayabilmeli ve bunları yoksaymalıdır. Bir *ıdempotent* havuzu, durum değişikliği olmadan aynı verilerle birden çok kez çağrılabilir.

İlk olarak veri deposunda gelen sonucun varlığını denetleyen mantığı uygulayarak ıdempotent havuzları oluşturabilirsiniz. Sonuç zaten varsa, Spark işinizin perspektifinden yazma başarılı olarak görünmelidir, ancak gerçekte veri deponuzda yinelenen verileri yok sayılır. Sonuç yoksa, havuz bu yeni sonucu depolamasına eklemesi gerekir. 

Örneğin, bir tabloya olayları ekleyen Azure SQL veritabanı ile saklı bir yordam kullanabilirsiniz. Bu saklı yordam ilk olarak olaya anahtar alanlara göre bakar ve yalnızca eşleşen bir olay bulunamadı tablosuna eklenir.

Azure depolama Blobları veya Azure Data Lake Storage gibi bölümlenmiş bir dosya sistemi kullanmak başka bir örnektir. Bu durumda, havuz mantığınızın bir dosyanın varlığını denetlemesi gerekmez. Olayı temsil eden dosya varsa, aynı verilerle yalnızca üzerine yazılır. Aksi takdirde, hesaplanan yolda yeni bir dosya oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark akışa genel bakış](apache-spark-streaming-overview.md)
* [Apache Hadoop YARN 'de yüksek oranda kullanılabilir Apache Spark akışı işleri oluşturma](apache-spark-streaming-high-availability.md)
