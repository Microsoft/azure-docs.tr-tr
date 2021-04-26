---
title: "Azure SQL veritabanı 'na SQL Server: geçişe genel bakış"
description: SQL Server veritabanlarınızı Azure SQL veritabanı 'na geçirmek için kullanabileceğiniz araçlar ve seçenekler hakkında bilgi edinin.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f515725ea0f306546039b92d953254a093b15b8b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065186"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Geçişe genel bakış: Azure SQL veritabanı 'na SQL Server
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

SQL Server veritabanlarınızı Azure SQL veritabanı 'na geçirmeye yönelik seçenekler ve konular hakkında bilgi edinin. 

Şirket içinde veya üzerinde çalışan SQL Server veritabanlarını geçirebilirsiniz: 

- Azure sanal makineler 'de SQL Server.  
- Amazon Web Services (AWS) elastik Işlem bulutu (EC2).
- AWS Ilişkisel veritabanı hizmeti (RDS).
- Google Cloud Platform (GCP) içinde işlem altyapısı.  
- GCP 'de SQL Server için bulut SQL. 

Diğer geçiş kılavuzlarında, bkz. [Veritabanı geçişi](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Genel Bakış

[Azure SQL veritabanı](../../database/sql-database-paas-overview.md) , tam olarak yönetilen bir hizmet olarak platform (PaaS) gerektiren SQL Server iş yükleri için önerilen bir hedef seçenektir. SQL veritabanı çoğu veritabanı yönetim işlevini işler. Ayrıca, birçok uygulama türüne uyacak yerleşik yüksek kullanılabilirlik, akıllı sorgu işleme, ölçeklenebilirlik ve performans özellikleri de vardır. 

SQL veritabanı, farklı türlerde uygulamalara veya iş yüklerine sahip birden çok [dağıtım modeli](../../database/sql-database-paas-overview.md#deployment-models) ve [hizmet katmanı](../../database/service-tiers-vcore.md#service-tiers) ile esneklik sağlar.

SQL veritabanına geçiş avantajlarından biri, PaaS yeteneklerini kullanarak uygulamanızı modernleştirin. Daha sonra, SQL Aracısı işleri gibi örnek düzeyinde kapsamlı olan teknik bileşenlerde herhangi bir bağımlılığı ortadan kaldırabilirsiniz.

Ayrıca, SQL Server şirket içi lisanslarınızı Azure SQL veritabanı 'na geçirmek için SQL Server [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) kullanarak da maliyetleri kaydedebilirsiniz. [Sanal çekirdek tabanlı satın alma modelini](../../database/service-tiers-vcore.md)seçerseniz bu seçenek kullanılabilir.

## <a name="considerations"></a>Dikkat edilmesi gerekenler 

Geçiş seçeneklerini değerlendirirken göz önünde bulundurmanız gereken anahtar faktörleri şunlardır: 

- Sunucu ve veritabanı sayısı
- Veritabanlarının boyutu
- Geçiş işlemi sırasında kabul edilebilir çalışma süresi 

Bu kılavuzda listelenen geçiş seçenekleri bu faktörleri hesaba götürür. Azure SQL veritabanı 'na mantıksal veri geçişi için, geçiş süresi bir veritabanındaki nesne sayısına ve veritabanının boyutuna bağlı olabilir. 

Çeşitli iş yükleri ve Kullanıcı tercihleri için araçlar mevcuttur. Bazı araçlar, bir kullanıcı arabirimi tabanlı araç aracılığıyla tek bir veritabanının hızlı geçişini yapmak için kullanılabilir. Diğer araçlar, geçişleri işlemek için birden çok veritabanının geçişini otomatik hale getirebilir. 

## <a name="choose-an-appropriate-target"></a>Uygun bir hedef seçin

Azure SQL veritabanı 'nın doğru dağıtım modelini ve hizmet katmanını seçmenize yardımcı olacak genel yönergeleri göz önünde bulundurun. Dağıtım sırasında işlem ve depolama kaynaklarını seçebilir ve sonra uygulamanız için kapalı kalma süresi olmadan [Azure Portal kullanarak bunları](../../database/scale-resources.md) daha sonra değiştirebilirsiniz.

**Dağıtım modelleri**: tek bir veritabanı veya elastik havuz arasında karar vermek için uygulama iş yükünüzü ve kullanım modelini anlayın. 

- [Tek bir veritabanı](../../database/single-database-overview.md) , en modern bulut uygulamaları ve mikro hizmetler için uygun olan tam olarak yönetilen bir veritabanını temsil eder.
- [Elastik havuz](../../database/elastic-pool-overview.md) , CPU veya bellek gibi paylaşılan bir kaynak kümesiyle tek bir veritabanı koleksiyonudur. Aynı kaynak kümesini etkin bir şekilde paylaşabilen öngörülebilir kullanım desenleriyle, bir havuzdaki veritabanlarını birleştirmek için uygundur.

**Model satın alma**: sanal çekirdek, veritabanı işlem BIRIMI (DTU) veya sunucusuz satın alma modelleri arasında seçim yapın. 

- [Sanal çekirdek modeli](../../database/service-tiers-vcore.md) , Azure SQL veritabanı Için sanal çekirdek sayısını seçmenizi sağlar, bu nedenle şirket içi SQL Server çevrilirken en kolay seçenektir. Bu, lisans maliyetlerinin [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)kaydedilmesini destekleyen tek seçenektir. 
- [DTU modeli](../../database/service-tiers-dtu.md) , KARıŞTıRıLAN bir DTU sağlamak için temel işlem, bellek ve g/ç kaynaklarını soyutlar. 
- [Sunucusuz model](../../database/serverless-tier-overview.md) , kullanım için saniyede faturalandırılan işlem kaynaklarıyla otomatik olarak isteğe bağlı ölçeklendirme gerektiren iş yükleri içindir. Sunucusuz işlem katmanı, etkin olmayan dönemler sırasında veritabanlarını otomatik olarak duraklatır (yalnızca depolamanın faturalandırılır). Etkinlik döndüğünde veritabanlarını otomatik olarak sürdürür. 

**Hizmet katmanları**: farklı uygulama türleri için tasarlanan üç hizmet katmanı arasından seçim yapın.

- [Genel amaçlı/standart hizmet katmanı](../../database/service-tier-general-purpose.md) , bir işlem ve depolama alanı ile, orta ve düşük katmanda uygulama sunmaya uygun, dengeli bir bütçe yönelimli seçenek sunar. Yedeklilik, hatalardan kurtulmak için depolama katmanında yerleşik olarak bulunur. Çoğu veritabanı iş yükü için tasarlanmıştır. 
- [İş açısından kritik/Premium hizmet katmanı](../../database/service-tier-business-critical.md) , yüksek işlem hızları, düşük gecikme süreli g/ç ve yüksek düzeyde dayanıklılık gerektiren yüksek katmanlı uygulamalardır. İkincil çoğaltmalar yük devretme için kullanılabilir ve okuma iş yüklerinin yükünü devreder.
- [Hiper ölçek hizmet katmanı](../../database/service-tier-hyperscale.md) , büyüyen veri birimlerine sahip veritabanları içindir ve veritabanı boyutunda otomatik olarak 100 TB 'ye kadar ölçeklendirilmesi gerekir. Çok büyük veritabanları için tasarlanmıştır. 

> [!IMPORTANT]
> [İşlem günlüğü oranı,](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) Azure SQL veritabanı 'na göre, yüksek alım tarifelerinin sınırlanmaya tabidir. Bu nedenle, geçiş sırasında CPU veya işleme hızını kolaylaştırmak için hedef veritabanı kaynaklarını (sanal çekirdekler veya DTU 'Lar) ölçeklendirmeniz gerekebilir. Uygun boyutta hedef veritabanını seçin, ancak gerekirse geçiş için kaynakları ölçeklendirmeye planlayın. 


### <a name="sql-server-vm-alternative"></a>SQL Server VM alternatifi

İşletmeniz Azure [sanal makinelerinde SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) Azure SQL veritabanı 'ndan daha uygun bir hedef olan gereksinimlere sahip olabilir. 

İşletmeniz için aşağıdaki koşullardan biri geçerliyse, bunun yerine SQL Server sanal makineye (VM) geçmeyi göz önünde bulundurun: 

- SQL Server ile aynı sanal makineye üçüncü taraf veya özel aracılar yüklemek gibi işletim sistemine veya dosya sistemine doğrudan erişmeniz gerekir. 
- FILESTREAM/FileTable, PolyBase ve platformlar arası işlemler gibi hala desteklenmeyen özelliklerde kesin bağımlılığa sahip olursunuz. 
- SQL Server belirli bir sürümünde (örneğin, 2012) kalbilmeniz gerekir. 
- İşlem gereksinimleriniz yönetilen örnek tekliflerinden çok daha düşüktür (örneğin, bir sanal çekirdek) ve veritabanı birleştirme kabul edilebilir bir seçenek değildir. 


## <a name="migration-tools"></a>Geçiş araçları 

Aşağıdaki geçiş araçlarını öneririz: 

|Teknoloji | Açıklama|
|---------|---------|
| [Azure Geçişi](../../../migrate/how-to-create-azure-sql-assessment.md) | Bu Azure hizmeti, VMware 'de ölçeklendirerek SQL verilerinizi keşfetmenize ve değerlendirmenize yardımcı olur. Azure SQL dağıtım önerileri, hedef boyutlandırma ve aylık tahminler sağlar. | 
|[Data Migration Yardımcısı](/sql/dma/dma-migrateonpremsqltosqldb)|Microsoft 'un bu masaüstü aracı, Azure SQL veritabanı 'na yönelik SQL Server ve tek veritabanı geçişlerinin (şema ve veri) sorunsuz değerlendirmelerini sağlar. </br></br>Araç, şirket içi veya kaynak veritabanlarınıza bağlantısı olan yerel makinenizde bir sunucuya yüklenebilir. Geçiş işlemi, kaynak ve hedef veritabanlarındaki nesneler arasındaki mantıksal bir veri taşıdır.|
|[Azure Veritabanı Geçiş Hizmeti](../../../dms/tutorial-sql-server-to-azure-sql.md)|Bu Azure hizmeti, Azure portal veya PowerShell aracılığıyla otomatik olarak SQL Server veritabanlarını Azure SQL veritabanı 'na geçirebilir. Veritabanı geçiş hizmeti, kaynak SQL Server veritabanlarınıza bağlantı sağlamak için sağlama sırasında tercih edilen bir Azure sanal ağını seçmenizi gerektirir. Tek veritabanlarını veya ölçeklendirmeye geçirebilirsiniz. |
| | |


Aşağıdaki tabloda alternatif geçiş araçları listelenmektedir: 

|Teknoloji |Açıklama  |
|---------|---------|
|[İşlem çoğaltması](../../database/replication-to-sql-database.md)|Kaynak SQL Server veritabanı tablolarından Azure SQL veritabanı 'na veri çoğaltmak için, işlem tutarlılığını koruyarak bir yayımcı-abone türü geçiş seçeneği sağlar. Artımlı veri değişiklikleri, yayımcılar üzerinde gerçekleştikleri sürece abonelere dağıtılır.|
|[İçeri/dışarı aktarma hizmeti/BACPAC](../../database/database-import.md)|[Bacpac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) , bir veritabanının şemasını ve verilerini kapsülleyen. bacpac uzantılı bir Windows dosyasıdır. BACPAC 'yi, SQL Server bir kaynaktaki verileri dışarı aktarmak ve verileri Azure SQL veritabanı 'na aktarmak için kullanabilirsiniz. BACPAC dosyası Azure portal aracılığıyla yeni bir SQL veritabanına aktarılabilir. </br></br> Büyük veritabanları boyutları veya çok sayıda veritabanı ile ölçek ve performans için, veritabanlarını dışa ve içe aktarmak için [SqlPackage](../../database/database-import.md#using-sqlpackage) komut satırı aracını kullanmayı düşünün.|
|[Toplu kopyalama](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[Toplu kopyalama programı (bcp) aracı](/sql/tools/bcp-utility) verileri bir SQL Server örneğinden bir veri dosyasına kopyalar. Verileri kaynağınızdan dışarı aktarmak ve veri dosyasını hedef SQL veritabanına aktarmak için aracını kullanın. </br></br> Verileri Azure SQL veritabanı 'na taşımak için yüksek hızlı toplu kopyalama işlemleri için [akıllı toplu kopyalama aracını](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) kullanarak, paralel kopyalama görevlerinin avantajlarından yararlanarak aktarım hızını en üst düzeye çıkarabilirsiniz.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|Azure Data Factory [kopyalama etkinliği](../../../data-factory/copy-activity-overview.md) , yerleşik bağlayıcılar ve bir [tümleştirme çalışma zamanı](../../../data-factory/concepts-integration-runtime.md)kullanarak kaynak SQL Server veritabanlarından Azure SQL veritabanı 'na veri geçirir.</br> </br> Data Factory, verileri SQL Server kaynaklardan Azure SQL veritabanı 'na taşımak için çok çeşitli [bağlayıcıları](../../../data-factory/connector-overview.md) destekler.|
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL Data Sync, Azure SQL veritabanı 'nda oluşturulan ve hem şirket içinde hem de buluttaki birden çok veritabanı genelinde seçili verileri eşlemenizi sağlayan bir hizmettir.</br>Veri eşitleme, verilerin Azure SQL veritabanı veya SQL Server çeşitli veritabanları arasında güncelleştirilmesi gereken durumlarda yararlıdır.|
| | |

## <a name="compare-migration-options"></a>Geçiş seçeneklerini karşılaştırma

Geçiş seçeneklerini, iş gereksinimlerinize uygun yolu seçmek üzere karşılaştırın. 

Aşağıdaki tabloda, önerdiğimiz geçiş seçenekleri karşılaştırılmaktadır: 

|Geçiş seçeneği  |Kullanılması gereken durumlar  |Dikkat edilmesi gerekenler  |
|---------|---------|---------|
|[Data Migration Yardımcısı](/sql/dma/dma-migrateonpremsqltosqldb) | -Tek veritabanlarını geçirme (şema ve veri).  </br> -Veri geçiş işlemi sırasında kapalı kalma süresine uyum sağlayabilir. </br> </br> Desteklenen kaynaklar: </br> -SQL Server (2005-2019) Şirket içi veya Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Işlem SQL Server VM | -Geçiş etkinliği veritabanı nesneleri arasında (kaynaktan hedefe) veri hareketini gerçekleştirir, bu nedenle onu yoğun olmayan saatlerde çalıştırmanızı öneririz. </br> -Data Migration Yardımcısı geçirilecek satır sayısı dahil olmak üzere veritabanı nesnesi başına geçiş durumunu raporlar.  </br> -Büyük geçişler için (veritabanı veya veritabanı boyutu sayısı), Azure veritabanı geçiş hizmeti 'ni kullanın.|
|[Azure Veritabanı Geçiş Hizmeti](../../../dms/tutorial-sql-server-to-azure-sql.md)| -Tek veritabanlarını veya ölçeklendirmeye geçirin. </br> -Geçiş işlemi sırasında kapalı kalma süresine uyum sağlayabilir. </br> </br> Desteklenen kaynaklar: </br> -SQL Server (2005-2019) Şirket içi veya Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Işlem SQL Server VM | -Ölçekte geçişler, [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md)aracılığıyla otomatikleştirilebilir. </br> -Geçişin tamamlanma süresi, veritabanı boyutuna ve veritabanındaki nesne sayısına bağlıdır. </br> -Kaynak veritabanının salt okunurdur olarak ayarlanmasını gerektirir. |
| | | |

Aşağıdaki tabloda alternatif geçiş seçenekleri karşılaştırılmaktadır: 

|Yöntem veya teknoloji |Kullanılması gereken durumlar    |Dikkat edilmesi gerekenler  |
|---------|---------|---------|
|[İşlem çoğaltması](../../database/replication-to-sql-database.md)| -Değişiklikleri kaynak veritabanı tablolarından hedef SQL veritabanı tablolarına sürekli olarak yayımlayarak geçiş yapın. </br> -Seçili tabloların (bir veritabanının alt kümesi) tam veya kısmi veritabanı geçişlerini yapın.  </br> </br> Desteklenen kaynaklar: </br> - [SQL Server (2016-2019) bazı sınırlamalar](/sql/relational-databases/replication/replication-backward-compatibility) </br> -AWS EC2  </br> -GCP Işlem SQL Server VM  | -Kurulum diğer geçiş seçeneklerine kıyasla görece karmaşıktır.   </br> -Verileri geçirmek için (veritabanlarını çevrimdışına almadan) sürekli bir çoğaltma seçeneği sağlar.  </br> -İşlemsel çoğaltmanın, kaynak SQL Server örneğinde yayımcıyı ayarlarken göz önünde bulundurulması gereken sınırlamalar vardır. Daha fazla bilgi için bkz. [nesneleri yayımlamayla Ilgili sınırlamalar](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) . </br>- [Çoğaltma etkinliğini izlemek](/sql/relational-databases/replication/monitor/monitoring-replication)mümkündür.    |
|[İçeri/dışarı aktarma hizmeti/BACPAC](../../database/database-import.md)| -Bireysel iş kolu uygulama veritabanlarını geçirin. </br>-Daha küçük veritabanları için uygundur.  </br>  -Ayrı bir geçiş hizmeti veya aracı gerektirmez. </br> </br> Desteklenen kaynaklar: </br> -SQL Server (2005-2019) Şirket içi veya Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Işlem SQL Server VM  |  -Verilerin kaynakta aktarılması ve hedefte içeri aktarılması gerektiğinden kapalı kalma süresi gerektirir.   </br> -Dışarı aktarma veya içeri aktarma işleminde kullanılan dosya biçimlerinin ve veri türlerinin, kesme veya veri türü uyuşmazlığı hatalarından kaçınmak için tablo şemaları ile tutarlı olması gerekir.  </br> -Çok sayıda nesne içeren bir veritabanını dışarı aktarmak için geçen süre önemli ölçüde daha yüksek olabilir.       |
|[Toplu kopyalama](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Tam veya kısmi veri geçişleri yapın. </br> -Kapalı kalma süresine uyum sağlayabilir. </br> </br> Desteklenen kaynaklar: </br> -SQL Server (2005-2019) Şirket içi veya Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Işlem SQL Server VM   | -Kaynaktan verileri dışarı aktarmak ve hedefe aktarmak için kapalı kalma süresi gerektirir. </br> -Dışarı aktarma veya içeri aktarma işleminde kullanılan dosya biçimlerinin ve veri türlerinin tablo şemaları ile tutarlı olması gerekir. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| -Kaynak SQL Server veritabanlarından verileri geçirin ve/veya dönüştürün. </br> -Birden çok veri kaynağından Azure SQL veritabanı 'na veri birleştirme, genellikle iş zekası (BI) iş yükleri içindir.  |  -Verileri kaynaktan hedefe taşımak için Data Factory veri taşıma işlem hatları oluşturulması gerekir.   </br> - [Maliyet](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) önemli bir konudur ve işlem hattı Tetikleyicileri, etkinlik çalıştırmaları ve veri hareketinin süresi gibi faktörlere dayanır. |
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Verileri kaynak ve hedef veritabanları arasında eşitler.</br> -Çift yönlü bir akışta Azure SQL veritabanı ve şirket içi SQL Server arasında sürekli eşitleme çalıştırmak için uygundur. | -Azure SQL veritabanı, bir üye veritabanı olarak şirket içi SQL Server veritabanı ile eşitleme için hub veritabanı olmalıdır.</br> -İşlemsel çoğaltmaya kıyasla SQL Data Sync, şirket içi ve Azure SQL veritabanı arasında çift yönlü veri eşitlemesini destekler. </br> -İş yüküne bağlı olarak daha yüksek bir performans etkisi olabilir.|
| | | |

## <a name="feature-interoperability"></a>Özellik birlikte çalışabilirliği 

Diğer SQL Server özelliklerine dayanan iş yüklerini geçirirken daha fazla önemli noktalar vardır.

### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Paketleri [Azure Data Factory](../../../data-factory/introduction.md)Azure-SSIS çalışma zamanına yeniden dağıtarak SQL Server INTEGRATION SERVICES (SSIS) paketlerini Azure 'a geçirin. Azure Data Factory, Azure 'da SSIS paketlerini çalıştırmak için oluşturulmuş bir çalışma zamanı sağlayarak [SSIS paketlerinin geçirilmesini destekler](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) . Alternatif olarak, [veri akışlarını](../../../data-factory/concepts-data-flow-overview.md)kullanarak SSIS ETL (Ayıkla, dönüştürme, yükleme) mantığını Azure Data Factory yerel olarak yeniden yazabilirsiniz.


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
SQL Server Reporting Services (SSRS) raporlarını Power BI sayfalandırılmış raporlara geçirin. Raporlarınızı hazırlama ve geçirmeye yardımcı olması için [rdl geçiş aracını](https://github.com/microsoft/RdlMigration) kullanın. Microsoft bu aracı, müşterilerin, SSRS sunucularından rapor tanım dili (RDL) raporlarını Power BI 'e geçirmeye yardımcı olmak için geliştirilmiştir. GitHub üzerinden sunulan bu araçla birlikte geçiş senaryosunun tüm aşamalarını kapsayan belgeler de sunulmaktadır. 

### <a name="high-availability"></a>Yüksek kullanılabilirlik
Her zaman açık yük devretme kümesi örnekleri ve her zaman açık kullanılabilirlik grupları gibi yüksek kullanılabilirlik özelliklerinin SQL Server el ile kurulması, hedef SQL veritabanında artık kullanılmıyor olur. Yüksek kullanılabilirlik mimarisi, Azure SQL veritabanı için [genel amaçlı (Standart kullanılabilirlik modeli)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) ve [iş açısından kritik (Premium kullanılabilirlik modeli)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) hizmet katmanlarında zaten yerleşik olarak bulunur. İş Açısından Kritik/Premium hizmet katmanı Ayrıca, salt okuma amacıyla ikincil düğümlerden birine bağlanmayı sağlayan okuma ölçeğini de sağlar. 

Azure SQL veritabanına dahil olan yüksek kullanılabilirliğe sahip mimarinin ötesinde, [otomatik yük devretme grupları](../../database/auto-failover-group-overview.md) özelliği, yönetilen bir örnekteki veritabanlarının başka bir bölgeye çoğaltılmasını ve yük devretmesini yönetmenizi sağlar. 

### <a name="sql-agent-jobs"></a>SQL Aracısı işleri
SQL Aracısı işleri, Azure SQL veritabanında doğrudan desteklenmez ve [elastik veritabanı işlerine (Önizleme)](../../database/job-automation-overview.md)dağıtılması gerekir.

### <a name="logins-and-groups"></a>Oturum açmalar ve gruplar
Veritabanı geçiş hizmeti 'ni çevrimdışı modda kullanarak SQL oturumlarını SQL Server kaynaktan Azure SQL veritabanı 'na taşıyın. Oturum açma işlemlerini hedef SQL veritabanınıza geçirmek için geçiş sihirbazındaki **Seçili oturumlar** bölmesini kullanın. 

Ayrıca, veritabanı geçiş hizmeti **yapılandırma** sayfasında karşılık gelen geçişi etkinleştirerek, veritabanı geçiş hizmeti aracılığıyla Windows kullanıcılarını ve gruplarını geçirebilirsiniz. 

Alternatif olarak, Microsoft veri geçiş mimarları tarafından özel olarak tasarlanan [PowerShell yardımcı programını](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) kullanabilirsiniz. Yardımcı programı, oturum açmaları yeniden oluşturmak ve kaynaktan hedefe veritabanı kullanıcıları seçmek için Transact-SQL (T-SQL) betiği oluşturmak üzere PowerShell kullanır. 

PowerShell yardımcı programı Windows Server Active Directory hesaplarını Azure Active Directory (Azure AD) hesaplarıyla otomatik olarak eşler ve kaynak Active Directory örneğine karşı her oturum açma için bir UPN araması yapabilir. Yardımcı program, rol üyeliğiyle ve Kullanıcı izinleriyle birlikte özel sunucu ve veritabanı rolleri komut dosyaları. Kapsanan veritabanları henüz desteklenmiyor ve yalnızca olası SQL Server izinlerinin bir alt kümesi betikleştirilmiş. 

### <a name="system-databases"></a>Sistem veritabanları
Azure SQL veritabanı için, yalnızca geçerli sistem veritabanları [ana](/sql/relational-databases/databases/master-database) ve tempdb ' dir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda tempdb](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="advanced-features"></a>Gelişmiş özellikler 

SQL veritabanı 'ndaki gelişmiş bulut tabanlı özelliklerden faydalandığınızdan emin olun. Örneğin, hizmet sizin için yaptığından yedeklemeleri yönetme konusunda endişelenmeniz gerekmez. [Bekletme dönemi içinde](../../database/recovery-using-backups.md#point-in-time-restore)herhangi bir noktaya geri yükleme yapabilirsiniz. 

Güvenliği güçlendirin, [Azure AD kimlik doğrulaması](../../database/authentication-aad-overview.md), [Denetim](../../database/auditing-overview.md), [tehdit algılama](../../database/azure-defender-for-sql.md), [satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security)ve [dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking)kullanmayı göz önünde bulundurun.

Gelişmiş Yönetim ve güvenlik özelliklerinin yanı sıra SQL veritabanı, [iş yükünüzü izlemenize ve ayarlamanıza](../../database/monitor-tune-overview.md)yardımcı olabilecek araçlar sağlar. [Azure SQL Analytics (Önizleme)](../../../azure-monitor/insights/azure-sql.md) , Azure SQL veritabanı 'ndaki tüm veritabanlarınızın performansını ölçek ve birden çok abonelik arasında tek bir görünümde izlemeye yönelik gelişmiş bir çözümdür. Azure SQL Analytics performans sorunlarını gidermek için yerleşik zeka ile önemli performans ölçümlerini toplayıp görselleştirir.

[Otomatik ayarlama](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   , SQL yürütme planınızın performansını sürekli olarak izler ve belirlenen performans sorunlarını otomatik olarak düzeltir. 


## <a name="migration-assets"></a>Geçiş varlıkları 

Daha fazla yardım için, gerçek dünyada geçiş projeleri için geliştirilen aşağıdaki kaynaklara bakın.

|Varlık  |Description  |
|---------|---------|
|[Veri iş yükü değerlendirmesi modeli ve aracı](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Bu araç, önerilen "en uygun" hedef platformları, bulut hazırlığı ve bir iş yükü için bir uygulama/veritabanı düzeltme düzeyi sağlar. Hedef platformlar için otomatik ve Tekdüzen karar verme süreci sağlayarak büyük Emlak değerlendirmelerini hızlandırmaya yardımcı olan basit, tek tıklamayla hesaplama ve rapor oluşturma olanakları sunar.|
|[DBLoader yardımcı programı](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Sınırsız metin dosyalarından verileri SQL Server içine yüklemek için DBLoader kullanabilirsiniz. Bu Windows konsol yardımcı programı, SQL Server Native Client toplu yükleme arabirimini kullanır. Arabirim, Azure SQL veritabanı ile birlikte SQL Server tüm sürümlerinde çalışmaktadır.|
|[PowerShell ile toplu veritabanı oluşturma](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Bir kaynak grubu (create_rg.ps1), [Azure 'daki mantıksal sunucu](../../database/logical-servers.md) (create_sqlserver.ps1) ve bir SQL veritabanı (create_sqldb.ps1) oluşturan üç PowerShell komut dosyasını kullanabilirsiniz. Betikler, gereken sayıda sunucu ve veritabanı oluşturmak için döngü özelliklerini içerir.|
|[MSSQL-Scripter ve PowerShell ile toplu şema dağıtımı](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Bu varlık bir kaynak grubu oluşturur, Azure SQL veritabanı 'nı barındırmak için Azure 'da bir veya birden çok [mantıksal sunucu](../../database/logical-servers.md) oluşturur, her şemayı şirket içi SQL Server örneğinden (veya birden çok SQL Server 2005 + örneğinden) dışa aktarır ve ŞEMALARı Azure SQL veritabanına aktarır.|
|[SQL Server Agent işleri elastik veritabanı işlerine dönüştürme](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Bu betik, kaynak SQL Server Agent işlerinizi elastik veritabanı işlerine geçirir.|
|[Azure SQL veritabanından e-posta gönder](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Bu çözüm, SendMail özelliğine bir alternatiftir ve şirket içi SQL Server için kullanılabilir. Azure SQL veritabanından e-posta göndermek için Azure Işlevleri ve SendGrid hizmetini kullanır.|
|[Şirket içi SQL Server oturumlarını Azure SQL veritabanı 'na taşımaya yardımcı program](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|PowerShell betiği, oturum açma işlemlerini yeniden oluşturmak ve şirket içi SQL Server Azure SQL veritabanı 'na veritabanı kullanıcılarını seçmek için bir T-SQL komut betiği oluşturabilir. Bu araç, Windows Server Active Directory hesaplarının Azure AD hesapları ile otomatik olarak eşlenmesini sağlar ve isteğe bağlı olarak SQL Server yerel oturumları geçirmeye olanak tanır.|
|[Logman kullanarak PerfMon veri toplama Otomasyonu](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Verileri (temel performansı anlamanıza yardımcı olmak için) ve geçiş hedefi önerilerini toplamak için, Logman aracını kullanabilirsiniz. Bu araç, uzak bir SQL Server örneğinde ayarlanan performans sayaçlarını oluşturacak, başlatacak, durduracak ve silecektir komutunu oluşturmak için logman.exe kullanır.|
|[BACPAC kullanarak Azure SQL veritabanına veritabanı geçişi](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Bu Teknik İnceleme, BACPAC dosyalarını kullanarak SQL Server ile Azure SQL veritabanı arasında geçişleri hızlandırmaya yardımcı olmak için rehberlik ve adımlar sağlar.|

Veri SQL Mühendisliği ekibi bu kaynakları geliştirdik. Bu takımın temel kurucu, veri platformu geçiş projelerini Microsoft 'un Azure veri platformu 'na yönelik karmaşık modernleştirmeyi engellemeyi ve hızlandırmanızı sağlar.


## <a name="next-steps"></a>Sonraki adımlar

- SQL Server veritabanlarınızı Azure SQL veritabanı 'na geçirmeye başlamak için [SQL Server Azure SQL veritabanı geçiş kılavuzu](sql-server-to-sql-database-guide.md)' na bakın.

- Veritabanı ve veri geçişi senaryolarında ve özel görevlerin yanı sıra size yardımcı olabilecek bir dizi hizmet ve araç için bkz. [veri geçişi Için hizmetler ve araçlar](../../../dms/dms-tools-matrix.md).

- SQL veritabanı hakkında daha fazla bilgi edinmek için bkz.:
   - [Azure SQL veritabanı 'na genel bakış](../../database/sql-database-paas-overview.md)
   - [Azure toplam sahiplik Hesaplayıcı maliyeti](https://azure.microsoft.com/pricing/tco/calculator/) 

- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.:
   -  [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure'a geçirilen iş yüklerini maliyetlendirme ve boyutlandırma için en iyi yöntemler](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Uygulama erişim katmanını değerlendirmek için bkz. [veri erişimi geçiş araç seti (Önizleme)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Veri erişim katmanı için A/B testi gerçekleştirme hakkında ayrıntılı bilgi için bkz. [veritabanı yükseltme deneyimi Yardımcısı](/sql/dea/database-experimentation-assistant-overview).
