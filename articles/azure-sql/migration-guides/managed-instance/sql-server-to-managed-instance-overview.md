---
title: "SQL yönetilen örneği 'ne SQL Server: geçişe genel bakış"
description: SQL Server veritabanlarınızı Azure SQL yönetilen örneği 'ne geçirebileceğiniz araçlar ve seçenekler hakkında bilgi edinin.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: 0a3fd1b492d19e241d89cc5477891c7c836e4640
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078987"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Geçişe genel bakış: Azure SQL yönetilen örneği 'ne SQL Server
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

SQL Server veritabanlarınızı Azure SQL yönetilen örneği 'ne geçirmeye yönelik seçenekler ve konular hakkında bilgi edinin. 

Şirket içinde veya üzerinde çalışan SQL Server veritabanlarını geçirebilirsiniz: 

- Azure sanal makineler 'de SQL Server.  
- Amazon Web Services (AWS) elastik Işlem bulutu (EC2). 
- AWS Ilişkisel veritabanı hizmeti (RDS). 
- Google Cloud Platform (GCP) içinde işlem altyapısı.  
- GCP 'de SQL Server için bulut SQL. 

Diğer geçiş kılavuzlarında, bkz. [Veritabanı geçişi](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Genel Bakış

[Azure SQL yönetilen örneği](../../managed-instance/sql-managed-instance-paas-overview.md) , sanal makineleri veya işletim sistemlerini yönetmek zorunda kalmadan tam olarak yönetilen bir hizmet gerektiren SQL Server iş yükleri için önerilen bir hedef seçenektir. SQL yönetilen örneği, en az uygulama veya veritabanı değişiklikleriyle şirket içi uygulamalarınızı Azure 'a taşımanızı sağlar. Yerel sanal ağ desteğiyle örneklerinizin tamamen yalıtımı sağlar. 

## <a name="considerations"></a>Dikkat edilmesi gerekenler 

Geçiş seçeneklerini değerlendirirken göz önünde bulundurmanız gereken anahtar faktörleri şunlardır: 
- Sunucu ve veritabanı sayısı
- Veritabanlarının boyutu
- Geçiş işlemi sırasında kabul edilebilir çalışma süresi 

SQL Server veritabanlarınızı SQL yönetilen örneği 'ne geçirmenin önemli avantajlarından biri, örneğin tamamını veya tek veritabanlarının yalnızca bir alt kümesini geçirmeyi tercih edebilirsiniz. Geçiş sürecinize aşağıdakileri eklemeyi dikkatle planlayın: 
- Aynı örneğe eklenmesi gereken tüm veritabanları 
- Uygulamanız için gereken örnek düzeyi nesneler, oturum açmalar, kimlik bilgileri, SQL Agent işleri ve işleçleri ve sunucu düzeyi Tetikleyiciler 

> [!NOTE]
> Azure SQL yönetilen örneği, kritik senaryolarda bile yüzde 99,99 kullanılabilirliği garanti eder. SQL yönetilen örneğindeki bazı özelliklerden kaynaklanan ek yük devre dışı bırakılamaz. Daha fazla bilgi için, [SQL yönetilen örneği ve SQL Server blog girdisi arasındaki performans farklılıklarının temel nedenleri](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) bölümüne bakın. 


## <a name="choose-an-appropriate-target"></a>Uygun bir hedef seçin

Aşağıdaki genel yönergeler, [performans temeinizle](sql-server-to-managed-instance-performance-baseline.md)eşleşecek yardımcı olması Için SQL yönetilen örneği 'nin doğru hizmet katmanını ve özelliklerini seçmenize yardımcı olabilir: 

- SQL Server örneğinizin kullandığı çekirdek sayısıyla eşleşen bir yönetilen örnek sağlamak için CPU kullanımı temelini kullanın. [Donanım oluşturma özellikleriyle](../../managed-instance/resource-limits.md#hardware-generation-characteristics)eşleşecek şekilde kaynakları ölçeklendirmek gerekebilir. 
- Bellek ayırmadan uygun bir şekilde eşleşen bir [Vcore seçeneği](../../managed-instance/resource-limits.md#service-tier-characteristics) seçmek için bellek kullanımı temelini kullanın. 
- Dosya alt sisteminin ana hat g/ç gecikmesini kullanarak Genel Amaçlı (5 MS 'den fazla gecikme) ve İş Açısından Kritik (3 MS 'den az gecikme) hizmet katmanlarından birini seçin. 
- Beklenen g/ç performansını elde etmek için, verilerin ve günlük dosyalarının boyutunu önceden ayırmak üzere temel aktarım hızını kullanın. 

Dağıtım sırasında işlem ve depolama kaynakları ' nı seçebilir ve ardından [Azure Portal kullanarak](../../database/scale-resources.md), uygulamanız için kapalı kalma süresi olmadan bunları daha sonra değiştirebilirsiniz. 

> [!IMPORTANT]
> [Yönetilen örnekler için sanal ağ gereksinimlerindeki](../../managed-instance/connectivity-architecture-overview.md#network-requirements) herhangi bir tutarsızlık, yeni örnekler oluşturmanızı veya var olanları kullanmanızı engelleyebilir.  [Yeni oluşturma](../../managed-instance/virtual-network-subnet-create-arm-template.md)   ve [Mevcut ağları yapılandırma](../../managed-instance/vnet-existing-add-subnet.md)hakkında daha fazla bilgi edinin   . 

Azure SQL yönetilen örneği 'nde (Genel Amaçlı ve İş Açısından Kritik) hedef hizmet katmanının seçiminde başka bir anahtar, yalnızca İş Açısından Kritik katmanında bulunan In-Memory OLTP gibi belirli özelliklerin kullanılabilirliğinden oluşur. 

### <a name="sql-server-vm-alternative"></a>SQL Server VM alternatifi

İşletmeniz Azure [sanal makinelerinde SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) Azure SQL yönetilen örneğinden daha uygun bir hedef haline getirmek için gerekli gereksinimlere sahip olabilir. 

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
|[Azure Veritabanı Geçiş Hizmeti](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Bu Azure hizmeti, geçiş işlemi sırasında kapalı kalma süresi sunan uygulamalar için çevrimdışı modda geçişi destekler. Çevrimiçi modda sürekli geçişin aksine, çevrimdışı mod geçişi kaynaktan hedefe tam bir veritabanı yedeklemesi için tek seferlik bir geri yükleme çalıştırır. | 
|[Yerel yedekleme ve geri yükleme](../../managed-instance/restore-sample-database-quickstart.md) | SQL yönetilen örneği, yerel SQL Server veritabanı yedeklerinin (. bak dosyaları) geri yüklenmesini destekler. Azure depolama 'ya tam veritabanı yedeklemeleri sağlayabilen müşteriler için en kolay geçiş seçeneğidir. Tam ve fark yedeklemeleri de desteklenir ve bu makalenin ilerleyen bölümlerinde [geçiş varlıkları hakkında bölümünde](#migration-assets) belgelenmiştir.| 
|[Günlük yeniden yürütme hizmeti](../../managed-instance/log-replay-service-migrate.md) | Bu bulut hizmeti, SQL Server günlük aktarma teknolojisine bağlı olarak SQL yönetilen örneği için etkinleştirilmiştir. Azure depolama 'ya tam, değişiklik ve günlük veritabanı yedeklemeleri sağlayabilen müşteriler için bir geçiş seçeneğidir. Günlük yeniden yürütme hizmeti, Azure Blob depolama 'dan SQL yönetilen örneğine yedekleme dosyalarını geri yüklemek için kullanılır.| 
| | |

Aşağıdaki tabloda alternatif geçiş araçları listelenmektedir: 

|**Teknoloji** |**Açıklama**  |
|---------|---------|
|[İşlem çoğaltması](../../managed-instance/replication-transactional-overview.md) | İşlem tutarlılığını koruyarak bir yayımcı-abone türü geçiş seçeneği sunarak, kaynak SQL Server veritabanı tablolarından SQL yönetilen örneğine veri çoğaltın. | 
|[Toplu kopyalama](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [Toplu kopyalama programı (bcp) aracı](/sql/tools/bcp-utility) verileri bir SQL Server örneğinden bir veri dosyasına kopyalar. Verileri kaynağınızdan dışarı aktarmak ve veri dosyasını hedef SQL yönetilen örneğine aktarmak için aracını kullanın. </br></br> Verileri Azure SQL yönetilen örneği 'ne taşımaya yönelik yüksek hızlı toplu kopyalama işlemleri için [akıllı toplu kopyalama aracını](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) kullanarak paralel kopyalama görevlerinden yararlanarak aktarım hızını en üst düzeye çıkarabilirsiniz. | 
|[İçeri aktarma Verme Sihirbazı/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [Bacpac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) , bir veritabanının şemasını ve verilerini kapsülleyen. bacpac uzantılı bir Windows dosyasıdır. BACPAC 'yi hem bir SQL Server kaynaktaki verileri dışarı aktarmak hem de verileri Azure SQL yönetilen örneğine geri aktarmak için kullanabilirsiniz. |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)|  Azure Data Factory [kopyalama etkinliği](../../../data-factory/copy-activity-overview.md) , yerleşik bağlayıcılar ve bir [tümleştirme çalışma zamanı](../../../data-factory/concepts-integration-runtime.md)kullanarak VERILERI kaynak SQL Server veritabanlarından SQL yönetilen örneğine geçirir.</br> </br> Data Factory, verileri SQL Server kaynaklardan SQL yönetilen örneğine taşımak için çok çeşitli [bağlayıcıları](../../../data-factory/connector-overview.md) destekler. |

## <a name="compare-migration-options"></a>Geçiş seçeneklerini karşılaştırma

Geçiş seçeneklerini, iş gereksinimlerinize uygun yolu seçmek üzere karşılaştırın. 

Aşağıdaki tabloda, önerdiğimiz geçiş seçenekleri karşılaştırılmaktadır: 

|Geçiş seçeneği  |Kullanılması gereken durumlar  |Dikkat edilmesi gerekenler  |
|---------|---------|---------|
|[Azure Veritabanı Geçiş Hizmeti](../../../dms/tutorial-sql-server-to-managed-instance.md) | -Tek veritabanlarını veya birden çok veritabanını ölçeklendirmeye geçirin. </br> -Geçiş işlemi sırasında kapalı kalma süresine uyum sağlayabilir. </br> </br> Desteklenen kaynaklar: </br> -SQL Server (2005-2019) Şirket içi veya Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Işlem SQL Server VM |  -Ölçekte geçişler, [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)aracılığıyla otomatikleştirilebilir. </br> -Geçişin tamamlanma süresi, veritabanı boyutuna bağlıdır ve yedekleme ve geri yükleme zamanından etkilenir. </br> -Yeterli kapalı kalma süresi gerekli olabilir. |
|[Yerel yedekleme ve geri yükleme](../../managed-instance/restore-sample-database-quickstart.md) | -Bireysel iş kolu uygulama veritabanlarını geçirin.  </br> -Ayrı bir geçiş hizmeti veya aracı olmadan hızlı ve kolay geçiş.  </br> </br> Desteklenen kaynaklar: </br> -SQL Server (2005-2019) Şirket içi veya Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Işlem SQL Server VM | -Veritabanı yedeklemesi, Azure Blob depolama alanına veri aktarımını iyileştirmek için birden çok iş parçacığı kullanır, ancak iş ortağı bant genişliği ve veritabanı boyutu aktarım hızını etkileyebilir. </br> -Kapalı kalma süresi, tam yedekleme ve geri yükleme (veri boyutu) için gereken süreyi içermelidir.| 
|[Günlük yeniden yürütme hizmeti](../../managed-instance/log-replay-service-migrate.md) | -Bireysel iş kolu uygulama veritabanlarını geçirin.  </br> -Veritabanı geçişleri için daha fazla denetim gereklidir.  </br> </br> Desteklenen kaynaklar: </br> -SQL Server (2008-2019) Şirket içi veya Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Işlem SQL Server VM | -Geçiş, SQL Server üzerinde tam veritabanı yedeklemeleri yapmayı ve yedekleme dosyalarını Azure Blob depolamaya kopyalamayı gerektirir. Günlük yeniden yürütme hizmeti, Azure Blob depolama 'dan SQL yönetilen örneğine yedekleme dosyalarını geri yüklemek için kullanılır. </br> -Geçiş işlemi sırasında geri yüklenen veritabanları geri yükleme modunda olur ve işlem tamamlanana kadar okumak veya yazmak için kullanılamaz.| 
| | | |

Aşağıdaki tabloda alternatif geçiş seçenekleri karşılaştırılmaktadır: 

|Yöntem veya teknoloji |Kullanılması gereken durumlar |Dikkat edilmesi gerekenler  |
|---------|---------|---------|
|[İşlem çoğaltması](../../managed-instance/replication-transactional-overview.md) | -Değişiklikleri kaynak veritabanı tablolarından hedef SQL yönetilen örnek veritabanı tablolarına sürekli olarak yayımlayarak geçirin. </br> -Seçili tabloların (bir veritabanının alt kümesi) tam veya kısmi veritabanı geçişlerini yapın.  </br> </br> Desteklenen kaynaklar: </br> -SQL Server (2012-2019) bazı sınırlamalar </br> -AWS EC2  </br> -GCP Işlem SQL Server VM | </br> -Kurulum diğer geçiş seçeneklerine kıyasla görece karmaşıktır.   </br> -Verileri geçirmek için (veritabanlarını çevrimdışına almadan) sürekli bir çoğaltma seçeneği sağlar.</br> -İşlemsel çoğaltmanın, kaynak SQL Server örneğinde yayımcıyı ayarlarken göz önünde bulundurulması gereken sınırlamalar vardır. Daha fazla bilgi için bkz. [nesneleri yayımlamayla Ilgili sınırlamalar](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) .  </br> - [Çoğaltma etkinliğini izleme](/sql/relational-databases/replication/monitor/monitoring-replication) özelliği kullanılabilir.    |
|[Toplu kopyalama](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Tam veya kısmi veri geçişleri yapın. </br> -Kapalı kalma süresine uyum sağlayabilir. </br> </br> Desteklenen kaynaklar: </br> -SQL Server (2005-2019) Şirket içi veya Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Işlem SQL Server VM   | -Kaynaktan verileri dışarı aktarmak ve hedefe aktarmak için kapalı kalma süresi gerektirir. </br> -Dışarı aktarma veya içeri aktarma işleminde kullanılan dosya biçimlerinin ve veri türlerinin tablo şemaları ile tutarlı olması gerekir. |
|[İçeri aktarma Verme Sihirbazı/BACPAC](../../database/database-import.md)| -Bireysel iş kolu uygulama veritabanlarını geçirin. </br>-Daha küçük veritabanları için uygundur.  </br>  Ayrı bir geçiş hizmeti veya aracı gerektirmez. </br> </br> Desteklenen kaynaklar: </br> -SQL Server (2005-2019) Şirket içi veya Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Işlem SQL Server VM  |   </br> -Verilerin kaynakta aktarılması ve hedefte içeri aktarılması gerektiğinden kapalı kalma süresi gerektirir.   </br> -Dışarı aktarma veya içeri aktarma işleminde kullanılan dosya biçimlerinin ve veri türlerinin, kesme veya veri türü uyuşmazlığı hatalarından kaçınmak için tablo şemaları ile tutarlı olması gerekir. </br> -Çok sayıda nesne içeren bir veritabanını dışarı aktarmak için geçen süre önemli ölçüde daha yüksek olabilir. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| -Kaynak SQL Server veritabanlarından verileri geçirin ve/veya dönüştürün.</br> -Birden çok veri kaynağından Azure SQL yönetilen örneğine veri birleştirme, genellikle iş zekası (BI) iş yükleri için kullanılır.   </br> -Verileri kaynaktan hedefe taşımak için Data Factory veri taşıma işlem hatları oluşturulması gerekir.   </br> - [Maliyet](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) önemli bir konudur ve işlem hattı Tetikleyicileri, etkinlik çalıştırmaları ve veri hareketinin süresi gibi faktörlere dayanır. |
| | | |

## <a name="feature-interoperability"></a>Özellik birlikte çalışabilirliği 

Diğer SQL Server özelliklerine dayanan iş yüklerini geçirirken daha fazla önemli noktalar vardır. 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

SSSıSDB 'deki SQL Server Integration Services (SSIS) paketlerini ve projelerini Azure [veritabanı geçiş hizmeti](../../../dms/how-to-migrate-ssis-packages-managed-instance.md)'Ni kullanarak Azure SQL yönetilen örneği 'ne geçirin. 

Yalnızca SSSıSDB içindeki SSIS paketleri SQL Server 2012 ile başlayarak geçiş için desteklenir. Geçişten önce eski SSIS paketlerini dönüştürün. Daha fazla bilgi için bkz. [Proje dönüştürme öğreticisi](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) . 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

SQL Server Reporting Services (SSRS) raporlarını, Power BI sayfalandırılmış raporlara geçirebilirsiniz. Raporlarınızı hazırlama ve geçirmeye yardımcı olması için [rdl geçiş aracını](https://github.com/microsoft/RdlMigration) kullanın. Microsoft bu aracı, müşterilerin, SSRS sunucularından rapor tanım dili (RDL) raporlarını Power BI 'e geçirmeye yardımcı olmak için geliştirilmiştir. GitHub üzerinden sunulan bu araçla birlikte geçiş senaryosunun tüm aşamalarını kapsayan belgeler de sunulmaktadır. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server 2012 ve sonrasındaki SQL Server Analysis Services tablolu modeller, Azure 'daki Analysis Services tablolu model için bir hizmet olarak platform (PaaS) dağıtım modeli olan Azure Analysis Services 'e geçirilebilir. [Bu video öğreticisindeki](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/)Azure Analysis Services şirket içi modelleri geçirme hakkında daha fazla bilgi edinebilirsiniz.

Alternatif olarak, [yenı XMLA okuma/yazma uç noktalarını kullanarak](/power-bi/admin/service-premium-connect-tools)şirket içi Analysis Services tablolu modellerinizi Power BI Premium taşımaya de göz önünde bulundurun. 

### <a name="high-availability"></a>Yüksek kullanılabilirlik

Yüksek kullanılabilirlik özellikleri SQL Server her zaman yük devretme kümesi örnekleri ve her zaman açık kullanılabilirlik grupları, hedef SQL yönetilen örneği üzerinde artık kullanılmıyor olur. Yüksek kullanılabilirlik mimarisi SQL yönetilen örneği için [genel amaçlı (Standart kullanılabilirlik modeli)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) ve [iş açısından kritik (Premium kullanılabilirlik modeli)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) hizmet katmanlarında zaten yerleşik olarak bulunur. Premium kullanılabilirlik modeli Ayrıca, salt okuma amacıyla ikincil düğümlerden birine bağlanmayı sağlayan okuma ölçeğini de sağlar.     

SQL yönetilen örneği 'ne dahil olan yüksek kullanılabilirliğe sahip mimarinin ötesinde, [otomatik yük devretme grupları](../../database/auto-failover-group-overview.md) özelliği, yönetilen bir örnekteki veritabanlarının başka bir bölgeye çoğaltılmasını ve yük devretmesini yönetmenizi sağlar. 

### <a name="sql-agent-jobs"></a>SQL Aracısı işleri

[SQL Aracısı işlerini](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)geçirmek Için çevrimdışı Azure veritabanı geçiş hizmeti seçeneğini kullanın. Aksi takdirde, SQL Server Management Studio kullanarak Transact-SQL (T-SQL) içindeki işleri betikten sonra hedef SQL yönetilen örneği üzerinde el ile yeniden oluşturun. 

> [!IMPORTANT]
> Azure veritabanı geçiş hizmeti şu anda yalnızca T-SQL alt sistemi adımlarıyla işleri destekler. SSIS paket adımlarının bulunduğu işlerin el ile geçirilmesi gerekir. 

### <a name="logins-and-groups"></a>Oturum açmalar ve gruplar

Veritabanı geçiş hizmeti 'ni çevrimdışı modda kullanarak SQL oturumlarını SQL Server kaynaktan Azure SQL yönetilen örneği 'ne taşıyın.  Oturum açma işlemlerini hedef SQL yönetilen örneğinize geçirmek için Geçiş Sihirbazı 'nda [oturum açma bölmesini seçin](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) bölmesini kullanın. 

Varsayılan olarak, Azure veritabanı geçiş hizmeti yalnızca SQL oturumlarını geçirmeyi destekler. Bununla birlikte, Windows oturumlarının geçişini şu şekilde etkinleştirebilirsiniz:

- Hedef SQL yönetilen örneğinin Azure Active Directory (Azure AD) okuma erişimi olduğundan emin olma. Genel yönetici rolüne sahip bir Kullanıcı, bu erişimi Azure portal aracılığıyla yapılandırabilir.
- Windows Kullanıcı veya grup oturumu geçişlerini etkinleştirmek için Azure veritabanı geçiş hizmeti 'ni yapılandırma. Bunu, **yapılandırma** sayfasında Azure Portal aracılığıyla ayarlarsınız. Bu ayarı etkinleştirdikten sonra değişikliklerin etkili olması için hizmeti yeniden başlatın.

Hizmeti yeniden başlattıktan sonra, Windows Kullanıcı veya grup oturum açmalar geçiş için kullanılabilen oturum açma listesinde görünür. Geçiş yaptığınız herhangi bir Windows Kullanıcı veya grup oturumu açma için, ilişkili etki alanı adını girmeniz istenir. Hizmet Kullanıcı hesapları (etki alanı adı NT YETKILISI olan hesaplar) ve Sanal Kullanıcı hesapları (etki alanı adı NT HIZMETI olan hesaplar) desteklenmez. Daha fazla bilgi edinmek için bkz. [T-SQL kullanarak bir SQL Server örneğindeki Windows kullanıcılarını ve grupları Azure SQL yönetilen örneği 'ne geçirme](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Alternatif olarak, Microsoft veri geçiş mimarları tarafından özel olarak tasarlanan [PowerShell yardımcı programını](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) kullanabilirsiniz. Yardımcı programı, oturum açmaları yeniden oluşturmak ve kaynaktan hedefe veritabanı kullanıcıları seçmek için bir T-SQL betiği oluşturmak üzere PowerShell kullanır. 

PowerShell yardımcı programı Windows Server Active Directory hesaplarını otomatik olarak Azure AD hesaplarıyla eşleştirir ve kaynak Active Directory örneğinde her oturum açma için bir UPN araması yapabilir. Yardımcı program, rol üyeliğiyle ve Kullanıcı izinleriyle birlikte özel sunucu ve veritabanı rolleri komut dosyaları. Kapsanan veritabanları henüz desteklenmiyor ve yalnızca olası SQL Server izinlerinin bir alt kümesi betikleştirilmiş. 

### <a name="encryption"></a>Şifreleme

 [](../../database/transparent-data-encryption-tde-overview.md)   Yerel geri yükleme seçeneğini kullanarak saydam veri şifrelemesi tarafından korunan veritabanlarını yönetilen bir örneğe geçirdiğinizde, veritabanı geri *yüklemeden önce* [karşılık gelen sertifikayı](../../managed-instance/tde-certificate-migrate.md) kaynak SQL Server örneğinden hedef SQL yönetilen örneğine geçirin. 

### <a name="system-databases"></a>Sistem veritabanları

Sistem veritabanlarının geri yüklenmesi desteklenmiyor. Örnek düzeyi nesneleri (ana ve msdb veritabanlarında depolanır) geçirmek için T-SQL ' i kullanarak bunları, sonra da hedef yönetilen örnekte yeniden oluşturun. 

### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (bellek için iyileştirilmiş tablolar)

SQL Server, In-Memory OLTP yeteneği sağlar. Bellek için iyileştirilmiş tablolar, bellek için iyileştirilmiş tablo türleri ve yerel koda derlenmiş SQL modüllerinin, işlemsel işleme için yüksek aktarım hızı ve düşük gecikme süresine sahip iş yüklerini çalıştırmak için kullanılmasına olanak tanır. 

> [!IMPORTANT]
> In-Memory OLTP yalnızca Azure SQL yönetilen örneğindeki İş Açısından Kritik katmanında desteklenir. Genel Amaçlı katmanında desteklenmez.

Şirket içi SQL Server Örneğiniz için bellek için iyileştirilmiş tablolar veya bellek için iyileştirilmiş tablo türleri varsa ve Azure SQL yönetilen örneği 'ne geçiş yapmak istiyorsanız şunlardan birini yapmalısınız:

- In-Memory OLTP 'yi destekleyen hedef SQL yönetilen örneğiniz için İş Açısından Kritik katmanını seçin.
- Azure SQL yönetilen örneği 'nde Genel Amaçlı katmanına geçiş yapmak istiyorsanız, bellek için iyileştirilmiş tabloları, bellek için iyileştirilmiş tablo türlerini ve veritabanlarını geçirmeden önce bellek için iyileştirilmiş nesnelerle etkileşime geçen yerel koda derlenmiş SQL modüllerini kaldırın. Genel Amaçlı katmanına geçişten önce kaldırılması gereken tüm nesneleri tanımlamak için aşağıdaki T-SQL sorgusunu kullanabilirsiniz:

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

Bellek içi teknolojiler hakkında daha fazla bilgi edinmek için bkz. [Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde bellek içi teknolojileri kullanarak performansı iyileştirme](../../in-memory-oltp-overview.md).

## <a name="advanced-features"></a>Gelişmiş özellikler 

SQL yönetilen örneğindeki gelişmiş bulut tabanlı özelliklerden faydalandığınızdan emin olun. Örneğin, hizmet sizin için yaptığından yedeklemeleri yönetme konusunda endişelenmeniz gerekmez. [Bekletme dönemi içinde](../../database/recovery-using-backups.md#point-in-time-restore)herhangi bir noktaya geri yükleme yapabilirsiniz. Ayrıca, yüksek kullanılabilirliği ayarlama konusunda endişelenmeniz gerekmez, çünkü [yüksek kullanılabilirlik yerleşik olarak oluşturulmuştur](../../database/high-availability-sla.md). 

Güvenliği güçlendirin, [Azure AD kimlik doğrulaması](../../database/authentication-aad-overview.md), [Denetim](../../managed-instance/auditing-configure.md), [tehdit algılama](../../database/azure-defender-for-sql.md), [satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security)ve [dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking)kullanmayı göz önünde bulundurun.

Gelişmiş Yönetim ve güvenlik özelliklerine ek olarak, SQL yönetilen örneği, [iş yükünüzü izlemenize ve ayarlamanıza](../../database/monitor-tune-overview.md)yardımcı olabilecek gelişmiş araçlar sağlar. [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) , büyük bir yönetilen örnek kümesini merkezi bir şekilde izlemenize olanak sağlar.  [Otomatik ayarlama](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   yönetilen örneklerde, SQL planı yürütmesinin performansını sürekli olarak izler ve belirlenen performans sorunlarını otomatik olarak düzeltir. 

Bazı özellikler yalnızca [veritabanı uyumluluk düzeyi](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) en son uyumluluk düzeyine (150) değiştirildikten sonra kullanılabilir. 

## <a name="migration-assets"></a>Geçiş varlıkları 

Daha fazla yardım için, gerçek dünyada geçiş projeleri için geliştirilen aşağıdaki kaynaklara bakın.

|Varlık  |Description  |
|---------|---------|
|[Veri iş yükü değerlendirmesi modeli ve aracı](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Bu araç, önerilen "en uygun" hedef platformları, bulut hazırlığı ve bir iş yükü için bir uygulama/veritabanı düzeltme düzeyi sağlar. Hedef platformlar için otomatik ve Tekdüzen karar verme süreci sağlayarak büyük Emlak değerlendirmelerini hızlandırmaya yardımcı olan basit, tek tıklamayla hesaplama ve rapor oluşturma olanakları sunar.|
|[DBLoader yardımcı programı](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Sınırsız metin dosyalarından verileri SQL Server içine yüklemek için DBLoader kullanabilirsiniz. Bu Windows konsol yardımcı programı, SQL Server Native Client toplu yükleme arabirimini kullanır. Arabirim, Azure SQL yönetilen örneği ile birlikte SQL Server tüm sürümlerinde kullanılabilir.|
|[Şirket içi SQL Server oturumlarını Azure SQL yönetilen örneğine taşımaya yönelik yardımcı program](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Bir PowerShell betiği, oturum açma işlemlerini yeniden oluşturmak ve şirket içi SQL Server Azure SQL yönetilen örneği için veritabanı kullanıcılarını seçmek üzere bir T-SQL komut betiği oluşturabilir. Bu araç, Windows Server Active Directory hesaplarının Azure AD hesapları ile otomatik olarak eşlenmesini sağlar ve isteğe bağlı olarak SQL Server yerel oturumları geçirmeye olanak tanır.|
|[Logman kullanarak PerfMon veri toplama Otomasyonu](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Verileri (temel performansı anlamanıza yardımcı olmak için) ve geçiş hedefi önerilerini toplamak için, Logman aracını kullanabilirsiniz. Bu araç, uzak bir SQL Server örneğinde ayarlanan performans sayaçlarını oluşturacak, başlatacak, durduracak ve silecektir komutunu oluşturmak için logman.exe kullanır.|
|[Tam ve değişiklik yedeklemelerini geri yükleyerek Azure SQL yönetilen örneğine Veritabanı geçişi](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Bu Teknik İnceleme, yalnızca tam ve fark yedeklemeleriniz (ve günlük yedekleme yeteneği olmadan) varsa SQL Server Azure SQL yönetilen örneğine geçişleri hızlandırmaya yardımcı olmak için rehberlik ve adımlar sağlar.|

Veri SQL Mühendisliği ekibi bu kaynakları geliştirdik. Bu takımın temel kurucu, veri platformu geçiş projelerini Microsoft 'un Azure veri platformu 'na yönelik karmaşık modernleştirmeyi engellemeyi ve hızlandırmanızı sağlar.


## <a name="next-steps"></a>Sonraki adımlar

- SQL Server veritabanlarınızı Azure SQL yönetilen örneği 'ne geçirmeye başlamak için bkz. [Azure SQL yönetilen örnek geçiş kılavuzu SQL Server](sql-server-to-managed-instance-guide.md).

- Veritabanı ve veri geçişi senaryolarında ve özel görevlerin yanı sıra size yardımcı olabilecek bir dizi hizmet ve araç için bkz. [veri geçişi Için hizmetler ve araçlar](../../../dms/dms-tools-matrix.md).

- Azure SQL yönetilen örneği hakkında daha fazla bilgi edinmek için bkz.:
   - [Azure SQL yönetilen örneği 'nde hizmet katmanları](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server ile Azure SQL yönetilen örneği arasındaki farklar](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure toplam sahiplik Hesaplayıcı maliyeti](https://azure.microsoft.com/pricing/tco/calculator/) 

- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.:
   -  [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure'a geçirilen iş yüklerini maliyetlendirme ve boyutlandırma için en iyi yöntemler](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Uygulama erişim katmanını değerlendirmek için bkz. [veri erişimi geçiş araç seti (Önizleme)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Veri erişim katmanında A/B testi gerçekleştirme hakkında daha fazla bilgi için bkz. [veritabanı yükseltme deneyimi Yardımcısı](/sql/dea/database-experimentation-assistant-overview).
