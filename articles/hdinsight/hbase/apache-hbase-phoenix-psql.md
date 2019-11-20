---
title: Psql-Azure HDInsight kullanarak Apache Phoenix toplu yükleme
description: Azure HDInsight 'ta Apache Phoenix tablolarına toplu yükleme verileri yüklemek için psql aracını kullanma
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: f00f6bcf07cbdc5aeaeb04aeccf7e88cf4822dbf
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311716"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Psql kullanarak Apache Phoenix’e toplu veri yükleme

[Apache Phoenix](https://phoenix.apache.org/) , [Apache HBase](../hbase/apache-hbase-overview.md)üzerinde oluşturulmuş açık kaynaklı, yüksek düzeyde paralel ilişkisel veritabanıdır. Phoenix, HBase üzerinde SQL benzeri sorgular sağlar. Phoenix, kullanıcıların SQL tabloları, dizinler, görünümler ve diziler oluşturma, silme ve değiştirme ve satırları tek tek ve toplu olarak değiştirme olanağı sağlamak için JDBC sürücülerini kullanır. Phoenix, HBase üzerinde düşük gecikme süreli uygulamalar oluşturmak için MapReduce kullanarak sorguları derlemek yerine noSQL yerel derlemesini kullanır. Phoenix, verilerle birlikte bulunan kodu yürüterek, istemci tarafından sağlanan kodu çalıştırmanın sunucu adres alanında çalıştırmayı desteklemek için ortak işlemciler ekler. Bu, istemci/sunucu veri aktarımını en aza indirir.  HDInsight 'ta Phoenix 'i kullanarak verilerle çalışmak için, önce tablolar oluşturun ve ardından bunlara veri yükleyin.

## <a name="bulk-loading-with-apache-phoenix"></a>Apache Phoenix ile toplu yükleme

İstemci API 'Leri kullanma, TableOutputFormat ile MapReduce işi veya HBase kabuğu kullanarak verileri el ile almak dahil olmak üzere HBase 'e veri almanın birden çok yolu vardır. Phoenix, CSV verilerini Phoenix tablolarına yüklemek için iki yöntem sunar: `psql` adlı istemci yükleme aracı ve MapReduce tabanlı toplu yükleme aracı.

@No__t-0 aracı tek iş parçacıklıdır ve megabayt veya gigabayt veri yüklemesi için idealdir. Yüklenecek tüm CSV dosyaları '. csv ' dosya uzantısına sahip olmalıdır.  Ayrıca, '. SQL ' dosya uzantısına sahip `psql` komut satırında SQL komut dosyaları belirtebilirsiniz.

MapReduce ile toplu yükleme, genellikle üretim senaryolarında, MapReduce birden çok iş parçacığı kullandığında çok daha büyük veri birimleri için kullanılır.

Verileri yüklemeye başlamadan önce, Phoenix 'in etkinleştirildiğini ve sorgu zaman aşımı ayarlarının beklenen şekilde olduğunu doğrulayın.  HDInsight kümesi [Apache ambarı](https://ambari.apache.org/) panonuza erişin, HBase ' i ve ardından yapılandırma sekmesini seçin.  Apache Phoenix gösterildiği gibi `enabled` olarak ayarlandığını doğrulamak için aşağı kaydırın:

![HDInsight kümesi ayarlarını Apache Phoenix](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Tabloları toplu olarak yüklemek için `psql` kullanın

1. Yeni bir tablo oluşturun ve sonra sorgunuzu @no__t dosya adı ile kaydedin-0.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Yeni oluşturduğunuz tablonuza yüklemek üzere CSV dosyanızı (gösterilen örnek içerikler) `customers.csv` ' a `/tmp/` dizinine kopyalayın.  CSV dosyasını istediğiniz kaynak konumunuza kopyalamak için `hdfs` komutunu kullanın.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Giriş verilerinin düzgün yüklendiğini doğrulamak için bir SQL SELECT sorgusu oluşturun, sonra sorgunuzu `listCustomers.sql` dosya adıyla kaydedin. Herhangi bir SQL sorgusu kullanabilirsiniz.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. *Yeni* bir Hadoop komut penceresi açarak verileri toplu olarak yükleyin. İlk olarak `cd` komutuyla yürütme dizini konumuna geçin ve ardından `psql` aracını (Python `psql.py` komutu) kullanın. 

    Aşağıdaki örnek, yukarıdaki adım 2 ' de olduğu gibi, bir depolama hesabındaki `customers.csv` dosyasını `hdfs` kullanarak yerel geçici dizininize kopyalamalarından bekler.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > @No__t-0 adını belirlemek için, `hbase.zookeeper.quorum` özellik adı ile `/etc/hbase/conf/hbase-site.xml` dosyasında [Apache ZooKeeper](https://zookeeper.apache.org/) çekirdek dizesini bulun.

5. @No__t-0 işlemi tamamlandıktan sonra, komut pencerenizde bir ileti görmeniz gerekir:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>MapReduce kullanarak tabloları toplu yükleme

Küme üzerinden dağıtılmış daha yüksek aktarım hızı için MapReduce yükleme aracını kullanın. Bu yükleyici önce tüm verileri HFiles 'a dönüştürür ve sonra oluşturulan HFiles 'ı HBase 'e sağlar.

1. Phoenix istemci jar ile `hadoop` komutunu kullanarak CSV MapReduce yükleyicisini başlatın:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Yukarıdaki 1. adımda `CreateCustomersTable.sql` ile birlikte SQL ifadesiyle yeni bir tablo oluşturun.

3. Tablonuzun şemasını doğrulamak için `!describe inputTable` ' ı çalıştırın.

4. Örnek `customers.csv` dosyası gibi giriş verilerinize ait konum yolunu saptayın. Giriş dosyaları, KADB/ADLS depolama hesabınızda olabilir. Bu örnek senaryoda, giriş dosyaları `<storage account parent>/inputFolderBulkLoad` dizininde bulunur.

5. MapReduce toplu yükleme komutu için yürütme dizinine geçin:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. @No__t-1 ' de `ZookeeperQuorum` değerini, özellik adı `hbase.zookeeper.quorum` ile bulun.

7. Sınıfyolunu ayarlayın ve `CsvBulkLoadTool` araç komutunu çalıştırın:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. MapReduce 'yi Azure Data Lake Storage kullanmak için, `hbase-site.xml` ' deki `hbase.rootdir` değeri olan Data Lake Storage kök dizinini bulun. Aşağıdaki komutta Data Lake Storage kök dizin `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1` ' dır. Bu komutta, Data Lake Storage giriş ve çıkış klasörlerini parametre olarak belirtin:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Öneriler

* Hem giriş hem de çıkış klasörleri (Azure depolama (ISB) veya Azure Data Lake Storage (ADL) için aynı depolama ortamını kullanın. Azure depolama 'dan Data Lake Storage verileri aktarmak için `distcp` komutunu kullanabilirsiniz:

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
