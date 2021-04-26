---
title: Azure HDInsight 'ta Apache HBase nedir?
description: Hadoop’ta oluşturulan bir NoSQL veritabanı olan HDInsight’ta Apache HBase’e giriş. Kullanım örnekleri hakkında bilgi edinin ve HBase’i diğer Hadoop kümeleriyle karşılaştırın.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 98872cc1315d946c63825318d2b98460031e128a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942911"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache HBase nedir?

[Apache HBase](https://hbase.apache.org/) , Apache Hadoop oluşturulan ve [Google BigTable](https://cloud.google.com/bigtable/)'dan sonra Modellenen açık kaynaklı, NoSQL veritabanıdır. HBase, daha seyrek erişimli bir veritabanında büyük miktarlarda veri için rastgele erişim ve güçlü tutarlılık sağlar. Veritabanı sütun ailelerine göre düzenlenir.

Kullanıcı perspektifinden HBase veritabanına benzer. Veriler bir tablonun satırları ve sütunlarında depolanır ve bir satırdaki veriler sütun ailesine göre gruplandırılır. HBase, şesız bir veritabanıdır. Sütunlar ve veri türleri kullanılmadan önce tanımsız olabilir. Açık kaynak kodu, binlerce düğümdeki petabaytlarca verileri işlemek için doğrusal olarak ölçeklendirir. Bu, Hadoop ortamında dağıtılmış uygulamalar tarafından sunulan veri yedekliliği, toplu işleme ve diğer özelliklere güvenebilirler.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache HBase nasıl uygulanır?

HDInsight HBase, Azure ortamına tümleştirilmiş yönetilen bir küme olarak sunulur. Kümeler, düşük gecikme süresi ve performans ve maliyet seçeneklerinde artan esneklik sağlayan verileri doğrudan [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md)'da depolayacak şekilde yapılandırılmıştır. Bu özellik, müşterilerin büyük veri kümeleriyle çalışan etkileşimli Web siteleri oluşturmasına olanak sağlar. Milyonlarca uç noktadan algılayıcı ve telemetri verilerini depolayan hizmetler oluşturmak için. Ve bu verileri Hadoop işleriyle analiz edin. HBase ve Hadoop, Azure 'da büyük veri projesi için iyi başlangıç noktalarıdır. Hizmetler gerçek zamanlı uygulamaların büyük veri kümeleriyle çalışmasını sağlayabilir.

HDInsight uygulama, tabloların otomatik olarak parçalara ilmesi için HBase 'in genişleme mimarisini kullanır. Ve okuma ve yazma işlemleri için güçlü tutarlılık ve otomatik yük devretme. Performans, okumalar için bellek içi önbelleğe alma ve yazmalar için yüksek verimlilikli akış tarafından geliştirilmiştir. HBase kümesi sanal ağda oluşturulabilir. Ayrıntılar için bkz. [Azure Sanal Ağ'da HDInsight kümeleri oluşturma](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Veriler HDInsight HBase’de nasıl yönetilir?

Veriler HBase kabuğunda `create`, `get`, `put` ve `scan` komutları kullanılarak HBase tarafından yönetilebilir. Veriler `put` kullanılarak veritabanına yazılır ve `get` kullanarak okunur. `scan` komutu, bir tablodaki birden çok satırdaki verileri almak için kullanılır. Veriler, HBase REST API’sinin üstünde bir istemci kitaplığı sağlayan HBase C# API’si kullanılarak da yönetilebilir. HBase veritabanı, [Apache Hive](https://hive.apache.org/)kullanılarak da sorgulanabilir. Bu programlama modellerine giriş için bkz. [HDInsight 'ta Apache Hadoop Ile Apache HBase kullanmaya başlama](./apache-hbase-tutorial-get-started-linux.md). Aynı zamanda, veritabanını barındıran düğümlerde veri işlemeye izin veren de mevcuttur.

> [!NOTE]  
> Thrift, HDInsight’ta HBase tarafından desteklenmez.

## <a name="use-cases-for-apache-hbase"></a>Apache HBase için kullanım örnekleri

Web aramadan BigTable (ve uzantısı ile HBase) oluşturulan kurallı kullanım durumu. Arama motorları terimleri bunları içeren web siteleriyle eşleştiren dizinler oluşturur. Ancak HBase için uygun olan diğer birçok kullanım örneği vardır; bunların birkaçı bu bölümde listelenmektedir.

|Senaryo |Açıklama |
|---|---|
|Anahtar değeri deposu|HBase anahtar-değer deposu olarak kullanılabilir ve ileti sistemlerini yönetmek için uygundur. Facebook, kendi mesajlaşma sistemleri için HBase kullanır ve Internet iletişimlerini depolamak ve yönetmek için idealdir. WebTable web sayfalarından çıkarılan tabloları aramak ve yönetmek için HBase kullanır.|
|Algılayıcı verileri|HBase çeşitli kaynaklardan artımlı olarak toplanan verileri yakalamak için yararlıdır. Bu veriler sosyal çözümlemeler ve zaman serisini içerir. Eğilimleri ve sayaçları ve denetim günlüğü sistemlerini yönetmek için etkileşimli panoları güncel tutun. Örnek olarak Bloomberg tüccar terminali ve açık zaman serisi veritabanı (opentsdb) verilebilir. OpenTSDB depolar ve sunucu sistemlerinin sistem durumu hakkında toplanan ölçümlere erişim sağlar.|
|Gerçek zamanlı sorgu|[Apache Phoenix](https://phoenix.apache.org/) , Apache HBase IÇIN bir SQL sorgu altyapısıdır. Bir JDBC sürücüsü olarak erişilir ve SQL kullanarak HBase tablolarının sorgulanmasına ve yönetilmesine izin vermez.|
|Bir platform olarak HBase|Uygulamalar, bir veri deposu olarak kullanarak HBase’in üstünde çalışabilir. Phoenix, OpenTSDB, `Kiji` ve Titan örnekleri sayılabilir. Uygulamalar HBase ile de tümleştirebilir. Örnekler şunlardır: [Apache Hive](https://hive.apache.org/), Apache Pig, [solr](https://lucene.apache.org/solr/), Apache Storm, Apache flome, [Apache Impala](https://impala.apache.org/), Apache Spark, `Ganglia` ve Apache detaya gitme.|

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta Apache Hadoop ile Apache HBase kullanmaya başlama](./apache-hbase-tutorial-get-started-linux.md)
* [Azure Sanal Ağ'da HDInsight kümeleri oluşturma](./apache-hbase-provision-vnet.md)
* [HDInsight 'ta Apache HBase çoğaltmasını yapılandırma](apache-hbase-replication.md)
