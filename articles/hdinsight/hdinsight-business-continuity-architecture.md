---
title: Azure HDInsight iş sürekliliği mimarileri
description: Bu makalede HDInsight için farklı olası iş sürekliliği mimarileri açıklanmaktadır
keywords: Hadoop yüksek kullanılabilirlik
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f74309370f1489714013344116e7feb9551fbfd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933444"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Azure HDInsight iş sürekliliği mimarileri

Bu makalede, Azure HDInsight için göz önünde bulundurmanız gerekebilecek iş sürekliliği mimarilerine yönelik birkaç örnek verilmiştir. Bir olağanüstü durum sırasında azaltılmış işlevsellik toleransı, bir uygulamadan sonrakine göre farklılık gösteren bir iş karardır. Bazı uygulamaların kullanılamaz durumda olması veya bir dönem için azaltılmış işlevlerle veya gecikmeden kısmen kullanılabilmesi kabul edilebilir. Diğer uygulamalar için, azaltılmış işlevler kabul edilemez.

> [!NOTE]
> Bu makalede sunulan mimariler ayrıntılı bir şekilde değildir. Beklenen iş sürekliliği, işlemsel karmaşıklık ve sahip olma maliyetini fark eden bir amaç yaptıktan sonra kendi benzersiz mimarlerinizi tasarlamanız gerekir.

## <a name="apache-hive-and-interactive-query"></a>Apache Hive ve etkileşimli sorgu

[Hive çoğaltma v2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS) , HDInsight Hive ve etkileşimli sorgu kümelerinde iş sürekliliği için önerilir. Tek başına Hive kümesinin çoğaltılması gereken kalıcı bölümleri depolama katmanıdır ve Hive meta veri deposu. Kurumsal Güvenlik Paketi ile çok kullanıcılı bir senaryoda Hive kümeleri Azure Active Directory Domain Services ve Ranger meta veri deposu gerekir.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Hive ve etkileşimli sorgu mimarisi":::

Hive olay tabanlı çoğaltma, birincil ve ikincil kümeler arasında yapılandırılır. Bu, önyükleme ve artımlı çalıştırmaların iki ayrı aşamasından oluşur:

* Önyükleme, Hive meta veri deposu bilgileri de dahil olmak üzere Hive ambarının tamamını Birincilden ikinciye çoğaltır.

* Artımlı çalıştırmalar, birincil kümede otomatikleştirilir ve artımlı çalıştırmalar sırasında oluşturulan olaylar ikincil kümede yürütülür. İkincil küme, çoğaltma çalıştıktan sonra ikincil kümenin birincil kümenin olaylarıyla tutarlı olmasını sağlamak üzere birincil kümeden oluşturulan olaylarla yakalar.

İkincil küme yalnızca, dağıtılmış kopyayı çalıştırmak için çoğaltma sırasında gereklidir, `DistCp` ancak depolama ve metasmres 'nin kalıcı olması gerekir. Çoğaltma işleminden önce isteğe bağlı olarak bir komut dosyası oluşturulmuş ikincil küme çalıştırmayı tercih edebilirsiniz, çoğaltma betiğini üzerinde çalıştırabilir ve sonra çoğaltmayı başarılı bir şekilde koparın.

İkincil küme genellikle salt okunurdur. İkincil kümeyi okuma-yazma yapabilirsiniz, ancak bu, değişiklikleri ikincil kümeden birincil kümeye çoğaltmayı kapsayan ek karmaşıklıklar ekler.

### <a name="hive-event-based-replication-rpo--rto"></a>Hive olay tabanlı çoğaltma RPO & RTO

* RPO: veri kaybı Birincilden ikinciye yapılan son başarılı artımlı çoğaltma olayı ile sınırlıdır.

* RTO: ikincil ile, hata ile yukarı akış ve aşağı akış işlemlerinin sürdürme arasındaki zaman.

### <a name="apache-hive-and-interactive-query-architectures"></a>Apache Hive ve etkileşimli sorgu mimarileri

#### <a name="hive-active-primary-with-on-demand-secondary"></a>İsteğe bağlı ikincil ile Hive etkin birincil

İsteğe bağlı *İkincil mimariye sahip etkin bir birincil* sürümünde, uygulamalar, normal işlemler sırasında ikincil bölgede hiçbir küme sağlanana sırada etkin birincil bölgeye yazılır. İkincil bölgedeki SQL meta veri deposu ve depolama kalıcıdır, ancak HDInsight kümesi betikleştirilmiş ve yalnızca zamanlanan Hive çoğaltması çalıştırılmadan önce isteğe bağlı olarak dağıtılır.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="isteğe bağlı ikincil ile etkin birincil":::

#### <a name="hive-active-primary-with-standby-secondary"></a>Hive etkin birincil, bekleme ikincili

Etkin bir *birincil durumda, bekleme ikincili olan* uygulamalar etkin birincil bölgeye yazılır, ancak bekleyen bir ikincil küme, normal işlemler sırasında salt okunurdur. Normal işlemler sırasında, bölgeye özgü okuma işlemlerinin ikinciye yük devretme işlemini seçebilirsiniz.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="Etkin ikincil ile etkin birincil":::

Hive çoğaltma ve kod örnekleri hakkında daha fazla bilgi için [Azure HDInsight kümelerinde Apache Hive çoğaltmaya](./interactive-query/apache-hive-replication.md) başvurabilirsiniz

## <a name="apache-spark"></a>Apache Spark

Spark iş yükleri Hive bileşeni içerebilir veya içermeyebilir. Spark SQL iş yüklerinin Hive 'den veri okuma ve yazma olanağı sağlamak için, HDInsight Spark kümeleri Hive/etkileşimli sorgu kümelerinden aynı bölgedeki Hive özel meta verileri paylaşır. Bu tür senaryolarda, Spark iş yüklerinin çapraz bölge çoğaltması, Hive meta veri ve depolama alanının çoğaltılmasına de eşlik etmelidir. Bu bölümdeki yük devretme senaryoları her ikisi için de geçerlidir:

* HDInsight etkileşimli sorgu kümesi kullanarak [Hive ambar Bağlayıcısı (HWC) kullanan ACID tablolarında Spark SQL](./interactive-query/apache-hive-warehouse-connector.md) .
* HDInsight Hadoop kümesi kullanan ACID olmayan tablolarda Spark SQL iş yükü.

Spark 'un tek başına modunda çalıştığı senaryolarda, seçkin verilerin ve depolanan Spark jars (Livy işleri için), Azure Data Factory ' i kullanarak, birincil bölgeden ikincil bölgeye düzenli olarak çoğaltılmalıdır `DistCP` .

Spark not defterlerini ve kitaplıklarını, birincil veya ikincil kümelerde kolayca dağıtılabilecek şekilde depolamak için sürüm denetim sistemlerini kullanmanızı öneririz. Not defteri tabanlı ve Not defteri olmayan çözümlerin, birincil veya ikincil çalışma alanındaki doğru veri takmaları yüklemeye hazır olduğundan emin olun.

HDInsight 'ın yerel olarak sağladığı daha fazla müşterinin özel kitaplıkları varsa, bunların izlenmesi ve düzenli aralıklarla bekleme ikincil kümesine yüklenmesi gerekir.  

### <a name="apache-spark-replication-rpo--rto"></a>Apache Spark çoğaltma RPO & RTO

* RPO: veri kaybı, Birincilden ikinciye kadar son başarılı artımlı çoğaltma (Spark ve Hive) ile sınırlıdır.

* RTO: ikincil ile, hata ile yukarı akış ve aşağı akış işlemlerinin sürdürme arasındaki zaman.

### <a name="apache-spark-architectures"></a>Apache Spark mimarileri

#### <a name="spark-active-primary-with-on-demand-secondary"></a>İsteğe bağlı ikincil ile Spark etkin birincil

Uygulamalar birincil bölgedeki Spark ve Hive kümelerini okur ve yazar ve normal işlemler sırasında ikincil bölgede hiçbir küme sağlanmamıştır. SQL meta veri deposu, Hive depolaması ve Spark Storage, ikincil bölgede kalıcıdır. Spark ve Hive kümeleri betikleştirilmiş ve isteğe bağlı olarak dağıtılır. Hive çoğaltması, Hive depolama ve Hive meta veri `DistCP` deposu, tek başına Spark depolamayı kopyalamak için kullanılabilir Azure Data Factory. Bağımlılık işlem nedeniyle, Hive kümelerinin her Hive çoğaltma çalıştırılmadan önce dağıtılması gerekir `DistCp` .

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="isteğe bağlı ikincil Apache Spark mimarisi ile etkin birincil":::

#### <a name="spark-active-primary-with-standby-secondary"></a>Hazır olan ikincil ile Spark etkin birincil

Uygulamalar, birincil bölgedeki Spark ve Hive kümelerini okur ve yazar. Bu arada, bekleyen ölçekli Hive ve Spark kümeleri, normal işlemler sırasında ikincil bölgede çalışır. Normal işlemler sırasında bölgeye özgü Hive ve Spark okuma işlemlerini ikincil için boşaltmasını seçebilirsiniz.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="etkin birincil bekleme ikincil Apache Spark ":::

## <a name="apache-hbase"></a>Apache HBase

HBase dışa aktarma ve HBase çoğaltma, HDInsight HBase kümeleri arasında iş sürekliliğini etkinleştirmenin yaygın yollarıdır.

HBase dışa aktarma, birincil HBase kümesinden tabloları temel alınan Azure Data Lake Storage Gen 2 depolamasına aktarmak için HBase dışa aktarma yardımcı programını kullanan bir toplu çoğaltma işlemidir. Daha sonra, aktarılan verilere ikincil HBase kümesinden erişilebilir ve ikincil tabloda önceden bulunması gereken tablolara içeri aktarılabilecek. HBase dışa aktarma Işlemi tablo düzeyinde ayrıntı düzeyi sağlarken, artımlı güncelleştirme durumlarında dışarı aktarma Otomasyon altyapısı her bir çalıştırmaya dahil edilecek artımlı satır aralığını denetler. Daha fazla bilgi için bkz. [HDInsight HBase yedekleme ve çoğaltma](./hbase/apache-hbase-backup-replication.md#export-then-import).

HBase çoğaltma, tam olarak otomatik bir şekilde HBase kümeleri arasında neredeyse gerçek zamanlı çoğaltma kullanır. Çoğaltma, tablo düzeyinde yapılır. Tüm tablolar ya da belirli tablolar çoğaltma için hedeflenebilir. HBase çoğaltma sonunda tutarlıdır, yani birincil bölgedeki bir tabloda yapılan son düzenlemeler hemen tüm ikincil öğeler tarafından kullanılamayabilir. İkincillerle, sonunda birincil ile tutarlı hale gelmeleri garanti edilir. HBase çoğaltma, iki veya daha fazla HDInsight HBase kümesi arasında ayarlanabilir:

* Birincil ve ikincil aynı sanal ağ içinde.
* Birincil ve ikincil aynı bölgedeki farklı eşlenmiş VNET 'lerde bulunur.
* Birincil ve ikincil, farklı bölgelerdeki farklı eşlenmiş sanal ağlarda bulunur.

Daha fazla bilgi için bkz. [Azure sanal ağlarında Apache HBase küme çoğaltmasını ayarlama](./hbase/apache-hbase-replication.md).

HBase kümelerinin yedeğini almanın birkaç yolu vardır, örneğin, [HBase klasörünü kopyalama](./hbase/apache-hbase-backup-replication.md#copy-the-hbase-folder), tabloları ve [anlık görüntüleri](./hbase/apache-hbase-backup-replication.md#snapshots) [kopyalama](./hbase/apache-hbase-backup-replication.md#copy-tables) .

### <a name="hbase-rpo--rto"></a>HBase RPO & RTO

#### <a name="hbase-export"></a>HBase dışarı aktarma

* RPO: veri kaybı, birincili birincil tarafından yapılan son başarılı toplu iş artımlı içeri aktarma ile sınırlıdır.
* RTO: ikincil üzerindeki g/ç işlemlerinin birincil ve sürdürme hatası arasındaki süre.

#### <a name="hbase-replication"></a>HBase çoğaltma

* RPO: veri kaybı, ikincide alınan son WalEdit sevkiyatla sınırlıdır.
* RTO: ikincil üzerindeki g/ç işlemlerinin birincil ve sürdürme hatası arasındaki süre.

### <a name="hbase-architectures"></a>HBase mimarileri

HBase çoğaltma üç modda ayarlanabilir: öncü-Izleyici, Leader-Leader ve döngüsel.

#### <a name="hbase-replication--leader--follower-model"></a>HBase çoğaltma: öncü – Izleyici modeli

Bu çapraz bölge kümesinde, çoğaltma birincil bölgeden ikincil bölgeye tek yönlü yapılır. Tek yönlü çoğaltma için birincil üzerindeki tüm tablolar ya da belirli tablolar tanımlanabilir. Normal işlemler sırasında, ikincil küme kendi bölgesinde okuma isteklerini karşılamak için kullanılabilir.

İkincil küme, kendi tablolarını barındırabilirler ve bölgesel uygulamalardan okuma ve yazma işlemleri için bir normal HBase kümesi olarak çalışır. Ancak, çoğaltılan tablolara veya ikincil için yerel tablolara yazma işlemleri birincil diske geri çoğaltılmaz.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="HBase öncü izleyici modeli":::

#### <a name="hbase-replication--leader--leader-model"></a>HBase çoğaltma: öncü – öncü model

Bu çapraz bölge kümesi, çoğaltmanın birincil bölge ve ikincil bölge arasında bir arada olması dışında tek yönlü ayarlamaya çok benzer. Uygulamalar, okuma yazma modlarında her iki kümeyi de kullanabilir ve güncelleştirmeler aralarında zaman uyumsuz olarak güncelleştirilir.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="HBase öncü öncü modeli":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>HBase çoğaltma: çok bölgeli veya döngüsel

Çok bölgeli/döngüsel çoğaltma modeli bir HBase çoğaltma uzantısıdır ve bölgeye özgü HBase kümelerini okuyan ve yazan birden çok uygulamayla küresel olarak yedekli bir HBase mimarisi oluşturmak için kullanılabilir. Kümeler, iş gereksinimlerine bağlı olarak, öncü/öncü veya öncü/Izleyici 'nin çeşitli birleşimlerinde ayarlanabilir.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="HBase döngüsel modeli":::

## <a name="apache-kafka"></a>Apache Kafka

Bölgeler arası kullanılabilirliği etkinleştirmek için HDInsight 4,0, farklı bir bölgedeki birincil Kafka kümesinin ikincil çoğaltmasını sürdürmek için kullanılabilen Kafka MirrorMaker 'ı destekler. MirrorMaker, üst düzey bir tüketici-üretici çifti olarak davranır, birincil kümedeki belirli bir konudan tüketir ve ikincide aynı ada sahip bir konuya üretir. MirrorMaker kullanarak yüksek kullanılabilirliğe sahip olağanüstü durum kurtarma için çapraz küme çoğaltma, üreticileri ve tüketicilerin çoğaltma kümesine yük devri yapması gerektiği varsayımıyla birlikte gelir. Daha fazla bilgi için bkz. [HDInsight 'Ta Kafka ile Apache Kafka konularını çoğaltmak Için MirrorMaker kullanma](./kafka/apache-kafka-mirroring.md)

Çoğaltma başlatıldığında konu ömrü ne olduğuna bağlı olarak, MirrorMaker konu çoğaltma, kaynak ve çoğaltma konuları arasında farklı uzaklıklara yol açabilir. HDInsight Kafka kümeleri, tek küme düzeyinde yüksek kullanılabilirlik özelliği olan konu Bölüm çoğaltmasını da destekler.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Apache Kafka çoğaltma":::

### <a name="apache-kafka-architectures"></a>Apache Kafka mimarileri

#### <a name="kafka-replication-active--passive"></a>Kafka çoğaltma: etkin – Pasif

Active-Passive kurulum, etkin olmayan tek yönlü yansıtmayı pasif 'e sağlar. Üreticileri ve tüketiciler etkin ve pasif bir kümenin varolup olmadığını ve etkin bir şekilde başarısız olması durumunda pasif 'e yük devretmek için hazırlanmalıdır. Active-Passive kurulumunun bazı avantajları ve dezavantajları aşağıda verilmiştir.

Artılar:

* Kümeler arasındaki ağ gecikmesi etkin kümenin performansını etkilemez.
* Tek yönlü çoğaltmanın basitliği.

Eksileri:

* Pasif küme aşırı kullanılabilir kalabilir.
* Uygulama üreticileri ve tüketicilerine yük devretme tanımayı ekleme konusunda tasarım karmaşıklığı.
* Etkin küme hatası sırasında olası veri kaybı.
* Etkin ve pasif kümeler arasındaki konular arasında nihai tutarlılık.
* Birincil öğesine geri dönme, konu başlığı altında ileti tutarsızlığına neden olabilir.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Etkin Pasif model Apache Kafka":::

#### <a name="kafka-replication-active--active"></a>Kafka çoğaltma: etkin – etkin

Active-Active kurulum, en çok iki düğümlü, VNet eşlenmiş HDInsight Kafka kümelerini, MirrorMaker ile çift yönlü zaman uyumsuz çoğaltma ile içerir. Bu tasarımda, birincil olarak tüketicileri tarafından tüketilen iletiler de aynı zamanda ikincil ve tam tersi olan tüketiciler tarafından kullanılabilir hale getirilir. Active-Active kurulumunun bazı avantajları ve dezavantajları aşağıda verilmiştir.

Artılar:

* Yinelenen durumları nedeniyle yük devretme ve geri göndermeler daha kolay yürütülür.

Eksileri:

* Kurma, yönetim ve izleme etkin-Pasif sürümünden daha karmaşıktır.
* Döngüsel çoğaltmanın sorunu giderilmesi gerekir.  
* Çift yönlü çoğaltma, bölgesel veri çıkış maliyetlerinin daha yüksek olmasına yol açar.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Etkin etkin modeli Apache Kafka":::

## <a name="hdinsight-enterprise-security-package"></a>HDInsight Kurumsal Güvenlik Paketi

Bu ayar, kullanıcıların her iki kümede da kimlik doğrulaması yapabilmeleri için [Azure AD DS çoğaltma kümelerinde](../active-directory-domain-services/tutorial-create-replica-set.md) hem birincil hem de ikincil olarak çok kullanıcılı işlevselliği etkinleştirmek üzere kullanılır. Normal işlemler sırasında, kullanıcıların okuma işlemleriyle sınırlı olduğundan emin olmak için Ranger ilkelerinin Ikincil içinde ayarlanması gerekir. Aşağıdaki mimaride, bir ESP etkin Hive etkin birincil – bekleme Ikincil kümesinin nasıl görünebileceği açıklanmaktadır.

Ranger meta veri deposu çoğaltma:

Ranger meta verileri, veri yetkilendirmesini denetlemek için bir Ranger ilkelerini kalıcı olarak depolamak ve çalıştırmak için kullanılır. Birincil ve ikincil içinde bağımsız Ranger ilkelerini korumanızı ve ikincil kopyayı bir okuma çoğaltması olarak korumanızı öneririz.
  
Eğer gereksinim, birincil ve ikincil arasında Ranger ilkelerini eşit tutmaya devam ediyorsanız, zaman içinde [içeri ve dışarı aktarma](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export) kullanarak Ranger ilkelerini birincili ikinciye içeri aktarın.

Ranger ilkelerini birincil ve ikincil arasında çoğaltmak, ikincörün yazma özelliğinin etkin hale gelmesine neden olabilir ve bu da ikincil lider üzerinde veri tutarsızlıklarına yanlışlıkla yazma yapılmasına yol açabilir.  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="HDInsight Kurumsal Güvenlik Paketi mimarisi":::

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede ele alınan öğeler hakkında daha fazla bilgi edinmek için bkz.:

* [Azure HDInsight iş sürekliliği](./hdinsight-business-continuity.md)
* [Azure HDInsight yüksek oranda kullanılabilir çözüm mimarisi örnek olay incelemesi](./hdinsight-high-availability-case-study.md)
* [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](./hadoop/hdinsight-use-hive.md)