---
title: Azure HDInsight 'ta Hive ambar Bağlayıcısı tarafından desteklenen Apache Spark işlemler
description: Azure HDInsight 'ta Hive ambarı bağlayıcısının farklı özellikleri hakkında bilgi edinin.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: 1799aff8bff96d404ddcbefbf58a5f5014cdba6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871597"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Azure HDInsight 'ta Hive ambar Bağlayıcısı tarafından desteklenen Apache Spark işlemler

Bu makalede, Hive ambar Bağlayıcısı (HWC) tarafından desteklenen Spark tabanlı işlemler gösterilmektedir. Aşağıda gösterilen tüm örnekler Apache Spark kabuğu aracılığıyla yürütülür.

## <a name="prerequisite"></a>Önkoşul

[Hive ambarı Bağlayıcısı kurulum](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) adımlarını doldurun.

## <a name="getting-started"></a>Kullanmaya başlama

Spark-Shell oturumu başlatmak için aşağıdaki adımları uygulayın:

1. Apache Spark kümenize bağlanmak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH oturumınızdan, sürümü göz önünde çalıştırmak için aşağıdaki komutu yürütün `hive-warehouse-connector-assembly` :

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Aşağıdaki kodu `hive-warehouse-connector-assembly` yukarıda belirtilen sürümle düzenleyin. Ardından Spark kabuğunu başlatmak için komutunu yürütün:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Spark-Shell ' i başlattıktan sonra, aşağıdaki komutları kullanarak bir Hive ambar Bağlayıcısı örneği başlatılabilir:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Hive sorguları kullanarak Spark veri çerçeveleri oluşturma

HWC kitaplığını kullanan tüm sorguların sonuçları bir veri çerçevesi olarak döndürülür. Aşağıdaki örneklerde, temel bir Hive sorgusunun nasıl oluşturulacağı gösterilmektedir.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Sorgunun sonuçları, MLIB ve mini-SQL gibi Spark kitaplıklarıyla kullanılabilen Spark veri çerçevelerinden oluşur.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Spark veri çerçevelerini Hive tablolarına yazma

Spark, Hive 'un yönetilen ACID tablolarına yazmayı yerel olarak desteklemez. Ancak, HWC 'yi kullanarak herhangi bir veri çerçevesini Hive tablosuna yazabilirsiniz. Aşağıdaki örnekte, bu işlevselliği çalışır durumda görebilirsiniz:

1. Adlı bir tablo oluşturun `sampletable_colorado` ve aşağıdaki komutu kullanarak sütunlarını belirtin:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. `hivesampletable`Sütunun eşit olduğu tabloyu filtreleyin `state` `Colorado` . Bu Hive sorgusu, işlevi kullanılarak Hive tablosuna kaydedilen bir Spark DataFrame ans SIS döndürür `sampletable_colorado` `write` .

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Aşağıdaki komutla sonuçları görüntüleyin:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png" alt-text="Hive ambar Bağlayıcısı Hive tablosunu göster" border="true":::


## <a name="structured-streaming-writes"></a>Yapılandırılmış akış yazmaları

Hive ambarı bağlayıcısını kullanarak, verileri Hive tablolarına yazmak için Spark akışını kullanabilirsiniz.

> [!IMPORTANT]
> Yapılandırılmış akış yazmaları, ESP özellikli Spark 4,0 kümelerinde desteklenmez.

Localhost bağlantı noktası 9999 ' deki bir Spark akışından verileri kullanarak Hive tablosuna almak için aşağıdaki adımları izleyin. Hive ambarı Bağlayıcısı.

1. Açık Spark kabuğınızdan aşağıdaki komutla bir Spark akışı başlatın:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Aşağıdaki adımları uygulayarak oluşturduğunuz Spark akışı için veri oluşturun:
    1. Aynı Spark kümesinde ikinci bir SSH oturumu açın.
    1. Komut istemine yazın `nc -lk 9999` . Bu komut, komut satırından belirtilen bağlantı noktasına veri göndermek için netcat yardımcı programını kullanır.

1. İlk SSH oturumuna dönün ve akış verilerini tutmak için yeni bir Hive tablosu oluşturun. Spark-Shell ' de aşağıdaki komutu girin:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Ardından aşağıdaki komutu kullanarak akış verilerini yeni oluşturulan tabloya yazın:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri`Ve `database` seçenekleri şu anda Apache Spark ' deki bilinen bir sorundan dolayı el ile ayarlanmalıdır. Bu sorun hakkında daha fazla bilgi için bkz. [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

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

İkinci SSH oturumunda netcat 'i durdurmak için **CTRL + C** tuşlarını kullanın. `:q`Ilk SSH oturumunda Spark-Shell ' i çıkmak için kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark ve Apache Hive ile HWC tümleştirmesi](./apache-hive-warehouse-connector.md)
* [Etkileşimli Sorguyu HDInsight ile Kullanma](./apache-interactive-query-get-started.md)
* [Apache Zeppelin ile HWC tümleştirmesi](./apache-hive-warehouse-connector-zeppelin.md)
