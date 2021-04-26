---
title: Satın alma modelleri
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği için kullanılabilen satın alma modelleri hakkında bilgi edinin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/28/2020
ms.openlocfilehash: 8883263d6ddb2fb8ddc809f464288fcd282531bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788834"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>VCore ve DTU satın alma modelleri arasında seçim yapın-Azure SQL veritabanı ve SQL yönetilen örneği
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve Azure SQL yönetilen örneği, tam olarak yönetilen bir hizmet olarak Yönetim (PaaS) veritabanı altyapısını, performans ve maliyet gereksinimlerinize uygun olarak kolayca satın almanızı sağlar. Azure SQL veritabanı için seçtiğiniz dağıtım modeline bağlı olarak, sizin için uygun olan satın alma modelini seçebilirsiniz:

- [Sanal çekirdek (vCore) tabanlı satın alma modeli](service-tiers-vcore.md) (önerilir). Bu satın alma modeli, sağlanan bir işlem katmanı ve sunucusuz bir işlem katmanı arasında seçim sağlar. Sağlanan işlem katmanıyla birlikte, iş yükünüz için her zaman sağlanan işlem kaynaklarının tam miktarını seçin. Sunucusuz bilgi işlem katmanı ile, işlem kaynaklarını yapılandırılabilir bir işlem aralığı üzerinden otomatik ölçeklendirmeyi belirtirsiniz. Bu işlem katmanıyla birlikte, iş yükü etkinliğine göre veritabanını otomatik olarak duraklatabilir ve devam ettirebilirsiniz. Birim başına sanal çekirdek birim fiyatı, sağlanan işlem katmanında sunucusuz işlem katmanında olduğundan daha düşüktür.
- [Veritabanı işlem birimi (DTU) tabanlı satın alma modeli](service-tiers-dtu.md). Bu satın alma modeli, ortak iş yükleri için dengeli işlem ve depolama paketleri sağlar.

İki satın alma modeli vardır:

- [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md) hem [Azure SQL veritabanı](sql-database-paas-overview.md) hem de [Azure SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md)için kullanılabilir. [Hiper ölçek hizmeti katmanı](service-tier-hyperscale.md) , [sanal çekirdek tabanlı satın alma modelini](service-tiers-vcore.md)kullanan tek veritabanları için kullanılabilir.
- [DTU tabanlı satın alma modeli](service-tiers-dtu.md) [Azure SQL veritabanı](single-database-manage.md)için kullanılabilir.

Aşağıdaki tablo ve grafik, sanal çekirdek tabanlı ve DTU tabanlı satın alma modellerini karşılaştırın ve kontrast vardır:

|**Satın alma modeli**|**Açıklama**|**En iyi kullanım alanı:**|
|---|---|---|
|DTU tabanlı|Bu model, işlem, depolama ve g/ç kaynaklarının paketlenmiş bir ölçüsünü temel alır. İşlem boyutları tek veritabanları için DTU cinsinden ve elastik havuzlar için elastik veritabanı işlem birimi (eDTU) cinsinden gösterilir. DTU’lar ve eDTU’lar hakkında daha fazla bilgi için bkz. [DTU’lar ve eDTU’lar nedir?](purchasing-models.md#dtu-based-purchasing-model).|Basit, önceden yapılandırılmış kaynak seçenekleri isteyen müşteriler|
|Sanal çekirdek tabanlı|Bu model, işlem ve depolama kaynaklarını bağımsız olarak seçmenizi sağlar. Sanal çekirdek tabanlı satın alma modeli maliyetleri azaltmak için SQL Server’ın [Azure Hibrit Avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)’nı kullanmanıza da olanak tanır.|Esneklik, denetim ve saydamlık değerlerini veren müşteriler|
||||  

![Fiyatlandırma modeli karşılaştırması](./media/purchasing-models/pricing-model.png)

Bulut harcamalarınızı iyileştirmek ve kaydetmek istiyor musunuz?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>İşlem maliyetleri

### <a name="provisioned-compute-costs"></a>Sağlanan işlem maliyetleri

Sağlanan işlem katmanında, işlem maliyeti, uygulama için sağlanan toplam işlem kapasitesini yansıtır.

İş Açısından Kritik hizmet katmanında, otomatik olarak en az üç çoğaltma ayırdık. Bu ek işlem kaynakları ayırmasını yansıtmak için, sanal çekirdek tabanlı satın alma modelindeki fiyat İş Açısından Kritik hizmet katmanında yaklaşık 2,7 kat daha yüksektir Genel Amaçlı hizmet katmanında olduğundan. Benzer şekilde, İş Açısından Kritik hizmet katmanındaki GB başına daha yüksek depolama fiyatı, SSD depolamanın daha yüksek GÇ sınırlarını ve düşük gecikme süresini yansıtır.

Her iki katman de yedeklemeler için standart depolama kullandığından, yedekleme depolama alanı İş Açısından Kritik hizmet katmanı ve Genel Amaçlı hizmet katmanı için aynıdır.

### <a name="serverless-compute-costs"></a>Sunucusuz işlem maliyetleri

İşlem kapasitesinin nasıl tanımlandığı ve maliyetlerin sunucusuz işlem katmanı için nasıl hesaplandığı hakkında bir açıklama için bkz. [SQL veritabanı sunucusuz katmanı](serverless-tier-overview.md).

## <a name="storage-costs"></a>Depolama maliyetleri

Farklı depolama türleri farklı şekilde faturalandırılır. Veri depolama için, seçtiğiniz en fazla veritabanı veya havuz boyutuna bağlı olarak sağlanan depolama alanı için ücret ödersiniz. Bu maksimum değeri azaltmadığınız veya artıramadığınız takdirde maliyet değişmez. Yedekleme depolaması, örneğinizin otomatikleştirilmiş yedeklemeleriyle ilişkilidir ve dinamik olarak ayrılır. Yedeklemenizi artırma-saklama süresi, örneğiniz tarafından tüketilen yedekleme depolama alanını arttırır.

Varsayılan olarak, veritabanlarınızın otomatik yedeklemelerinin yedi günü, Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) standart BLOB depolama hesabına kopyalanır. Bu depolama, haftalık tam yedeklemeler, günlük değişiklik yedeklemeleri ve beş dakikada bir kopyalanmış işlem günlüğü yedeklemeleri tarafından kullanılır. İşlem günlüklerinin boyutu, veritabanının değişim hızına bağlıdır. Veritabanı boyutunun yüzde 100 ' una eşit olan minimum depolama miktarı, ek ücret ödemeden sağlanır. Yedek depolamanın ek tüketimi ayda GB olarak ücretlendirilir.

Depolama fiyatları hakkında daha fazla bilgi için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/sql-database/single/) sayfası.

## <a name="vcore-based-purchasing-model"></a>Sanal çekirdek tabanlı satın alma modeli

Sanal çekirdek (vCore), mantıksal bir CPU 'YU temsil eder ve donanım kuşaklarını ve donanımın fiziksel özelliklerini (örneğin, çekirdek sayısı, bellek ve depolama boyutu) arasından seçim yapmanıza olanak sağlar. Sanal çekirdek tabanlı satın alma modeli esneklik, denetim, tek tek kaynak tüketiminin saydamlığını ve şirket içi iş yükü gereksinimlerini buluta çevirecek basit bir yol sunar. Bu model, iş yükü gereksinimlerinize göre işlem, bellek ve depolama kaynakları seçmenize olanak sağlar.

Sanal çekirdek tabanlı satın alma modelinde, SQL veritabanı ve SQL yönetilen örneği için [genel amaçlı](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) ve [iş açısından kritik](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) hizmet katmanları arasında seçim yapabilirsiniz.  Tek veritabanları için [hiper ölçek hizmet katmanını](service-tier-hyperscale.md)da seçebilirsiniz.

Sanal çekirdek tabanlı satın alma modeli, işlem ve depolama kaynaklarını bağımsız olarak seçmenizi, şirket içi performansı eşleştirmeye ve fiyatı iyileştirmenize olanak tanır. Sanal çekirdek tabanlı satın alma modelinde şunları ödeyin:

- İşlem kaynakları (hizmet katmanı + sanal çekirdek sayısı ve bellek miktarı + donanım oluşturma).
- Veri türü ve miktarı ile günlük depolaması.
- Yedekleme depolaması (RA-GRS).

> [!IMPORTANT]
> İşlem kaynakları, g/ç ve veri ve günlük depolama, veritabanı veya elastik havuz başına ücretlendirilir. Yedekleme depolaması her bir veritabanı için ücretlendirilir. SQL yönetilen örnek ücretleri hakkında daha fazla bilgi için bkz. [SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md).
> **Bölge sınırlamaları:** Desteklenen bölgelerin geçerli listesi için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Şu anda desteklenmeyen bir bölgede yönetilen bir örnek oluşturmak için [Azure Portal aracılığıyla bir destek isteği gönderin](quota-increase-request.md).

Veritabanınız 300 ' den fazla DTU kullanıyorsa, sanal çekirdek tabanlı satın alma modeline dönüştürmek maliyetlerinizi azaltabilir. İstediğiniz API 'nizi veya Azure portal kullanarak, kapalı kalma süresi olmadan dönüştürme yapabilirsiniz. Ancak, dönüştürme gerekli değildir ve otomatik olarak yapılmaz. DTU tabanlı satın alma modeli, performans ve iş gereksinimlerinizi karşılıyorsa, uygulamayı kullanmaya devam etmelisiniz.

DTU tabanlı satın alma modelinden sanal çekirdek tabanlı satın alma modeline dönüştürmek için, bkz. [DTU 'Dan vCore 'A geçiş](migrate-dtu-to-vcore.md).

## <a name="dtu-based-purchasing-model"></a>DTU tabanlı satın alma modeli

Bir veritabanı işlem birimi (DTU), CPU, bellek, okuma ve yazma işlemlerinin karışan bir ölçüsünü temsil eder. DTU tabanlı satın alma modeli, farklı uygulama performansı düzeylerini sağlamak için, önceden yapılandırılmış işlem kaynakları ve dahil edilen depolama paketleri kümesi sunar. Her ay önceden yapılandırılmış bir paket ve sabit ödemeler basitliğini tercih ediyorsanız, DTU tabanlı model gereksinimleriniz için daha uygun olabilir.

DTU tabanlı satın alma modelinde, Azure SQL veritabanı için temel, standart ve Premium hizmet katmanları arasında seçim yapabilirsiniz. DTU tabanlı satın alma modeli, Azure SQL yönetilen örneği için kullanılamaz.

### <a name="database-transaction-units-dtus"></a>Veritabanı işlem birimleri (DTU 'Lar)

Bir [hizmet katmanındaki](single-database-scale.md)belirli bir işlem boyutundaki tek bir veritabanı için, Azure bu veritabanı için belirli bir kaynak düzeyini garanti eder (Azure bulutundaki diğer herhangi bir veritabanından bağımsız olarak). Bu garanti, öngörülebilir bir performans düzeyi sağlar. Bir veritabanı için ayrılan kaynak miktarı, bir dizi DTU olarak hesaplanarak işlem, depolama ve g/ç kaynaklarının paketlenmiş bir ölçümüdür.

Bu kaynaklar arasındaki oran başlangıçta gerçek dünya OLTP iş yüklerinden tipik olarak tasarlanan bir [çevrimiçi işlem işleme (OLTP) kıyaslama iş yüküne](service-tiers-dtu.md) göre belirlenir. İş yükünüz bu kaynakların herhangi birinin miktarını aştığında, aktarım hızı kısıtlanıyor ve daha yavaş performans ve zaman aşımları elde edilir.

İş yükünüz tarafından kullanılan kaynaklar, Azure bulutundaki diğer veritabanlarının kullanabildiği kaynakları etkilemez. Benzer şekilde, diğer iş yükleri tarafından kullanılan kaynaklar veritabanınızın kullanabildiği kaynakları etkilemez.

![Sınırlayıcı kutusu](./media/purchasing-models/bounding-box.png)

DTU 'lar, farklı işlem boyutlarında ve hizmet katmanlarında veritabanları için ayrılan göreli kaynakları anlamak için kullanışlıdır. Örnek:

- Bir veritabanının işlem boyutunu artırarak, bu veritabanı için kullanılabilir kaynak kümesini ikiye katlayarak DTU 'ları katlama.
- 1750 DTU içeren bir Premium hizmet katmanı P11 veritabanı, 5 DTU ile temel bir hizmet katmanı veritabanından 350 kat daha fazla DTU işlem gücü sağlar.  

İş yükünüzün kaynak (DTU) tüketimi hakkında daha derin Öngörüler elde etmek için [Query-Performance Insights](query-performance-insight-use.md) ' ı kullanarak şunları yapın:

- CPU/süre/yürütme sayısına göre en iyi sorguları, gelişmiş performans için ayarlanabilir olabilecek şekilde belirler. Örneğin, g/ç yoğun bir sorgu, kullanılabilir belleğin belirli bir hizmet katmanında ve işlem boyutunda daha iyi kullanımını sağlamak için [bellek içi iyileştirme tekniklerinden](../in-memory-oltp-overview.md) yararlanabilir.
- Metnin ayrıntılarını ve kaynak kullanımı geçmişini görüntülemek için bir sorgunun ayrıntılarına gidin.
- [SQL veritabanı Danışmanı](database-advisor-implement-performance-recommendations.md)tarafından gerçekleştirilen eylemleri gösteren performans ayarlama önerilerine erişin.

### <a name="elastic-database-transaction-units-edtus"></a>Elastik veritabanı işlem birimleri (eDTU 'lar)

Her zaman gerekli olmayan adanmış bir kaynak kümesi (DTU) sağlamak yerine her zaman kullanılabilen veritabanları için bu veritabanlarını bir [elastik havuza](elastic-pool-overview.md)yerleştirebilirsiniz. Elastik havuzdaki veritabanları tek bir sunucuda bulunur ve kaynak havuzunu paylaşır.

Elastik havuzdaki paylaşılan kaynaklar elastik veritabanı işlem birimleri (eDTU 'lar) ile ölçülür. Elastik havuzlar, yaygın olarak değişen ve öngörülemeyen kullanım düzenlerine sahip birden çok veritabanına ilişkin performans hedeflerini yönetmek için basit ve uygun maliyetli bir çözüm sunar. Elastik havuz, tüm kaynakların havuzdaki bir veritabanı tarafından tüketilmeyeceğinden, havuzdaki her bir veritabanının her zaman gereken en düşük miktarda gerekli kaynağa sahip olmasını güvence altına alır.

Bir havuza bir set fiyatı için dizi eDTU sayısı verilir. Elastik havuzda, bireysel veritabanları yapılandırılan sınırlar içinde otomatik ölçeklendirme yapabilir. Daha ağır bir yük altında bulunan bir veritabanı, talebi karşılamak için daha fazla eDTU tüketir. Daha hafif yük altındaki veritabanları, daha az eDTU kullanır. Yükü olmayan veritabanları eDTU 'yu tüketmez. Kaynaklar her veritabanı yerine tüm havuz için sağlandığından, elastik havuzlar yönetim görevlerinizi basitleştirir ve havuz için öngörülebilir bir bütçe sağlar.

Mevcut bir havuza, veritabanı kapalı kalma süresi olmadan ve havuzdaki veritabanları üzerinde herhangi bir etkisi olmadan ek eDTU 'lar ekleyebilirsiniz. Benzer şekilde, artık ek eDTU 'lar gerekmiyorsa, bunları dilediğiniz zaman mevcut bir havuzdan kaldırın. Ayrıca, herhangi bir zamanda bir havuza veritabanları ekleyebilir veya havuzdan veritabanları çıkarabilirsiniz. Diğer veritabanları için eDTU 'ları ayırmak üzere bir veritabanının ağır bir yük altında kullanabileceği eDTU sayısını sınırlayın. Bir veritabanı, kaynakları tutarlı bir şekilde kullanıyorsa havuzun dışına taşıyın ve tahmin edilebilir miktarda gerekli kaynakla tek bir veritabanı olarak yapılandırın.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Bir iş yükü için gereken DTU sayısını belirleme

Mevcut bir şirket içi veya SQL Server sanal makine iş yükünü SQL veritabanına geçirmek istiyorsanız, gerekli DTU sayısını yaklaşık olarak tahmin etmek için [DTU Hesaplayıcı](https://dtucalculator.azurewebsites.net/) ' ı kullanın. Mevcut bir SQL veritabanı iş yükü için, veritabanı-kaynak tüketiminizi (DTU 'Lar) anlamak üzere [Query-Performance Insights](query-performance-insight-use.md) 'ı kullanın ve iş yükünüzü iyileştirmek için daha derin Öngörüler elde edin. [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dinamik yönetim görünümü (DMV), son saatin kaynak tüketimini görüntülemenize olanak sağlar. [Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) Katalog görünümü son 14 güne ait kaynak tüketimini görüntüler, ancak beş dakikalık Ortalamalar için daha düşük bir hassaside.

### <a name="determine-dtu-utilization"></a>DTU kullanımını belirleme

Bir veritabanının veya elastik havuzun DTU/eDTU sınırına göre DTU/eDTU kullanımının ortalama yüzdesini öğrenmek için aşağıdaki formülü kullanın:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Bu formülün giriş değerleri [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)ve [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMVs 'den elde edilebilir. Diğer bir deyişle, bir veritabanının veya elastik havuzun DTU/eDTU sınırına yönelik DTU/eDTU kullanımının yüzdesini öğrenmek için aşağıdakilerden en büyük yüzde değerini seçin: `avg_cpu_percent` , `avg_data_io_percent` , ve `avg_log_write_percent` belirli bir noktada.

> [!NOTE]
> Bir veritabanının DTU sınırı, CPU, okuma, yazma ve veritabanı tarafından kullanılabilir bellek tarafından belirlenir. Ancak, SQL veritabanı altyapısı performansı artırmak için genellikle veri önbelleği için tüm kullanılabilir belleği kullandığından, `avg_memory_usage_percent` geçerli veritabanı yüküne bakılmaksızın değer genellikle yüzde 100 ' e yakın olur. Bu nedenle, bellek, DTU sınırını dolaylı olarak etkilese de, DTU kullanım formülünde kullanılmaz.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Elastik bir kaynak havuzundan faydalanabilir iş yükleri

Havuzlar, düşük kaynak kullanımı ortalaması olan veritabanları için uygundur ve görece seyrek kullanım ani artışları vardır. Daha fazla bilgi için bkz. [SQL veritabanı elastik havuzunu ne zaman düşünmeniz gerekir?](elastic-pool-overview.md).

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>DTU tabanlı satın alma modelinde donanım nesilleri

DTU tabanlı satın alma modelinde, müşteriler veritabanları için kullanılan donanım üretimini seçemezsiniz. Belirli bir veritabanı, genellikle uzun bir süre boyunca belirli bir donanım oluşturma üzerinde kalır (genellikle birden çok ay için), bir veritabanının başka bir donanım oluşturmaya taşınmasına neden olabilecek bazı olaylar vardır.

Örneğin, bir veritabanı farklı bir hizmet hedefine göre yukarı veya aşağı ölçeklendirildiğinde ya da bir veri merkezindeki geçerli altyapı kapasite sınırlarına yaklaşıyorsa ya da o anda kullanılmakta olan donanım kullanım ömrü nedeniyle kullanımdan bulunursa, bir veritabanı farklı bir donanım oluşturmaya taşınabilir.

Bir veritabanı farklı bir donanıma taşınırsa, iş yükü performansı değişebilir. DTU modeli, hizmet hedefi (DTU sayısı) aynı kaldığı sürece, [DTU kıyaslama](./service-tiers-dtu.md#dtu-benchmark) iş yükünün işleme ve yanıt süresinin, veritabanı farklı bir donanım oluşturmaya giderken önemli ölçüde aynı kalacağından emin olur.

Ancak, Azure SQL veritabanı 'nda çalışan çok sayıda müşteri iş yüklerinin tamamında, aynı hizmet hedefi için farklı donanımlar kullanmanın etkisi daha fazla olabilir. Farklı iş yükleri, farklı donanım yapılandırma ve özelliklerinden faydalanır. Bu nedenle, DTU kıyaslamalarından başka iş yükleri için, veritabanı bir donanım oluşturma işleminden diğerine taşınırsa performans farklılıklarını görmek mümkündür.

Örneğin, ağ gecikmesine duyarlı bir uygulama, 5. nesil 'de hızlandırılmış ağ kullanımı nedeniyle 5. nesil Hardware vs. 4. nesil üzerinde daha iyi performans görebilir, ancak yoğun okuma GÇ kullanan bir uygulama, 5. nesil üzerinde çekirdek oranına göre daha yüksek bir bellek nedeniyle 4. nesil donanımında daha iyi performans sağlayabilir.

Donanım değişiklikleri veya veritabanları için donanım oluşturma seçimini denetlemek isteyen müşteriler iş yüklerine duyarlı olan müşteriler, veritabanı oluşturma ve ölçekleme sırasında tercih edilen donanım üretimini seçmek için [Vcore](service-tiers-vcore.md) modelini kullanabilir. Sanal çekirdek modelinde her bir her bir hizmet hedefinin kaynak limitleri her bir donanım oluşturma işlemi için, hem [tek veritabanları](resource-limits-vcore-single-databases.md) hem de [elastik havuzlar](resource-limits-vcore-elastic-pools.md)için belgelenmiştir. Sanal çekirdek modelindeki donanım oluşturmaları hakkında daha fazla bilgi için bkz. [donanım nesilleri](./service-tiers-vcore.md#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Sık sorulan sorular (SSS)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>DTU tabanlı bir hizmet katmanından sanal çekirdek tabanlı hizmet katmanına dönüştürmek için uygulamamı çevrimdışına alma yapmam gerekir mi?

Hayır. Uygulamayı çevrimdışına almanız gerekmez. Yeni hizmet katmanları, mevcut veritabanlarını standart, Premium hizmet katmanına ve diğer şekilde yükseltme sürecine benzer basit bir çevrimiçi dönüştürme yöntemi sunar. Azure portal, PowerShell, Azure CLı, T-SQL veya REST API kullanarak bu dönüştürmeyi başlatabilirsiniz. Bkz. [Tek veritabanlarını yönetme](single-database-scale.md) ve [Elastik havuzları yönetme](elastic-pool-overview.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Sanal çekirdek tabanlı satın alma modelindeki hizmet katmanından bir veritabanını DTU tabanlı satın alma modelindeki bir hizmet katmanına dönüştürebilir miyim?

Evet, Azure portal, PowerShell, Azure CLı, T-SQL veya REST API kullanarak veritabanınızı desteklenen herhangi bir performans hedefine kolayca dönüştürebilirsiniz. Bkz. [Tek veritabanlarını yönetme](single-database-scale.md) ve [Elastik havuzları yönetme](elastic-pool-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- Sanal çekirdek tabanlı satın alma modeli hakkında daha fazla bilgi için bkz. [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md).
- DTU tabanlı satın alma modeli hakkında daha fazla bilgi için bkz. [DTU tabanlı satın alma modeli](service-tiers-dtu.md).