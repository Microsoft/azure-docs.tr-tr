---
title: Spark işlerini performans için iyileştirin
description: Bu makalede, Azure SYNAPSE Analytics 'te Apache Spark bir giriş sunulmaktadır.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b94ece73d5f9dc9b8343e45fb1f616599b9a1c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96450927"
---
# <a name="optimize-apache-spark-jobs-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te Apache Spark işlerini iyileştirme

Belirli bir iş yükünüz için [Apache Spark](https://spark.apache.org/) kümesi yapılandırmasını nasıl iyileştireceğinizi öğrenin.  En yaygın zorluk hatalı yapılandırmalardan (özellikle yanlış boyutlandırılmış yürütücüler), uzun süre çalışan işlemlerden ve Kartezyen işlemlerle sonuçlanan görevlerden kaynaklanan bellek baskısıdır. Uygun önbelleğe alma ile işleri hızlandırabilir ve [veri eğmaya](#optimize-joins-and-shuffles)izin verebilirsiniz. En iyi performansı elde etmek için, uzun süreli ve kaynak kullanan Spark iş yürütmelerini izleyin ve gözden geçirin.

Aşağıdaki bölümlerde ortak Spark iş iyileştirmeleri ve önerileri açıklanır.

## <a name="choose-the-data-abstraction"></a>Veri soyutlamasını seçin

Önceki Spark sürümleri, verileri, Spark 1,3 ve 1,6 ' nin sırasıyla veri çerçeveleri ve veri kümelerini kullanıma sunmuştur. Aşağıdaki göreli birleşmenizi göz önünde bulundurun:

* **Veri Çerçeveleri**
  * Çoğu durumda en iyi seçim.
  * Catalyst aracılığıyla sorgu iyileştirmesi sağlar.
  * Tam aşamalı kod oluşturma.
  * Doğrudan bellek erişimi.
  * Düşük atık toplama (GC) ek yükü.
  * Derleme zamanı denetimleri veya etki alanı nesne programlaması olmadığı için geliştirici kullanımı, veri kümeleri olarak kolay değildir.
* **Kümelerinin**
  * Performans etkisinin kabul edilebilir olduğu karmaşık ETL işlem hatları ile iyidir.
  * Performans etkisinin önemli ölçüde önemli olabileceği toplamalar iyi değildir.
  * Catalyst aracılığıyla sorgu iyileştirmesi sağlar.
  * Etki alanı nesne programlama ve derleme zamanı denetimleri sunarak kolayca geliştirme.
  * Serileştirme/seri kaldırma ek yükü ekler.
  * Yüksek GC ek yükü.
  * Tüm kod üretimini keser.
* **RDD'ler**
  * Yeni bir özel RDD oluşturmanız gerekmedikçe, RDDs kullanmanız gerekmez.
  * Catalyst aracılığıyla sorgu iyileştirmesi yok.
  * Tamamen aşamalı kod üretimi yok.
  * Yüksek GC ek yükü.
  * Spark 1. x eski API 'Leri kullanılmalıdır.

## <a name="use-optimal-data-format"></a>En iyi veri biçimini kullanma

Spark, CSV, JSON, XML, Parquet, Orc ve avro gibi birçok biçimi destekler. Spark, dış veri kaynaklarıyla çok sayıda daha fazla biçim desteklemek için genişletilebilir. daha fazla bilgi için bkz. [Apache Spark paketleri](https://spark-packages.org).

Performans için en iyi biçim, Spark 2. x içinde varsayılan değer olan *Snappy sıkıştırması* ile birlikte Parquet 'tir. Parquet, verileri sütunlu biçimde depolar ve Spark 'ta çok iyileştirilir. Ayrıca, *Snappy sıkıştırması* , gzip sıkıştırmasından daha büyük dosyalar oluşmasına neden olabilir. Bu dosyaların bölünmüş tablo doğası nedeniyle daha hızlı açılır.

## <a name="use-the-cache"></a>Önbelleği kullanma

Spark,, ve gibi farklı yöntemler aracılığıyla kullanılabilecek kendi yerel önbelleğe alma mekanizmalarını sağlar `.persist()` `.cache()` `CACHE TABLE` . Bu yerel önbelleğe alma, küçük veri kümeleri ve ara sonuçları önbelleğe almanız gereken ETL işlem hatları ile etkilidir. Ancak, bir önbelleğe alınmış tablo bölümleme verilerini tutduğundan Spark Native Caching Şu anda bölümlendirme ile iyi çalışmaz.

## <a name="use-memory-efficiently"></a>Belleği verimli kullanma

Spark, verileri belleğe yerleştirerek çalışır, bu nedenle bellek kaynaklarını yönetmek Spark işlerinin yürütülmesini iyileştirmeye yönelik önemli bir yönüdür.  Kümenizin belleğini verimli bir şekilde kullanmak için uygulayabileceğiniz çeşitli teknikler vardır.

* Bölümleme stratejinizde veri boyutu, türleri ve dağıtımı için daha küçük veri bölümleri ve hesabı tercih edin.
* Varsayılan Java serileştirmesi yerine, daha yeni, daha verimli [Kronyo veri serileştirmesini](https://github.com/EsotericSoftware/kryo)göz önünde bulundurun.
* Spark yapılandırma ayarlarını izleyin ve ayarlayın.

Başvurunuz için Spark bellek yapısı ve bazı önemli yürütücü belleği parametreleri sonraki görüntüde gösterilmiştir.

### <a name="spark-memory-considerations"></a>Spark bellek konuları

Azure SYNAPSE Apache Spark Yarn [Apache HADOOP Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)kullanır, Yarn her Spark düğümündeki tüm kapsayıcılar tarafından kullanılan en fazla bellek toplamını denetler.  Aşağıdaki diyagramda, temel nesneler ve bunların ilişkileri gösterilmektedir.

![YARN Spark bellek yönetimi](./media/apache-spark-perf/apache-yarn-spark-memory.png)

' Bellek yetersiz ' iletilerini ele almak için şunu deneyin:

* DAG yönetim karışık Les 'yi gözden geçirin. Harita-tarafı azaltma, bölüm öncesi (veya bucketize) kaynak verileri ile, tek bir karayı en üst düzeye çıkarıp gönderilen veri miktarını azaltarak azaltın.
* `ReduceByKey` `GroupByKey` Toplamaları, Pencereleme ve diğer işlevleri sağlayan ancak sınırsız bir bellek sınırına sahip olan sabit bellek sınırı ile tercih edilir.
* `TreeReduce`Yürüticilere veya bölümlerle üzerinde daha fazla çalışmayı, yani `Reduce` sürücü üzerinde çalışmayı tercih eder.
* Alt düzey RDD nesneleri yerine veri çerçevelerinden yararlanın.
* "Ilk N", çeşitli toplamalar veya Pencereleme işlemleri gibi eylemleri kapsülleyen Karmaşıktürler oluşturun.

## <a name="optimize-data-serialization"></a>Veri serileştirmesini iyileştirme

Spark işleri dağıtılır, bu nedenle en iyi performans için uygun veri serileştirme önem taşır.  Spark için iki serileştirme seçeneği vardır:

* Java serileştirme varsayılandır.
* Kronyo Serialization daha yeni bir biçimdir ve Java 'dan daha hızlı ve daha küçük seri hale getirme ile sonuçlanabilir.  Kronyo, sınıfları programınıza kaydetmenizi gerektirir ve henüz tüm serileştirilebilir türleri desteklemez.

## <a name="use-bucketing"></a>Gruplandırmayı kullanma

Demetlenmesidir, veri bölümlendirme ile benzerdir, ancak her demet yalnızca bir tane yerine bir sütun değerleri kümesi tutabilir. Demetlenmesidir, ürün tanımlayıcıları gibi büyük (milyonlarca veya daha fazla) değer üzerinde bölümlendirme için iyi sonuç verir. Bir demet, satırın demet anahtarı karma tarafından belirlenir. Bulaştırılmış tablolar, verilerin nasıl kullanıldığı ve sıralandığı hakkında meta verileri depoladıklarından benzersiz iyileştirmeler sunmaktadır.

Bazı gelişmiş demetlenmesidir özellikleri şunlardır:

* Demetlenmesidir meta bilgilerine dayalı sorgu iyileştirmesi.
* İyileştirilmiş toplamalar.
* İyileştirilmiş birleşimler.

Bölümleme ve demetlenmesidir 'ı aynı anda kullanabilirsiniz.

## <a name="optimize-joins-and-shuffles"></a>Birleştirmeleri ve karıştırmaları iyileştirme

Bir JOIN veya karıştırma üzerinde işleriniz yavaşsa, neden büyük olasılıkla iş verilerinizde asymmetry olan *veri eğriliği* olur. Örneğin, bir harita işi 20 saniye sürebilir, ancak verilerin katıldığı veya karıştırılmış bir işi çalıştıralsa da saat sürer. Veri eğriliğini onarmak için, anahtarın tamamını tam olarak yapmanız gerekir ya da anahtarların yalnızca bir alt kümesi için *yalıtılmış bir anahtar* kullanırsınız. Yalıtılmış bir güvenlik kullanıyorsanız, eşleme birleştirmelerde sallanan anahtarların alt kümesini yalıtmak için daha fazla filtre uygulamanız gerekir. Diğer bir seçenek de demet sütunu tanıtmak ve öncelikle demetlerde ön toplama işlemi yapmak.

Yavaş birleştirmelere neden olan başka bir faktör birleştirme türü olabilir. Spark, varsayılan olarak, `SortMerge` JOIN türünü kullanır. Bu tür bir birleştirme, büyük veri kümeleri için idealdir, ancak bunları birleştirmeden önce verilerin sol ve sağ tarafını sıralacağından hesaplama açısından pahalıdır.

Bir `Broadcast` JOIN, daha küçük veri kümeleri için en uygun seçenektir veya birleştirmenin bir tarafı diğer taraftan çok daha küçüktür. Bu tür bir JOIN, tüm yürüticilerine bir taraf yayınlar ve bu nedenle genel olarak yayınlar için daha fazla bellek gerektirir.

Ayarları yaparak yapılandırmanızda JOIN türünü değiştirebilir `spark.sql.autoBroadcastJoinThreshold` veya DataFrame API 'lerini () kullanarak bir JOIN ipucu ayarlayabilirsiniz `dataframe.join(broadcast(df2))` .

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Buckelenmiş tablolar kullanıyorsanız, birleşimi üçüncü bir birleşme türüne sahip olursunuz `Merge` . Doğru bir önceden bölümlenmiş ve önceden sıralanmış veri kümesi, bir birleşimden pahalı sıralama aşamasını atlar `SortMerge` .

Birleştirme sırası, özellikle daha karmaşık sorgularda önemlidir. En seçmeli birleştirmelere başlayın. Ayrıca, mümkün olduğunda toplamaların ardından satır sayısını artıran birleştirmeleri taşıyın.

Kartezyen birleştirmeler için paralellik 'i yönetmek üzere iç içe yapılar, Pencereleme ve belki Spark işinizdeki bir veya daha fazla adımı atlayabilirsiniz.

### <a name="select-the-correct-executor-size"></a>Doğru yürütücü boyutunu seçin

Yürütücü yapılandırmanıza karar verirken Java çöp toplama (GC) ek yükünü göz önünde bulundurun.

* Yürütücü boyutunu azaltmanın faktörleri:
  * %10 < GC yükünü korumak için 32 GB altındaki yığın boyutunu küçültün.
  * %10 < GC yükünü korumak için çekirdek sayısını azaltın.

* Yürütücü boyutunu artırma faktörleri:
  * Yürüticileri arasındaki iletişim yükünü azaltın.
  * Daha büyük kümelerdeki (>100 yürüticileri) yürüticileri (N2) arasındaki açık bağlantı sayısını azaltın.
  * Bellek kullanımı yoğun görevlere uyum sağlamak için yığın boyutunu artırın.
  * İsteğe bağlı: yürütücü başına bellek ek yükünü azaltın.
  * İsteğe bağlı: CPU 'YU aşırı abone yaparak kullanımı ve eşzamanlılık düzeyini artırın.

Yürütücü boyutunu seçerken bir Thumb genel kuralı olarak:

* Yürütücü başına 30 GB ile başlayın ve kullanılabilir makine çekirdeğini dağıtın.
* Daha büyük kümeler (> 100 yürüticileri) için Yürütücü çekirdekleri sayısını artırın.
* Hem deneme çalışmalarından hem de GC yükü gibi önceki faktörlere göre boyut boyutunu değiştirin.

Eşzamanlı sorgular çalıştırılırken şunları göz önünde bulundurun:

* Yürütücü başına 30 GB ve tüm makine çekirdekleri ile başlayın.
* Daha fazla sayıda paralel Spark uygulaması oluşturun (%30 ' dan fazla gecikme geliştirmesi).
* Sorguları paralel uygulamalar arasında dağıtın.
* Hem deneme çalışmalarından hem de GC yükü gibi önceki faktörlere göre boyut boyutunu değiştirin.

Zaman çizelgesi görünümüne, SQL grafiğine, iş istatistiklerine vb. bakarak, aykırı değerler veya diğer performans sorunları için sorgu performansınızı izleyin. Bazen yürüticilerinin biri veya birkaçı diğerlerinden daha yavaştır ve görevlerin yürütülmesi çok daha uzun sürer. Bu çoğunlukla daha büyük kümelerde (> 30 düğüm) oluşur. Bu durumda, Scheduler 'ın yavaş görevleri dengeyapabilmesi için çalışmayı daha fazla sayıda göreve bölün. 

Örneğin, uygulamadaki yürütücü çekirdekleri sayısı kadar en az iki görev vardır. Ayrıca, ile görevlerin yansımalı yürütülmesini de etkinleştirebilirsiniz `conf: spark.speculation = true` .

## <a name="optimize-job-execution"></a>İş yürütmeyi iyileştirme

* Gerekirse, örneğin, verileri iki kez kullanırsanız, önbelleğe alma.
* Değişkenleri tüm yürüticilerine yayınla. Değişkenler yalnızca bir kez serileştirilir ve daha hızlı aramalar elde edilir.
* Sürücü üzerindeki iş parçacığı havuzunu kullanın, bu da birçok görev için daha hızlı işlem sağlar.

Spark 2. x sorgu performansına yönelik anahtar, tam aşamalı kod oluşturmaya bağlı olan tungsten altyapısıdır. Bazı durumlarda, tam aşama kod üretimi devre dışı bırakılabilir. 

Örneğin, toplama ifadesinde kesilebilir olmayan bir tür ( `string` ) kullanırsanız, `SortAggregate` yerine görünür `HashAggregate` . Örneğin, daha iyi performans için aşağıdakileri deneyin ve kod oluşturmayı yeniden etkinleştirin:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Sonraki adımlar

- [Apache Spark ayarlama](https://spark.apache.org/docs/2.4.5/tuning.html)
- [Apache Spark Işlerinizi, çalışması için fiili olarak ayarlama](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Kronyo serileştirme](https://github.com/EsotericSoftware/kryo)
