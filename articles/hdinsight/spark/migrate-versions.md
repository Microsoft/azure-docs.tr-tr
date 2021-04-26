---
title: Apache Spark 2,1 veya 2,2 iş yüklerini 2,3 veya 2,4-Azure HDInsight 'a geçirin
description: 2,1 ve 2,2 Apache Spark 2,3 veya 2,4 ' e geçirmeyi öğrenin.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: af1894d2f63357006e87fa8e4533f135ecc02f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944758"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Apache Spark 2,1 ve 2,2 iş yüklerini 2,3 ve 2,4 'e geçirin

Bu belgede Spark 2,1 ve 2,2 ' de Apache Spark iş yüklerini 2,3 veya 2,4 olarak nasıl geçirebileceğiniz açıklanmaktadır.

[Sürüm notlarında](../hdinsight-release-notes.md#upcoming-changes), 1 Temmuz 2020 ' den itibaren, aşağıdaki küme yapılandırmalarının desteklenmeyecektir ve müşteriler bu yapılandırmalara sahip yeni kümeler oluşturamayacak:
 - HDInsight 3,6 Spark kümesinde Spark 2,1 ve 2,2
 - HDInsight 4,0 Spark kümesinde Spark 2,3

Bu yapılandırmalarda bulunan mevcut kümeler, Microsoft 'tan destek olmadan olduğu gibi çalışır. HDInsight 3,6 üzerinde Spark 2,1 veya 2,2 ' de çalışıyorsanız, olası sistem/destek kesintisini önlemek için Haziran 3,6 ' de HDInsight 'ta Spark 2,3 ' e geçin. HDInsight 4,0 kümesinde Spark 2,3 ' de çalışıyorsanız, olası sistem/destek kesintisini önlemek için Haziran 30 2020 ile HDInsight 4,0 ' 2,4 e geçin.

HDInsight kümesini 3,6 ' den 4,0 ' e geçirme hakkında genel bilgi için bkz. [HDInsight kümesini daha yeni bir sürüme geçirme](../hdinsight-upgrade-cluster.md). Daha yeni bir Apache Spark sürümüne geçme hakkında genel bilgi için bkz. [Apache Spark: sürüm oluşturma ilkesi](https://spark.apache.org/versioning-policy.html).

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>HDInsight 'ta Spark sürüm yükseltmeleri hakkında rehberlik

| Yükseltme senaryosu | Mechanism | Dikkate alınması gereken noktalar | Spark/Hive tümleştirmesi |
|------------------|-----------|--------------------|------------------------|
|HDInsight 3,6 Spark 2,1-HDInsight 3,6 Spark 2,3| HDInsight Spark 2,3 ile kümeleri yeniden oluşturma | Aşağıdaki makaleleri gözden geçirin: <br> [Apache Spark: Spark SQL 2,2 ' den 2,3 ' e yükseltme](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Spark SQL 2,1 ' den 2,2 ' e yükseltme](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Değişiklik yok |
|HDInsight 3,6 Spark 2,2-HDInsight 3,6 Spark 2,3 | HDInsight Spark 2,3 ile kümeleri yeniden oluşturma | Aşağıdaki makaleleri gözden geçirin: <br> [Apache Spark: Spark SQL 2,2 ' den 2,3 ' e yükseltme](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Değişiklik yok |
| HDInsight 3,6 Spark 2,1-HDInsight 4,0 Spark 2,4 | HDInsight 4,0 Spark 2,4 ile kümeleri yeniden oluşturma | Aşağıdaki makaleleri gözden geçirin: <br> [Apache Spark: Spark SQL 2,3 ' den 2,4 ' e yükseltme](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Spark SQL 2,2 ' den 2,3 ' e yükseltme](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Spark SQL 2,1 ' den 2,2 ' e yükseltme](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | HDInsight 4,0 ' de Spark ve Hive tümleştirmesi değiştirilmiştir. <br><br> HDInsight 4,0 ' de Spark ve Hive, Mini SQL veya Hive tablolarına erişmek için bağımsız kataloglar kullanır. Spark kataloğunda Spark tarafından oluşturulan bir tablo. Hive tarafından oluşturulan bir tablo Hive kataloğunda yaşar. Bu davranış, Hive ve Spark paylaşılan ortak kataloğunun HDInsight 3,6 ' inden farklıdır. HDInsight 4,0 ' de Hive ve Spark tümleştirmesi Hive ambar Bağlayıcısı 'nı (HWC) kullanır. HWC, Spark ve Hive arasında bir köprü olarak çalışmaktadır. Hive ambar Bağlayıcısı hakkında bilgi edinin. <br> 4,0 HDInsight 'ta, Hive ve Spark arasında meta veri deposunu paylaştırmak istiyorsanız, metassıst. Catalog. Default özelliğini Spark kümenizdeki Hive olarak değiştirerek bunu yapabilirsiniz. Bu özelliği, ambarı gelişmiş spark2-Hive-site-override ' da bulabilirsiniz. Yalnızca dış Hive tablolarında çalışan, iç/yönetilen Hive tablolarına veya ACID tablolarına sahipseniz, bu, meta veri paylaşımının paylaşımını anlamak önemlidir. <br><br>Daha fazla bilgi için [Azure hdınsight 3,6 Hive iş yüklerini hdınsight 4,0 'e geçirin](../interactive-query/apache-hive-migrate-workloads.md) .<br><br> |
| HDInsight 3,6 Spark 2,2-HDInsight 4,0 Spark 2,4 | HDInsight 4,0 Spark 2,4 ile kümeleri yeniden oluşturma | Aşağıdaki makaleleri gözden geçirin: <br> [Apache Spark: Spark SQL 2,3 ' den 2,4 ' e yükseltme](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Spark SQL 2,2 ' den 2,3 ' e yükseltme](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | HDInsight 4,0 ' de Spark ve Hive tümleştirmesi değiştirilmiştir. <br><br> HDInsight 4,0 ' de Spark ve Hive, Mini SQL veya Hive tablolarına erişmek için bağımsız kataloglar kullanır. Spark kataloğunda Spark tarafından oluşturulan bir tablo. Hive tarafından oluşturulan bir tablo Hive kataloğunda yaşar. Bu davranış, Hive ve Spark paylaşılan ortak kataloğunun HDInsight 3,6 ' inden farklıdır. HDInsight 4,0 ' de Hive ve Spark tümleştirmesi Hive ambar Bağlayıcısı 'nı (HWC) kullanır. HWC, Spark ve Hive arasında bir köprü olarak çalışmaktadır. Hive ambar Bağlayıcısı hakkında bilgi edinin. <br> 4,0 HDInsight 'ta, Hive ve Spark arasında meta veri deposunu paylaştırmak istiyorsanız, metassıst. Catalog. Default özelliğini Spark kümenizdeki Hive olarak değiştirerek bunu yapabilirsiniz. Bu özelliği, ambarı gelişmiş spark2-Hive-site-override ' da bulabilirsiniz. Yalnızca dış Hive tablolarında çalışan, iç/yönetilen Hive tablolarına veya ACID tablolarına sahipseniz, bu, meta veri paylaşımının paylaşımını anlamak önemlidir. <br><br>Daha fazla bilgi için [Azure hdınsight 3,6 Hive iş yüklerini hdınsight 4,0 'e geçirin](../interactive-query/apache-hive-migrate-workloads.md) .|

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümesini daha yeni bir sürüme geçir](../hdinsight-upgrade-cluster.md)
* [Azure HDInsight 3,6 Hive iş yüklerini HDInsight 'a geçirme 4,0](../interactive-query/apache-hive-migrate-workloads.md)
