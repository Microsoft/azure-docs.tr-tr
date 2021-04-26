---
title: Azure HDInsight yüksek oranda kullanılabilir çözüm mimarisi örnek olay incelemesi
description: Bu makale, olası bir Azure HDInsight yüksek oranda kullanılabilir çözüm mimarisine yönelik kurgusal bir olay incelemesi.
keywords: Hadoop yüksek kullanılabilirlik
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6b995e2ab5ba663f6e33b009062859eb32928cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99508600"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Azure HDInsight yüksek oranda kullanılabilir çözüm mimarisi örnek olay incelemesi

Azure HDInsight 'ın çoğaltma mekanizmaları, yüksek oranda kullanılabilir bir çözüm mimarisiyle tümleştirilebilir. Bu makalede, olası yüksek kullanılabilirliğe sahip olağanüstü durum kurtarma yaklaşımları, maliyet konuları ve bunlara karşılık gelen tasarımlarının açıklanmasında contoso perakende için kurgusal bir büyük/küçük harf incelemesi kullanılır.

Yüksek kullanılabilirlik olağanüstü durum kurtarma önerileri birçok PERMÜTASYONA ve kombinasyona sahip olabilir. Bu çözümler, deliberating ve her seçeneğin dezavantajlarını ve dezavantajlarını bir sonraki bir noktada ulaşmalıdır. Bu makalede yalnızca olası bir çözüm açıklanmaktadır.

## <a name="customer-architecture"></a>Müşteri mimarisi

Aşağıdaki görüntüde contoso perakende birincil mimarisi gösterilmektedir. Mimari, bir akış iş yükü, Batch iş yükü, katman, tüketim katmanı, depolama katmanı ve sürüm denetiminden oluşur.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Contoso perakende mimarisi":::

### <a name="streaming-workload"></a>Akış iş yükü

Cihazlar ve sensörler, mesajlaşma çerçevesini oluşturan HDInsight Kafka 'a veri üretir. Kafka konularındaki Spark tüketicisini okur An HDInsight. Spark, gelen iletileri dönüştürür ve hizmet katmanındaki HDInsight HBase kümesine yazar.

### <a name="batch-workload"></a>Batch iş yükü

Hive ve şirket içi işlem sistemlerinden MapReduce verileri çalıştıran Hadoop kümesi An HDInsight. Hive ve MapReduce tarafından dönüştürülen ham veriler, Azure Data Lake Storage 2. tarafından desteklenen Data Lake 'in mantıksal bir bölümünde Hive tablolarında depolanır. Hive tablolarında depolanan veriler de Spark SQL için kullanılabilir hale getirilir ve bu veriler hizmet sunmak için HBase 'de depolanmadan önce toplu dönüştürme yapar.

### <a name="serving-layer"></a>Sunum katmanı

Apache Phoenix olan An HDInsight HBase kümesi, verileri Web uygulamalarına ve görselleştirme panolarına sunacak şekilde kullanılır. An HDInsight LLAP kümesi, iç raporlama gereksinimlerini karşılamak için kullanılır.

### <a name="consumption-layer"></a>Tüketim katmanı

Bir Azure API Apps ve genel kullanıma yönelik bir Web sayfasını geri API Management. İç raporlama gereksinimleri Power BI tarafından yerine getirilir.

### <a name="storage-layer"></a>Depolama katmanı

Mantıksal olarak bölümlenmiş Azure Data Lake Storage 2., kurumsal veri Gölü olarak kullanılır. HDInsight meta tasmres, Azure SQL DB tarafından desteklenir.

### <a name="version-control-system"></a>Sürüm denetim sistemi

Bir Azure Pipelines tümleştirilmiş ve Azure dışında barındırılan bir sürüm denetim sistemi.

## <a name="customer-business-continuity-requirements"></a>Müşteri iş sürekliliği gereksinimleri

Olağanüstü durum varsa, ihtiyacınız olan en düşük iş işlevlerini belirlemeniz önemlidir.

### <a name="contoso-retails-business-continuity-requirements"></a>Contoso perakende iş sürekliliği gereksinimleri

* Bölgesel bir hata veya bölgesel hizmet durumu sorununa karşı korunması gerekir.
* Müşterilerimin bir 404 hatası görmemesi gerekir. Ortak içerik her zaman sunulmalıdır. (RTO = 0)  
* Yılın çoğu kapsamında, 5 saatten eski olan ortak içerikleri gösterebiliriz. (RPO = 5 saat)
* Tatil döneminde, herkese açık olan içerik her zaman güncel olmalıdır. (RPO = 0)
* İç raporlama gereksinimlerim iş sürekliliği açısından kritik olarak kabul edilmez.
* İş sürekliliği maliyetlerini iyileştirin.

## <a name="proposed-solution"></a>Önerilen çözüm

Aşağıdaki görüntüde contoso perakende yüksek kullanılabilirlik olağanüstü durum kurtarma mimarisi gösterilmektedir.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Contoso çözümü":::

**Kafka** , birincil bölgeden Ikincil bölgeye Kafka konularını yansıtmak için [etkin – Pasif](hdinsight-business-continuity-architecture.md#apache-kafka) çoğaltma kullanır. Kafka çoğaltma için bir alternatif, her iki bölgede de Kafka 'e üretmek olabilir.

**Hive ve Spark,** normal saatlerde [etkin birincil – isteğe bağlı ikincil](hdinsight-business-continuity-architecture.md#apache-spark) çoğaltma modelleri kullanır. Hive çoğaltma işlemi düzenli aralıklarla çalışır ve Hive Azure SQL meta veri deposu ve Hive depolama hesabı çoğaltmasını eşlik eden bir işlemdir. Spark Storage hesabı, ADF DistCP kullanılarak düzenli aralıklarla çoğaltılır. Bu kümelerin geçici doğası maliyetleri iyileştirmenize yardımcı olur. Çoğaltmalar, beş saatlik gereksinimin içinde iyi bir RPO 'ya ulaşmak için her 4 saatte bir zamanlanır.

**HBase** çoğaltma, verilerin bölgeden bağımsız olarak her zaman sunulmasını sağlamak için [öncü – izleyici](hdinsight-business-continuity-architecture.md#apache-hbase) modelini normal zamanlarda kullanır ve RPO çok düşüktür.

Birincil bölgede bölgesel bir hata varsa, Web sayfası ve arka uç içeriği, bir miktar derecesi ile 5 saat boyunca ikincil bölgeden sunulur. Azure hizmet durumu panosu, beş saatlik pencerede bir kurtarma ETA belirtmezse, contoso perakende, ikincil bölgede Hive ve Spark dönüştürme katmanını oluşturur ve ardından tüm yukarı akış veri kaynaklarını ikincil bölgeye işaret eder. İkincil bölgenin yazılabilir olması, çoğaltmayı birincil olarak geri çalıştıran bir yeniden çalışma işlemine neden olur.

Yoğun bir alışveriş döneminde, tüm ikincil işlem hattı her zaman etkin ve çalışır. Kafka üreticileri hem bölgelere hem de HBase çoğaltmaya Leader-Follower, genel kullanıma açık içeriğin her zaman güncel olduğundan emin olmak için Leader-Leader olarak değiştirilir.

İş sürekliliği açısından önemli olmadığından, hiçbir yük devretme çözümünün iç raporlama için tasarlanması gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede ele alınan öğeler hakkında daha fazla bilgi edinmek için bkz.:

* [Azure HDInsight iş sürekliliği](./hdinsight-business-continuity.md)
* [Azure HDInsight iş sürekliliği mimarileri](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](./hadoop/hdinsight-use-hive.md)
