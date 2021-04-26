---
title: HDInsight 4,0 genel bakış-Azure
description: HDInsight 3.6 ile HDInsight 4.0 özelliklerinin karşılaştırılması, sınırlamalar ve yükseltme önerileri.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 694acc0005e90d8299d46528f83ba68ea3fcf1c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931182"
---
# <a name="azure-hdinsight-40-overview"></a>Azure HDInsight 4,0 genel bakış

Azure HDInsight, Apache Hadoop ve Apache Spark kurumsal müşteriler arasındaki en popüler hizmetlerden biridir. HDInsight 4,0, Apache Hadoop bileşenlerinden oluşan bir bulut dağıtımıdır. Bu makalede en güncel Azure HDInsight sürümü hakkında bilgiler verilmekte ve yükseltme yöntemleri anlatılmaktadır.

## <a name="whats-new-in-hdinsight-40"></a>HDInsight 4,0 ' deki yenilikler nelerdir?

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3,0 ve düşük gecikme süreli analitik işleme

Düşük gecikmeli analitik işleme (LLAP) Apache Hive kalıcı sorgu sunucuları ve bellek içi önbelleğe alma kullanır. Bu işlem, uzak bulut depolamadaki verilere hızlı SQL sorgu sonuçları sunar. Hive LLAP, Hive sorgularının parçalarını çalıştıran kalıcı bir Daemon 'ları kümesi kullanır. LLAP üzerinde sorgu yürütme LLAP kullanılmayan Hive ile benzerdir ve çalışan görevleri kapsayıcıların değil LLAP daemon'larının içinde çalışır.

Hive LLAP hizmetinin avantajları şunlardır:

* Performans ve uyumluluk olmadan taviz vermeden derin SQL Analytics yapabilme özelliği. Karmaşık birleşimler, alt sorgular, Pencereleme işlevleri, sıralama, Kullanıcı tanımlı işlevler ve karmaşık toplamalar gibi.

* Etkileşimli sorguların verilerin hazırlandığı depolama alanındaki verilerle gerçekleştirilerek analitik işlem için verilerin depolamadan başka bir altyapıya taşınması ihtiyacınız ortadan kaldırma.

* Sorgu sonuçlarının önbelleğe alınması, önceden hesaplanan sorgu sonuçlarının yeniden kullanılmasını sağlar. Bu önbellek, sorgu için gereken küme görevlerini çalıştırırken harcanan zaman ve kaynakları kaydeder.

### <a name="hive-dynamic-materialized-views"></a>Hive dinamik gerçekleştirilmiş görünümleri

Hive artık dinamik gerçekleştirilmiş görünümleri veya ilgili özetler için önceden hesaplamayı desteklemektedir. Görünümler, veri ambarlarında sorgu işlemeyi hızlandırır. Gerçekleştirilmiş görünümler yerel Hive ortamında depolanabilir ve LLAP hızlandırmasından sorunsuz bir şekilde faydalanabilir.

### <a name="hive-transactional-tables"></a>Hive işlem tabloları

HDI 4,0 Apache Hive 3 içerir. Hive 3 ' te, Hive ambarında bulunan işlem tabloları için Atomicity, tutarlılık, yalıtım ve dayanıklılık uyumluluğu gerekir. ACID uyumluluğuna sahip tablolar ve tablo verileri için erişim ve yönetim Hive tarafından gerçekleştirilir. Oluşturma, alma, güncelleştirme ve silme (CRUD) tablolarının verileri en Iyileştirilmiş satır sütunu (ORC) dosya biçiminde olmalıdır. Yalnızca INSERT tabloları tüm dosya biçimlerini destekler. 

> [!Note]
> ACID/işlemsel destek yalnızca yönetilen tablolar için ve dış tablolar için geçerlidir. Hive dış tabloları, dış tarafların, temel alınan verilerin herhangi bir değişikliği yapmadan tablo verilerini okuyup yazabilmesi için tasarlanmıştır. ACID tablolarında Hive, temel alınan verileri compactions ve Transactions ile değiştirebilir.

ACID tablolarının bazı avantajları şunlardır:

* ACID v2 hem depolama biçimi hem de yürütme altyapısı alanında performans geliştirmelerine sahiptir.

* ACID, veri güncelleştirmelerine tam destek sunmak için varsayılan olarak etkinleştirilir.

* Geliştirilmiş ACID özellikleri sayesinde satır düzeyinde güncelleştirme ve silme işlemi gerçekleştirebilirsiniz.

* Bu durum Performans açısından ek yük oluşturmaz.

* Gruplandırma gerekli değildir.

* Spark, Hive Warehouse Connector ile Hive ACID tablolarında veri okuma ve yazma işlemleri gerçekleştirebilir.

[Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html) hakkında daha fazla bilgi edinin.

### <a name="apache-spark"></a>Apache Spark

Apache Spark, güncelleştirilebilir tabloları ve ACID işlemlerini Hive Warehouse Connector ile alır. Hive Warehouse Connector, tam işlevlere erişmek için Hive işlem tablolarını Spark'ta dış tablo olarak kaydetmenize izin verir. Önceki sürümler yalnızca tablo bölümü değiştirmeyi destekliyordu. Hive ambarı Bağlayıcısı akış veri çerçevelerini de destekler.  Bu işlem, Spark 'dan işlem ve akış Hive tablolarına okuma ve yazma akışlarını akışa kaydeder.

Spark yürütücüleri doğrudan Hive LLAP daemon'larına bağlanarak verileri işlemsel bir şekilde alabilir ve bu sayede verilerin denetimi Hive'da kalır.

HDInsight 4.0'da Apache Spark şu senaryoları destekler:

* Raporlama için kullanılan işlem tablosunda makine öğrenmesi modeli eğitimi çalıştırma.
* ACID işlemlerini kullanarak Spark ML'den Hive tablosuna güvenli bir şekilde sütun ekleme.
* Hive akış tablosundaki değişiklik akışında bir Spark akış işi çalıştırma.
* Doğrudan bir Spark Yapılandırılmış Akış işinden ORC dosyası oluşturma.

Artık doğrudan Spark 'tan Hive işlem tablolarına erişmeye çalışırken endişelenmeniz gerekmez. Tutarsız sonuçlara, yinelenen verilere veya veri bozulmasına neden olur. HDInsight 4,0 ' de Spark tabloları ve Hive tabloları ayrı bir meta Tasteres içinde tutulur. Hive Data Warehouse Connector ile Hive işlem tablolarını açıkça Spark dış tabloları olarak kaydedebilirsiniz.

[Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html) hakkında daha fazla bilgi edinin.

### <a name="apache-oozie"></a>Apache Oozie

HDI 4.0 sürümünde bulunan Apache Oozie 4.3.1'de aşağıdaki değişiklikler yapılmıştır:

* Oozie artık Hive eylemlerini çalıştırmaz. Hive CLI kaldırılmış ve yerine BeeLine getirilmiştir.

* **job.properties** dosyanıza bir hariç tutma deseni ekleyerek istenmeyen bağımlılıkları paylaşma kitaplığından hariç tutabilirsiniz.

[Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html) hakkında daha fazla bilgi edinin.

## <a name="how-to-upgrade-to-hdinsight-40"></a>HDInsight 4,0 sürümüne nasıl yükseltilir?

Bir üretim ortamında en son sürümü uygulamadan önce bileşenlerinizi kapsamlı bir şekilde test edin. HDInsight 4,0, yükseltme işlemini başlatmak için kullanılabilir. HDInsight 3,6, yanlışlıkla mishaps engellemek için varsayılan seçenektir.

HDInsight 'ın önceki sürümlerinden HDInsight 4,0 sürümüne desteklenen bir yükseltme yolu yoktur. Metasme ve blob veri biçimleri değiştiğinden, 4,0 önceki sürümlerle uyumlu değildir. Yeni HDInsight 4,0 ortamınızı geçerli üretim ortamınızdan ayrı tutmanız önemlidir. HDInsight 4,0 ' i geçerli ortamınıza dağıtırsanız, meta veri deposu kalıcı olarak yükseltilir.  

## <a name="limitations"></a>Sınırlamalar

* HDInsight 4,0, Apache Hive için MapReduce 'yi desteklemez. Bunun yerine Apache Tez kullanın. [Apache Tez](https://tez.apache.org/) hakkında daha fazla bilgi edinin.
* HDInsight 4,0 Apache Storm desteklemez.
* HDInsight 4,0, ML Hizmetleri küme türünü desteklemez.
* Hive görünümü yalnızca, 4,1 ' e eşit veya daha büyük bir sürüm numarasına sahip HDInsight 4,0 kümelerinde kullanılabilir. Bu sürüm numarası, ambarı yönetici > sürümlerinde kullanılabilir.
* Apache Zeppelin içindeki kabuk yorumlayıcısı Spark ve etkileşimli sorgu kümelerinde desteklenmez.
* Spark-LLAP kümesinde LLAP özelliğini *devre dışı* bırakamazsınız. LLAP özelliğini yalnızca kapatabilirsiniz.
* Azure Data Lake Storage 2., bir Spark kümesinde Jupyıter not defterlerini kaydedemez.
* Apache Pig, tez 'de varsayılan olarak çalışır, ancak bunu MapReduce olarak değiştirebilirsiniz
* Satır ve sütun güvenliği için Spark SQL Ranger tümleştirmesi kullanım dışıdır
* Spark 2,4 ve Kafka 2,1, HDInsight 4,0 sürümünde sunulmaktadır. bu nedenle Spark 2,3 ve Kafka 1,1 artık desteklenmemektedir. HDInsight 4,0 içinde Spark 2,4 & Kafka 2,1 ve üstünü kullanmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar

* [HBase geçiş kılavuzu](./hbase/apache-hbase-migrate-new-version.md)
* [Hive geçiş kılavuzu](./interactive-query/apache-hive-migrate-workloads.md)
* [Kafka geçiş kılavuzu](./kafka/migrate-versions.md)
* [Spark geçiş kılavuzu](./spark/migrate-versions.md)
* [Azure HDInsight belgeleri](index.yml)
* [Sürüm Notları](hdinsight-release-notes.md)
