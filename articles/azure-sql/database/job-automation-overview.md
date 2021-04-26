---
title: Elastik ışlerle iş Otomasyonu 'na genel bakış
description: Bir veya daha fazla veritabanı kümesi üzerinde Transact-SQL (T-SQL) betikleri çalıştırmak için Iş otomasyonu için esnek Işleri kullanın
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, contperf-fy21q3
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: conceptual
author: williamdassafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/1/2021
ms.openlocfilehash: 295889cf64d27761021dd09549a3366ea142516e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752041"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>Esnek işleri kullanarak yönetim görevlerini otomatikleştirme (Önizleme)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Transact-SQL (T-SQL) sorgularını çalıştırmak ve bakım görevlerini gerçekleştirmek için, bir veya birden çok Azure SQL veritabanında düzenli olarak yürütülen elastik işler oluşturabilir ve zamanlayabilirsiniz. 

İşin yürütüldüğü hedef veritabanı veya veritabanı gruplarını tanımlayabilir ve iş çalıştırmak için zamanlamaları belirleyebilirsiniz.
İşler, hedef veritabanında oturum açma görevini üstlenir. Veritabanı grubunda çalıştırılacak Transact-SQL betiklerinin tanımlama, bakımını yapma ve sürekliliğini sağlama konusunda da denetim sahibi olursunuz.

Her iş yürütmenin durumunu günlüğe kaydeder ve herhangi bir hata oluşursa otomatik olarak işlemleri yeniden dener.

## <a name="when-to-use-elastic-jobs"></a>Elastik işlerin ne zaman kullanılacağı

Esnek iş Otomasyonu 'nu kullanabileceğiniz birkaç senaryo vardır:

- Yönetim görevlerini otomatikleştirin ve saatler, saat sonra her hafta içinde çalışacak şekilde zamanlayın.
  - Şema değişiklikleri, kimlik bilgileri yönetimi, performans verisi toplama veya kiracı (müşteri) telemetri verilerini toplama gibi görevleri dağıtın.
  - Başvuru verilerini güncelleştirme (tüm veritabanlarında ortak bilgiler), Azure Blob depolamadan veri yükleme.
- İşleri bir veritabanı koleksiyonunda yoğun saatlerin dışında yenilenecek şekilde yapılandırın.
  - Bir veritabanı kümesinden alınan sorgu sonuçlarını düzenli olarak merkezi bir tabloya toplayın. Performans sorguları sürekli yürütülebilir ve yürütülecek ek görevleri tetikleyecek şekilde yapılandırılabilir.
- Raporlama için veri toplama
  - Veritabanları koleksiyonundan tek bir hedef tabloya veri toplama.
  - Çok sayıda veritabanında müşteri telemetri verilerinin toplanması gibi daha uzun süre çalışan veri işleme sorguları çalıştırın. Sonuçlar daha ayrıntılı analiz için tek bir hedef tabloda toplanır.
- Veri taşımaları 

### <a name="automation-on-other-platforms"></a>Diğer platformlarda Otomasyon

Farklı platformlarda aşağıdaki iş zamanlama teknolojilerini göz önünde bulundurun:

- **Elastik işler** , Azure SQL veritabanı 'ndaki bir veya daha fazla veritabanında özel Işleri yürütülen iş zamanlama hizmetlerdir.
- **SQL Aracısı işleri** , SQL Server görev otomasyonu için kullanılmaya devam eden SQL Agent hizmeti tarafından yürütülür ve Azure SQL yönetilen örneklerine da dahildir. SQL Aracısı Işleri Azure SQL veritabanı 'nda kullanılamıyor.

Elastik Işler [Azure SQL veritabanlarını](sql-database-paas-overview.md), [Azure SQL veritabanı elastik HAVUZLARıNı](elastic-pool-overview.md)ve Azure SQL veritabanlarını parça [haritalarında](elastic-scale-shard-map-management.md)hedefleyebilir.

SQL Server ve Azure SQL yönetilen örneğinde T-SQL betiği iş otomasyonu için [SQL Agent](job-automation-managed-instances.md)'ı düşünün. 

Azure SYNAPSE Analytics 'te T-SQL betiği iş otomasyonu için, [Azure Data Factory temel alan](../../synapse-analytics/data-integration/concepts-data-factory-differences.md) [yinelenen tetikleyicilerle işlem hatlarını](../../synapse-analytics/data-integration/concepts-data-factory-differences.md)düşünün.

SQL Agent (SQL Server ve SQL yönetilen örneği kapsamında mevcuttur) arasındaki farkları ve veritabanı elastik Iş Aracısı 'nı (Azure SQL veritabanlarında veya SQL Server ve Azure SQL yönetilen örneği, Azure SYNAPSE Analytics 'te T-SQL ' i yürütebilen) dikkat edin.

| |Elastik İşler |SQL Aracısı |
|---------|---------|---------|
|**Kapsam** | Azure SQL veritabanı ve/veya veri ambarlarında iş aracısıyla aynı Azure bulutundaki tüm veritabanları. Hedefler farklı sunucularda, aboneliklerde ve/veya bölgelerde olabilir. <br><br>Hedef gruplar tek tek veritabanlarından veya veri ambarlarından veya bir sunucu, havuz veya parça eşlemesindeki tüm veritabanlarından (iş çalışma zamanında dinamik olarak numaralandırılır) oluşabilir. | SQL aracısıyla aynı örnekteki her bir veritabanı. SQL Server Agent çoklu sunucu yönetimi özelliği, ana/hedef örneklerin iş yürütmeyi koordine etmesine olanak tanır, ancak bu özellik SQL yönetilen örneği 'nde kullanılamaz. |
|**Desteklenen API’ler ve Araçlar** | Portal, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>Elastik iş hedefleri

**Elastik işler** , bir veya daha fazla T-SQL betiğini paralel olarak çok sayıda veritabanı üzerinde, bir zamanlamaya göre veya isteğe bağlı olarak çalıştırma olanağı sunar.

Zamanlanan işleri herhangi bir veritabanı birleşimine karşı çalıştırabilirsiniz: bir veya daha fazla veritabanı, bir sunucudaki tüm veritabanları, bir elastik havuzdaki tüm veritabanları veya parça Haritası, belirli bir veritabanını dahil etme veya hariç tutma esnekliği ile birlikte kullanılabilir. İşler birden fazla sunucu ve birden fazla havuzda çalışabilir, hatta farklı aboneliklerde bulunan veritabanlarını kullanabilir. Sunucular ve havuzlar çalışma zamanında dinamik olarak numaralandırıldığından işler, yürütme zamanında hedef grupta bulunan tüm veritabanlarında çalışır.

Aşağıdaki resimde farklı türlerdeki hedef gruplarda iş yürüten bir iş aracısı gösterilmektedir:

![Elastik İş aracısı kavramsal modeli](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Elastik iş bileşenleri

|Bileşen | Açıklama (ek ayrıntılar tablonun altındadır) |
|---------|---------|
|[**Elastik İş aracısı**](#elastic-job-agent) | İşleri çalıştırmak ve yönetmek için oluşturduğunuz Azure kaynağıdır. |
|[**İş veritabanı**](#elastic-job-database) | Azure SQL veritabanı 'nda iş aracısının işle ilgili verileri, iş tanımlarını vb. depolamak için kullandığı bir veritabanı. |
|[**Hedef grup**](#target-group) | Bir işin çalıştırılacağı sunucu, havuz, veritabanı ve parça eşlemesi kümesidir. |
|[**İş**](#elastic-jobs-and-job-steps) | İş, bir veya daha fazla iş adımından oluşan bir iş birimidir. İş adımları çalıştırılacak T-SQL betiğinin yanı sıra betiğin yürütülmesi için gerekli olan diğer ayrıntıları belirtir. |

#### <a name="elastic-job-agent"></a>Elastik iş Aracısı

Elastik İş aracısı; işlerin oluşturulması, çalıştırılması ve yönetilmesi için kullanılan Azure kaynağıdır. Elastik İş aracısı, portalda oluşturduğunuz bir Azure kaynağıdır ([PowerShell](elastic-jobs-powershell-create.md) ve REST de desteklenir).

**Elastik iş Aracısı** oluşturmak IÇIN Azure SQL veritabanı 'nda mevcut bir veritabanı gerekir. Aracı, bu mevcut Azure SQL veritabanını [*iş veritabanı*](#elastic-job-database)olarak yapılandırır.

Elastik İş aracısı ücretsizdir. İş veritabanı, Azure SQL veritabanı 'ndaki tüm veritabanları ile aynı hızda faturalandırılır.

#### <a name="elastic-job-database"></a>Elastik iş veritabanı

*İş veritabanı*, işleri tanımlamanın yanı sıra iş yürütme durumunu ve geçmişini takip etmek için kullanılır. *İş veritabanı* , aracı meta verilerini, günlükleri, sonuçları, iş tanımlarını depolamak için de kullanılır ve T-SQL ' i kullanarak iş oluşturmak, çalıştırmak ve yönetmek için çok sayıda kullanışlı saklı yordam ve diğer veritabanı nesneleri içerir.

Geçerli önizleme için, elastik bir Iş Aracısı oluşturmak için Azure SQL veritabanı 'nda (S0 veya üzeri) var olan bir veritabanı gereklidir.

*İş veritabanının* temiz, boş, S0 veya daha yüksek bir hizmet hedefı Azure SQL veritabanı olması gerekir. *İş veritabanının* önerilen hizmet hedefi S1 veya daha yükseği, ancak en uygun seçenek, işinizin performans ihtiyaçlarına bağlıdır: iş adımları sayısı, iş hedefi sayısı ve işlerin ne sıklıkta çalıştırıldığı. 

İş veritabanına yönelik işlemler beklenenden yavaşsa, Azure portal veya [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV kullanarak yavaşlık süreleri sırasında veritabanı performansını ve kaynak kullanımını [izleyin](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) . CPU, veri g/ç gibi bir kaynağın kullanımı veya %100 günlük yazma yaklaşımının ve yavaşlığın süreleriyle ilişkili olması halinde, iş veritabanı performansı yeterince iyileştirene kadar veritabanını daha yüksek hizmet hedeflerine ( [DTU modelinde](service-tiers-dtu.md) veya [Vcore modelinde](service-tiers-vcore.md)) artımlı olarak ölçeklendirin.

##### <a name="elastic-job-database-permissions"></a>Elastik iş veritabanı izinleri

İş aracısı oluşturma sırasında *İş veritabanında* bir şema, tablolar ve *jobs_reader* adlı bir rol oluşturulur. Rol, aşağıdaki izinle oluşturulur ve yöneticilere iş izleme için daha ayrıntılı erişim denetimi sunmak üzere tasarlanmıştır:

|Rol adı |'jobs' şeması izinleri |'jobs_internal' şeması izinleri |
|---------|---------|---------|
|**jobs_reader** | SELECT | Yok |

> [!IMPORTANT]
> Veritabanı yöneticisi olarak *İş veritabanına* erişim izni vermeden önce güvenlik durumunu gözden geçirin. İş oluşturma veya düzenleme izinlerine sahip kötü niyetli bir Kullanıcı, kötü niyetli kullanıcının denetimindeki bir veritabanına bağlanmak için depolanan bir kimlik bilgisi kullanan bir iş oluşturabilir veya düzenleyebilir, bu da kötü niyetli kullanıcının kimlik bilgisinin parolasını belirlemesine izin verebilir.

#### <a name="target-group"></a>Hedef grup

*Hedef grup*, işin üzerinde çalışacağı veritabanı kümesini tanımlar. Hedef grup içinde aşağıdaki bileşenlerden birden fazlası veya birleşimi bulunabilir:

- **MANTıKSAL SQL Server** -bir sunucu belirtilmişse, iş yürütmesi sırasında sunucuda bulunan tüm veritabanları grubun bir parçasıdır. İş yürütülmeden önce grubun numaralandırılması ve güncelleştirilmesi için asıl veritabanı kimlik bilgisinin sağlanması gerekir. Mantıksal sunucular hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı ve Azure SYNAPSE Analytics 'te sunucu nedir?](logical-servers.md).
- **Elastik havuz**: Elastik havuz belirtilirse işin yürütülmesi sırasında elastik havuzda mevcut olan tüm veritabanları gruba dahil edilir. Sunucuda olduğu gibi iş yürütülmeden önce grubun güncelleştirilmesi için asıl veritabanı kimlik bilgisinin sağlanması gerekir.
- **Tek veritabanı**: Gruba eklenmek üzere bir veya daha fazla tek veritabanı belirtin.
- Parça **eşleme-parça haritasının veritabanları** .

> [!TIP]
> İşin yürütülmesi sırasında *dinamik numaralandırma*, sunucuları veya havuzları içeren hedef gruplardaki veritabanı kümesini yeniden değerlendirir. Dinamik numaralandırma, **işlerin, yürütüldükleri sırada sunucuda veya havuzda mevcut olan tüm veritabanlarında çalıştırılmasını sağlar**. Veritabanı listesinin çalışma zamanında yeniden değerlendirilmesi özellikle havuz veya sunucu üyeliğinin sık değiştiği senaryolar için kullanışlıdır.

Havuzlar ve tek veritabanları gruba dahil edilebilir veya gruptan hariç tutulabilir. Bu sayede istenen veritabanlarını içeren bir hedef grup oluşturulabilir. Örneğin hedef gruba bir sunucuyu ekleyebilir ancak bir elastik havuzdaki belirli veritabanlarını (veya bir havuzun tamamını) hariç tutabilirsiniz.

Hedef grupta birden fazla abonelikte ve bölgede bulunan veritabanları mevcut olabilir. Birden fazla bölgenin söz konusu olduğu yürütme işlemlerinin aynı bölgedeki yürütmelere kıyasla daha yüksek gecikme süresine sahip olacağını unutmayın.

Aşağıdaki örneklerde işin çalıştırılacağı veritabanlarının belirlenmesi için farklı hedef grubu tanımlarının iş yürütme sırasında nasıl dinamik olarak numaralandırıldığı gösterilmektedir:

![Hedef grup örnekleri](./media/job-automation-overview/targetgroup-examples1.png)

**Örnek 1**'de tek veritabanlarının listesini içeren bir hedef grup gösterilmektedir. Bir iş adımı bu hedef grup kullanılarak yürütüldüğünde iş adımının eylemi bu veritabanlarının her birinde yürütülür.<br>
**Örnek 2** , hedef olarak sunucu içeren bir hedef grubu gösterir. Bir iş adımı bu hedef grup kullanılarak yürütüldüğünde sunucudaki veritabanı listesinin belirlenmesi için sunucu dinamik olarak numaralandırılır. İş adımının eylemi bu veritabanlarının her birinde yürütülür.<br>
**Örnek 3**'te gösterilen hedef grup *Örnek 2*'dekine benzer ancak belirli veritabanları özel olarak hariç tutulmuştur. İş adımının eylemi hariç tutulan veritabanında *yürütülmez*.<br>
**Örnek 4**'te hedef olarak bir elastik havuz içeren bir hedef grup gösterilmektedir. *Örnek 2*'ye benzer şekilde havuz iş çalıştırma zamanında dinamik olarak numaralandırılarak havuzdaki veritabanlarının listesi belirlenir.
<br><br>

![Ek hedef grubu örnekleri](./media/job-automation-overview/targetgroup-examples2.png)

**Örnek 5** ve **örnek 6** , sunucuların, elastik havuzların ve veritabanlarının dahil etme ve dışlama kuralları kullanılarak birleştirilebileceği gelişmiş senaryolar göstermektedir.<br>
**Örnek 7**'de parça eşlemesi içinde bulunan ve iş çalıştırma zamanında değerlendirilebilecek parçalar gösterilmektedir.

> [!NOTE]
> Iş veritabanının kendisi bir işin hedefi olabilir. Bu senaryoda, Iş veritabanı tıpkı diğer hedef veritabanları gibi değerlendirilir. İş kullanıcısının oluşturulması ve iş veritabanında yeterli izinleri verilmesi gerekir ve iş kullanıcısına ait veritabanı kapsamlı kimlik bilgileri, diğer hedef veritabanları için de olduğu gibi Iş veritabanında da bulunmalıdır.

#### <a name="elastic-jobs-and-job-steps"></a>Elastik işler ve iş adımları

*İş*, bir zamanlamaya göre veya tek seferlik yürütülen bir çalışma birimidir. Bir işte bir veya daha fazla *iş adımı* bulunur.

Her işte yürütülecek bir T-SQL betiği, bu T-SQL betiğinin çalıştırılacağı bir veya daha fazla hedef grup ve iş aracısının hedef veritabanına bağlanması için gereken kimlik bilgileri belirtilir. İşin her adımı özelleştirilebilir zaman aşımı ve yeniden deneme ilkelerine sahiptir ve her adımda isteğe bağlı çıkış parametreleri belirtilebilir.

#### <a name="job-output"></a>İş çıktısı

İş adımlarının her bir hedef veritabanında elde ettiği sonuç ayrıntılı olarak kaydedilir ve betik çıktısı, belirtilen bir tabloya aktarılabilir. Bir işin döndürdüğü verilerin kaydedileceği bir veritabanı belirtebilirsiniz.

#### <a name="job-history"></a>İş geçmişi

[Tablo jobs.job_executions sorgulayarak](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status) *Iş veritabanındaki* elastik iş yürütme geçmişini görüntüleyin. Sistem temizleme işlemi 45 günden daha eski olan yürütme geçmişi verilerini siler. 45 günden daha eski geçmişi kaldırmak için *iş veritabanında* **sp_purge_jobhistory** saklı yordamını çağırın.

#### <a name="job-status"></a>İş durumu

[Tablo jobs.job_executions sorgulayarak](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status) *Iş veritabanındaki* elastik iş yürütmelerini izleyebilirsiniz. 

### <a name="agent-performance-capacity-and-limitations"></a>Aracı performansı, kapasitesi ve sınırlamaları

Elastik İşler, uzun süren işlerin tamamlanması sırasında en az düzeyde işlem kaynağı kullanır.

Hedef veritabanı grubunun boyutuna ve bir işin istenen yürütme süresine (eşzamanlı çalışan sayısı) bağlı olarak aracı için gerekli olan işlem süresi ve *İş veritabanı* performansı değişiklik gösterir (hedef ve iş sayısı ne kadar yüksek olursa gereken işlem zamanı o kadar fazla olur).

Şu anda sınır 100 eşzamanlı iş olur.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>İşlerin hedef veritabanının performansını düşürmesini engelleme

Bir SQL elastik havuzundaki veritabanları üzerinde iş çalıştırılması sırasında kaynakların aşırı yüklenmesini önlemek için işler aynı anda üzerinde çalışılabilecek veritabanı sayısını sınırlayacak şekilde yapılandırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Elastik işler oluşturma ve yönetme](elastic-jobs-overview.md)
- [PowerShell kullanarak elastik Işler oluşturma ve yönetme](elastic-jobs-powershell-create.md)
- [Transact-SQL (T-SQL) kullanarak Elastik İşler oluşturma ve yönetme](elastic-jobs-tsql-create-manage.md)
