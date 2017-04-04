---
title: "HDInsight’ta HBase nedir? | Microsoft Belgeleri"
description: "Hadoop’ta oluşturulan bir NoSQL veritabanı olan HDInsight’ta Apache HBase’e giriş. Kullanım örnekleri hakkında bilgi edinin ve HBase’i diğer Hadoop kümeleriyle karşılaştırın."
keywords: bigtable,nosql,hbase nedir
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: d2a76d53-133a-4849-a30c-88d9c794391c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 45fb85f83570ed7d79e70dc6fb8f0fdabb7ea191
ms.lasthandoff: 03/24/2017


---
# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>HDInsight’ta HBase nedir: Hadoop için BigTable benzeri özellikler sağlayan bir NoSQL veritabanıdır.
Apache HBase, Hadoop’ta oluşturulan ve Google BigTable’a göre modellenen açık kaynaklı bir NoSQL veritabanıdır. HBase, sütun aileleri tarafından veritabanında büyük miktarlardaki yapılandırılmamış ve yarı yapılandırılmış veriler için rasgele erişim ve güçlü tutarlılık sağlar.

Veriler bir tablonun satırlarında depolanır ve satır içindeki veriler sütun ailesi tarafından gruplandırılır. HBase, kullanılmadan önce sütunların ya da bunlarda depolanan veri türünün tanımlanmasına gerek duyulmayan, şemasız bir veritabanıdır. Açık kaynak kodu, binlerce düğümdeki petabaytlarca verileri işlemek için doğrusal olarak ölçeklendirir. Veri yedekleme, toplu işleme ve Hadoop ekosistemindeki dağıtılmış uygulamalar tarafından sağlanan diğer özelliklere dayanabilir.

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Azure HDInsight’ta HBase nasıl uygulanır?
HDInsight HBase, Azure ortamına tümleştirilmiş yönetilen bir küme olarak sunulur. Kümeler, düşük gecikme süresi ve performans ve maliyet seçeneklerinde artan esneklik sağlayan, Azure Depolama’da verileri doğrudan depolamak için yapılandırılmıştır. Bu, müşterilerin milyonlarca uç noktadan gelen algılayıcı ve telemetri verilerini depolayan hizmetleri oluşturmak ve bu verileri Hadoop işleriyle çözümlemek üzere etkileşimli web siteleri oluşturmalarını sağlar. HBase ve Hadoop Azure’da büyük veri projeleri için yi başlangıç noktalarıdır; bunlar özellikle büyük veri kümeleriyle çalışmak üzere gerçek zamanlı uygulamaları etkinleştirebilir.

HDInsight uygulaması, tabloların otomatik parçalanmasını, okumalar ve yazmalar için yüksek tutarlılık ve otomatik yük devretme sağlamak için HBase’in ölçek genişletmeli mimarisinden yararlanır. Performans, okumalar için bellek içi önbelleğe alma ve yazmalar için yüksek verimlilikli akış tarafından geliştirilmiştir. HBase kümesi sanal ağda oluşturulabilir. Ayrıntılar için bkz. [Azure Sanal Ağ'da HDInsight kümeleri oluşturma][hbase-provision-vnet].

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Veriler HDInsight HBase’de nasıl yönetilir?
Veriler HBase kabuğunda `create`, `get`, `put` ve `scan` komutları kullanılarak HBase tarafından yönetilebilir. Veriler `put` kullanılarak veritabanına yazılır ve `get` kullanarak okunur. `scan` komutu, bir tablodaki birden çok satırdaki verileri almak için kullanılır. Veriler, HBase REST API’sinin üstünde bir istemci kitaplığı sağlayan HBase C# API’si kullanılarak da yönetilebilir. Bir HBase veritabanı, Hive kullanarak da sorgulanabilir. Bu programlama modellerine giriş için bkz. [HDInsight'ta Hadoop ile HBase kullanmaya başlama][hbase-get-started]. Veritabanı barındıran düğümlerde veri işlemeye olanak sağlayan ortak işlemciler de kullanılabilir.

>
> [!NOTE]
> Thrift, HDInsight’ta HBase tarafından desteklenmez.
>

## <a name="scenarios-use-cases-for-hbase"></a>Senaryolar: HBase kullanım örnekleri
BigTable’ın (ve uzantılarının, HBase) oluşturulma nedeni olan kurallı kullanım örneği amacı web aramasıydı. Arama motorları terimleri bunları içeren web siteleriyle eşleştiren dizinler oluşturur. Ancak HBase için uygun olan diğer birçok kullanım örneği vardır; bunların birkaçı bu bölümde listelenmektedir.

* Anahtar değeri deposu
  
    HBase bir anahtar değeri deposu olarak kullanılabilir ve ileti sistemlerini yönetmeye uygundur. Facebook kendi ileti sistemi için HBase kullanır ve Internet iletişimlerini depolamak ve yönetmek için idealdir. WebTable web sayfalarından çıkarılan tabloları aramak ve yönetmek için HBase kullanır.
* Algılayıcı verileri
  
    HBase çeşitli kaynaklardan artımlı olarak toplanan verileri yakalamak için yararlıdır. Bu, sosyal analizler, zaman serileri, etkileşimli panoları eğilimler ve sayaçlar ile güncel tutma ve denetim günlüğü sistemlerini yönetmeyi içerir. Örnek olarak, Bloomberg tüccar terminali ve sunucu sistemlerinin durumuna ilişkin toplanan ölçümleri toplayan ve bunlara erişim imkanı sağlayan Open Time Series Veritabanı (OpenTSDB) verilebilir.
* Gerçek zamanlı sorgu
  
    [Phoenix](http://phoenix.apache.org/) Apache HBase için bir SQL sorgu alt yapısıdır. Buna JDBC sürücüsü olarak erişilir ve bu SQL kullanarak HBase tablolarını sorgulamayı ve yönetmeyi sağlar.
* Bir platform olarak HBase
  
    Uygulamalar, bir veri deposu olarak kullanarak HBase’in üstünde çalışabilir. Örnek olarak Phoenix, OpenTSDB, Kiji ve Titan verilebilir. Uygulamalar HBase ile de tümleştirebilir. Örnek olarak, Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia ve Dril verilebilir.

## <a name="next-steps"></a>Sonraki adımlar
* [HDInsight'ta Hadoop ile HBase kullanmaya başlama][hbase-get-started]
* [Azure Sanal Ağ'da HDInsight kümeleri oluşturma][hbase-provision-vnet]
* [HDInsight’ta HBase çoğaltmayı yapılandırma](hdinsight-hbase-replication.md)
* [HDInsight'ta HBase ile Twitter düşüncelerini çözümleme][hbase-twitter-sentiment]
* [HDInsight ile HBase kullanan Java uygulamaları oluşturmak için Maven kullanma (Hadoop)][hbase-build-java-maven]

## <a name="see-also"></a>Ayrıca bkz.
* [Apache HBase](https://hbase.apache.org/)
* [Bigtable: Yapılandırılmış Veriler için Dağıtılmış Depolama Sistemi](http://research.google.com/archive/bigtable.html)

[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/

