---
title: Spark işlerini performans için iyileştirin-Azure HDInsight
description: Azure HDInsight 'ta Apache Spark kümelerinin en iyi performansı için ortak stratejileri görüntüleyin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/01/2019
ms.openlocfilehash: 0d8890eeba7fcb53517d6ee653c8dd09866805ef
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177370"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>HDInsight 'ta Apache Spark işlerini iyileştirme

Belirli bir iş yükünüz için [Apache Spark](https://spark.apache.org/) kümesi yapılandırmasını iyileştirmenizi öğrenin.  En yaygın zorluk, yanlış yapılandırma (özellikle yanlış boyutlu yürüticileri), uzun süreli işlemler ve Kartezyen işlemlere neden olan görevler nedeniyle bellek baskısı olur. Uygun önbelleğe alma ile işleri hızlandırabilir ve [veri eğmaya](#optimize-joins-and-shuffles)izin verebilirsiniz. En iyi performansı elde etmek için, uzun süreli ve kaynak kullanan Spark iş yürütmelerini izleyin ve gözden geçirin.

Aşağıdaki bölümlerde ortak Spark iş iyileştirmeleri ve önerileri açıklanır.

## <a name="choose-the-data-abstraction"></a>Veri soyutlamasını seçin

Önceki Spark sürümleri, verileri, Spark 1,3 ve 1,6 ' nin sırasıyla veri çerçeveleri ve veri kümelerini kullanıma sunmuştur. Aşağıdaki göreli birleşmenizi göz önünde bulundurun:

* **Veri çerçeveleri**
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
* **RDD**
    * Yeni bir özel RDD oluşturmanız gerekmedikçe, RDDs kullanmanız gerekmez.
    * Catalyst aracılığıyla sorgu iyileştirmesi yok.
    * Tamamen aşamalı kod üretimi yok.
    * Yüksek GC ek yükü.
    * Spark 1. x eski API 'Leri kullanılmalıdır.

## <a name="use-optimal-data-format"></a>En iyi veri biçimini kullan

Spark, CSV, JSON, XML, Parquet, Orc ve avro gibi birçok biçimi destekler. Spark, dış veri kaynaklarıyla çok sayıda daha fazla biçim desteklemek için genişletilebilir. daha fazla bilgi için bkz. [Apache Spark paketleri](https://spark-packages.org).

Performans için en iyi biçim, Spark 2. x içinde varsayılan değer olan *Snappy sıkıştırması*ile birlikte Parquet 'tir. Parquet, verileri sütunlu biçimde depolar ve Spark 'ta çok iyileştirilir.

## <a name="select-default-storage"></a>Varsayılan depolamayı seçin

Yeni bir Spark kümesi oluşturduğunuzda, kümenizin varsayılan depolama alanı olarak Azure Blob depolama veya Azure Data Lake Storage seçebilirsiniz. Her iki seçenek de, geçici kümeler için uzun süreli depolamanın avantajını sağlar. bu sayede, kümenizi sildiğinizde verileriniz otomatik olarak silinmez. Geçici bir kümeyi yeniden oluşturabilir ve verilerinize erişmeye devam edebilirsiniz.

| Mağaza türü | Dosya Sistemi | Hız | Larsa | Kullanım Örnekleri |
| --- | --- | --- | --- | --- |
| Azure Blob Depolama | fazla **b:** // | **Standart** | Yes | Geçici küme |
| Azure Blob depolama (güvenli) | fazla **BS:** // | **Standart** | Yes | Geçici küme |
| Azure Data Lake Storage Gen 2| **ABFS:** // | **Lýdýr** | Yes | Geçici küme |
| Azure Data Lake Storage Gen 1| **adl:** // | **Lýdýr** | Yes | Geçici küme |
| Yerel olarak | Bu **:** // | **En hızlı** | Hayır | Etkileşimli 24/7 kümesi |

## <a name="use-the-cache"></a>Önbelleği kullanma

Spark, `.persist()`, `.cache()`ve `CACHE TABLE`gibi farklı yöntemler aracılığıyla kullanılabilecek kendi yerel önbelleğe alma mekanizmalarını sağlar. Bu yerel önbelleğe alma, küçük veri kümeleri ve ara sonuçları önbelleğe almanız gereken ETL işlem hatları ile etkilidir. Ancak, bir önbelleğe alınmış tablo bölümleme verilerini tutduğundan Spark Native Caching Şu anda bölümlendirme ile iyi çalışmaz. Daha genel ve güvenilir önbelleğe alma tekniği, *depolama katmanı önbelleğe alma*tekniğidir.

* Yerel Spark önbelleğe alma (önerilmez)
    * Küçük veri kümeleri için iyi.
    * Bölümlendirme ile çalışmaz ve bu, gelecekteki Spark sürümlerinde değişebilir.

* Depolama düzeyi önbelleğe alma (önerilir)
    * , [Alluxıo](https://www.alluxio.io/)kullanılarak uygulanabilir.
    * Bellek içi ve SSD önbelleği kullanır.

* Yerel olarak (önerilir)
    * `hdfs://mycluster` yolu.
    * SSD önbelleği kullanır.
    * Önbelleğe alınmış veriler, kümeyi sildiğinizde, önbellek yeniden oluşturması gerekmeden kaybedilir.

## <a name="use-memory-efficiently"></a>Belleği verimli bir şekilde kullanma

Spark, verileri belleğe yerleştirerek çalışır, bu nedenle bellek kaynaklarını yönetmek Spark işlerinin yürütülmesini iyileştirmeye yönelik önemli bir yönüdür.  Kümenizin belleğini verimli bir şekilde kullanmak için uygulayabileceğiniz çeşitli teknikler vardır.

* Bölümleme stratejinizde veri boyutu, türleri ve dağıtımı için daha küçük veri bölümleri ve hesabı tercih edin.
* Varsayılan Java serileştirmesi yerine, daha yeni, daha verimli [Kronyo veri serileştirmesini](https://github.com/EsotericSoftware/kryo)göz önünde bulundurun.
* `spark-submit` Batch 'e göre ayıracak şekilde YARN kullanmayı tercih edin.
* Spark yapılandırma ayarlarını izleyin ve ayarlayın.

Başvurunuz için Spark bellek yapısı ve bazı önemli yürütücü belleği parametreleri sonraki görüntüde gösterilmiştir.

### <a name="spark-memory-considerations"></a>Spark bellek konuları

[Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)kullanıyorsanız, Yarn her Spark düğümündeki tüm kapsayıcılar tarafından kullanılan en fazla bellek toplamını denetler.  Aşağıdaki diyagramda, temel nesneler ve bunların ilişkileri gösterilmektedir.

![YARN Spark bellek yönetimi](./media/apache-spark-perf/apache-yarn-spark-memory.png)

' Bellek yetersiz ' iletilerini ele almak için şunu deneyin:

* DAG yönetim karışık Les 'yi gözden geçirin. Harita tarafı reducting, ön bölüm (veya bucketize) kaynaklı verileri azaltarak, tek bir karayı en üst düzeye çıkarın ve gönderilen veri miktarını azaltın.
* Toplamaları, Pencereleme ve diğer işlevleri sağlayan ancak sınırsız bellek sınırına sahip olan `GroupByKey`için sabit bellek sınırının `ReduceByKey` tercih edin.
* Yürüticileri veya `Reduce`bölümleri üzerinde daha fazla iş olan `TreeReduce`tercih eder, bu, sürücü üzerinde çalışır.
* Alt düzey RDD nesneleri yerine veri çerçevelerinden yararlanın.
* "Ilk N", çeşitli toplamalar veya Pencereleme işlemleri gibi eylemleri kapsülleyen Karmaşıktürler oluşturun.

## <a name="optimize-data-serialization"></a>Veri serileştirmesini iyileştirme

Spark işleri dağıtılır, bu nedenle en iyi performans için uygun veri serileştirme önem taşır.  Spark için iki serileştirme seçeneği vardır:

* Java serileştirme varsayılandır.
* Kronyo Serialization daha yeni bir biçimdir ve Java 'dan daha hızlı ve daha küçük seri hale getirme ile sonuçlanabilir.  Kronyo, sınıfları programınıza kaydetmenizi gerektirir ve henüz tüm serileştirilebilir türleri desteklemez.

## <a name="use-bucketing"></a>Demetlenmesidir kullanma

Demetlenmesidir, veri bölümlendirme ile benzerdir, ancak her demet yalnızca bir tane yerine bir sütun değerleri kümesi tutabilir. Demetlenmesidir, ürün tanımlayıcıları gibi büyük (milyonlarca veya daha fazla) değer üzerinde bölümlendirme için iyi sonuç verir. Bir demet, satırın demet anahtarı karma tarafından belirlenir. Bulaştırılmış tablolar, verilerin nasıl kullanıldığı ve sıralandığı hakkında meta verileri depoladıklarından benzersiz iyileştirmeler sunmaktadır.

Bazı gelişmiş demetlenmesidir özellikleri şunlardır:

* Demetlenmesidir meta bilgilerine dayalı sorgu iyileştirmesi.
* İyileştirilmiş toplamalar.
* İyileştirilmiş birleşimler.

Bölümleme ve demetlenmesidir 'ı aynı anda kullanabilirsiniz.

## <a name="optimize-joins-and-shuffles"></a>Birleştirmeleri ve karışık yerleri iyileştirme

Bir JOIN veya karıştırma üzerinde işleriniz yavaşsa, neden büyük olasılıkla iş verilerinizde asymmetry olan *veri eğriliği*olur. Örneğin, bir harita işi 20 saniye sürebilir, ancak verilerin katıldığı veya karıştırılmış bir işi çalıştıralsa da saat sürer. Veri eğriliğini onarmak için, anahtarın tamamını tam olarak yapmanız gerekir ya da anahtarların yalnızca bir alt kümesi için *yalıtılmış bir anahtar* kullanırsınız. Yalıtılmış bir güvenlik kullanıyorsanız, eşleme birleştirmelerde sallanan anahtarların alt kümesini yalıtmak için daha fazla filtre uygulamanız gerekir. Diğer bir seçenek de demet sütunu tanıtmak ve öncelikle demetlerde ön toplama işlemi yapmak.

Yavaş birleştirmelere neden olan başka bir faktör birleştirme türü olabilir. Spark, varsayılan olarak `SortMerge` JOIN türünü kullanır. Bu tür bir birleştirme, büyük veri kümeleri için idealdir, ancak bunları birleştirmeden önce verilerin sol ve sağ tarafını sıralacağından hesaplama açısından pahalıdır.

`Broadcast` JOIN, daha küçük veri kümeleri için en iyi seçenektir veya birleştirmenin bir tarafı diğer taraftan çok daha küçüktür. Bu tür bir JOIN, tüm yürüticilerine bir taraf yayınlar ve bu nedenle genel olarak yayınlar için daha fazla bellek gerektirir.

`spark.sql.autoBroadcastJoinThreshold`ayarlayarak yapılandırmanızda JOIN türünü değiştirebilir veya DataFrame API 'Lerini (`dataframe.join(broadcast(df2))`) kullanarak bir JOIN ipucu ayarlayabilirsiniz.

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

Buckelenmiş tablolar kullanıyorsanız, `Merge` birleşimi üçüncü bir birleşme türüne sahip olursunuz. Doğru bir önceden bölümlenmiş ve önceden sıralanmış veri kümesi, `SortMerge` bir birleşimden pahalı sıralama aşamasını atlar.

Birleştirme sırası, özellikle daha karmaşık sorgularda önemlidir. En seçmeli birleştirmelere başlayın. Ayrıca, mümkün olduğunda toplamaların ardından satır sayısını artıran birleştirmeleri taşıyın.

Kartezyen birleştirmeler için paralellik 'i yönetmek üzere iç içe yapılar, Pencereleme ve belki Spark işinizdeki bir veya daha fazla adımı atlayabilirsiniz.

## <a name="customize-cluster-configuration"></a>Küme yapılandırmasını özelleştirme

Spark kümesi iş yükünüze bağlı olarak, varsayılan olmayan bir Spark yapılandırmasının daha iyileştirilmiş Spark iş yürütmeye neden olabileceğini belirleyebilirsiniz.  Varsayılan olmayan küme yapılandırmalarının doğrulanması için örnek iş yükleri ile kıyaslama testi gerçekleştirin.

Ayarlayabileceğiniz bazı yaygın parametreler şunlardır:

* `--num-executors` uygun yürütme sayısını belirler.
* `--executor-cores` her bir yürütücü için çekirdek sayısını ayarlar. Genellikle, diğer süreçler kullanılabilir belleğin bir kısmını tükettiği için orta ölçekli yürüticiler olmalıdır.
* `--executor-memory`, YARN üzerindeki yığın boyutunu denetleyen her bir yürütücü için bellek boyutunu ayarlar. Yürütme ek yükü için bazı bellek bırakmanız gerekir.

### <a name="select-the-correct-executor-size"></a>Doğru yürütücü boyutunu seçin

Yürütücü yapılandırmanıza karar verirken Java çöp toplama (GC) ek yükünü göz önünde bulundurun.

* Yürütücü boyutunu azaltmanın faktörleri:
    1. %10 < GC yükünü korumak için 32 GB altındaki yığın boyutunu küçültün.
    2. %10 < GC yükünü korumak için çekirdek sayısını azaltın.

* Yürütücü boyutunu artırma faktörleri:
    1. Yürüticileri arasındaki iletişim yükünü azaltın.
    2. Daha büyük kümelerdeki (> 100 yürütme) yürüticileri (N2) arasındaki açık bağlantı sayısını azaltın.
    3. Bellek kullanımı yoğun görevlere uyum sağlamak için yığın boyutunu artırın.
    4. İsteğe bağlı: yürütücü başına bellek ek yükünü azaltın.
    5. İsteğe bağlı: CPU 'YU aşırı abone yaparak kullanımı ve eşzamanlılık düzeyini artırın.

Yürütücü boyutunu seçerken bir Thumb genel kuralı olarak:

1. Yürütücü başına 30 GB ile başlayın ve kullanılabilir makine çekirdeğini dağıtın.
2. Daha büyük kümeler (> 100 yürüticileri) için Yürütücü çekirdekleri sayısını artırın.
3. Hem deneme çalışmalarından hem de GC yükü gibi önceki faktörlere göre boyut boyutunu değiştirin.

Eşzamanlı sorgular çalıştırılırken şunları göz önünde bulundurun:

1. Yürütücü başına 30 GB ve tüm makine çekirdekleri ile başlayın.
2. Daha fazla sayıda paralel Spark uygulaması oluşturun (%30 ' dan fazla gecikme geliştirmesi).
3. Sorguları paralel uygulamalar arasında dağıtın.
4. Hem deneme çalışmalarından hem de GC yükü gibi önceki faktörlere göre boyut boyutunu değiştirin.

Zaman çizelgesi görünümüne, SQL grafiğine, iş istatistiklerine vb. bakarak, aykırı değerler veya diğer performans sorunları için sorgu performansınızı izleyin. Bazen yürüticilerinin biri veya birkaçı diğerlerinden daha yavaştır ve görevlerin yürütülmesi çok daha uzun sürer. Bu çoğunlukla daha büyük kümelerde (> 30 düğüm) oluşur. Bu durumda, Scheduler 'ın yavaş görevleri dengeyapabilmesi için çalışmayı daha fazla sayıda göreve bölün. Örneğin, uygulamadaki yürütücü çekirdekleri sayısı kadar en az iki görev vardır. Ayrıca, `conf: spark.speculation = true`görevlerin yansımalı yürütülmesini de etkinleştirebilirsiniz.

## <a name="optimize-job-execution"></a>İş yürütmeyi iyileştirme

* Gerekirse, örneğin, verileri iki kez kullanırsanız, önbelleğe alma.
* Değişkenleri tüm yürüticilerine yayınla. Değişkenler yalnızca bir kez serileştirilir ve daha hızlı aramalar elde edilir.
* Sürücü üzerindeki iş parçacığı havuzunu kullanın, bu da birçok görev için daha hızlı işlem sağlar.

Performans sorunları için çalışan işlerinizi düzenli olarak izleyin. Belirli sorunlar hakkında daha fazla bilgi için, aşağıdaki performans profili oluşturma araçlarından birini göz önünde bulundurun:

* [INTEL PAL aracı](https://github.com/intel-hadoop/PAT) CPU, depolama ve ağ bant genişliği kullanımını izler.
* [Oracle Java 8 görev denetimi](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profilleri Spark ve yürütücü kodu.

Spark 2. x sorgu performansına yönelik anahtar, tam aşamalı kod oluşturmaya bağlı olan tungsten altyapısıdır. Bazı durumlarda, tam aşama kod üretimi devre dışı bırakılabilir. Örneğin, toplama ifadesinde kesilebilir olmayan bir tür (`string`) kullanırsanız, `HashAggregate`yerine `SortAggregate` görünür. Örneğin, daha iyi performans için aşağıdakileri deneyin ve kod oluşturmayı yeniden etkinleştirin:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HDInsight üzerinde çalışan Apache Spark işlerinin hatasını ayıklama](apache-spark-job-debugging.md)
* [HDInsight üzerinde Apache Spark kümesi için kaynakları yönetme](apache-spark-resource-manager.md)
* [Uzak işleri bir Apache Spark kümesine göndermek için Apache Spark REST API kullanma](apache-spark-livy-rest-interface.md)
* [Apache Spark ayarlama](https://spark.apache.org/docs/latest/tuning.html)
* [Apache Spark Işlerinizi, çalışması için fiili olarak ayarlama](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kronyo serileştirme](https://github.com/EsotericSoftware/kryo)
