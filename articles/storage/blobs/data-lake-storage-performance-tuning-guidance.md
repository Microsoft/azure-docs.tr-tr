---
title: Azure Data Lake Storage 2. performans ayarlama yönergeleri | Microsoft Docs
description: Azure Data Lake Storage 2. performans ayarlama yönergeleri
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b134842303bebdf10efdf388057c8ad7b3be61be
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855582"
---
# <a name="tuning-azure-data-lake-storage-gen2-for-performance"></a>Performans için Azure Data Lake Storage 2. ayarlama

Azure Data Lake Storage 2., g/ç yoğun analiz ve veri hareketi için yüksek aktarım hızını destekler.  Data Lake Storage 2., tüm kullanılabilir üretilen iş üretimini kullanarak, en iyi performansı elde etmek için, bir saniyede okunabilen veya yazılan veri miktarı önemlidir.  Bu, olabildiğince çok okuma ve yazma işlemleri gerçekleştirerek elde edilir.

![Data Lake Storage 2. performans](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage 2., tüm analiz senaryosu için gerekli aktarım hızını sağlamak üzere ölçeklendirebilir. Varsayılan olarak, Data Lake Storage 2. bir hesap, geniş bir kullanım durumu kategorisinin ihtiyaçlarını karşılamak için otomatik olarak yeterli aktarım hızı sağlar. Müşterilerin varsayılan limite çalıştığı durumlar için Data Lake Storage 2. hesabı, [Azure desteği](https://azure.microsoft.com/support/faq/)ile iletişime geçerek daha fazla verimlilik sunacak şekilde yapılandırılabilir.

## <a name="data-ingestion"></a>Veri alımı

Kaynak sistemden Data Lake Storage 2. verileri alırken, kaynak donanımın, kaynak ağ donanımının ve Data Lake Storage 2. ağ bağlantısının performans sorunu olduğunu göz önünde bulundurmanız önemlidir.  

![Data Lake Storage 2. performans](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Veri hareketinin bu faktörlerden etkilenmemesini sağlamak önemlidir.

### <a name="source-hardware"></a>Kaynak donanım

Azure 'da şirket içi makineleri veya VM 'Leri kullanıp kullanmayacağınızı, uygun donanımı dikkatle seçmeniz gerekir. Kaynak disk donanımı için SSD 'leri ve daha hızlı bir şekilde disk donanımı seçmek için SSDs 'yi tercih edin. Kaynak ağ donanımı için mümkün olan en hızlı NIC 'Leri kullanın.  Azure 'da, uygun şekilde güçlü disk ve ağ donanımlarına sahip Azure D14 VM 'Leri öneririz.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Data Lake Storage 2. ağ bağlantısı

Kaynak verileriniz ve Data Lake Storage 2. arasındaki ağ bağlantısı bazen performans sorunlarına neden olabilir. Kaynak verileriniz şirket Içinde olduğunda [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) ile adanmış bir bağlantı kullanmayı göz önünde bulundurun. Kaynak verileriniz Azure 'da ise, veriler Data Lake Storage 2. hesabıyla aynı Azure bölgesinde olduğunda performans en iyi sonucu verir.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>En yüksek paralelleştirme için veri alma araçlarını yapılandırın

Yukarıdaki kaynak donanım ve ağ bağlantısı sorunlarını giderdikten sonra, Alım araçlarınızı yapılandırmaya hazırsanız. Aşağıdaki tabloda, çeşitli popüler alım araçları için anahtar ayarları özetlenmektedir ve bunlar için ayrıntılı performans ayarlama makaleleri sağlanmaktadır.  Senaryolarınız için kullanılacak araç hakkında daha fazla bilgi edinmek için bu [makaleyi](data-lake-storage-data-scenarios.md)ziyaret edin.

| Tool               | Ayarlar     | Diğer Ayrıntılar                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -a (Eşleyici)   | [Bağlantı](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Bağlantı](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS. Azure. Block. size,-ı (Mapper)    |   [Bağlantı](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Veri kümesini yapı

Veriler Data Lake Storage 2. depolandığında, dosya boyutu, dosya sayısı ve klasör yapısı performansı etkiler.  Aşağıdaki bölümde bu alanlardaki en iyi uygulamalar açıklanmaktadır.  

### <a name="file-size"></a>Dosya boyutu

Genellikle, HDInsight ve Azure Data Lake Analytics gibi analiz altyapılarının dosya başına ek yükü vardır. Verilerinizi birçok küçük dosya olarak depoluseniz bu, performansı olumsuz etkileyebilir. Genel olarak, daha iyi performans için verilerinizi daha büyük boyutlu dosyalara düzenleyin (256 MB/100 GB boyutunda). Bazı altyapıların ve uygulamaların boyutu 100 GB 'tan büyük dosyaları verimli bir şekilde işleme sorunu olabilir.

Bazen veri işlem hatları, çok sayıda küçük dosya içeren ham veriler üzerinde sınırlı denetime sahiptir. Aşağı akış uygulamalarında kullanılmak üzere daha büyük dosyalar üreten bir "pişirme" işleminin olması önerilir.

### <a name="organizing-time-series-data-in-folders"></a>Klasörlerdeki zaman serisi verilerini düzenleme

Hive iş yükleri için, zaman serisi verilerinin bölüm ayıklanması, bazı sorguların performansı artıran verilerin yalnızca bir alt kümesini okumasına yardımcı olabilir.    

Zaman serisi verileri alan işlem hatları, genellikle dosyalarını dosya ve klasörler için çok yapılandırılmış bir adlandırma ile yerleştirir. Aşağıda, şu tarihe göre yapılandırılmış veriler için görtiğimiz çok yaygın bir örnek verilmiştir:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Tarih saat bilgilerinin hem klasör hem de dosya adında göründüğünü unutmayın.

Tarih ve saat için aşağıdaki genel bir örüntü

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Yine, klasör ve dosya kuruluşunda yaptığınız seçim, daha büyük dosya boyutları ve her klasörde makul sayıda dosya için iyileştirmelidir.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>HDInsight 'ta Hadoop ve Spark iş yüklerinde g/ç yoğunluklu işleri en iyi duruma getirme

İşler aşağıdaki üç kategoriden birine girer:

* **Yoğun CPU kullanımı.**  Bu işlerin en az g/ç süreleriyle uzun hesaplama süreleri vardır.  Makine öğrenimi ve doğal dil işleme işleri örnekleri içerir.  
* **Bellek kullanımı.**  Bu işler çok fazla bellek kullanır.  Örnekler PageRank ve gerçek zamanlı analiz işleri içerir.  
* **G/ç yoğun.**  Bu işler, çoğu zaman g/ç yaparken harcamaktadır.  Ortak bir örnek, yalnızca okuma ve yazma işlemleri olan bir kopyalama işdir.  Diğer örnekler arasında çok fazla veri okuyan veri hazırlama işleri, bazı veri dönüştürme işlemleri gerçekleştirir ve ardından veriler depoya geri yazılır.  

Aşağıdaki kılavuz yalnızca g/ç yoğun işler için geçerlidir.

## <a name="general-considerations"></a>Genel hususlar

Tek bir işlemde 100 MB 'a kadar okuyan veya yazan bir işiniz olabilir, ancak bu boyutta bir arabellek performansı tehlikeye atabilir.
Performansı iyileştirmek için, bir g/ç işleminin boyutunu 4 MB ile 16 arasında tutmaya çalışın.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>HDInsight kümesi için genel konular

* **HDInsight sürümleri.** En iyi performans için HDInsight 'ın en son sürümünü kullanın.
* **Düzenleye.** Data Lake Storage 2. hesabını HDInsight kümesiyle aynı bölgeye yerleştirin.  

An HDInsight küme iki baş düğümden ve bazı çalışan düğümlerinden oluşur. Her çalışan düğümü, VM türü tarafından belirlenen belirli sayıda çekirdek ve bellek sağlar.  Bir işi çalıştırırken YARN, kapsayıcılar oluşturmak için kullanılabilir belleği ve çekirdekleri ayıran kaynak Negotiator ' dır.  Her kapsayıcı, işi tamamlaması için gereken görevleri çalıştırır.  Kapsayıcılar, görevleri hızla işlemek için paralel olarak çalışır. Bu nedenle, mümkün olduğunca çok sayıda paralel kapsayıcı çalıştırılarak performans geliştirilmiştir.

HDInsight kümesi içinde, kapsayıcıların sayısını artırmak ve tüm kullanılabilir üretilen işi kullanmak için ayarlanabilir üç katman vardır.  

* **Fiziksel katman**
* **YARN katmanı**
* **İş yükü katmanı**

### <a name="physical-layer"></a>Fiziksel katman

**Kümeyi daha fazla düğüm ve/veya daha büyük boyutlu VM 'Ler ile çalıştırın.**  Daha büyük bir küme, aşağıdaki resimde gösterildiği gibi daha fazla YARN kapsayıcısı çalıştırmanızı sağlar.

![Data Lake Storage 2. performans](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Daha fazla ağ bant genişliğine sahip VM 'Leri kullanın.**  Data Lake Storage 2. aktarım hızına kıyasla daha az ağ bant genişliği varsa, ağ bant genişliği miktarı bir performans sorunu olabilir.  Farklı VM 'Lerde farklı ağ bant genişliği boyutları olacaktır.  Olası en büyük ağ bant genişliğine sahip bir VM türü seçin.

### <a name="yarn-layer"></a>YARN katmanı

**Daha küçük YARN kapsayıcıları kullanın.**  Aynı miktarda kaynakla daha fazla kapsayıcı oluşturmak için her bir YARN kapsayıcısının boyutunu küçültün.

![Data Lake Storage 2. performans](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

İş yükünüze bağlı olarak, gereken en az YARN kapsayıcı boyutu her zaman olacaktır. Çok küçük bir kapsayıcı seçerseniz, işleriniz bellek dışı sorunlara çalışacaktır. Genellikle YARN kapsayıcıları 1 GB 'tan küçük olmamalıdır. 3GB YARN kapsayıcıları görmek yaygındır. Bazı iş yükleri için daha büyük YARN kapsayıcıları gerekebilir.  

**YARN kapsayıcısı başına çekirdekleri artırın.**  Her bir kapsayıcıda çalışan paralel görev sayısını artırmak için her bir kapsayıcıya ayrılan çekirdek sayısını artırın.  Bu, kapsayıcı başına birden çok görevi çalıştıran Spark gibi uygulamalar için çalışır.  Her kapsayıcıda tek bir iş parçacığı çalıştıran Hive gibi uygulamalar için, kapsayıcı başına daha fazla çekirdeğe sahip olmak yerine daha fazla kapsayıcı olması daha iyidir.

### <a name="workload-layer"></a>İş yükü katmanı

**Tüm kullanılabilir kapsayıcıları kullanın.**  Tüm kaynakların kullanılabilmesi için kullanılabilir kapsayıcı sayısına eşit veya daha büyük olacak görev sayısını ayarlayın.

![Data Lake Storage 2. performans](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Başarısız görevler maliyetlidir.** Her görevin işlemek için büyük miktarda veri varsa, bir görevin başarısızlığı pahalı bir yeniden denemeye neden olur.  Bu nedenle, her biri az miktarda veri işleyen daha fazla görev oluşturmak daha iyidir.

Yukarıdaki genel yönergelere ek olarak, her uygulamanın belirli bir uygulama için ayarlanacak farklı parametreleri vardır. Aşağıdaki tabloda, her bir uygulama için performans ayarlama ile çalışmaya başlamak için bazı parametreler ve bağlantılar listelenmektedir.

| İş yükü | Görevleri ayarlanacak parametre |
|----------|------------------------|
| [HDInsight 'ta Spark](data-lake-storage-performance-tuning-spark.md) | <ul><li>Sayı yürüticileri</li><li>Yürütücü-bellek</li><li>Yürütücü-çekirdekler</li></ul> |
| [HDInsight üzerinde Hive](data-lake-storage-performance-tuning-hive.md) | <ul><li>Hive. tez. Container. size</li></ul> |
| [HDInsight üzerinde MapReduce](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>MapReduce. Map. Memory</li><li>MapReduce. job. Maps</li><li>MapReduce. küçültme. bellek</li><li>MapReduce. job. azaltıyor</li></ul> |
| [HDInsight üzerinde fırtınası](data-lake-storage-performance-tuning-storm.md)| <ul><li>Çalışan işlem sayısı</li><li>Spout yürütücü örneklerinin sayısı</li><li>Cıvagular yürütücü örneklerinin sayısı </li><li>Spout görevi sayısı</li><li>Cıvatın görevi sayısı</li></ul>|

## <a name="see-also"></a>Ayrıca bkz.
* [Azure Data Lake Storage 2. genel bakış](data-lake-storage-introduction.md)
