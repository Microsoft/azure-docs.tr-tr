---
title: Danışman ile Azure uygulamalarının performansını geliştirme
description: İş açısından kritik uygulamalarınızın hızını ve yanıt hızını artırmak için Azure Danışmanı 'nda performans önerilerini kullanın.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 69fd86226375073c71903af5704ef3a635eeaffb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579914"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Azure Advisor 'ı kullanarak Azure uygulamalarının performansını geliştirme

Azure Advisor 'daki performans önerileri, iş açısından kritik uygulamalarınızın hızını ve yanıt hızını artırmaya yardımcı olabilir. Danışman panosunun **performans** sekmesinde, Advisor 'ın performans önerilerini alabilirsiniz.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Traffic Manager profilinizde DNS yaşam süresini, sağlıklı uç noktalara daha hızlı yük devretmek için azaltın

Belirli bir uç noktanın sorgulara yanıt vermemesi durumunda bitiş noktalarına ne kadar hızlı geçiş yapılacağını belirlemek için, Azure Traffic Manager profilinizde [yaşam süresi (TTL) ayarlarını](../traffic-manager/traffic-manager-performance-considerations.md) kullanabilirsiniz. TTL değerlerini azaldıysanız istemciler, çalışan uç noktalara daha hızlı yönlendirilir.

Azure Advisor, daha uzun TTL yapılandırılmış Traffic Manager profillerini tanımlar. Bu, profilin [hızlı yük devretme](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)için yapılandırılıp yapılandırılmadığını bağlı olarak, TTL 'yi 20 saniye veya 60 saniye olarak yapılandırmanızı önerir.

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>SQL Veritabanı Danışmanı kullanarak veritabanı performansını iyileştirme (geçici olarak devre dışı)

Azure Danışmanı, tüm Azure kaynaklarınız için önerilerin tutarlı, birleştirilmiş bir görünümünü sağlar. Veritabanlarınızın performansını iyileştirmeye yönelik öneriler getirmek için SQL Veritabanı Danışmanı ile tümleşir. SQL Veritabanı Danışmanı kullanım geçmişinizi çözümleyerek veritabanlarınızın performansını değerlendirir. Daha sonra veritabanının tipik iş yükünü çalıştırmaya en uygun öneriler sunar.

> [!NOTE]
> Öneriler alabilmeniz için veritabanınızın bir hafta boyunca kullanımda olması gerekir ve bu hafta içinde bazı tutarlı etkinlik olması gerekir. SQL Veritabanı Danışmanı, rastgele ek etkinlik için daha kolay olan tutarlı sorgu desenleri için daha kolay iyileştirebilirler.

Daha fazla bilgi için bkz. [SQL veritabanı Danışmanı](../azure-sql/database/database-advisor-implement-performance-recommendations.md).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Daha iyi güvenilirlik ve performans için depolama istemci kitaplığınızı en son sürüme yükseltin

Depolama istemci kitaplığı SDK 'sının en son sürümü, müşteriler tarafından bildirilen sorunlara yönelik düzeltmeler içerir ve soru-cevap sürecimiz aracılığıyla önceden belirlenir. En son sürüm, Azure Storage 'ı kullanmaya ilişkin genel deneyiminizi iyileştirebilecek yeni özelliklerle birlikte güvenilirlik ve performans iyileştirmesi de taşır. Danışman, eski bir sürüm kullanıyorsanız SDK 'nın en son sürümüne yükseltmek için gereken öneriler ve adımlar sağlar. Öneriler, desteklenen diller için: C++ ve .NET.

## <a name="improve-app-service-performance-and-reliability"></a>Performansı ve güvenilirliği App Service geliştirme

Azure Advisor, App Service deneyiminizi iyileştirmek ve ilgili platform özelliklerini bulmak için önerileri tümleştirir. App Service önerilerle ilgili örnekler şunlardır:
* Bellek veya CPU kaynaklarının, risk azaltma seçenekleriyle uygulama çalışma zamanları tarafından tükenmiş olduğu örneklerin algılanması.
* Web uygulamaları ve veritabanları gibi kaynakların birlikte bulunması, performansı artırmak ve maliyeti azaltmak için örneklerin algılanması.

Daha fazla bilgi için bkz. [Azure App Service Için en iyi uygulamalar](../app-service/app-service-best-practices.md).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Disk g/ç azaltmasını engellemek için yönetilen diskleri kullanma

Danışman, ölçeklenebilirlik hedefine ulaşan bir depolama hesabına ait sanal makineleri tanımlar. Bu durum, bu VM 'Leri, g/ç daraltma saldırılarına açık hale getirir. Danışman, performans düşüşünü engellemek için yönetilen diskleri kullanmalarını önerir.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Premium Depolama kullanarak sanal makine disklerinin performansını ve güvenilirliğini geliştirme

Danışman, depolama hesabınızda yüksek işlem hacmi olan standart disklere sahip sanal makineleri tanımlar. Premium disklere yükseltmeyi önerir. 

Azure Premium Depolama, g/ç yoğunluklu iş yüklerini çalıştıran sanal makineler için yüksek performanslı ve düşük gecikmeli disk desteği sunar. Premium Depolama hesapları kullanan sanal makine diskleri, verileri katı hal sürücülerinde (SSD 'Ler) depolar. Uygulamanız için en iyi performans için, yüksek ıOPS gerektiren tüm sanal makine disklerini Premium depolamaya geçirmeniz önerilir.

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Sorgu performansını artırmak için Azure SYNAPSE Analytics tablolarınızdaki veri eğriliğini kaldırma

Veri eğriltme, iş yükünüzü çalıştırdığınızda gereksiz veri hareketine veya kaynak performans sorunlarına neden olabilir. Advisor, %15 ' ten büyük olan dağıtım verisi eğriliğini algılar. Verilerinizi yeniden dağıtmanız ve tablo dağıtım anahtarı seçimlerinizi geri yüklemenizi önerir. Eğriliği belirleme ve kaldırma hakkında daha fazla bilgi edinmek için bkz. [skew sorunlarını giderme](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Sorgu performansını artırmak için Azure SYNAPSE Analytics Tablolarınızda geçmiş tablo istatistiklerini oluşturun veya güncelleştirin

Danışman güncel [tablo istatistiklerine](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) sahip olmayan tabloları tanımlar ve istatistiklerin oluşturulmasını veya güncelleştirilmesini önerir. Azure SYNAPSE Analytics 'te sorgu iyileştiricisi, sorgu sonuçlarında kardinalite veya satır sayısını tahmin etmek için güncel istatistikleri kullanır. Bu tahminler, en hızlı performansı sağlamak üzere sorgu iyileştiricinin bir sorgu planı oluşturmasını sağlar.

## <a name="improve-mysql-connection-management"></a>MySQL bağlantı yönetimini geliştirme

Advisor analizine, bir MySQL sunucusuna bağlanan uygulamanızın bağlantıları verimli bir şekilde yönetmediğinden emin olabilirsiniz. Bu durum gereksiz kaynak tüketimine ve genel uygulama gecikmesine neden olabilir. Bağlantı yönetimini geliştirmek için, kısa süreli bağlantıların sayısını azaltmanızı ve boşta kalmış gereksiz bağlantıları kaldırmanızı öneririz. Bu geliştirmeleri, ProxySQL gibi bir sunucu tarafı bağlantı havuzlayıcı yapılandırarak yapabilirsiniz.


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Sorgu performansını artırmak için Azure SYNAPSE Analytics tablolarında önbellek kullanımını iyileştirmek üzere ölçeği büyütün

Azure Advisor, Azure SYNAPSE Analytics tablolarınızın yüksek önbellek kullanan yüzdesi ve düşük bir isabet yüzdesi olup olmadığını algılar. Bu durum, Azure SYNAPSE Analytics örneğinizin performansını etkileyebilecek yüksek önbellek çıkarmayı gösterir. Danışman, iş yükünüz için yeterli önbellek kapasitesi ayırdığınızdan emin olmak için Azure SYNAPSE Analytics örneğinizi ölçeklendirmenizi önerir.

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>Sorgu performansını artırmak için Azure SYNAPSE Analytics tablolarını çoğaltılan tablolara dönüştürme

Danışman, çoğaltılan tablo olmayan ancak dönüşümden faydalanabilir olan tabloları tanımlar. Bu tabloları dönüştürmenizi önerir. Önerilerin temeli:
- Çoğaltılan tablonun boyutu. 
- Sütun sayısı. 
- Tablo dağıtım türü. 
- Azure SYNAPSE Analytics tablosundaki bölümlerin sayısı. 

Bağlam için öneri bölümünde ek buluşsal yöntemler sağlanmış olabilir. Bu önerinin nasıl belirlendiği hakkında daha fazla bilgi edinmek için bkz. [Azure SYNAPSE Analytics önerileri](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>En son Azure özelliklerini almak için depolama hesabınızı Azure Resource Manager geçirin

' Den faydalanmak için depolama hesabı dağıtım modelinizi Azure Resource Manager geçirin:
- Şablon dağıtımları.
- Ek güvenlik seçenekleri.
- En son Azure depolama özelliklerini kullanabilmeniz için bir GPv2 hesabına yükseltme özelliği. 

Danışman, klasik dağıtım modelini kullanan tek başına depolama hesaplarını tanımlar ve Kaynak Yöneticisi dağıtım modeline geçiş yapmanızı önerir.

> [!NOTE]
> Azure Izleyici 'de klasik uyarılar Ağustos 2019 ' de kullanımdan kaldırıldı. Yeni platformla uyarı işlevlerini sürdürmek için Kaynak Yöneticisi kullanmak üzere klasik depolama hesabınızı yükseltmenizi öneririz. Daha fazla bilgi için bkz. [Klasik uyarılar kullanımdan](../azure-monitor/alerts/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)kaldırma.

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Maksimum abonelik sınırına ulaşmasını engellemek için depolama hesaplarınızı tasarlayın

Bir Azure bölgesi, abonelik başına en fazla 250 depolama hesabını destekler. Bu sınıra ulaşıldığında, bu bölgede/abonelik birleşiminde depolama hesapları oluşturamazsınız. Danışman aboneliklerinizi denetler ve en yüksek sınıra ulaşmaya yakın olan herhangi bir abonelik/bölge için daha az depolama hesabı tasarlamanızı sağlayacak öneriler sağlar.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>VPN Gateway SKU 'sunun boyutunu yüksek P2S kullanımı ele almak için artırmayı düşünün

Her Azure VPN Gateway SKU 'SU yalnızca belirtilen sayıda eş zamanlı P2S bağlantısı destekleyebilir. Bağlantı sayılıınız ağ geçidinize yakınsa, ek bağlantı girişimleri başarısız olabilir. Ağ geçidinizin boyutunu artırdıysanız, daha fazla eşzamanlı P2S kullanıcısı destekleyebilirsiniz. Danışman, ağ geçidinizin boyutunu artırmaya yönelik öneriler ve yönergeler sağlar.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>VPN Gateway SKU 'sunun boyutunu yüksek CPU 'ya yönelik olarak artırmayı düşünün

Yüksek trafik yükü altında, VPN ağ geçidiniz yüksek CPU nedeniyle paketleri bırakabilir. VPN Gateway SKU 'nuzu yükseltmeniz göz önünde bulundurun. VPN ağ geçidinizin boyutunu artırmak, bağlantıların yüksek CPU nedeniyle bırakılmadığından emin olur. Advisor bu sorunu önceden ele almak için öneriler sağlar. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Yük aktarım hızını, veri sıkıştırma oranını ve sorgu performansını en üst düzeye çıkarmak için yükleme sırasında toplu iş boyutunu artırın

Advisor, veritabanınıza yüklerken toplu iş boyutunu artırarak yükleme performansını ve aktarım hızını artırıp artıramayacağını algılar. COPY ifadesini kullanmayı düşünebilirsiniz. COPY ifadesini kullanmezseniz, SQLBulkCopy API veya BCP gibi yardımcı programları yüklerken toplu iş boyutunu artırmayı düşünün. İyi bir genel kural, 100.000 ve 1.000.000 satırlık bir toplu iş boyutu kullanmaktır. Toplu iş boyutunu artırmak, yük aktarım hızını, veri sıkıştırmayı ve sorgu performansını artıracaktır.

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>Yükleme sırasında gecikme süresini en aza indirmek için depolama hesabını aynı bölgede birlikte bulun

Advisor, adanmış SQL havuzunuzdaki farklı bir bölgeden yükleme yapılıp yapılmayacağını algılar. Verileri yüklerken gecikme süresini en aza indirmek için adanmış SQL havuzunuzun bulunduğu bir depolama hesabından yüklemeyi göz önünde bulundurun. Bu değişiklik gecikme süresini en aza indirmenize ve yükleme performansını artırmaya yardımcı olur.

## <a name="use-a-supported-kubernetes-version"></a>Desteklenen bir Kubernetes sürümü kullanma

Advisor, desteklenmeyen Kubernetes sürümlerini algılar.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>MySQL için Azure veritabanı, PostgreSQL için Azure veritabanı ve MariaDB sunucuları için Azure veritabanı performansını iyileştirin

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>MySQL için Azure veritabanı, PostgreSQL için Azure veritabanı ve CPU performansı sorunları olan MariaDB sunucuları için Azure veritabanı ile ilgili CPU baskısı 'nı onarın
CPU 'nun genişletilmiş bir süre boyunca yüksek kullanımı, iş yükünüz için yavaş sorgu performansına neden olabilir. CPU boyutunu artırmak, veritabanı sorgularının çalışma zamanının iyileştirilmesine ve genel performansı iyileştirmenize yardımcı olur. Danışman, büyük olasılıkla CPU kısıtlı iş yüklerini çalıştıran ve işlem izlemenizi ölçeklendirmenizi gerektiren yüksek CPU kullanımına sahip sunucuları tanımlar.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>MySQL için Azure veritabanı, PostgreSQL için Azure veritabanı ve MariaDB sunucuları için Azure veritabanı için bellek kısıtlamalarını azaltın veya bellek için Iyileştirilmiş bir SKU 'ya geçiş yapın
Düşük önbellek isabet oranı, daha yavaş sorgu performansına ve daha fazla ıOPS oluşmasına neden olabilir. Bu durumun nedeni hatalı bir sorgu planı veya bellek açısından yoğun bir iş yükü olabilir. Sorgu planını [düzeltme veya](../postgresql/concepts-pricing-tiers.md) PostgreSQL Için Azure veritabanı, MySQL Için Azure veritabanı veya MariaDB sunucusu Için Azure veritabanı, veritabanı iş yükünün yürütülmesini iyileştirmenize yardımcı olur. Azure Advisor, bu yüksek arabellek havuzu karmaşıklığının etkilediği sunucuları tanımlar. Aşağıdaki eylemlerden birini yapmanızı önerir: 
- Sorgu planını çözme
- Daha fazla belleğe sahip bir SKU 'ya taşıma 
- Daha fazla ıOPS almak için depolama boyutunu artırın.

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Okuma yoğunluğu yoğun iş yükleri için genişleme okumalarını genişletmek için MySQL için Azure veritabanı veya PostgreSQL için Azure veritabanı okuma çoğaltması kullanın
Danışman, son yedi gün içinde sunucuda yazma işlemleri için okuma yoğunluklu iş yüklerini belirlemek üzere okuma oranı gibi iş yükü tabanlı buluşsal yöntemler kullanır. PostgreSQL için Azure veritabanı veya yüksek bir okuma/yazma oranına sahip MySQL için Azure veritabanı kaynağı, CPU veya bellek çekişmesine yol açabilir ve sorgu performansının yavaşlamasına neden olabilir. [Çoğaltma](../postgresql/howto-read-replicas-portal.md) eklemek, Çoğaltma sunucusuna yapılan okumaların ölçeğini ölçeklendirmeye ve BIRINCIL sunucuda CPU veya bellek kısıtlamalarını engellemeye yardımcı olur. Danışman, okuma yoğunluklu iş yükleri olan sunucuları tanımlar ve bazı okuma iş yüklerini boşaltmak için bir [okuma çoğaltması](../postgresql/concepts-read-replicas.md) eklemenizi önerir.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Bağlantı kısıtlamalarını engellemek için MySQL için Azure veritabanınızı, PostgreSQL için Azure veritabanı 'nı veya MariaDB sunucusu için Azure veritabanı 'nı daha yüksek bir SKU 'ya ölçeklendirin
Veritabanı sunucunuza yönelik her yeni bağlantı belleği kaplar. Veritabanı sunucusunun performansı, bellekteki [üst sınır](../postgresql/concepts-limits.md) nedeniyle sunucu bağlantısı başarısız olursa düşer. Azure Advisor, çok sayıda bağlantı hatalarıyla çalışan sunucuları belirler. Aşağıdaki eylemlerden birini gerçekleştirerek sunucunuzun bağlantı sınırlarını sunucunuza daha fazla bellek sağlamak üzere yükseltmeyi önerir:
- İşlem ölçeğini ölçeklendirin. 
- Çekirdek başına daha fazla işlem içeren bellek için Iyileştirilmiş SKU 'Ları kullanın.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Önbelleği ve uygulama performansını artırmak için önbelleğinizi farklı bir boyut veya SKU 'ya ölçeklendirin

Önbellek örnekleri, yüksek bellek baskısı, yüksek sunucu yükü veya yüksek ağ bant genişliği altında çalıştıklarında en iyi şekilde gerçekleştirilir. Bu koşullar, yanıt vermemeye, veri kaybına veya kullanılamaz duruma gelmesine neden olabilir. Advisor, bu koşullarda önbellek örneklerini tanımlar. Aşağıdaki eylemlerden birini yapmanızı önerir:
- Bellek baskısı, sunucu yükü veya ağ bant genişliğini azaltmak için en iyi yöntemleri uygulayın.
- Daha fazla kapasiteye sahip farklı bir boyuta veya SKU 'ya ölçeklendirin.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Azure Cosmos DB hesabınıza trafiğe sahip bölgeler ekleyin

Danışman, şu anda yapılandırılmamış bir bölgeden gelen trafiğe sahip Azure Cosmos DB hesaplarını algılar. Bu bölge eklenmesini önerir. Bunun yapılması, bu bölgeden gelen istekler için gecikme süresini geliştirir ve bölge kesintileri durumunda kullanılabilirliği sağlar. [Azure Cosmos DB ile genel veri dağıtımı hakkında daha fazla bilgi edinin.](../cosmos-db/distribute-data-globally.md)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>Azure Cosmos DB Dizin oluşturma ilkenizi özel dahil edilen veya dışlanan yollar kullanarak yapılandırma

Advisor varsayılan dizin oluşturma ilkesini kullanan, ancak özel bir dizin oluşturma ilkesinden faydalanabilecek Azure Cosmos DB kapsayıcıları tanımlar. Bu belirleme iş yükü düzenine göre belirlenir. Varsayılan dizin oluşturma ilkesi tüm özellikleri dizine ekler. Sorgu filtrelerinde açık dahil edilen veya dışlanan yolların kullanıldığı özel bir dizin oluşturma ilkesi, dizin oluşturma için tüketilen ru ve depolamayı azaltabilir. [Dizin ilkelerini değiştirme hakkında daha fazla bilgi edinin.](../cosmos-db/index-policy.md)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Azure Cosmos DB sorgu sayfası boyutunu (Maxıtemcount)-1 olarak ayarlayın 

Azure Advisor, 100 sorgu sayfası boyutunu kullanan Azure Cosmos DB kapsayıcıları tanımlar. Daha hızlı taramalar için bir sayfa boyutu-1 kullanılmasını önerir. [Maxıtemcount hakkında daha fazla bilgi edinin.](../cosmos-db/sql-api-query-metrics.md)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>Küme performansını geliştirmek için HBase kümenizdeki hızlandırılmış yazma özelliğini kullanmayı düşünün
Azure Advisor, son 7 gün içinde sistem günlüklerini analiz eder ve kümenizin aşağıdaki senaryolarla karşılaşıp karşılaşdığını tanımlar:
1. Yüksek WAL eşitleme süresi gecikmesi 
2. Yüksek yazma isteği sayısı (1000 avg_write_requests/second/node için en az 3 adet bir saatlik zaman penceresi)

Bu koşullar, kümenizin yüksek yazma gecikme sürelerine sahip olduğunu gösterir. Bunun nedeni, kümenizde gerçekleştirilen ağır iş yükünün olması olabilir. Kümenizin performansını artırmak için, Azure HDInsight HBase tarafından sunulan hızlandırılmış yazma özelliğini kullanmayı düşünmek isteyebilirsiniz. HDInsight Apache HBase kümeleri için Hızlandırılmış Yazma İşlemleri özelliği, bulut depolaması kullanmak yerine her bir RegionServer örneğine (çalışan düğümü) premium SSD yönetilen diskler ekler. Sonuç olarak, uygulamalarınız için düşük yazma gecikme süresine ve daha iyi dayanıklılığa sahip olursunuz. Bu özellik hakkında daha fazla [bilgi edinmek için daha fazla bilgi edinin](../hdinsight/hbase/apache-hbase-accelerated-writes.md#how-to-enable-accelerated-writes-for-hbase-in-hdinsight)

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>Daha iyi performans için Azure Veri Gezgini tablo önbelleği-süre (ilke) gözden geçirin (Önizleme)
Bu öneri, yapılandırılmış olan önbelleğe alma süresinden (ilke) daha ötesine bakan çok sayıda sorguya sahip olan Azure Veri Gezgini tablolarını gösterir (Önbellek dışındaki verilere erişen sorgu yüzdesine göre sıralanmış ilk 10 tablo görüntülenir). Kümenin performansını geliştirmek için önerilen eylem: Bu tablodaki sorguları gereken en düşük zaman aralığıyla (tanımlanan ilke içinde) sınırlayın. Alternatif olarak tüm zaman aralığındaki veriler gerekliyse önbellek süresini önerilen değere yükseltin.

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>MySQL geçici tablo boyutlandırmasını iyileştirerek performansı artırma
Advisor Analysis, MySQL sunucunuzun düşük geçici tablo parametresi ayarları nedeniyle gereksiz g/ç yükünü ortadan kaldırır. Bu, gereksiz disk tabanlı işlemlere ve düşük performansa neden olabilir. Disk tabanlı işlemlerin sayısını azaltmak için 'tmp_table_size' ve 'max_heap_table_size' parametre değerlerini artırmanızı öneririz. [Daha fazla bilgi edinin](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>Düğümler arasında iş yükünü dağıtmak için sunucu grubundaki verileri dağıtma
Danışman, verilerin dağıtılmayan ancak düzenleyici üzerinde kalan sunucu gruplarını tanımlar. Bu, danışman, tam Hyperscale (Citus) avantajlarının sunucu gruplarınız için çalışan düğümlerine veri dağıtmasını önerir. Bu, sunucu grubundaki her bir düğümün kaynağını kullanarak sorgu performansını geliştirir. [Daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>VM 'Leri Windows Sanal Masaüstü Dağıtım konumuna daha yakın bir şekilde dağıtarak Kullanıcı deneyimini ve bağlantısını geliştirme
Sanal makinelerinizin, kullanıcılarınızın Windows Sanal Masaüstü (WVD) kullanarak bağlandığı bölgeden farklı veya fazla uzak bir bölgede bulunduğunu belirledik. Bu durum bağlantı sürelerinin uzamasına yol açabilir ve genel olarak WVD'de kullanıcının deneyimini etkiler. Konak havuzlarınız için sanal makine oluştururken kullanıcıya daha yakın bir bölge kullanmaya çalışmalısınız. Yakında bulunmak WVD hizmetinden memnuniyetin devam etmesini ve genel olarak daha iyi bir deneyim kalitesi elde edilmesini sağlar. [Bağlantı gecikme süresi hakkında daha fazla bilgi edinin](../virtual-desktop/connection-latency.md).

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>Tam Ekran Okuyucu SDK'sının en son sürümüne yükseltme yapın
Bu abonelik kapsamındaki kaynakların güncel olmayan Tam Ekran Okuyucu SDK’sı sürümlerini kullandığını belirledik. Tam Ekran Okuyucu SDK'sının en son sürümünü kullanmak, tümleştirme deneyiminizi özelleştirme ve geliştirmeye yönelik güncelleştirilmiş güvenlik, performans ve genişletilmiş özellik kümesi sağlar.
[Modern Okuyucu SDK 'sı](../cognitive-services/immersive-reader/index.yml)hakkında daha fazla bilgi edinin.

## <a name="improve-vm-performance-by-changing-the-maximum-session-limit"></a>Maksimum oturum sınırını değiştirerek VM performansını iyileştirme

Danışman, yük dengeleme algoritması olarak ilk ayarlanan derinliği olan bir konak havuzunuzun olduğunu ve ana bilgisayar havuzunun en fazla oturum sınırı 999999 ' den büyük veya buna eşit olduğunu algılar. Derinlik öncelikli yük dengeleme, tek bir oturum konağında eşzamanlı oturum açabilecek kullanıcı sayısı üst sınırını saptamak için en yüksek oturum sınırını kullanır. Maksimum oturum sınırı çok yüksekse, tüm Kullanıcı oturumları aynı oturum ana bilgisayarına yönlendirilir ve bu, performans ve güvenilirlik sorunlarına neden olur. Bu nedenle, bir konak havuzunu derinlemesine ilk yük dengelemeye sahip olacak şekilde ayarlarken, sanal makinelerinizin ve kapasitenizin yapılandırmasına göre uygun bir en fazla oturum sınırı ayarlamanız gerekir. 

Windows sanal masaüstü 'nde yük dengeleme hakkında daha fazla bilgi için bkz. [Windows sanal masaüstü yük dengeleme yöntemini yapılandırma](../virtual-desktop/troubleshoot-set-up-overview.md).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Danışman 'de performans önerilerine erişme

1. [Azure Portal](https://portal.azure.com)oturum açın ve [danışman](https://aka.ms/azureadvisordashboard)' yi açın.

2.  Danışman panosunda **performans** sekmesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Danışman önerileri hakkında daha fazla bilgi edinmek için bkz.:

* [Advisor 'a giriş](advisor-overview.md)
* [Danışman'ı kullanmaya başlama](advisor-get-started.md)
* [Danışman puanı](azure-advisor-score.md)
* [Danışman maliyet önerileri](advisor-cost-recommendations.md)
* [Advisor güvenilirlik önerileri](advisor-high-availability-recommendations.md)
* [Danışman güvenlik önerileri](advisor-security-recommendations.md)
* [Danışman işletimsel üstün öneriler](advisor-operational-excellence-recommendations.md)
* [Danışman REST API](/rest/api/advisor/)