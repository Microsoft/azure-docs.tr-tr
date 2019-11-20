---
title: Apache Spark & Hive-Hive ambar Bağlayıcısı-Azure HDInsight
description: Apache Spark ve Apache Hive Azure HDInsight 'ta Hive ambarı Bağlayıcısı ile tümleştirmeyi öğrenin.
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 2448550cf35f92bc8d91bc6ad9d5b22cc90b5ae0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494300"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Hive ambarı Bağlayıcısı ile Apache Spark ve Apache Hive tümleştirme

Apache Hive ambar Bağlayıcısı (HWC), verileri Spark veri çerçeveleri ve Hive tabloları arasında taşıma ve ayrıca Spark akış verilerini Hive tablolarına yönlendirme gibi görevleri destekleyerek Apache Spark ve Apache Hive ile daha kolay çalışmanıza olanak tanıyan bir kitaplıktır. Hive ambar Bağlayıcısı Spark ve Hive arasında bir köprü gibi çalışmaktadır. Geliştirme için Scala, Java ve Python 'u destekler.

Hive ambarı Bağlayıcısı, güçlü büyük veri uygulamaları oluşturmak için Hive ve Spark 'ın benzersiz özelliklerinden yararlanmanıza olanak tanır. Apache Hive atomik, tutarlı, yalıtılmış ve dayanıklı (ACID) veritabanı işlemleri için destek sunar. Hive içindeki ACID ve işlemler hakkında daha fazla bilgi için bkz. [Hive işlemleri](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive Ayrıca Apache Ranger aracılığıyla ayrıntılı güvenlik denetimleri ve düşük gecikme süreli analitik Işlem Apache Spark kullanılamaz.

Apache Spark, Apache Hive içinde kullanılamayan akış özellikleri sağlayan yapılandırılmış bir akış API 'SI vardır. HDInsight 4,0 ' den başlayarak, Apache Spark 2.3.1 ve Apache Hive 3.1.0, birlikte çalışabilirliğin zor olmasına yönelik ayrı metastores 'e sahiptir. Hive ambarı Bağlayıcısı Spark ve Hive 'yi birlikte kullanmayı kolaylaştırır. HWC kitaplığı, LLAP Daemon 'ları 'ten Spark yürüticilerine verileri paralel olarak yükler ve Spark 'tan Hive 'e standart bir JDBC bağlantısı kullanmaktan daha verimli ve ölçeklenebilir hale getirir.

![Hive ambarı bağlayıcı mimarisi](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive ambarı Bağlayıcısı tarafından desteklenen işlemlerden bazıları şunlardır:

* Bir tabloyu açıklama
* ORC biçimli veriler için tablo oluşturma
* Hive verilerini seçme ve bir veri çerçevesini alma
* Toplu işteki bir veri çerçevesini Hive 'e yazma
* Hive güncelleştirme ekstresi yürütülüyor
* Hive 'den tablo verilerini okuma, Spark 'ta dönüştürme ve yeni bir Hive tablosuna yazma
* HiveStreaming kullanarak Hive 'e veri çerçevesi veya Spark akışı yazma

## <a name="hive-warehouse-connector-setup"></a>Hive ambar Bağlayıcısı kurulumu

Azure HDInsight 'ta Spark ve etkileşimli sorgu kümesi arasında Hive ambarı bağlayıcısını ayarlamak için aşağıdaki adımları izleyin:

### <a name="create-clusters"></a>Küme oluşturma

1. Depolama hesabı ve özel bir Azure sanal ağı ile bir HDInsight Spark **4,0** kümesi oluşturun. Azure sanal ağında bir küme oluşturma hakkında daha fazla bilgi için bkz. [mevcut bir sanal ağa HDInsight ekleme](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Spark kümesi ile aynı depolama hesabı ve Azure sanal ağı ile bir HDInsight etkileşimli sorgu (LLAP) **4,0** kümesi oluşturun.

### <a name="modify-hosts-file"></a>Hosts dosyasını Değiştir

Etkileşimli sorgu kümenizin headnode0 üzerindeki `/etc/hosts` dosyasındaki düğüm bilgilerini kopyalayın ve bilgileri Spark kümenizin headnode0 üzerindeki `/etc/hosts` dosyasına birleştirin. Bu adım, Spark kümenizin etkileşimli sorgu kümesindeki düğümlerin IP adreslerini çözümlemesine izin verir. Güncelleştirilmiş dosyanın içeriğini `cat /etc/hosts`görüntüleyin. Son çıktı aşağıdaki ekran görüntüsünde gösterilen gibi görünmelidir.

![Hive ambar Bağlayıcısı dosyayı barındırır](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Ön bilgileri toplayın

#### <a name="from-your-interactive-query-cluster"></a>Etkileşimli sorgu kümeinizden

1. `https://LLAPCLUSTERNAME.azurehdinsight.net` kullanarak kümenin Apache ambarı giriş sayfasına gidin; burada `LLAPCLUSTERNAME` etkileşimli sorgu kümenizin adıdır.

1. Gelişmiş Hive ** > gelişmiş** > Hive **-site** > **hive. Zookeeper. Quorum** > , **Hive** **configs** ' a gidin ve değeri aklınızda edin. Değer şuna benzer olabilir: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

1. Gelişmiş > **genel** > **Hive. metasileri. Uri 'leri** > **Hive** > **configs** ' a gidin ve değeri aklınızda edin. Değer şuna benzer olabilir: `thrift://hn0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

#### <a name="from-your-apache-spark-cluster"></a>Apache Spark kümenizdeki

1. Apache Spark Kümenizin adı `SPARKCLUSTERNAME` `https://SPARKCLUSTERNAME.azurehdinsight.net` kullanarak kümenin Apache ambarı giriş sayfasına gidin.

1. Gelişmiş Hive ** > ** **configs** > **Gelişmiş** > **Gelişmiş Hive-Interactive-site** > **Hive. LLAP. Daemon. Service. konaklarına** gidin ve değeri aklınızda yapın. Değer şuna benzer olabilir: `@llap0`.

### <a name="configure-spark-cluster-settings"></a>Spark kümesi ayarlarını yapılandırma

Spark ambarı Web Kullanıcı arabiriminizden **Spark2** > **configs** > **özel Spark2-varsayılanlar**' a gidin.

![Apache ambarı Spark2 yapılandırması](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Aşağıdakileri eklemek/güncelleştirmek için gereken **özelliği Ekle...** öğesini seçin:

| Anahtar | Değer |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|Daha önce **Hive. LLAP. Daemon. Service. konaklarından**edindiğiniz değer.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Etkileşimli sorgu kümesinde Hiveserver2 'e bağlanan JDBC bağlantı dizesine ayarlayın. `LLAPCLUSTERNAME`, etkileşimli sorgu kümenizin adıyla DEĞIŞTIRIN. `PWD` gerçek parolayla değiştirin.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Uygun bir ile uyumlu hazırlama dizinine ayarlayın. İki farklı kümeniz varsa, HiveServer2 erişimi olması için, hazırlama dizini LLAP kümesinin depolama hesabının hazırlama dizininde bir klasör olmalıdır.  `STORAGE_ACCOUNT_NAME`, küme tarafından kullanılan depolama hesabının adıyla ve `STORAGE_CONTAINER_NAME` depolama kapsayıcısının adıyla değiştirin.|
|`spark.datasource.hive.warehouse.metastoreUri`|Daha önce **Hive. metaser. Uri**'lerden edindiğiniz değer.|
|`spark.security.credentials.hiveserver2.enabled`|YARN istemci dağıtım modu için `false`.|
|`spark.hadoop.hive.zookeeper.quorum`|Daha önce **Hive. Zookeeper. Quorum**öğesinden edindiğiniz değer.|

Değişiklikleri kaydedin ve gerektiğinde bileşenleri yeniden başlatın.

## <a name="using-the-hive-warehouse-connector"></a>Hive ambarı bağlayıcısını kullanma

### <a name="connecting-and-running-queries"></a>Sorguları bağlama ve çalıştırma

Etkileşimli sorgu kümenize bağlanmak ve Hive ambarı bağlayıcısını kullanarak sorguları yürütmek için birkaç farklı yöntem arasından seçim yapabilirsiniz. Desteklenen yöntemler aşağıdaki araçları içerir:

* [Spark-Shell](../spark/apache-spark-shell.md)
* PySpark
* Spark-gönder
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Bu makalede sunulan tüm örnekler Spark-Shell aracılığıyla yürütülür.

Spark-Shell oturumu başlatmak için aşağıdaki adımları uygulayın:

1. Apache Spark kümeniz için baş düğümüne 'a SSH. SSH ile kümenize bağlanma hakkında daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'A bağlanma (Apache Hadoop)](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Spark kabuğunu başlatmak için aşağıdaki komutu girin:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.2.1-8.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Komut girebileceğiniz bir hoş geldiniz iletisi ve `scala>` istemi görürsünüz.

1. Spark-Shell ' i başlattıktan sonra, aşağıdaki komutları kullanarak bir Hive ambar Bağlayıcısı örneği başlatılabilir:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Kurumsal Güvenlik Paketi (ESP) kümelerinde sorguları bağlama ve çalıştırma

Kurumsal Güvenlik Paketi (ESP), Azure HDInsight 'ta Apache Hadoop kümelerine yönelik Active Directory tabanlı kimlik doğrulaması, çoklu Kullanıcı desteği ve rol tabanlı erişim denetimi gibi kurumsal düzeyde yetenekler sağlar. ESP hakkında daha fazla bilgi için bkz. [HDInsight 'ta kurumsal güvenlik paketi kullanma](../domain-joined/apache-domain-joined-architecture.md).

1. Apache Spark kümeniz için baş düğümüne 'a SSH. SSH ile kümenize bağlanma hakkında daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'A bağlanma (Apache Hadoop)](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. `kinit` yazın ve bir etki alanı kullanıcısı ile oturum açın.

1. Spark-Shell ' i aşağıda gösterildiği gibi yapılandırma parametrelerinin tam listesiyle başlatın. Açılı ayraçlar arasındaki tüm büyük harflerde bulunan tüm değerler kümenize göre belirtilmelidir. Aşağıdaki parametrelerden herhangi birine giriş yapmak için değerleri bulmanız gerekiyorsa, [Hive ambarı bağlayıcı kurulumu](#hive-warehouse-connector-setup)'ndaki bölümüne bakın.:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Hive sorgularından Spark veri çerçeveleri oluşturma

HWC kitaplığını kullanan tüm sorguların sonuçları bir veri çerçevesi olarak döndürülür. Aşağıdaki örneklerde, temel bir sorgunun nasıl oluşturulacağı gösterilmektedir.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Sorgunun sonuçları, MLIB ve mini-SQL gibi Spark kitaplıklarıyla kullanılabilen Spark veri çerçevelerinden oluşur.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Spark veri çerçevelerini Hive tablolarına yazma

Spark, Hive 'un yönetilen ACID tablolarına yazmayı yerel olarak desteklemez. Ancak, HWC 'yi kullanarak herhangi bir veri çerçevesini Hive tablosuna yazabilirsiniz. Aşağıdaki örnekte, bu işlevselliği çalışır durumda görebilirsiniz:

1. `sampletable_colorado` adlı bir tablo oluşturun ve aşağıdaki komutu kullanarak sütunlarını belirtin:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Sütunun `state` `Colorado`eşit olduğu `hivesampletable` tablo filtreleyin. Hive tablosunun bu sorgusu Spark veri çerçevesi olarak döndürülür. Sonra DataFrame, `write` işlevi kullanılarak `sampletable_colorado` Hive tablosuna kaydedilir.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Aşağıdaki komutla sonuçları görüntüleyin:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![Hive ambar Bağlayıcısı Hive tablosunu göster](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Yapılandırılmış akış yazmaları

Hive ambarı bağlayıcısını kullanarak, verileri Hive tablolarına yazmak için Spark akışını kullanabilirsiniz.

Localhost bağlantı noktası 9999 üzerindeki bir Spark akışından Hive tablosuna veri döndüren bir Hive ambarı bağlayıcı örneği oluşturmak için aşağıdaki adımları izleyin.

1. [Sorguları bağlama ve çalıştırma](#connecting-and-running-queries)altındaki adımları izleyin.

1. Spark akışını aşağıdaki komutla başlatın:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Aşağıdaki adımları uygulayarak oluşturduğunuz Spark akışı için veri oluşturun:
    1. Aynı Spark kümesinde ikinci bir SSH oturumu açın.
    1. Komut isteminde `nc -lk 9999`yazın. Bu komut, komut satırından belirtilen bağlantı noktasına veri göndermek için netcat yardımcı programını kullanır.

1. İlk SSH oturumuna dönün ve akış verilerini tutmak için yeni bir Hive tablosu oluşturun. Spark-Shell ' de aşağıdaki komutu girin:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Ardından aşağıdaki komutu kullanarak akış verilerini yeni oluşturulan tabloya yazın:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri` ve `database` seçeneklerinin Şu anda Apache Spark bilinen bir sorundan dolayı el ile ayarlanması gerekir. Bu sorun hakkında daha fazla bilgi için bkz. [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. İkinci SSH oturumuna dönün ve aşağıdaki değerleri girin:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. İlk SSH oturumuna dönün ve kısa etkinliği göz önünde edin. Verileri görüntülemek için aşağıdaki komutu kullanın:

    ```scala
    hive.table("stream_table").show()
    ```

İkinci SSH oturumunda netcat 'i durdurmak için **CTRL + C** tuşlarını kullanın. İlk SSH oturumunda Spark-Shell ' i çıkmak için `:q` kullanın.

### <a name="securing-data-on-spark-esp-clusters"></a>Spark ESP kümelerinde verilerin güvenliğini sağlama

1. Aşağıdaki komutları girerek bazı örnek verilerle `demo` tablo oluşturun:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Aşağıdaki komutla tablonun içeriğini görüntüleyin. İlkeyi uygulamadan önce, `demo` tablosu tam sütunu gösterir.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![Ranger ilkesini uygulamadan önce demo tablosu](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Sütunun yalnızca son dört karakterini gösteren bir sütun maskeleme ilkesi uygulayın.  
    1. `https://CLUSTERNAME.azurehdinsight.net/ranger/`adresindeki Ranger Yönetici Kullanıcı arabirimine gidin.
    1. **Hive**altındaki kümeniz için Hive hizmetine tıklayın.
        ![Ranger Service Manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. **Maskeleme** sekmesine tıklayın ve ardından **Yeni ilke ekleyin**

        ![Hive ambar Bağlayıcısı Ranger Hive ilke listesi](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. İstenen bir ilke adı belirtin. Veritabanı seçin: **varsayılan**, Hive tablosu: **demo**, Hive sütunu: **ad**, Kullanıcı: **Rsadmin2**, erişim türleri: **Select**ve **kısmi maske:** **maskeleme seç seçenek** menüsünden son 4 ' ü göster. **Ekle**'ye tıklayın.
                ilke oluşturma ![](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Tablonun içeriğini yeniden görüntüleyin. Ranger ilkesini uyguladıktan sonra sütunun yalnızca son dört karakterini görebiliriz.

    ![Ranger ilkesini uyguladıktan sonra demo tablosu](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Etkileşimli Sorguyu HDInsight ile Kullanma](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Zeppelin, Livy, Spark-gönder ve pyspark kullanılarak Hive ambar Bağlayıcısı ile etkileşim örnekleri](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
