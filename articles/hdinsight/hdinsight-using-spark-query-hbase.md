---
title: HBase verilerini okumak ve yazmak için Spark kullanma-Azure HDInsight
description: Spark kümesindeki verileri bir HBase kümesine okumak ve yazmak için Spark HBase bağlayıcısını kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/12/2020
ms.openlocfilehash: 344caf4080380f5d9dfdaf452798ada6d1dc9f1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931226"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Apache HBase verilerini okuyup yazmak için Apache Spark kullanma

Apache HBase genellikle alt düzey API (taramalar, alır ve koyar) ile veya Apache Phoenix kullanarak bir SQL söz dizimi ile sorgulanır. Apache ayrıca Apache Spark HBase bağlayıcısını de sağlar. Bağlayıcı, HBase tarafından depolanan verileri sorgulamak ve değiştirmek için kullanışlı ve verimli bir alternatiftir.

## <a name="prerequisites"></a>Önkoşullar

* Aynı [Sanal ağda](./hdinsight-plan-virtual-network-deployment.md)dağıtılan Iki ayrı HDInsight kümesi. Bir HBase ve en az Spark 2,1 (HDInsight 3,6) yüklü bir Spark. Daha fazla bilgi için bkz. [HDInsight 'ta Azure Portal kullanarak Linux tabanlı kümeler oluşturma](hdinsight-hadoop-create-linux-clusters-portal.md).

* Kümelerinizin birincil depolama alanı için URI şeması. Bu düzen, `abfs://` Azure Data Lake Storage 1. için Azure Data Lake Storage 2. veya adl://Için Azure Blob depolama için wasb://olacaktır. BLOB depolama için güvenli aktarım etkinse URI olur `wasbs://` .  Ayrıca bkz. [Güvenli aktarım](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Genel işlem

Spark kümenizi HBase kümenizi sorgulamak üzere etkinleştirmeye yönelik üst düzey işlem aşağıdaki gibidir:

1. Bazı örnek verileri HBase 'de hazırlayın.
2. hbase-site.xml dosyasını HBase küme yapılandırma klasöründen (/etc/HBase/conf) alın ve hbase-site.xml bir kopyasını Spark 2 yapılandırma klasörünüze yerleştirin (/etc/mini \ sunucu). (Isteğe bağlı: Bu işlemi otomatikleştirmek için HDInsight ekibi tarafından sunulan betiği kullanın)
4. , `spark-shell` Seçeneğinde Maven koordinatlarıyla Spark HBase bağlayıcısından başvurma komutunu çalıştırın `packages` .
5. Spark ile HBase arasındaki şemayı eşleyen bir katalog tanımlayın.
6. RDD veya DataFrame API 'Lerini kullanarak HBase verileriyle etkileşim kurun.

## <a name="prepare-sample-data-in-apache-hbase"></a>Apache HBase 'de örnek verileri hazırlama

Bu adımda, Apache HBase 'de, daha sonra Spark kullanarak sorgulayabilmeniz için bir tablo oluşturup doldurursunuz.

1. `ssh`HBase kümenize bağlanmak için komutunu kullanın. Aşağıdaki komutu, `HBASECLUSTER` HBase kümenizin adıyla değiştirerek düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. `hbase shell`HBase etkileşimli kabuğu 'nu başlatmak için komutunu kullanın. SSH bağlantınıza aşağıdaki komutu girin:

    ```bash
    hbase shell
    ```

3. `create`İki sütunlu ailelerle bir HBase tablosu oluşturmak için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Belirli bir `put` tabloda belirtilen bir sütunda belirtilen bir sütuna değer eklemek için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. `exit`HBase etkileşimli kabuğunu durdurmak için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    exit
    ```
    
## <a name="run-scripts-to-set-up-connection-between-clusters"></a>Kümeler arasında bağlantı kurmak için betikleri çalıştırma

Kümeler arasındaki iletişimi ayarlamak için, kümelerinizde iki komut dosyası çalıştırmak için aşağıdaki adımları izleyin. Bu betikler, aşağıdaki ' iletişim el ile ayarlama ' bölümünde açıklanan dosya kopyalama işlemini otomatikleştirebilir. 

* HBase kümesinden çalıştırdığınız betik, `hbase-site.xml` IP eşleme bilgilerini Spark kümenize bağlı olan varsayılan depolama alanına yükler ve HBase 'e yükler. 
* Spark kümesinden çalıştırdığınız betik, iki yardımcı betiği düzenli aralıklarla çalıştırmak için iki cron işi ayarlar:  
    1.  HBase cron işi – `hbase-site.xml` Spark varsayılan depolama hesabından yerel düğüme yeni dosyaları ve HBase IP eşlemesini indirin
    2.  Spark cron işi – bir Spark ölçeklendirmesinin oluşup olmadığını ve kümenin güvenli olup olmadığını denetler. Öyleyse, `/etc/hosts` yerel olarak depolanan HBase IP eşlemesini dahil etmek için Düzenle

__Note__: devam etmeden önce, Spark kümesinin depolama hesabını HBase kümenize ikincil depolama hesabı olarak eklediğinizden emin olun. Aşağıda gösterildiği gibi betiklerin sırada olduğundan emin olun.


1. Değişiklikleri aşağıdaki noktalara göre uygulamak için HBase kümenizdeki [betik eylemini](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) kullanın: 


    |Özellik | Değer |
    |---|---|
    |Bash betiği URI 'SI|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-hbase.sh`|
    |Düğüm türleri|Region|
    |Parametreler|`-s SECONDARYS_STORAGE_URL`|
    |Kalıcı|evet|

    * `SECONDARYS_STORAGE_URL` Spark tarafı varsayılan depolamanın URL 'sidir. Parametre örneği: `-s wasb://sparkcon-2020-08-03t18-17-37-853z@sparkconhdistorage.blob.core.windows.net`


2.  Aşağıdaki noktalara göre değişiklikleri uygulamak için Spark kümenizde betik eylemi kullanın:

    |Özellik | Değer |
    |---|---|
    |Bash betiği URI 'SI|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-spark.sh`|
    |Düğüm türleri|Baş, çalışan, Zookeeper|
    |Parametreler|`-s "SPARK-CRON-SCHEDULE"` (isteğe bağlı) `-h "HBASE-CRON-SCHEDULE"` seçim|
    |Kalıcı|evet|


    * Bu kümenin güncelleştirme olup olmadığını ne sıklıkta otomatik olarak denetlemesini istediğinizi belirtebilirsiniz. Varsayılan:-s "*/1 * * * *"-h 0 (Bu örnekte, Spark cron her dakikada çalışır, ancak HBase cron çalıştırılmıyor)
    * HBase cron varsayılan olarak ayarlanmamış olduğundan, HBase kümenize ölçeklendirme gerçekleştirirken bu betiği yeniden çalıştırmanız gerekir. HBase kümeniz sık ölçeklenirken, HBase cron işini otomatik olarak ayarlamayı seçebilirsiniz. Örneğin: `-h "*/30 * * * *"` her 30 dakikada bir denetim gerçekleştirmek için betiği yapılandırır. Bu, genel depolama hesabındaki yeni HBase bilgilerinin yerel düğüme indirilmesini otomatik hale getirmek için HBase cron zamanlamasını düzenli aralıklarla çalıştırır.
    
    

## <a name="set-up-communication-manually-optional-if-provided-script-in-above-step-fails"></a>İletişimi el ile ayarlayın (Yukarıdaki adımda belirtilen komut dosyası başarısız olursa Isteğe bağlı)

__Note:__ Bu adımların, kümelerden birinin bir ölçeklendirme etkinliğine sahip olduğu her seferinde gerçekleştirmesi gerekir.

1. Yerel depolamadan hbase-site.xml, Spark kümenizin varsayılan depolama alanının köküne kopyalayın.  Yapılandırmanızı yansıtmak için aşağıdaki komutu düzenleyin.  Ardından, açık SSH oturumunından HBase kümesine şu komutu girin:

    | Söz dizimi değeri | Yeni değer|
    |---|---|
    |[URI düzeni](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Depolama alanınızı yansıtacak şekilde değiştirin.  Aşağıdaki sözdizimi, güvenli aktarım özellikli blob depolamaya yöneliktir.|
    |`SPARK_STORAGE_CONTAINER`|Spark kümesi için kullanılan varsayılan depolama kapsayıcısı adıyla değiştirin.|
    |`SPARK_STORAGE_ACCOUNT`|Spark kümesi için kullanılan varsayılan depolama hesabı adıyla değiştirin.|

    ```bash
    hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
    ```

2. Ardından, SSH bağlantınızı HBase kümenize çıkın.

    ```bash
    exit
    ```


3. SSH kullanarak Spark kümenizin baş düğümüne bağlanın. Aşağıdaki komutu `SPARKCLUSTER` Spark kümenizin adıyla değiştirerek düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

4. `hbase-site.xml`Spark kümenizin varsayılan depolama alanından kümenin yerel depolama alanındaki Spark 2 yapılandırma klasörüne kopyalamak için aşağıdaki komutu girin:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spark HBase bağlayıcısına başvuran Spark kabuğunu Çalıştır

Önceki adımı tamamladıktan sonra Spark HBase bağlayıcısının uygun sürümüne başvurarak Spark kabuğunu çalıştırabilmelisiniz. Küme senaryonuz için en son uygun Spark HBase Bağlayıcısı temel sürümünü bulmak için bkz. [SHC Core Repository](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/).

Örnek olarak, aşağıdaki tabloda, HDInsight ekibinin Şu anda kullandığı iki sürüm ve ilgili komutlar listelenmektedir. HBase ve Spark sürümleri tabloda belirtilen şekilde aynıysa, kümeleriniz için de aynı sürümleri kullanabilirsiniz. 


1. Spark kümesine yönelik açık SSH oturumunda, bir Spark kabuğu başlatmak için aşağıdaki komutu girin:

    |Spark sürümü| HDI HBase sürümü  | SHC sürümü    |  Komut  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3,6 (HBase 1,1) | 1.1.1-2.1-s_2.11    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2.4    | HDI 4,0 (HBase 2,0) | 1.1.0.3.1.2.2-1  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. Bu Spark kabuğu örneğini açık tutun ve [bir katalog ve sorgu tanımlamaya](#define-a-catalog-and-query)devam edin. SHC çekirdek deposundaki sürümleriniz için karşılık gelen jar dosyaları dışındaki ' ı bulamazsanız okumaya devam edin. 

Jar dosyaları dışındaki 'ı doğrudan [Spark-HBase-Connector](https://github.com/hortonworks-spark/shc) GitHub dalından oluşturabilirsiniz. Örneğin, Spark 2,3 ve HBase 1,1 ile çalıştırıyorsanız, şu adımları izleyin:

1. Depoyu kopyalama:

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. Dala git-2,3:

    ```bash
    git checkout branch-2.3
    ```

3. Daldan oluştur (. jar dosyası oluşturur):

    ```bash
    mvn clean package -DskipTests
    ```
    
3. Aşağıdaki komutu çalıştırın (oluşturduğunuz. jar dosyasına karşılık gelen. jar adını değiştirdiğinizden emin olun):

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. Bu Spark kabuğu örneğini açık tutun ve sonraki bölüme devam edin. 



## <a name="define-a-catalog-and-query"></a>Katalog ve sorgu tanımlama

Bu adımda, Apache Spark şemayı Apache HBase ile eşleyen bir katalog nesnesi tanımlarsınız.  

1. Açık Spark kabuğunuzun aşağıdaki `import` deyimlerini girin:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. HBase 'de oluşturduğunuz kişiler tablosu için bir katalog tanımlamak üzere aşağıdaki komutu girin:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    Kod:  

    1. Adlı HBase tablosu için bir katalog şeması tanımlar `Contacts` .  
    1. Rowkey 'i olarak tanımlar `key` ve Spark içinde kullanılan sütun adlarını, HBase 'de kullanılan sütun ailesi, sütun adı ve sütun türüne eşleyin.  
    1. Rowkey ' `rowkey` i belirli bir sütun ailesine sahip olan adlandırılmış bir sütun () olarak ayrıntılı şekilde tanımlar `cf` `rowkey` .  

1. HBase 'de tablonuzun etrafında bir veri çerçevesi sağlayan bir yöntemi tanımlamak için aşağıdaki komutu girin `Contacts` :

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Veri çerçevesinin bir örneğini oluşturun:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Veri çerçevesini sorgulama:

    ```scala
    df.show()
    ```

    İki satırlık veri görmeniz gerekir:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Spark SQL kullanarak HBase tablosunu sorgulayabilmeniz için geçici bir tablo kaydedin:

    ```scala
    df.createTempView("contacts")
    ```

1. Tabloya karşı bir SQL sorgusu verme `contacts` :

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Şöyle bir sonuç görmeniz gerekir:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Yeni veri Ekle

1. Yeni bir kişi kaydı eklemek için bir sınıf tanımlayın `ContactRecord` :

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Bir örneği oluşturun `ContactRecord` ve bir diziye koyun:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Yeni veri dizisini HBase 'e Kaydet:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Sonuçları inceleyin:

    ```scala  
    df.show()
    ```

    Şunun gibi bir çıktı görmeniz gerekir:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Aşağıdaki komutu girerek Spark kabuğunu kapatın:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark HBase Bağlayıcısı](https://github.com/hortonworks-spark/shc)
