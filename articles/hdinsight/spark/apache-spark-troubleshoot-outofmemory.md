---
title: Azure HDInsight 'ta Apache Spark için OutOfMemoryError özel durumları
description: Azure HDInsight 'ta Apache Spark kümesi için çeşitli OutOfMemoryError özel durumları
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: f3f89de07e2e17a4dda47ce3650391af38663004
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087198"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Spark için OutOfMemoryError özel durumları

Bu makalede, Azure HDInsight kümelerinde Apache Spark bileşenleri kullanılırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Senaryo: Apache Spark için OutOfMemoryError özel durumu

### <a name="issue"></a>Sorun

Apache Spark uygulamanız OutOfMemoryError işlenmemiş özel durumuyla başarısız oldu. Şuna benzer bir hata iletisi alabilirsiniz:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Nedeni

Bu özel durumun en olası nedeni, Java sanal makineleri (JVMs) yeterli yığın bellek tahsis edildiği ' dir. Bu JVM 'Ler, Apache Spark uygulamasının bir parçası olarak yürüticileri veya sürücüler olarak başlatılır.

### <a name="resolution"></a>Çözüm

1. Spark uygulamasının işleyeceği verilerin boyut üst sınırını belirleyin. Giriş verilerini dönüştürerek oluşturulan ara veriler ve ara verileri daha fazla dönüştürme ile üretilen çıkış verileri temelinde, en fazla giriş verisi boyutuna göre boyut tahmini yapın. Başlangıçtaki tahmin yeterli değilse, boyutu biraz artırabilir ve bellek hataları alt tarafına kadar yineleme yapın.

1. Kullanılacak HDInsight kümesinin, Spark uygulamasını barındırmak için yeterli kaynağa (bellek ve ayrıca çekirdek olarak) sahip olduğundan emin olun. Bu, **kullanılan bellek** değerleri için kümenin Yarn Kullanıcı arabiriminin küme ölçümleri bölümü görüntülenirken belirlenebilir. Kullanılan **bellek toplamı** ve **sanal çekirdekler** karşılaştırması **Sanal çekirdekler toplam**.

    ![Yarn çekirdek bellek görünümü](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Aşağıdaki Spark yapılandırmasını uygun değerlere ayarlayın. Uygulama gereksinimlerini kümedeki kullanılabilir kaynaklarla dengeleyin. Bu değerler, YARN tarafından görüntülenen kullanılabilir bellek ve çekirdekler için% 90 ' ı aşmamalıdır ve Spark uygulamasının en düşük bellek gereksinimini de karşılamalıdır:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Tüm yürüticileri tarafından kullanılan toplam bellek =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Sürücü tarafından kullanılan toplam bellek =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Senaryo: Apache Spark geçmiş sunucusunu açmaya çalışırken Java yığın alanı hatası

### <a name="issue"></a>Sorun

Spark geçmiş sunucusunda olayları açarken aşağıdaki hatayı alıyorsunuz:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Nedeni

Bu sorun genellikle büyük Spark-Event dosyalarını açarken kaynak yetersizliği nedeniyle oluşur. Spark yığın boyutu varsayılan olarak 1 GB olarak ayarlanır, ancak büyük Spark olay dosyaları bundan daha fazlasını gerektirebilir.

Yüklemeye çalıştığınız dosyaların boyutunu doğrulamak isterseniz, aşağıdaki komutları gerçekleştirebilirsiniz:.

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Çözüm

Spark yapılandırmasındaki `SPARK_DAEMON_MEMORY` özelliği düzenleyerek Spark geçmiş sunucu belleğini artırabilir ve tüm hizmetleri yeniden başlatabilirsiniz.

Bunu, Spark2/config/Advanced Spark2-env bölümünü seçerek, ambarı tarayıcısı kullanıcı arabiriminden yapabilirsiniz.

![Advanced spark2-env bölümü](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Spark geçmiş sunucusu belleğini 1G 'den 4g 'ye değiştirmek için aşağıdaki özelliği ekleyin: `SPARK_DAEMON_MEMORY=4g`.

![Spark özelliği](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Tüm etkilenen hizmetleri ambarı 'ndan yeniden başlattığınızdan emin olun.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Senaryo: Apache Spark kümesinde Livy sunucusu başlatılamıyor

### <a name="issue"></a>Sorun

Livy sunucu bir Apache Spark [(Linux üzerinde Spark 2,1 (HDI 3,6)] üzerinde başlatılamaz. Aşağıdaki hata yığınında, tüm sonuçları yeniden başlatmaya çalışmak, Livy günlüklerinden:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Nedeni

`java.lang.OutOfMemoryError: unable to create new native thread`Ana işletim sistemi, JVM 'lere daha fazla yerel iş parçacığı atayamaz. Bu özel durumun, işlem başına iş parçacığı sayısı sınırının ihlalinden kaynaklanmış olduğunu doğrulamıştır.

Livy sunucusu beklenmedik şekilde sonlandırıldığında, Spark kümelerine yapılan tüm bağlantılar da sonlandırılır. Bu, tüm işlerin ve ilgili verilerin kaybedildiği anlamına gelir. HDP 2,6 oturum kurtarma mekanizması tanıtılmıştı, Livy sunucu geri alındıktan sonra kurtarılacak Zookeeper içinde oturum ayrıntılarını depolar.

Livy aracılığıyla çok sayıda iş gönderildiğinde, Livy sunucusu için yüksek kullanılabilirlik parçası olan bu oturum durumlarını ZK 'de (HDInsight kümelerinde) depolar ve Livy hizmeti yeniden başlatıldığında bu oturumları kurtarır. Beklenmedik sonlandırma sonrasında, Livy oturum başına bir iş parçacığı oluşturur ve bu, çok fazla iş parçacığı oluşturulmasını neden olan belirli bir sayıda kurtarılabilir oturumu biriktirir.

### <a name="resolution"></a>Çözüm

Aşağıda açıklanan adımları kullanarak tüm girdileri silin.

1. Kullanarak Zookeeper düğümlerinin IP adresini al

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Yukarıdaki komut, Kümem için tüm zookeepers listelendi

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Ping kullanarak Zookeeper düğümlerinin tüm IP adreslerini alın veya Ayrıca ZK adını kullanarak baş düğümüne 'dan Zookeeper 'e bağlanabilirsiniz

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Zookeeper 'e bağlandıktan sonra, yeniden başlatılmaya çalışılan tüm oturumları listelemek için aşağıdaki komutu yürütün.

    1. Çoğu durumda bu durum 8000 ' den fazla oturumu bir liste olabilir ####

        ```bash
        ls /livy/v1/batch
        ```

    1. Aşağıdaki komut, tüm kurtarılabilir oturumları kaldırmalıdır. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Yukarıdaki komutun tamamlanmasını bekleyin ve imleci bir süre sonra yeniden başlatın ve bu işlemin başarılı olması gerekir.

> [!NOTE]
> `DELETE`Yürütme tamamlandıktan sonra tüm oturum. Tek tek toplu oturumlar, bir tasarıma göre olan Spark uygulaması tamamlandıktan hemen sonra otomatik olarak silinmez. Livy oturumu, Livy Rest sunucusuna yönelik bir POST isteği tarafından oluşturulan bir varlıktır. Varlığı `DELETE` silmek için bir çağrı gerekir. Ya da GC 'nin başlatılmasını beklememiz gerekir.

---

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* [Spark bellek yönetimine genel bakış](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [HDInsight kümelerinde Spark uygulamasında hata ayıklama](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport) Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
