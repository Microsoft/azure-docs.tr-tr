---
title: HDInsight 'ta Apache Phoenix-Azure HDInsight
description: Apache Phoenix genel bakış
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: cb5230ae42703d19726fb8ea0d6c88aa70e589a8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864151"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Phoenix

[Apache Phoenix](https://phoenix.apache.org/) , [Apache HBase](hbase/apache-hbase-overview.md)üzerinde oluşturulmuş açık kaynaklı, yüksek düzeyde paralel ilişkisel veritabanı katmanıdır. Phoenix, HBase üzerinde SQL benzeri sorgular kullanmanıza olanak sağlar. Phoenix, kullanıcıların SQL tabloları, dizinler, görünümler ve diziler oluşturma, silme, değiştirme ve tek tek ve toplu olarak satır oluşturma, silme, değiştirme ve silme gibi bir JDBC sürücülerini kullanır. Phoenix, sorguları derlemek için MapReduce kullanmak yerine noSQL yerel derlemesini kullanır ve bu, HBase üzerinde düşük gecikmeli uygulamalar oluşturulmasını sağlar. Phoenix, istemci tarafından sağlanan kodu sunucunun adres alanında çalıştırmayı desteklemek için ortak işlemciler ekler ve kodun verilerle birlikte yürütülmesi sağlanır. Bu yaklaşım, istemci/sunucu veri aktarımını en aza indirir.

Apache Phoenix, programlama yerine SQL benzeri bir sözdizimi kullanabilecek geliştiricilere büyük veri sorguları açar. Phoenix, [Apache Hive](hadoop/hdinsight-use-hive.md) ve SQL Apache Spark gibi diğer araçların aksine HBase için yüksek oranda iyileştirilmiştir. Geliştiricilerin avantajı çok daha az kod ile yüksek performanslı sorgular yazıyor.

Bir SQL sorgusu gönderdiğinizde, Phoenix sorguyu HBase yerel çağrılarına derler ve taramayı (veya planı) iyileştirme için paralel olarak çalıştırır. Bu soyutlama katmanı, geliştiricilerin iş mantığı üzerine odaklanmak üzere MapReduce işleri yazmasını ve bu verilerin Phoenix 'in büyük veri depolaması etrafında iş akışını sağlar.

## <a name="query-performance-optimization-and-other-features"></a>Sorgu performansı iyileştirmesi ve diğer özellikler

Apache Phoenix, HBase sorgularına birçok performans geliştirmesi ve özelliği ekler.

### <a name="secondary-indexes"></a>İkincil dizinler

HBase 'in birincil satır anahtarında lexıgrafik olarak sıralanan tek bir dizini vardır. Bu kayıtlara yalnızca satır anahtarı üzerinden erişilebilir. Satır anahtarı dışında herhangi bir sütun aracılığıyla kayıtlara erişilmesi gereken filtre uygulanırken tüm verilerin taranmasını gerektirir. İkincil bir dizinde, dizine eklenen sütunlar veya ifadeler, bu dizinde aramalar ve Aralık taramalarına olanak sağlayan alternatif bir satır anahtarı oluşturur.

Komutuyla ikincil dizin oluşturun `CREATE INDEX` :

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Bu yaklaşım, tek dizinli sorguları yürütmek için önemli bir performans artışı elde edebilir. Bu tür ikincil dizin, sorguya dahil edilen tüm sütunları içeren **kapsayan bir dizindir**. Bu nedenle, tablo arama gerekli değildir ve dizin tüm sorguyu karşılar.

### <a name="views"></a>Görünümler

Phoenix görünümleri, yaklaşık 100 fiziksel tablo oluştururken performansın düşmeye başladığı bir HBase sınırlamasını aşmak için bir yol sağlar. Phoenix görünümleri birden çok *sanal tablonun* temeldeki bir fiziksel HBase tablosunu paylaşmasını sağlar.

Phoenix görünümü oluşturmak, standart SQL görünümü sözdizimi kullanmaya benzer. Bunun farkı, kendi temel tablosundan devralınan sütunlara ek olarak görünümünizin sütunlarını tanımlayabilmeniz gerektiğidir. Ayrıca yeni `KeyValue` sütunlar ekleyebilirsiniz.

Örneğin, aşağıdaki tanımla adlı bir fiziksel tablo aşağıda verilmiştir `product_metrics` :

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Bu tablo üzerinde ek sütunlarla bir görünüm tanımlayın:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Daha sonra daha fazla sütun eklemek için, `ALTER VIEW` ifadesini kullanın.

### <a name="skip-scan"></a>Taramayı atla

Atlama taraması, farklı değerleri bulmak için bileşik dizinin bir veya daha fazla sütununu kullanır. Bir Aralık taramasından farklı olarak, atlama taraması, satır içi tarama uygular ve [Gelişmiş performans](https://phoenix.apache.org/performance.html#Skip-Scan)sağlar. Tarama sırasında, sonraki değer bulunana kadar, eşleşen ilk değer dizinle birlikte atlanır.

Bir atlama taraması `SEEK_NEXT_USING_HINT` HBase filtresinin sabit listesini kullanır. `SEEK_NEXT_USING_HINT`' Yi kullanarak, atlama taraması, her sütunda hangi anahtar veya anahtar aralıklarının arandığını izler. Atlama taraması, filtre değerlendirmesi sırasında kendisine geçirilmiş bir anahtar alır ve birleşimlerden birinin olup olmadığını belirler. Aksi takdirde, atlama taraması, sonraki en yüksek anahtarı atlanacak şekilde değerlendirir.

### <a name="transactions"></a>İşlemler

HBase, satır düzeyinde işlemler sağlarken, Phoenix, tam [ACID](https://en.wikipedia.org/wiki/ACID) semantiğine sahip çapraz satır ve çapraz tablo işlem desteği eklemek Için [tephra](https://tephra.io/) ile tümleşir.

Geleneksel SQL işlemlerinde olduğu gibi, Phoenix Transaction Manager aracılığıyla sağlanan işlemler, atomik bir veri biriminin başarılı bir şekilde geri alınmasını güvence altına aldığınızdan ve bu işlem, işlem etkinleştirilmiş herhangi bir tabloda, upsert işlem başarısız olursa işlem geri alınıyor.

Phoenix işlemlerini etkinleştirmek için [Apache Phoenix işlem belgelerine](https://phoenix.apache.org/transactions.html)bakın.

İşlemleri etkin olan yeni bir tablo oluşturmak için, `TRANSACTIONAL` özelliğini `true` bir ifadesinde olarak ayarlayın `CREATE` :

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Mevcut bir tabloyu işlemsel olacak şekilde değiştirmek için, bir ifadede aynı özelliği kullanın `ALTER` :

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> İşlem tablosunu işlemsel olmayan şekilde geri geçirebilirsiniz.

### <a name="salted-tables"></a>Sallanan tablolar

*Bölge sunucusu hotspool* , sıralı anahtarlarla HBase 'e sahip kayıtları yazarken meydana gelebilir. Kümenizde birden çok bölge sunucusu olsa da, yazmaların hepsi yalnızca bir tane üzerinde gerçekleşmiş olabilir. Bu yoğunlaşma, yazma iş yükünüzün tüm kullanılabilir bölge sunucularında dağıtılması yerine, yükü işleme sürecinde olan, en az bir sorun oluşturur. Her bölge önceden tanımlanmış en büyük boyuta sahip olduğundan, bir bölge bu boyut sınırına ulaştığında, iki küçük bölgeye ayrılır. Söz konusu olduğunda, bu yeni bölgelerden biri yeni etkin nokta haline gelir.

Bu sorunu azaltmak ve tüm bölge sunucularının eşit olarak kullanılması için daha iyi bir performans, önceden bölünmüş tablolar elde etmek. Phoenix, belirli bir tablonun satır anahtarına saydam bir şekilde sallayan bayt ekleyerek, *sallanan tablolar* sağlar. Tablo, tablonun ilk aşamasında bölge sunucuları arasında eşit yük dağıtımına olanak sağlamak için, salt, anahtar bayt sınırlarına önceden bölünür. Bu yaklaşım, yazma iş yükünü tüm kullanılabilir bölge sunucularında dağıtır, yazma ve okuma performansını geliştirir. Bir tabloyu almak için tablo `SALT_BUCKETS` oluşturulduğunda tablo özelliğini belirtin:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Apache ambarı ile Phoenix 'i etkinleştirme ve ayarlama

An HDInsight HBase kümesi, yapılandırma değişiklikleri yapmak için [ambarı Kullanıcı arabirimini](hdinsight-hadoop-manage-ambari.md) içerir.

1. Phoenix 'i etkinleştirmek veya devre dışı bırakmak ve Phoenix 'in sorgu zaman aşımı ayarlarını denetlemek için, `https://YOUR_CLUSTER_NAME.azurehdinsight.net` Hadoop Kullanıcı kimlik bilgilerinizi kullanarak ambarı Web Kullanıcı arabiriminde () oturum açın.

2. Sol taraftaki menüdeki hizmetler listesinden **HBase** ' i seçin, sonra da **configs** sekmesini seçin.

    :::image type="content" source="./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png" alt-text="Apache ambarı HBase yapılandırması":::

3. Phoenix 'i etkinleştirmek veya devre dışı bırakmak için **PHOENIX SQL** yapılandırma bölümünü bulun ve sorgu zaman aşımını ayarlayın.

    :::image type="content" source="./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png" alt-text="Ambarı Phoenix SQL yapılandırması bölümü":::

## <a name="see-also"></a>Ayrıca bkz.

* [HDInsight 'ta Linux tabanlı HBase kümeleriyle Apache Phoenix kullanma](hbase/apache-hbase-query-with-phoenix.md)

* [Azure HDInsight 'ta Apache HBase üzerinde Apache Phoenix sorguları çalıştırmak için Apache Zeppelin kullanma](./hbase/apache-hbase-phoenix-zeppelin.md)
