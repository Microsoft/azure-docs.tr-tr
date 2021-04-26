---
title: Apache Spark-Azure HDInsight için veri depolamayı iyileştirin
description: Azure HDInsight üzerinde Apache Spark ile kullanım için veri depolamayı iyileştirmeden nasıl iyileştireceğinizi öğrenin.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 10f99bdc4a5d418ae1b432a6799c5979e473c5ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98926989"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Apache Spark için veri depolama iyileştirmesi

Bu makalede, Azure HDInsight 'ta verimli Apache Spark iş yürütmesi için veri depolamayı iyileştirmek için stratejiler ele alınmaktadır.

## <a name="overview"></a>Genel Bakış

Spark, CSV, JSON, XML, Parquet, Orc ve avro gibi birçok biçimi destekler. Spark, dış veri kaynaklarıyla çok sayıda daha fazla biçim desteklemek için genişletilebilir. daha fazla bilgi için bkz. [Apache Spark paketleri](https://spark-packages.org).

Performans için en iyi biçim, Spark 2. x içinde varsayılan değer olan *Snappy sıkıştırması* ile birlikte Parquet 'tir. Parquet, verileri sütunlu biçimde depolar ve Spark 'ta çok iyileştirilir.

## <a name="choose-data-abstraction"></a>Veri soyutlama seçin

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

## <a name="select-default-storage"></a>Varsayılan depolamayı seçin

Yeni bir Spark kümesi oluşturduğunuzda, kümenizin varsayılan depolama alanı olarak Azure Blob depolama veya Azure Data Lake Storage seçebilirsiniz. Her iki seçenek de, geçici kümeler için uzun süreli depolamanın avantajını sağlar. Bu nedenle, kümenizi sildiğinizde verileriniz otomatik olarak silinmez. Geçici bir kümeyi yeniden oluşturabilir ve verilerinize erişmeye devam edebilirsiniz.

| Mağaza Türü | Dosya Sistemi | Hız | Larsa | Kullanım Örnekleri |
| --- | --- | --- | --- | --- |
| Azure Blob Depolama | fazla **b:**// | **Standart** | Yes | Geçici küme |
| Azure Blob depolama (güvenli) | fazla **BS:**// | **Standart** | Yes | Geçici küme |
| Azure Data Lake Storage 2. Nesil| **ABFS:**// | **Lýdýr** | Yes | Geçici küme |
| Azure Data Lake Storage 1. Nesil| **adl:**// | **Lýdýr** | Yes | Geçici küme |
| Yerel olarak | Bu **:**// | **En hızlı** | No | Etkileşimli 24/7 kümesi |

Depolama seçeneklerinin tam açıklaması için bkz. [Azure HDInsight kümeleri ile kullanım için depolama seçeneklerini karşılaştırma](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Önbelleği kullanma

Spark,, ve gibi farklı yöntemler aracılığıyla kullanılabilecek kendi yerel önbelleğe alma mekanizmalarını sağlar `.persist()` `.cache()` `CACHE TABLE` . Bu yerel önbelleğe alma, küçük veri kümeleri ve ara sonuçları önbelleğe almanız gereken ETL işlem hatları ile etkilidir. Ancak, bir önbelleğe alınmış tablo bölümleme verilerini tutduğundan Spark Native Caching Şu anda bölümlendirme ile iyi çalışmaz. Daha genel ve güvenilir önbelleğe alma tekniği, *depolama katmanı önbelleğe alma* tekniğidir.

* Yerel Spark önbelleğe alma (önerilmez)
    * Küçük veri kümeleri için iyi.
    * Bölümlendirme ile çalışmaz ve bu, gelecekteki Spark sürümlerinde değişebilir.

* Depolama düzeyi önbelleğe alma (önerilir)
    * , [GÇ önbelleği](apache-spark-improve-performance-iocache.md) özelliği kullanılarak HDInsight üzerinde uygulanabilir.
    * Bellek içi ve SSD önbelleği kullanır.

* Yerel olarak (önerilir)
    * `hdfs://mycluster` Yolun.
    * SSD önbelleği kullanır.
    * Önbelleğe alınmış veriler, kümeyi sildiğinizde, önbellek yeniden oluşturması gerekmeden kaybedilir.

## <a name="optimize-data-serialization"></a>Veri serileştirmesini iyileştirme

Spark işleri dağıtılır, bu nedenle en iyi performans için uygun veri serileştirme önem taşır.  Spark için iki serileştirme seçeneği vardır:

* Java serileştirme varsayılandır.
* `Kryo` serileştirme daha yeni bir biçimdir ve Java 'dan daha hızlı ve daha küçük seri hale getirme oluşmasına neden olabilir.  `Kryo` , sınıfları programınıza kaydetmenizi gerektirir ve henüz tüm serileştirilebilir türleri desteklemez.

## <a name="use-bucketing"></a>Gruplandırmayı kullanma

Demetlenmesidir, veri bölümlemeye benzer. Ancak her demet yalnızca bir tane yerine bir sütun değerleri kümesi tutabilir. Bu yöntem, ürün tanımlayıcıları gibi büyük (milyonlarca veya daha fazla) değer üzerinde bölümlendirme için iyi bir sonuç verir. Bir demet, satırın demet anahtarı karma tarafından belirlenir. Bulaştırılmış tablolar, verilerin nasıl kullanıldığı ve sıralandığı hakkında meta verileri depoladıklarından benzersiz iyileştirmeler sunmaktadır.

Bazı gelişmiş demetlenmesidir özellikleri şunlardır:

* Demetlenmesidir meta bilgilerine dayalı sorgu iyileştirmesi.
* İyileştirilmiş toplamalar.
* İyileştirilmiş birleşimler.

Bölümleme ve demetlenmesidir 'ı aynı anda kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark için veri işlemeyi iyileştirin](optimize-cluster-configuration.md)
* [Apache Spark için bellek kullanımını iyileştirin](optimize-memory-usage.md)
* [Apache Spark için küme yapılandırmasını iyileştirme](optimize-cluster-configuration.md)
