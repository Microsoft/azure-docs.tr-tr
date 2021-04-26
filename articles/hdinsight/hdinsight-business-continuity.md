---
title: Azure HDInsight iş sürekliliği
description: Bu makalede, Azure HDInsight iş sürekliliği planlaması için en iyi yöntemlere, tek bölge kullanılabilirliğine ve en iyi duruma getirme seçeneklerine genel bakış sunulmaktadır.
keywords: Hadoop yüksek kullanılabilirlik
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 74f8bdd26e000b89bfae84102077c241f85abf7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933311"
---
# <a name="azure-hdinsight-business-continuity"></a>Azure HDInsight iş sürekliliği

Azure HDInsight kümeleri depolama, veritabanları, Active Directory, Active Directory Domain Services, ağ ve Key Vault gibi birçok Azure hizmetine bağımlıdır. İyi tasarlanmış, yüksek oranda kullanılabilir ve hataya dayanıklı bir analiz uygulaması, bu hizmetlerden bir veya daha fazlasına yönelik bölgesel veya yerel kesintiler için yeterli artıklık ile tasarlanmalıdır. Bu makale, iş sürekliliği planlaması için en iyi yöntemlere, tek bölge kullanılabilirliğine ve en iyi duruma getirme seçeneklerine genel bir bakış sunar.

## <a name="general-best-practices"></a>Genel en iyi uygulamalar

Bu bölümde, iş sürekliliği planlaması sırasında göz önünde bulundurmanız gereken birkaç en iyi yöntem ele alınmaktadır.

* Olağanüstü durum ve neden olması durumunda ihtiyacınız olacak en düşük iş işlevlerini saptayın. Örneğin, veri dönüştürme katmanı (sarı renkle gösterilir) için yük devretme özelliklerine *ve* (mavi renkle gösterilir) veya yalnızca veri hizmeti katmanı için yük devretmeye ihtiyacınız varsa değerlendirin.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="veri dönüştürme ve veri sunma katmanları":::

* Kümelerinizi iş yüküne, geliştirme yaşam döngüsüne ve departmanlara göre segmentlere ayırın. Daha fazla küme olması, birden çok farklı iş işlemini etkileyen tek bir büyük başarısızlığın olasılığını azaltır.

* İkincil bölgelerinizi salt okunurdur. Hem okuma hem de yazma özelliğine sahip yük devretme bölgeleri karmaşık mimarilere yol açabilir.

* Geçici kümeler bir olağanüstü durum olduğunda daha kolay yönetilebilir. İş yüklerinizi kümelerin açılıp kümelenebilir bir şekilde tasarlayın ve kümelerde hiçbir durum korunmaz.

* Genellikle bir olağanüstü durum varsa ve yeni bölgede yeniden başlatılması gerekiyorsa, iş yükleri genellikle tamamlanmamış olarak kalır. İş yüklerinizi ıdempotent olarak tasarlayın.

* Küme dağıtımları sırasında Otomasyonu kullanın ve bir olağanüstü durum varsa hızlı ve tamamen otomatikleştirilmiş dağıtım sağlamak için küme yapılandırma ayarlarının mümkün olduğunca uzak şekilde komut dosyası olduğundan emin olun.

* Kümedeki anormal davranışları algılamak ve ilgili uyarı bildirimlerini ayarlamak için HDInsight 'ta Azure izleme araçları 'nı kullanın. Belirli küme türünün önemli performans ölçümlerini toplayacak şekilde, önceden yapılandırılmış HDInsight kümesine özgü yönetim çözümlerini dağıtabilirsiniz. Daha fazla bilgi için bkz. [HDInsight Için Azure izleme](./hdinsight-hadoop-oms-log-analytics-tutorial.md).  

* Abonelik, hizmet veya bölge için hizmet sorunları, planlı bakım, sağlık ve Güvenlik Danışma belgeleri hakkında bildirim almak için Azure sistem durumu uyarılarına abone olun. Sorun nedeni ve Resolute ETA sorunları dahil sistem durumu bildirimleri yük devretme ve geri alma işlemlerini daha iyi yürütebilmeniz için size yardımcı olur. Daha fazla bilgi için bkz. [Azure hizmet durumu belgeleri](../service-health/index.yml).

## <a name="single-region-availability"></a>Tek bölge kullanılabilirliği

Temel bir HDInsight sisteminde aşağıdaki bileşenler bulunur. Tüm bileşenlerin kendi tek bölge hata toleransı mekanizmaları vardır.

* İşlem (sanal makineler): Azure HDInsight kümesi
* Meta veri deposu: Azure SQL veritabanı
* Depolama: Azure Data Lake Gen2 veya blob depolama
* Kimlik doğrulaması: Azure Active Directory, Azure Active Directory Domain Services, Kurumsal Güvenlik Paketi
* Etki alanı adı çözümlemesi: Azure DNS

Azure Key Vault ve Azure Data Factory gibi kullanılabilecek başka isteğe bağlı hizmetler vardır.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="HDInsight bileşenleri":::

### <a name="azure-hdinsight-cluster-compute"></a>Azure HDInsight kümesi (işlem)

HDInsight,% 99,9 kullanılabilirlik SLA 'sı sunmaktadır. Tek bir dağıtımda yüksek kullanılabilirlik sağlamak için, HDInsight, varsayılan olarak yüksek kullanılabilirlik modundaki birçok hizmet ile birlikte sunulur. HDInsight 'ta hata toleransı mekanizmaları hem Microsoft hem de Apache OSS ekosistem yüksek kullanılabilirlik Hizmetleri tarafından sağlanır.

Aşağıdaki hizmetler yüksek oranda kullanılabilir olacak şekilde tasarlanmıştır:

#### <a name="infrastructure"></a>Altyapı

* Etkin ve hazır bekleyen yayın düğümleri
* Birden çok ağ geçidi düğümü
* Üç Zookeeper çekirdek düğümü
* Hata ve güncelleştirme etki alanlarına göre dağıtılan çalışan düğümleri

#### <a name="service"></a>Hizmet

* Apache ambarı sunucusu
* YARN için uygulama zaman çizelgesi Merkezi
* Hadoop MapReduce için iş geçmişi sunucusu
* Apache Livy
* HDFS
* YARN Kaynak Yöneticisi
* HBase Master

Daha fazla bilgi edinmek için [Azure HDInsight tarafından desteklenen yüksek kullanılabilirliğe sahip hizmetlere](./hdinsight-high-availability-components.md) yönelik belgelere bakın.

İş işlevselliğini etkilemek için her zaman çok zararlı bir olay almaz. Tek bir bölgedeki aşağıdaki hizmetlerden bir veya daha fazla hizmet olayı beklenen iş işlevselliği kaybına neden olabilir.

### <a name="hdinsight-metastore"></a>HDInsight meta veri deposu

HDInsight, [Azure SQL veritabanını](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) bir% 99,99 SLA 'sı sağlayan bir meta veri deposu olarak kullanır. Verilerin üç çoğaltması, zaman uyumlu çoğaltma ile bir veri merkezi içinde kalır. Bir çoğaltma kaybı varsa, alternatif bir çoğaltma sorunsuz bir şekilde sunulur. [Etkin coğrafi çoğaltma](../azure-sql/database/active-geo-replication-overview.md) , en fazla dört veri merkeziyle oluşan kutudan daha fazlasını destekler. El ile veya veri merkezi olan bir yük devretme olduğunda, hiyerarşideki ilk çoğaltma otomatik olarak okuma-yazma özelliğine sahip olur. Daha fazla bilgi için bkz. [Azure SQL veritabanı iş sürekliliği](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="hdinsight-storage"></a>HDInsight depolaması

HDInsight, temel depolama katmanı olarak Azure Data Lake Storage 2. önerir. Azure Data Lake Storage 2. dahil olmak üzere [Azure depolama](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)% 99,9 SLA 'sı sağlar. HDInsight, veri merkezinde üç veri çoğaltmasının kalıcı olduğu ve çoğaltmanın zaman uyumlu olduğu LRS hizmetini kullanır. Bir çoğaltma kaybı olduğunda, bir çoğaltma sorunsuz bir şekilde sunulur.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) % 99,9 SLA 'sı sağlar. Active Directory, çok sayıda iç artıklık ve otomatik kurtarılabilirlik düzeyi olan küresel bir hizmettir. Daha fazla bilgi için, [Microsoft 'un Azure Active Directory güvenilirliğini sürekli olarak nasıl geliştirdiğine](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/)bakın.

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) % 99,9 SLA 'sı sağlar. Azure AD DS, küresel olarak dağıtılmış veri merkezlerinde barındırılan, yüksek oranda kullanılabilir bir hizmettir. Çoğaltma kümeleri, bir Azure bölgesi çevrimdışı kalırsa coğrafi olağanüstü durum kurtarmayı sağlayan bir Azure AD DS önizleme özelliğidir. Daha fazla bilgi için bkz. [çoğaltma kümesi kavramları ve özellikleri Azure Active Directory Domain Services](../active-directory-domain-services/concepts-replica-sets.md) daha fazla bilgi için.  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) %100 SLA 'sı sağlar. HDInsight, etki alanı adı çözümlemesi için çeşitli konumlarda Azure DNS kullanır.

## <a name="multi-region-cost-and-complexity-optimizations"></a>Çok bölgeli maliyet ve karmaşıklık iyileştirmeleri

Bölgeler arası yüksek kullanılabilirlik olağanüstü durum kurtarma kullanarak iş sürekliliği geliştirme, yüksek karmaşıklık ve daha yüksek maliyetli mimari tasarımlar gerektirir. Aşağıdaki tablolarda, toplam sahip olma maliyetini artırabileceğiniz bazı teknik bölgeler ayrıntılı olarak verilmiştir.

### <a name="cost-optimizations"></a>Maliyet iyileştirmeleri

|Alan|Maliyet yükseltme nedeni|İyileştirme stratejileri|
|----|------------------------|-----------------------|
|Veri Depolama|İkincil bölgedeki birincil verileri/tabloları çoğaltma|Yalnızca seçkin verileri Çoğalt|
|Veri çıkış|Giden çapraz bölge veri aktarımları bir fiyata gelir. Bant genişliği fiyatlandırma yönergelerini gözden geçirin|Bölge çıkış parmak izini azaltmak için yalnızca seçkin verileri Çoğalt|
|Küme Işlem|İkincil bölgede ek HDInsight kümesi/s|Birincil hatadan sonra ikincil işlem dağıtmak için otomatikleştirilmiş betikleri kullanın. İkincil küme boyutunu en düşük düzeyde tutmak için otomatik ölçeklendirmeyi kullanın. Ucuz VM SKU 'larını kullanın. VM SKU 'Larının indirimli olabileceği bölgelerde ikincil öğeler oluşturun.|
|Kimlik Doğrulaması |İkincil bölgedeki çok kullanıcılı senaryolar, ek Azure AD DS kurulumları oluşturacak|İkincil bölgedeki çok kullanıcılı kurulumlardan kaçının.|

### <a name="complexity-optimizations"></a>Karmaşıklık iyileştirmeleri

|Alan|Karmaşıklık yükseltme nedeni|İyileştirme stratejileri|
|----|------------------------|-----------------------|
|Okuma yazma desenleri |Hem birincil hem de ikincil için okuma ve yazma özelliği gerektirme |İkincili salt okunacak şekilde tasarlayın|
|Sıfır RPO & RTO |Sıfır veri kaybı (RPO = 0) ve sıfır kapalı kalma süresi (RTO = 0) gerektirme |Yük devri gereken bileşenlerin sayısını azaltmak için RPO ve RTO 'u tasarlayın.|
|İş işlevleri |İkincil olarak tam iş işlevselliği gerektirme |İkincil olarak iş işlevselliğinin en düşük kritik alt kümesiyle çalıştırıp çalıştıracağınızı değerlendirin.|
|Bağlantı |Birincil olan tüm yukarı akış ve aşağı akış sistemlerinin ikincil sunucuya da bağlanmasını isteme|İkincil bağlantıyı en düşük bir kritik alt kümeyle sınırlayın.|

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede ele alınan öğeler hakkında daha fazla bilgi edinmek için bkz.:

* [Azure HDInsight iş sürekliliği mimarileri](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight yüksek oranda kullanılabilir çözüm mimarisi örnek olay incelemesi](./hdinsight-high-availability-case-study.md)
* [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](./hadoop/hdinsight-use-hive.md)