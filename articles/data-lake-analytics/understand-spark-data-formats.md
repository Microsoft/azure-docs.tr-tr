---
title: Azure Data Lake Analytics U-SQL geliştiricileri için Apache Spark veri biçimlerini anlayın.
description: Bu makalede, U_SQL geliştiricilerin U-SQL ve Spark veri biçimleri arasındaki farkları anlamalarına yardımcı olmak için Apache Spark kavramları açıklanmaktadır.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648447"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>U-SQL ve Spark veri biçimleri arasındaki farkları anlayın

[Azure Databricks](../azure-databricks/what-is-azure-databricks.md) veya [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)kullanmak istiyorsanız, [Azure Data Lake Storage 1.](../data-lake-store/data-lake-store-overview.md) verilerinizi [Azure Data Lake Storage 2.](../storage/blobs/data-lake-storage-introduction.md)'e geçirmeniz önerilir.

Dosyalarınızı taşımaya ek olarak, c-SQL tablolarında depolanacak ve Spark tarafından erişilebilen verilerinizi de yapmak isteyeceksiniz.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Azure Data Lake Storage 1. dosyalarında depolanan verileri taşıma

Dosyalarda depolanan veriler çeşitli yollarla taşınabilir:

- [Azure Data Lake Storage 1.](../data-lake-store/data-lake-store-overview.md) hesabındaki verileri [Azure Data Lake Storage 2.](../storage/blobs/data-lake-storage-introduction.md) hesabına kopyalamak için bir [Azure Data Factory](../data-factory/introduction.md) işlem hattı yazın.
- [Azure Data Lake Storage 1.](../data-lake-store/data-lake-store-overview.md) hesabından verileri okuyan bir Spark işi yazın ve [Azure Data Lake Storage 2.](../storage/blobs/data-lake-storage-introduction.md) hesabına yazar. Özgün dosya biçimini korumanız gerekmiyorsa, kullanım ihtimaline bağlı olarak, Parquet gibi farklı bir biçimde yazmak isteyebilirsiniz.

[Büyük veri analizi çözümlerinizi Azure Data Lake Storage 1. ' den Azure Data Lake Storage 2. sürümüne yükseltme](../storage/blobs/data-lake-storage-upgrade.md) makalesini incelemenizi öneririz

## <a name="move-data-stored-in-u-sql-tables"></a>U-SQL tablolarında depolanan verileri taşıma

U-SQL tabloları Spark tarafından anlaşılamıyor. U-SQL tablolarında depolanan verileriniz varsa, tablo verilerini çıkaran ve Spark 'ın anlayacağı bir biçimde kaydeden bir U-SQL işini çalıştıracaksınız. En uygun biçim, Hive meta veri deposu klasör düzeninden sonra bir dizi Parquet dosyası oluşturmaktır.

Çıktı, yerleşik Parquet outputter ile U-SQL ' i n içinde ve bölüm klasörlerini oluşturmak için dosya kümeleriyle dinamik çıkış bölümleme kullanılarak elde edilebilir. [Her zamankinden daha fazla dosya işleyin ve Parquet kullanımı](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) , bu tür Spark tüketilebilir verilerin nasıl oluşturulacağı hakkında bir örnek sağlar.

Bu dönüşümden sonra, [Azure Data Lake Storage 1. dosyalarında depolanan verileri taşıma](#move-data-stored-in-azure-data-lake-storage-gen1-files)bölümünde özetlenen verileri kopyalayın.

## <a name="caveats"></a>Uyarılar

- Veri semantiği dosya kopyalanırken kopya bayt düzeyinde gerçekleşir. Bu nedenle [Azure Data Lake Storage 2.](../storage/blobs/data-lake-storage-introduction.md) hesapta aynı verilerin görünmesi gerekir. Ancak Spark bazı karakterleri farklı yorumlayabilir. Örneğin, bir CSV dosyasındaki satır sınırlayıcısı için farklı bir varsayılan kullanabilir.
    Ayrıca, yazılı verileri (tablolardan) kopyalıyorsanız, Parquet ve Spark, yazılan bazı değerler (örneğin, bir float) için farklı duyarlığa ve ölçeğe sahip olabilir ve null değerleri farklı şekilde işleyebilir. Örneğin, U-SQL null değerleri için C# semantiğe sahip olsa da Spark, null değerler için üç değerli mantığa sahiptir.

- Veri organizasyonu (bölümlendirme) U-SQL tabloları iki düzey bölümlendirme sağlar. Dış düzey (`PARTITIONED BY`) değere göre ve genellikle klasör hiyerarşileri kullanarak Hive/Spark bölümlendirme düzenine eşlenir. Null değerlerinin doğru klasöre eşlendiğinden emin olmanız gerekir. U-SQL içindeki iç düzey (`DISTRIBUTED BY`) 4 dağıtım şeması sunar: hepsini bir kez deneme, Aralık, karma ve doğrudan karma.
    Hive/Spark tabloları yalnızca U-SQL ' den farklı bir karma işlevi kullanarak değer bölümleme veya karma bölümlendirme desteği destekler. U-SQL tablo verilerinizin çıktısını aldığınızda, muhtemelen yalnızca Spark için değer bölümlemesini eşleyebilir ve son Spark sorgularınıza bağlı olarak veri düzeninizi daha fazla ayarlamaya gerek olabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [U-SQL geliştiricileri için Spark kod kavramlarını anlama](understand-spark-code-concepts.md)
- [Büyük veri analizi Çözümlerinizi Azure Data Lake Storage 1. Azure Data Lake Storage 2. ' dan yükseltin](../storage/blobs/data-lake-storage-upgrade.md)
- [Apache Spark için .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Azure Data Factory Spark etkinliğini kullanarak verileri dönüştürme](../data-factory/transform-data-using-spark.md)
- [Azure Data Factory Hadoop Hive etkinliğini kullanarak verileri dönüştürme](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight 'ta Apache Spark nedir?](../hdinsight/spark/apache-spark-overview.md)
