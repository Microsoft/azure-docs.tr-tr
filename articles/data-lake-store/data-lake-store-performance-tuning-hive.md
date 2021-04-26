---
title: Performans ayarlama-Hive Azure Data Lake Storage 1.
description: HDInsight ve Azure Data Lake Storage 1. Hive için performans ayarlama hakkında bilgi edinin. G/ç yoğunluklu sorgular için, daha iyi performans sağlamak üzere Hive 'yi ayarlayın.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 082d684ed0a29cb6bf2de9c506886b6d98cf174a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723790"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight ve Azure Data Lake Storage 1. Hive için performans ayarlama Kılavuzu

Varsayılan ayarlar, birçok farklı kullanım durumunda iyi bir performans sağlamak üzere ayarlanmıştır.  G/ç yoğun sorgularda, Hive Azure Data Lake Storage 1. daha iyi performans sağlamak için ayarlanabilir.  

## <a name="prerequisites"></a>Önkoşullar

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* **Data Lake Storage 1. hesabı**. Bir oluşturma hakkında yönergeler için bkz. Azure Data Lake Storage 1. kullanmaya [başlama](data-lake-store-get-started-portal.md)
* Data Lake Storage 1. hesabına erişimi olan **Azure HDInsight kümesi** . Bkz. [Data Lake Storage 1. HDInsight kümesi oluşturma](data-lake-store-hdinsight-hadoop-use-portal.md). Küme için Uzak Masaüstü 'Nü etkinleştirdiğinizden emin olun.
* **HDInsight üzerinde Hive çalıştırma**.  HDInsight üzerinde Hive işleri çalıştırma hakkında bilgi edinmek için bkz. [HDInsight 'Ta Hive kullanma](../hdinsight/hadoop/hdinsight-use-hive.md)
* **Data Lake Storage 1. performans ayarlama yönergeleri**.  Genel performans kavramları için bkz. [Data Lake Storage 1. performans ayarlama Kılavuzu](./data-lake-store-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametreler

Gelişmiş Data Lake Storage 1. performansını ayarlamaya yönelik en önemli ayarlar şunlardır:

* **Hive. tez. Container. size** : her görev tarafından kullanılan bellek miktarı

* **tez. Grouping. min-size** – her bir eşleştiricisindeki minimum boyut

* **tez. Grouping. Max-size** – her bir Eşleştiricideki en büyük boyut

* **hive.exec. Reducer. bytes. per. Reducer** – her Reducer boyutu

**Hive. tez. Container. size** -kapsayıcı boyutu, her görev için kullanılabilir bellek miktarını belirler.  Bu, Hive içindeki eşzamanlılık denetimi için ana giriştir.  

**tez. Grouping. min-size** – Bu parametre, her Eşleyici için en küçük boyutu ayarlamanıza olanak sağlar.  Tez 'nin seçtiği mapbir sayı bu parametrenin değerinden küçükse tez burada ayarlanan değeri kullanır.

**tez. Grouping. Max-size** – parametresi, her bir Eşleştiricideki en büyük boyutu ayarlamanıza olanak sağlar.  Tez 'nin seçtiği mapbir sayı bu parametrenin değerinden büyükse tez burada ayarlanan değeri kullanır.

**hive.exec. Reducer. bytes. per. Reducer** – Bu parametre her bir Reducer boyutunu ayarlar.  Varsayılan olarak, her Reducer 256 MB 'dir.  

## <a name="guidance"></a>Rehber

**hive.exec. Reducer. bytes. per. Reducer** – varsayılan değer sıkıştırılmamış olduğunda iyi sonuç verir.  Sıkıştırılmış veriler için Reducer boyutunu azaltmanız gerekir.  

**Hive. tez. Container. size ayarlayın** – her düğümde, bellek yarn. NodeManager. Resource. Memory-MB tarafından belirtilir ve varsayılan olarak HDI kümesi üzerinde doğru şekilde ayarlanmalıdır.  YARN 'de uygun belleği ayarlama hakkında daha fazla bilgi için bu [gönderisini](../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md)inceleyin.

G/ç yoğunluklu iş yükleri, tez kapsayıcı boyutunu azaltarak daha paralellik avantajdan faydalanabilir. Bu, kullanıcıya eşzamanlılık artıran daha fazla kapsayıcı sağlar.  Ancak, bazı Hive sorguları önemli miktarda bellek (ör. Mapjoın) gerektirir.  Görev yeterli belleğe sahip değilse, çalışma zamanı sırasında yetersiz bellek özel durumu alacaksınız.  Bellek dışında özel durumlar alıyorsanız belleği artırmanız gerekir.   

Ya da paralellik çalıştıran, eşzamanlı görev sayısı toplam YARN bellekle sınırlı olacaktır.  YARN kapsayıcılarının sayısı, kaç tane eşzamanlı görevin çalıştırılacağını dikte eder.  Düğüm başına YARN belleği bulmak için, ambarı 'na gidebilirsiniz.  YARN 'ye gidin ve configs sekmesini görüntüleyin.  YARN belleği Bu pencerede görüntülenir.  

> Toplam YARN bellek = düğüm * her düğüm için Yarn bellek sayısı/Yarn kapsayıcıları = toplam YARN bellek/tez kapsayıcı boyutu

Data Lake Storage 1. kullanarak performansı iyileştirmeye yönelik anahtar, olabildiğince fazla eşzamanlılık artışı sağlar.  Tez, oluşturulması gereken görev sayısını otomatik olarak hesaplar, bu nedenle ayarlamanız gerekmez.   

## <a name="example-calculation"></a>Örnek hesaplama

8 düğümlü bir D14 kümeniz olduğunu varsayalım.  

> Toplam YARN bellek = düğüm * her düğüm için YARN bellek toplam YARN bellek = 8 düğüm * 96GB = 768GB/Yarn kapsayıcı sayısı = 768/3072MB = 256

## <a name="limitations"></a>Sınırlamalar

**Data Lake Storage 1. azaltma** 

Data Lake Storage 1. tarafından belirtilen bant genişliği sınırlarına ulaşırsanız, görev başarısızlıklarını görmek için başlangıç yapmanız gerekir. Bu, görev günlüklerinde azaltma hataları gözlemleyerek belirlenebilir.  Tez kapsayıcı boyutunu artırarak paralellik değerini azaltabilirsiniz.  İşiniz için daha fazla eşzamanlılık gerekiyorsa lütfen bizimle iletişime geçin.

Kısıtlanıyor olup olmadığınızı denetlemek için istemci tarafında hata ayıklama günlüğünü etkinleştirmeniz gerekir. Şunları yapabilirsiniz:

1. Aşağıdaki özelliği Hive config içindeki Log4J özelliklerine koyun. Bu işlem, ambarı görünümünden yapılabilir: Log4J. günlükçü. com. Microsoft. Azure. datalake. Store = DEBUG hata ayıkla yapılandırma için tüm düğümleri/hizmeti yeniden başlatın.

2. Daraldıysanız, Hive günlük dosyasında HTTP 429 hata kodunu görürsünüz. Hive günlük dosyası/t MP/ &lt; user &gt; /Hive.log konumunda

## <a name="further-information-on-hive-tuning"></a>Hive ayarlama hakkında daha fazla bilgi

Hive sorgularınızı ayarlamaya yardımcı olacak birkaç blog aşağıda verilmiştir:
* [HDInsight 'ta Hadoop için Hive sorgularını iyileştirme](../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Azure HDInsight 'ta Hive sorgu dosyasını kodlama](/archive/blogs/bigdatasupport/encoding-the-hive-query-file-in-azure-hdinsight)
* [HDInsight 'ta en iyileştirme Hive ile Ignite konuşur](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)