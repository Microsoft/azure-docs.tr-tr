---
title: Apache Hive ve HiveQL nedir-Azure HDInsight
description: Apache Hive, Apache Hadoop için bir veri ambarı sistemidir. Hive içinde depolanan verileri, Transact-SQL ' e benzer HiveQL kullanarak sorgulayabilirsiniz. Bu belgede, Azure HDInsight ile Hive ve HiveQL kullanmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: 4e8c6b25055dfc38d56509e1744b8c7fcac40700
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944299"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Azure HDInsight 'ta Apache Hive ve HiveQL nedir?

[Apache Hive](https://hive.apache.org/) , Apache Hadoop için bir veri ambarı sistemidir. Hive, verilerin özetlenmesi, sorgulanmasına ve analizine izin vermez. Hive sorguları, SQL 'e benzer bir sorgu dili olan HiveQL 'e yazılır.

Hive büyük ölçüde yapılandırılmamış veriler üzerinde proje yapısı oluşturmanızı sağlar. Yapıyı tanımladıktan sonra, bir Java veya MapReduce bilgisi olmadan verileri sorgulamak için HiveQL kullanabilirsiniz.

HDInsight, belirli iş yükleri için ayarlanmış çeşitli küme türleri sağlar. Aşağıdaki küme türleri genellikle Hive sorguları için kullanılır:

|Küme türü |Description|
|---|---|
|Interactive Query|Etkileşimli sorguların yanıt sürelerini geliştirmek için [düşük gecikme süreli analitik işleme (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) işlevselliği sağlayan bir Hadoop kümesi. Daha fazla bilgi için bkz. [HDInsight 'Ta etkileşimli sorguya başlama](../interactive-query/apache-interactive-query-get-started.md) belgesi.|
|Hadoop|Toplu işleme iş yükleri için ayarlanmış bir Hadoop kümesi. Daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop kullanmaya başlama](../hadoop/apache-hadoop-linux-tutorial-get-started.md) belgesi.|
|Spark|Apache Spark Hive ile çalışmaya yönelik yerleşik işlevlere sahiptir. Daha fazla bilgi için bkz. [HDInsight 'ta Apache Spark kullanmaya başlama](../spark/apache-spark-jupyter-spark-sql.md) belgesi.|
|HBase|HiveQL, Apache HBase 'de depolanan verileri sorgulamak için kullanılabilir. Daha fazla bilgi için bkz. [HDInsight 'Ta Apache HBase Ile başlama](../hbase/apache-hbase-tutorial-get-started-linux.md) belgesi.|

## <a name="how-to-use-hive"></a>Hive kullanma

HDInsight ile Hive kullanmanın farklı yollarını saptamak için aşağıdaki tabloyu kullanın:

| İsterseniz **Bu yöntemi kullanın** ... | ... **etkileşimli** sorgular | ... **toplu** işleme | ... Bu **istemci işletim sisteminden** |
|:--- |:---:|:---:|:--- |:--- |
| [Visual Studio Code için HDInsight araçları](../hdinsight-for-vscode.md) |✔ |✔ | Linux, Unix, Mac OS X veya Windows |
| [Visual Studio için HDInsight araçları](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Hive Görünümü](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Herhangi biri (tarayıcı tabanlı) |
| [Beeline istemci](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, Unix, Mac OS X veya Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, Unix, Mac OS X veya Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>HiveQL dil başvurusu

HiveQL dil başvurusu, [dil el ile](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)kullanılabilir.

## <a name="hive-and-data-structure"></a>Hive ve veri yapısı

Hive, yapılandırılmış ve yarı yapılandırılmış verilerle çalışmayı anlamıştır. Örneğin, alanların belirli karakterler tarafından sınırlandırıldığından metin dosyaları. Aşağıdaki HiveQL deyimleri boşlukla ayrılmış veriler üzerinde bir tablo oluşturur:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive Ayrıca, karmaşık veya Iris olarak yapılandırılmış veriler için özel **seri hale getirici/Desericiler (serde)** destekler Daha fazla bilgi için bkz. [HDInsight ile özel JSON SerDe kullanımı](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) belgesi.

Hive tarafından desteklenen dosya biçimleri hakkında daha fazla bilgi için bkz. [dil el kitabı https://cwiki.apache.org/confluence/display/Hive/LanguageManual) (](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Hive iç tabloları ve dış tablolar

Hive ile oluşturabileceğiniz iki tür tablo vardır:

* __İç__: veriler Hive veri ambarında depolanır. Veri ambarı, `/hive/warehouse/` kümenin varsayılan depolama alanında bulunur.

    Aşağıdaki koşullardan biri geçerli olduğunda iç tabloları kullanın:

    * Veriler geçicidir.
    * Hive 'ın tablo ve veri yaşam döngüsünü yönetmesini istiyorsunuz.

* __Harici__: veriler veri ambarının dışında saklanır. Veriler, küme tarafından erişilebilen herhangi bir depolama alanında depolanabilir.

    Aşağıdaki koşullardan biri geçerli olduğunda dış tabloları kullanın:

    * Veriler, Hive dışında da kullanılır. Örneğin, veri dosyaları başka bir işlem tarafından güncelleştirilir (dosyaları kilitlemez.)
    * Verilerin, tablo bırakıldıktan sonra bile temeldeki konumda kalması gerekir.
    * Varsayılan olmayan bir depolama hesabı gibi özel bir konuma ihtiyacınız vardır.
    * Hive dışında bir program veri biçimini, konumunu ve benzerlerini yönetir.

Daha fazla bilgi için [Hive iç ve dış tablolar giriş](/archive/blogs/cindygross/hdinsight-hive-internal-and-external-tables-intro) blog gönderisine bakın.

## <a name="user-defined-functions-udf"></a>Kullanıcı tanımlı işlevler (UDF)

Hive, **Kullanıcı tanımlı işlevler (UDF)** aracılığıyla da genişletilebilir. UDF, HiveQL içinde kolayca Modellenmemiş işlevselliği veya mantığı uygulamanıza olanak tanır. Hive ile UDF kullanma örneği için aşağıdaki belgelere bakın:

* [Apache Hive ile Java Kullanıcı tanımlı bir işlev kullanma](../hadoop/apache-hadoop-hive-java-udf.md)

* [Apache Hive ile Kullanıcı tanımlı Python işlevi kullanma](../hadoop/python-udf-hdinsight.md)

* [Apache Hive ile C# Kullanıcı tanımlı bir işlev kullanma](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [HDInsight 'a özel Apache Hive Kullanıcı tanımlı bir işlev ekleme](/archive/blogs/bigdatasupport/how-to-add-custom-hive-udfs-to-hdinsight)

* [Bir örnek Apache Hive tarih/saat biçimlerini Hive zaman damgasına dönüştürmek için Kullanıcı tanımlı işlev](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Örnek veriler

HDInsight 'ta Hive adlı bir iç tabloyla önceden yüklenmiş olarak gelir `hivesampletable` . HDInsight, Hive ile kullanılabilecek örnek veri kümeleri de sağlar. Bu veri kümeleri `/example/data` ve `/HdiSamples` dizinlerinde depolanır. Bu dizinler, kümenizin varsayılan depolamada bulunur.

## <a name="example-hive-query"></a>Örnek Hive sorgusu

Aşağıdaki HiveQL deyimleri dosyası üzerine proje sütunları `/example/data/sample.log` :

```hiveql
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
    GROUP BY t4;
```

Önceki örnekte, HiveQL deyimleri aşağıdaki eylemleri gerçekleştirir:

|Deyim |Açıklama |
|---|---|
|DROP TABLE|Tablo zaten varsa, silin.|
|DıŞ TABLO OLUŞTUR|Hive içinde yeni bir **dış** tablo oluşturur. Dış tablolar yalnızca tablo tanımını Hive içinde depolar. Veriler özgün konumunda ve özgün biçimde bırakılır.|
|SATıR BIÇIMI|Hive verilerin nasıl biçimlendirildiğini söyler. Bu durumda, her günlükteki alanlar boşlukla ayrılır.|
|TEXTFILE KONUMU OLARAK DEPOLANDı|Hive verilerinin nerede depolandığını ( `example/data` Dizin) ve metin olarak depolandığını söyler. Veriler tek bir dosyada olabilir veya dizin içindeki birden çok dosyaya yayılabilir.|
|SELECT|**T4** sütununun **[Error]** değerini içerdiği tüm satırların sayısını seçer. Bu değer içeren üç satır olduğundan, bu ifade **3** değerini döndürür.|
|'%. Log ' gıbı INPUT__FILE__NAME|Hive, şemayı dizindeki tüm dosyalara uygulamayı dener. Bu durumda, Dizin şemayla eşleşmeyen dosyaları içerir. Sonuçlarda çöp verilerinin oluşmasını engellemek için, bu ifade Hive 'ye yalnızca. log ile biten dosyalardan veri döndürmemiz gerektiğini söyler.|

> [!NOTE]  
> Dış tablolar, temel alınan verilerin bir dış kaynak tarafından güncelleştirilmesini beklediğinde kullanılmalıdır. Örneğin, otomatik bir veri yükleme işlemi veya MapReduce işlemi.
>
> Dış tablonun atılması, verileri **silmez,** yalnızca tablo tanımını siler.

Dış yerine **iç** tablo oluşturmak Için aşağıdaki HiveQL kullanın:

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Bu deyimler aşağıdaki eylemleri gerçekleştirir:

|Deyim |Açıklama |
|---|---|
|MEVCUT DEĞILSE CREATE TABLE|Tablo yoksa, oluşturun. **Dış** anahtar sözcük kullanılmadığından, bu ifade bir iç tablo oluşturur. Tablo Hive veri ambarında depolanır ve tamamen Hive tarafından yönetilir.|
|ORC OLARAK DEPOLANıR|Verileri Iyileştirilmiş satır sütunlu (ORC) biçimde depolar. ORC, Hive verilerinin depolanması için yüksek düzeyde iyileştirilmiş ve etkili bir biçimdir.|
|ÜZERINE YAZMA EKLE... SEÇIN|**Log4jLogs** tablosundan **[Error]** içeren satırları seçer ve ardından verileri **errorlogs** tablosuna ekler.|

> [!NOTE]  
> Dış tablolardan farklı olarak, iç tablo bırakılırken temel alınan veriler de silinir.

## <a name="improve-hive-query-performance"></a>Hive sorgu performansını geliştirme

### <a name="apache-tez"></a>Apache Tez

[Apache tez](https://tez.apache.org) , Hive gibi veri kullanımı yoğun uygulamaların ölçeğe çok daha verimli bir şekilde çalışmasını sağlayan bir çerçevedir. Tez varsayılan olarak etkindir.  [Tez tasarım belgelerindeki Apache Hive](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) , uygulama seçimleri ve ayarlama yapılandırmalarına ilişkin ayrıntıları içerir.

### <a name="low-latency-analytical-processing-llap"></a>Düşük gecikme süreli analitik Işleme (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (bazen canlı uzun ve işlem olarak bilinir), Hive 2,0 ' de sorguların bellek içi önbelleğe alınmasına izin veren yeni bir özelliktir. LLAP, bazı durumlarda Hive sorgularını çok daha hızlı ve [Hive 1. x 'ten daha hızlı](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)hale getirir.

HDInsight etkileşimli sorgu kümesi türünde LLAP sağlar. Daha fazla bilgi için bkz. [etkileşimli sorgu belgesine başlama](../interactive-query/apache-interactive-query-get-started.md) .

## <a name="scheduling-hive-queries"></a>Hive sorgularını zamanlama

Hive sorgularını zamanlanan veya isteğe bağlı bir iş akışının parçası olarak çalıştırmak için kullanılabilecek çeşitli hizmetler vardır.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory, HDInsight 'ı Data Factory bir işlem hattının parçası olarak kullanmanıza olanak tanır. Bir işlem hattından Hive kullanma hakkında daha fazla bilgi için [Azure Data Factory belgesinde Hive kullanarak verileri dönüştürme etkinliği](../../data-factory/transform-data-using-hadoop-hive.md) bölümüne bakın.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive işleri ve SQL Server Integration Services

Hive işini çalıştırmak için SQL Server Integration Services (SSSıS) kullanabilirsiniz. SSIS için Azure Özellik paketi, HDInsight üzerinde Hive işlerinde çalışan aşağıdaki bileşenleri sağlar.

* [Azure HDInsight Hive görevi](/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Azure aboneliği bağlantı Yöneticisi](/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Daha fazla bilgi için bkz. [Azure Feature Pack](/sql/integration-services/azure-feature-pack-for-integration-services-ssis) belgeleri.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie, Hadoop işlerini yöneten bir iş akışı ve düzenleme sistemidir. Hive ile Oozie kullanma hakkında daha fazla bilgi için bkz. [bir iş akışı belgesi tanımlamak ve çalıştırmak Için Apache Oozie kullanma](../hdinsight-use-oozie-linux-mac.md) .

## <a name="next-steps"></a>Sonraki adımlar

Artık Hive 'nin ne olduğunu ve HDInsight 'ta Hadoop ile nasıl kullanılacağını öğrendiğinize göre, Azure HDInsight ile çalışmanın diğer yollarını incelemek için aşağıdaki bağlantıları kullanın.

* [HDInsight'a veri yükleme](../hdinsight-upload-data.md)
* [HDInsight 'ta Apache Hive ve Apache Pig ile Python Kullanıcı tanımlı Işlevleri (UDF) kullanma](./python-udf-hdinsight.md)
* [HDInsight ile MapReduce işleri kullanma](hdinsight-use-mapreduce.md)