---
title: Azure Data Lake Analytics U-SQL geliştiricileri için Apache Spark anlayın.
description: Bu makalede U-SQL geliştiricileri arasındaki farklara yardımcı olacak Apache Spark kavramlar açıklanmaktadır.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648434"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>U-SQL geliştiricileri için Apache Spark anlayın

Microsoft, [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) ve [Azure HDInsight](../hdinsight/hdinsight-overview.md) gibi çeşitli analiz hizmetlerini ve Azure Data Lake Analytics destekler. Geliştiricilerden analitik işlem hatları oluştururken açık kaynaklı çözümler için açık bir tercih olduğunu duyduk. U-SQL geliştiricilerinin Apache Spark anlamalarına yardımcı olmak ve U-SQL betiklerini Apache Spark 'ye nasıl dönüştürebileceğinizi öğrenmek için bu kılavuzu oluşturduk.

Uygulayabileceğiniz birçok adımı ve çeşitli alternatifleri içerir.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>U-SQL ' y i dönüştürme adımları Apache Spark

1. İş düzenleme işlem hatlarınızı dönüştürün.

   Azure Data Lake Analytics betiklerinizi düzenlemek için [Azure Data Factory](../data-factory/introduction.md) kullanıyorsanız, bunları yeni Spark programlarını yönetmek için ayarlamanız gerekir.
2. U-SQL ve Spark 'ın verileri yönetme arasındaki farkları anlayın

   [Azure Data Lake Storage 1.](../data-lake-store/data-lake-store-overview.md) verilerinizi [Azure Data Lake Storage 2.](../storage/blobs/data-lake-storage-introduction.md)taşımak istiyorsanız, hem dosya verilerini hem de katalog tarafından tutulan verileri kopyalamanız gerekir. Azure Data Lake Analytics yalnızca Azure Data Lake Storage 1. desteklediğini unutmayın. Bkz. [Spark veri biçimlerini anlama](understand-spark-data-formats.md)
3. U-SQL komut dosyalarınızı Spark 'a dönüştürme

   U-SQL komut dosyalarınızı dönüştürmeden önce bir analiz hizmeti seçmeniz gerekir. Kullanılabilir bazı kullanılabilir işlem hizmetleri şunlardır:
      - [Azure Data Factory veri akışı](../data-factory/concepts-data-flow-overview.md) Veri akışlarını eşleme, veri mühendislerinin kod yazmadan bir grafik veri dönüştürme mantığı geliştirmesini sağlayan görsel olarak tasarlanan veri dönüştürmelerdir. Karmaşık Kullanıcı kodu yürütmeye uygun olmasa da, geleneksel SQL benzeri veri akışı dönüşümlerini kolayca temsil edebilirler
      - [Azure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) HDInsight üzerinde Apache Hive, ayıklama, dönüştürme ve yükleme (ETL) işlemlerine uygundur. Bu, U-SQL komut dosyalarınızı Apache Hive için çevileyeceğiz anlamına gelir.
      - [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) veya [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) gibi Apache Spark ALTYAPıLARı, U-SQL komut dosyalarınızı Spark 'a çevileyeceğiz anlamına gelir. Daha fazla bilgi için bkz. [Spark veri biçimlerini anlama](understand-spark-data-formats.md)

> [!CAUTION]
> Hem [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) hem de [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) küme hizmetlerdir ve Azure Data Lake Analytics gibi sunucusuz işler değildir. Uygun maliyet/performans oranını almak için kümelerin nasıl sağlanacağını ve maliyetlerinizi en aza indirmek için yaşam sürelerinin nasıl yönetileceğini göz önünde bulundurmanız gerekir. Bu hizmetler, .NET dilinde yazılmış kullanıcı koduna sahip farklı performans özelliklerine sahiptir, bu nedenle sarmalayıcıları yazmanız veya kodunuzu desteklenen bir dilde yeniden yazmanız gerekecektir. Daha fazla bilgi için bkz. [Spark veri biçimlerini anlama](understand-spark-data-formats.md), [U-SQL geliştiricileri için Apache Spark kod kavramlarını anlama](understand-spark-code-concepts.md), [Apache Spark için .net](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Sonraki adımlar

- [U-SQL geliştiricileri için Spark veri biçimlerini anlama](understand-spark-data-formats.md)
- [U-SQL geliştiricileri için Spark kod kavramlarını anlama](understand-spark-code-concepts.md)
- [Büyük veri analizi Çözümlerinizi Azure Data Lake Storage 1. Azure Data Lake Storage 2. ' dan yükseltin](../storage/blobs/data-lake-storage-upgrade.md)
- [Apache Spark için .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Azure Data Factory Hadoop Hive etkinliğini kullanarak verileri dönüştürme](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure Data Factory Spark etkinliğini kullanarak verileri dönüştürme](../data-factory/transform-data-using-spark.md)
- [Azure HDInsight 'ta Apache Spark nedir?](../hdinsight/spark/apache-spark-overview.md)
