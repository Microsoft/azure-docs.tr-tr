---
title: Azure Data Lake Analytics U-SQL geliştiricileri için Apache Spark kod kavramlarını anlayın.
description: Bu makalede, U-SQL geliştiricilerinin Spark kod kavramlarını anlamasına yardımcı olacak Apache Spark kavramları açıklanmaktadır.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: 4ed23beae6edb13efabf034c1e87b9cb76048f82
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648473"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>U-SQL geliştiricileri için Apache Spark kodunu anlayın

Bu bölüm, U-SQL betiklerini Apache Spark dönüştürmek için üst düzey rehberlik sağlar.

- [İki dilin işleme paradigmalarına karşılaştırması](#understand-the-u-sql-and-spark-language-and-processing-paradigms) ile başlar
- Hakkında ipuçları sağlar:
   - U-SQL [satır kümesi ifadeleri](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) de dahil olmak üzere [betikleri Dönüştür](#transform-u-sql-scripts)
   - [.NET kodu](#transform-net-code)
   - [Veri türleri](#transform-typed-values)
   - [Katalog nesneleri](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>U-SQL ve Spark dilini anlayın ve işleme paradigmalarına

Azure Data Lake Analytics ' U-SQL betiklerini Spark 'a geçirmeye başlamadan önce, iki sistemin genel dilini ve işlemesini anlamak yararlı olur.

U-SQL, veri akışı paradigması kullanan ve .NET (örneğin C#), Python ve R gibi Kullanıcı kodunu kolayca katıştırmanıza ve ölçeklendirmenize olanak tanıyan bir SQL benzeri bildirime dayalı sorgu dilidir. Kullanıcı Uzantıları basit ifadeler veya Kullanıcı tanımlı işlevler uygulayabilir, ancak kullanıcıya özel işleçler uygulayan kullanıcı tanımlı işleçler, satır kümesi düzeyi dönüştürmeleri, Dışlamalar ve çıkış yazılıyor.

Spark, .NET, Python, .NET gibi çeşitli dil bağlamaları sunan bir genişleme çerçevesidir. kodunuzu bu dillerden birine ilk olarak yazdığınızda dayanıklı Dağıtılmış veri kümeleri (RDD), dataframes ve veri kümeleri adlı veri soyutlamalarını oluşturun ve ardından, bir LINQ benzeri etki alanına özgü dil (DSL) kullanarak bunları dönüştürün. Ayrıca, veri çerçevesi ve veri kümesi soyutlamaları üzerinde bildirim temelli bir alt dil olarak parlak SQL de sağlar. DSL, iki işlem, dönüşüm ve eylem kategorisi sağlar. Veri soyutlamalarını dönüşümler uygulamak dönüştürmeyi yürütmez, bunun yerine bir eylemle (örneğin, sonucu geçici bir tabloya veya dosyaya yazmak ya da) bir eylem ile değerlendirilmesi için gönderilecek yürütme planını derleme Sonuç).

Bu nedenle, bir U-SQL betiğini bir Spark programına çevirirken, en azından veri çerçevesi soyutlamasını (Şu anda en sık kullanılan veri soyutlama olan) ve bildirim oluşturmayı yazmak isteyip istemediğinizi belirlemek istediğiniz dile karar vermeniz gerekir DSL veya parlak SQL kullanan veri akışı dönüşümleri. Bazı daha karmaşık durumlarda, U-SQL betiğini Azure Batch veya Azure Işlevleri ile uygulanan bir Spark dizisine ve diğer adımlara bölmeniz gerekebilir.

Ayrıca, Azure Data Lake Analytics bir sunucusuz iş hizmeti ortamında, hem Azure Databricks hem de Azure HDInsight bir küme hizmeti biçiminde Spark sağlar. Uygulamanızı dönüştürürken, şimdi kümelerin oluşturulması, boyutlandırılması, ölçeklendirilmesi ve yetkisini alma etkilerini dikkate almanız gerekir.

## <a name="transform-u-sql-scripts"></a>U-SQL betiklerini Dönüştür

U-SQL betikleri aşağıdaki işleme modelini izler:

1. Veriler, `EXTRACT` bildirimi, bir konum veya dosya kümesi belirtimi ve yerleşik ya da Kullanıcı tanımlı Extractor ve istenen şemayı ve U-SQL tablolarından (yönetilen ya da dış tablolar) kullanarak yapılandırılmamış dosyalardan okuma alır. Satır kümesi olarak temsil edilir.
2. Satır kümeleri, satır kümelerine U-SQL ifadeleri uygulayan ve yeni satır kümeleri üreten birden çok U-SQL deyiminde dönüştürülür.
3. Son olarak, ortaya çıkan satır kümeleri, konumları ve yerleşik veya Kullanıcı tanımlı bir çıkışı belirten `OUTPUT` ifadesiyle veya bir U-SQL tablosuna çıktılardır.

Komut dosyası değerlendirilir, yani ayıklama ve dönüştürme adımının bir ifade ağacında (veri akışı) bulunduğu ve genel olarak değerlendirilen anlamına gelir.

Spark programları, verileri okumak ve veri çerçevelerini oluşturmak için Spark bağlayıcılarını kullanmanıza, sonra LINQ benzeri DSL veya parlak SQL kullanarak dönüştürmeleri veri çerçevelerine uygulamanıza, sonra da sonucu dosyalara, geçici Spark tablolarına yazmanıza benzer. Bazı programlama dil türleri veya konsolu.

## <a name="transform-net-code"></a>.NET kodunu Dönüştür

U-SQL C# ' i n ifade dili ve özel .NET kodu ölçeğini genişletmek için kullanabileceğiniz çeşitli yollar sunar.

Spark Şu anda .NET kodu yürütmeyi yerel olarak desteklemediğinden, ifadelerinizi eşdeğer bir Spark, Scala, Java veya Python ifadesine yeniden yazmanız ya da .NET kodunuzda çağrı yapmak için bir yol bulmanız gerekir. Betiğinizin .NET kitaplıklarını kullanması durumunda aşağıdaki seçenekleriniz vardır:

- .NET kodunuzu Scala veya Python 'a çevirin.
- U-SQL betiğini, .NET dönüştürmeleri uygulamak için Azure Batch işlemler kullandığınız birkaç adıma ayırın (kabul edilebilir ölçek alabilirsiniz)
- Moebius adlı açık kaynakta kullanılabilen bir .NET dil bağlaması kullanın. Bu proje desteklenen bir durumda değil.

Herhangi bir durumda, U-SQL betiklerinizde büyük miktarda .NET mantığınız varsa, daha ayrıntılı bilgiler için lütfen Microsoft hesap temsilcinizle bizimle iletişim kurun.

Aşağıdaki ayrıntılar, U-SQL betiklerinde .NET ve C# kullanımlarının farklı durumları içindir.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Skalar satır içi U-SQL C# ifadelerini Dönüştür

U-SQL ' n i n C#ifade dili. Çok sayıda skalar satır içi U-SQL ifadesi, gelişmiş performans için yerel olarak uygulanır, ancak .NET Framework 'e çağrı yoluyla daha karmaşık ifadeler yürütülebilmiştir.

Spark, kendi skalar ifade diline sahiptir (DSL 'nin bir parçası olarak veya parlak SQL 'te) ve barındırma dilinde yazılan Kullanıcı tanımlı işlevlere çağrı yapılmasına izin verir.

U-SQL ' de skalar deyimleriniz varsa, öncelikle en fazla performansı almak için en uygun yerel olarak anlaşılan Spark skaler ifadesini bulmanız ve ardından diğer ifadeleri tercih ettiğiniz Spark barındırma dilinin Kullanıcı tanımlı işleviyle eşlemeniz gerekir.

.NET ' C# ın, Spark barındırma dillerinin ve Spark 'ın DSL 'den farklı tür semantiklerine sahip olduğunu unutmayın. Tür sistemi farklılıkları hakkında daha fazla bilgi için [aşağıya](#transform-typed-values) bakın.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Kullanıcı tanımlı skaler .NET işlevlerini ve Kullanıcı tanımlı aggregdönüştür

U-SQL rastgele skaler .net işlevlerini çağırmak ve .net 'te yazılan Kullanıcı tanımlı toplayıcısını değiştirme 'ı çağırmak için yollar sağlar.

Spark ayrıca, Spark 'un DSL ve mini SQL 'den çağrılabilen barındırma dillerinin çoğunda yazılan Kullanıcı tanımlı işlevler ve Kullanıcı tanımlı aggregiçin destek sunar.

### <a name="transform-user-defined-operators-udos"></a>Kullanıcı tanımlı işleçleri (UDOs) dönüştürme

U-SQL; ayıklayıcıları, outputters, azaltıcının, işlemciler, appliers ve combiners gibi Kullanıcı tanımlı işleçler (UDOs) gibi çeşitli kategoriler sağlar ve .NET 'te (ve-bir dizi Python ve R 'ye) yazılabilir.

Spark, işleçler için aynı genişletilebilirlik modelini sunmaz, ancak bazıları için eşdeğer bir özelliğe sahiptir.

Ayıklayıcıları ve outputters Spark, Spark bağlayıcılarına eşdeğerdir. Birçok U-SQL ayıklayıcıları için Spark Community 'de eşdeğer bir bağlayıcı bulabilirsiniz. Başkaları için özel bağlayıcı yazmanız gerekir. U-SQL ayıklayıcısı karmaşıktır ve birkaç .NET kitaplığı kullanıyorsa, .NET kitaplığı 'nda verilerin gerçek işlenme işlemini çağırmak için birlikte çalışabilirliği kullanan Scala 'da bir bağlayıcı oluşturmak tercih edilebilir. Bu durumda, .NET Core çalışma zamanını Spark kümesine dağıtmanız ve başvurulan .NET kitaplıklarının .NET Standard 2,0 uyumlu olduğundan emin olmanız gerekir.

Diğer U-SQL UDOs türlerinin, Kullanıcı tanımlı işlevler ve aggregıcılar ile anlamsal olarak uygun Spark DLS veya mini SQL ifadesi kullanılarak yeniden yazılması gerekir. Örneğin, bir işlemci, bir veri çerçevesini bağımsız değişken olarak alan ve bir veri çerçevesi döndüren bir işlev olarak paketlenmiş çeşitli UDF çağırmaları SEÇIMI ile eşleştirilebilir.

### <a name="transform-u-sqls-optional-libraries"></a>U-SQL ' i n isteğe bağlı kitaplıklarını Dönüştür

U-SQL, [Python](data-lake-analytics-u-sql-python-extensions.md), [R](data-lake-analytics-u-sql-r-extensions.md), [JSON, XML, avro desteği](https://github.com/Azure/usql/tree/master/Examples/DataFormats)ve bazı bilişsel [Hizmetler özellikleri](data-lake-analytics-u-sql-cognitive.md)sunan bir isteğe bağlı ve tanıtım kitaplığı kümesi sağlar.

Spark, kendi Python ve R tümleştirmesini, pySpark ve parlak r 'yi sırasıyla sağlar ve JSON, XML ve AVRO okuma ve yazma için bağlayıcılar sağlar.

Bilişsel hizmetler kitaplıklarına başvuran bir betiği dönüştürmeniz gerekiyorsa, Microsoft hesap temsilcinizle bizimle iletişim kurmayı öneririz.

## <a name="transform-typed-values"></a>Yazılan değerleri Dönüştür

U-SQL ' n i n tür sistemi .NET türü sistemine dayalıdır ve Spark 'ın, ana bilgisayar dili bağlamalarından etkilenen kendi tür sistemi var olduğundan, çalıştırdığınız türlerin kapalı ve belirli türler için tür aralıklarının kapalı olduğundan emin olmanız gerekir. , duyarlık ve/veya ölçek biraz farklı olabilir. Ayrıca, U-SQL ve Spark `null` değerleri farklı işler.

### <a name="data-types"></a>Veri türleri

Aşağıdaki tabloda, Spark, Scala ve PySpark içindeki eşdeğer türler verilen U-SQL türleri için verilmiştir.

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

Daha fazla bilgi için bkz.

- [org. Apache. spark. Sql. Types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark SQL ve DataFrames türleri](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Scala değer türleri](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark. Sql. Types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>NULL işleme

Spark 'ta, varsayılan olarak türler, U-SQL ' i n Ken NULL değerlere izin verir, skalar, nesne olmayan olarak boş değer olarak işaretlenir. Spark, bir sütunu null değer atanamaz olarak tanımlamanızı sağlarken, kısıtlamayı zorunlu kılmaz ve [yanlış sonuca yol](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836)açabilir.

Spark içinde NULL değeri, değerin bilinmediğini gösterir. Spark NULL değeri, kendisi de dahil olmak üzere herhangi bir değerden farklıdır. İki Spark NULL değeri veya NULL değer ile diğer herhangi bir değer arasında karşılaştırmalar, her NULL değeri bilinmediği için bilinmeyen döndürür.  

Bu davranış U-SQL ' den farklıdır, bu durum C# , `null` herhangi bir değerden farklı, ancak kendine eşit olduğu semantiğini izler.  

Bu nedenle, `column_name`içinde NULL değerler olsa bile `WHERE column_name = NULL` kullanan bir mini SQL `SELECT` deyimleri sıfır satır döndürür. Bu, U-SQL ' de, `column_name` `null`olarak ayarlandığı satırları döndürür. Benzer şekilde, `WHERE column_name != NULL` kullanan bir Spark `SELECT` deyimleri, `column_name`null olmayan değerler olsa bile sıfır satır döndürür. Bu, U-SQL ' de, null olmayan satırları döndürür. Bu nedenle, U-SQL null denetimi semantiğini istiyorsanız, sırasıyla [IsNull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull)) ve [ınotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) (ya da DSL eşdeğerini) kullanmanız gerekir.

## <a name="transform-u-sql-catalog-objects"></a>U-SQL Katalog nesnelerini dönüştürme

Büyük bir fark, U-SQL betiklerinin, çoğu doğrudan Spark eşdeğeri olmayan, Katalog nesnelerini de kullanmalarından biridir.

Spark, Hive meta veri deposu kavramları, temel olarak veritabanları ve tablolar için destek sağlar; böylece, c-SQL veritabanlarını ve şemalarını Hive veritabanlarına ve U-SQL tablolarını Spark tablolarında (bkz. [U-SQL tablolarında depolanan verileri taşıma](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)) eşleyebilirsiniz, ancak görünüm desteği yoktur. tablo değerli işlevler (TVFs), saklı yordamlar, U-SQL derlemeleri, dış veri kaynakları vb.

Görünümler, TVFs, saklı yordamlar ve derlemeler gibi U-SQL kodu nesneleri Spark 'daki kod işlevleri ve kitaplıkları aracılığıyla modellenebilir ve ana bilgisayar dilinin işlevi ve yordamsal soyutlama mekanizmaları kullanılarak başvurulabilir (örneğin, içeri aktarma aracılığıyla) Python modülleri ya da Scala işlevlerine başvuruluyor).

U-SQL kataloğu, projeler ve takımlar arasında veri ve kod nesneleri paylaşmak için kullanılmışsa, paylaşım için eşdeğer mekanizmaların kullanılması gerekir (örneğin, kod nesnelerini paylaşmak için Maven).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>U-SQL satır kümesi ifadelerini ve SQL tabanlı skaler ifadeleri Dönüştür

U-SQL ' n i n temel dili, satır kümelerini dönüştürülüyor ve SQL tabanlı. Aşağıda, U-SQL ' de sunulan en yaygın satır kümesi ifadelerinin ayrıntılı olmayan bir listesi verilmiştir:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+ toplamalar +`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI` `JOIN` ifadeleri
- `CROSS`/`OUTER` `APPLY` ifadeleri
- `PIVOT`/`UNPIVOT` ifadeleri
- `VALUES` satır kümesi Oluşturucusu

- İfadeleri `UNION`/`OUTER UNION`/`INTERSECT`/ayarla `EXCEPT`

Ayrıca, U-SQL gibi çeşitli SQL tabanlı skalar ifadeler sağlar

- `OVER` Pencereleme ifadeleri
- çeşitli yerleşik toplayıcısını değiştirme 'lar ve derecelendirme işlevleri (`SUM`, `FIRST` vb.)
- En tanıdık SQL skalar ifadelerinden bazıları: `CASE`, `LIKE`, (`NOT`) `IN`, `AND`, `OR` vb.

Spark, bu ifadelerin çoğu için hem DSL hem de mini SQL biçiminde denk ifadeler sunar. Spark içinde yerel olarak desteklenmeyen ifadelerden bazılarının, yerel Spark ifadelerinin ve anlamsal olarak denk desenlerin bir birleşimi kullanılarak yeniden yazılması gerekir. Örneğin, `OUTER UNION` projeksiyonlar ve birleşimlerin eşdeğer birleşimine çevrilmesi gerekir.

NULL değerlerin farklı işlemesi nedeniyle, bir U-SQL birleştirmesi her ikisi de bir satır ile eşleşir, ancak karşılaştırılan her iki sütun da NULL bir değer içeriyorsa, bir Spark içindeki bir JOIN, açık null denetimleri eklenmediği sürece bu sütunlarla eşleşmeyecektir.

## <a name="transform-other-u-sql-concepts"></a>Diğer U-SQL kavramlarını Dönüştür

U-SQL Ayrıca SQL Server veritabanlarına, parametrelere, skaler ve lambda ifadesi değişkenlerine, sistem değişkenlerine ve `OPTION` ipuçlarına karşı federal sorgular gibi diğer çeşitli özellikler ve kavramlar sunmaktadır.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>SQL Server veritabanlarına/dış tablolara yönelik Federasyon sorguları

U-SQL, Azure SQL veritabanı 'nda veri kaynağı ve dış tablolar ve doğrudan sorgular sağlar. Spark aynı nesne soyutlamalarını sunmadığından, SQL veritabanlarını sorgulamak için kullanılabilen [Azure SQL veritabanı Spark bağlayıcısını](../sql-database/sql-database-spark-connector.md) sağlar.

### <a name="u-sql-parameters-and-variables"></a>U-SQL parametreleri ve değişkenleri

Parametrelerde ve Kullanıcı değişkenlerinde, Spark ve barındırma dillerinde eşdeğer kavramlar vardır.

Örneğin Scala 'da, `var` anahtar sözcüğüyle bir değişken tanımlayabilirsiniz:

```
var x = 2 * 3;
println(x)
```

U-SQL ' n i n Sistem değişkenleri (`@@`başlayan değişkenler) iki kategoriye ayrılabilir:

- Betik davranışlarını etkilemek için belirli değerlere ayarlanabilen ayarlanabilir sistem değişkenleri
- Sistem ve iş düzeyi bilgilerini sorgulama yapan bilgilendirici sistem değişkenleri

Ayarlanabilen sistem değişkenlerinin çoğunun Spark içinde doğrudan eşdeğeri yoktur. Bilgilendirici sistem değişkenlerinden bazıları, iş yürütmesi sırasında bilgileri bağımsız değişken olarak geçirerek modellenebilir, diğerlerinin Spark 'ın barındırma dilinde bir eşdeğer işlevi olabilir.

### <a name="u-sql-hints"></a>U-SQL ipuçları

U-SQL, sorgu iyileştiricisi ve yürütme altyapısına ipuçları sağlamak için çeşitli sözdizimsel yollar sunar:  

- U-SQL sistem değişkenini ayarlama
- veri veya plan ipucu sağlamak için satır kümesi ifadesiyle ilişkili bir `OPTION` yan tümcesi
- JOIN ifadesinin sözdiziminde bir JOIN ipucu (örneğin, `BROADCASTLEFT`)

Spark 'un maliyet tabanlı sorgu iyileştiricisi, ipuçları sağlamak ve sorgu performansını ayarlamak için kendi özelliklerine sahiptir. Lütfen ilgili belgelere başvurun.

## <a name="next-steps"></a>Sonraki adımlar

- [U-SQL geliştiricileri için Spark veri biçimlerini anlama](understand-spark-data-formats.md)
- [Apache Spark için .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Büyük veri analizi Çözümlerinizi Azure Data Lake Storage 1. Azure Data Lake Storage 2. ' dan yükseltin](../storage/blobs/data-lake-storage-upgrade.md)
- [Azure Data Factory Spark etkinliğini kullanarak verileri dönüştürme](../data-factory/transform-data-using-spark.md)
- [Azure Data Factory Hadoop Hive etkinliğini kullanarak verileri dönüştürme](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight 'ta Apache Spark nedir?](../hdinsight/spark/apache-spark-overview.md)
