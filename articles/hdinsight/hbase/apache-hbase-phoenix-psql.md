---
title: Psql-Azure HDInsight kullanarak Apache Phoenix toplu yükleme
description: Azure HDInsight 'ta Apache Phoenix tablolarına toplu yükleme verileri yüklemek için psql aracını kullanma
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: d1ddf69acda442212500200eb6dc326dcbcb3c1b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867806"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Psql kullanarak Apache Phoenix’e toplu veri yükleme

[Apache Phoenix](https://phoenix.apache.org/) , [Apache HBase](../hbase/apache-hbase-overview.md)üzerinde oluşturulmuş açık kaynaklı, yüksek düzeyde paralel ilişkisel veritabanıdır. Phoenix, HBase üzerinde SQL benzeri sorgular sağlar. Phoenix, kullanıcıların SQL tabloları, dizinler, görünümler ve diziler oluşturma, silme ve değiştirme ve satırları tek tek ve toplu olarak değiştirme olanağı sağlamak için JDBC sürücülerini kullanır. Phoenix, HBase üzerinde düşük gecikme süreli uygulamalar oluşturmak için MapReduce kullanarak sorguları derlemek yerine noSQL yerel derlemesini kullanır. Phoenix, istemci tarafından sağlanan kodu sunucunun adres alanında çalıştırmayı desteklemek için ortak işlemciler ekler ve kodun verilerle birlikte yürütülmesi sağlanır. Bu, istemci/sunucu veri aktarımını en aza indirir.  HDInsight 'ta Phoenix 'i kullanarak verilerle çalışmak için, önce tablolar oluşturun ve ardından bunlara veri yükleyin.

## <a name="bulk-loading-with-apache-phoenix"></a>Apache Phoenix ile toplu yükleme

İstemci API 'Leri kullanma, TableOutputFormat ile MapReduce işi veya HBase kabuğu kullanarak verileri el ile almak dahil olmak üzere HBase 'e veri almanın birden çok yolu vardır. Phoenix, CSV verilerini Phoenix tablolarına yüklemek için iki yöntem sunar: adlı istemci yükleme aracı `psql` ve MapReduce tabanlı toplu yükleme aracı.

`psql`Araç tek iş parçacıklı ve megabayt veya gigabayt veri yüklemesi için idealdir. Yüklenecek tüm CSV dosyaları '. csv ' dosya uzantısına sahip olmalıdır.  Ayrıca, `psql` komut satırında '. SQL ' dosya uzantısına sahıp SQL komut dosyalarını da belirtebilirsiniz.

MapReduce ile toplu yükleme, genellikle üretim senaryolarında, MapReduce birden çok iş parçacığı kullandığında çok daha büyük veri birimleri için kullanılır.

Verileri yüklemeye başlamadan önce, Phoenix 'in etkinleştirildiğini ve sorgu zaman aşımı ayarlarının beklenen şekilde olduğunu doğrulayın.  HDInsight kümesi [Apache ambarı](https://ambari.apache.org/) panonuza erişin, HBase ' i ve ardından yapılandırma sekmesini seçin.  Apache Phoenix gösterildiği gibi ayarlandığını doğrulamak için aşağı kaydırın `enabled` :

:::image type="content" source="./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png" alt-text="HDInsight kümesi ayarlarını Apache Phoenix" border="true":::

### <a name="use-psql-to-bulk-load-tables"></a>`psql`Tabloları toplu olarak yüklemek için kullanın

1. Adlı bir dosya oluşturun `createCustomersTable.sql` ve aşağıdaki kodu dosyasına kopyalayın. Sonra dosyayı kaydedin ve kapatın.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Adlı bir dosya oluşturun `listCustomers.sql` ve aşağıdaki kodu dosyasına kopyalayın. Sonra dosyayı kaydedin ve kapatın.

    ```sql
    SELECT * from Customers;
    ```

1. Adlı bir dosya oluşturun `customers.csv` ve aşağıdaki kodu dosyasına kopyalayın. Sonra dosyayı kaydedin ve kapatın.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Adlı bir dosya oluşturun `customers2.csv` ve aşağıdaki kodu dosyasına kopyalayın. Sonra dosyayı kaydedin ve kapatın.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Bir komut istemi açın ve dizini yeni oluşturulan dosyaların konumuyla değiştirin. Aşağıdaki CLUSTERNAME değerini, HBase kümenizin gerçek adıyla değiştirin. Ardından, dosyaları kümenizin yayın düğümüne yüklemek için kodu yürütün:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Kümenize bağlanmak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH oturumunuzda, dizini **psql** aracının konumuyla değiştirin. Aşağıdaki komutu yürütün:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Verileri toplu olarak yükleyin. Aşağıdaki kod, **müşteriler** tablosunu oluşturur ve sonra verileri karşıya yükler.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    İşlem tamamlandıktan sonra `psql` aşağıdakine benzer bir ileti görürsünüz:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. `psql`Müşteriler tablosunun içeriğini görüntülemek için kullanmaya devam edebilirsiniz. Aşağıdaki kodu yürütün:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Alternatif olarak, verileri sorgulamak için [HBase kabuğu](./query-hbase-with-hbase-shell.md)veya [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) kullanabilirsiniz.

1. Ek verileri karşıya yükleyin. Artık tablo zaten var olduğuna göre, komut tabloyu belirtir. Aşağıdaki komutu yürütün:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>MapReduce kullanarak tabloları toplu yükleme

Küme üzerinden dağıtılmış daha yüksek aktarım hızı için MapReduce yükleme aracını kullanın. Bu yükleyici önce tüm verileri HFiles 'a dönüştürür ve sonra oluşturulan HFiles 'ı HBase 'e sağlar.

1. Bu bölüm, SSH oturumu ve daha önce oluşturulan nesneler ile devam eder. Yukarıdaki adımları kullanarak gereken şekilde **müşteriler** tablosu ve **customers.csv** dosyasını oluşturun. Gerekirse, SSH bağlantınızı yeniden kurun.

1. **Müşteriler** tablosunun içeriğini kesin. Açık SSH oturumunuzla aşağıdaki komutları yürütün:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. `customers.csv`Dosyayı baş düğümüne 'dan Azure depolama 'ya kopyalayın.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. MapReduce toplu yükleme komutu için yürütme dizinine geçin:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Phoenix istemci jar ile komutunu kullanarak CSV MapReduce yükleyicisini başlatın `hadoop` :

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Karşıya yükleme tamamlandıktan sonra aşağıdakine benzer bir ileti görürsünüz:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. MapReduce 'yi Azure Data Lake Storage kullanmak için, içindeki değeri olan Data Lake Storage kök dizinini bulun `hbase.rootdir` `hbase-site.xml` . Aşağıdaki komutta, Data Lake Storage kök dizini olur `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1` . Bu komutta, Data Lake Storage giriş ve çıkış klasörlerini parametre olarak belirtin:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Verileri sorgulamak ve görüntülemek için, **psql** ' i daha önce açıklandığı gibi kullanabilirsiniz. Ayrıca, [HBase kabuğu](./query-hbase-with-hbase-shell.md)veya [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md)da kullanabilirsiniz.

## <a name="recommendations"></a>Öneriler

* Hem giriş hem de çıkış klasörleri (Azure depolama (ISB) veya Azure Data Lake Storage (ADL) için aynı depolama ortamını kullanın. Azure depolama 'dan Data Lake Storage verileri aktarmak için şu `distcp` komutu kullanabilirsiniz:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Daha büyük boyutlu çalışan düğümlerini kullanın. MapReduce toplu kopyasının harita işlemlerinde, kullanılabilir DFS olmayan alanı dolduran büyük miktarlarda geçici çıkış oluşur. Büyük miktarda toplu yükleme için, daha fazla ve daha fazla çalışan düğümü kullanın. Kümenize tahsis ettiğiniz çalışan düğümlerinin sayısı, işlem hızını doğrudan etkiler.

* Giriş dosyalarını ~ 10 GB öbeklere Böl. Toplu yükleme, depolama yoğun bir işlemdir, bu nedenle giriş dosyalarınızı birden çok öbekte bölmek daha iyi performans elde edilir.

* Bölge sunucusu etkin noktaları kullanmaktan kaçının. Satır anahtarınız tek bir şekilde artıyorsa, HBase sıralı yazma işlemleri bölge sunucusu delçesini alabilir. Satır anahtarını *sallamak* sıralı yazma işlemlerini azaltır. Phoenix, aşağıda Başvurulmuş gibi belirli bir tablo için bir salya bir bayt olan satır anahtarını saydam bir şekilde bir şekilde bir şekilde bir yoldan sağlar.

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Phoenix ile toplu veri yükleme](https://phoenix.apache.org/bulk_dataload.html)
* [HDInsight 'ta Linux tabanlı Apache HBase kümeleriyle Apache Phoenix kullanma](../hbase/apache-hbase-query-with-phoenix.md)
* [Sallanan tablolar](https://phoenix.apache.org/salted.html)
* [Apache Phoenix dilbilgisi](https://phoenix.apache.org/language/index.html)
