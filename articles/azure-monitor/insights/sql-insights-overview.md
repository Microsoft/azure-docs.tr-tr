---
title: SQL Insights ile SQL dağıtımlarınızı izleme (Önizleme)
description: Azure Izleyici 'de SQL öngörülerine genel bakış
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d0bb5c55d3f7ba0573dfe9b511f4d31dcc64ed85
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567840"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>SQL Insights ile SQL dağıtımlarınızı izleme (Önizleme)
SQL Insights, [Azure SQL ailesinden](../../azure-sql/index.yml)herhangi bir ürünü izlemeye yönelik kapsamlı bir çözümdür. SQL Insights, sistem durumunu izlemek, sorunları tanılamak ve performansı ayarlamak için ihtiyaç duyduğunuz verileri göstermek için [dinamik yönetim görünümlerini](../../azure-sql/database/monitoring-with-dmvs.md) kullanır.  

SQL Insights, tüm izlemeyi uzaktan gerçekleştirir. Adanmış sanal makinelerdeki izleme aracıları SQL kaynaklarınıza bağlanır ve uzaktan veri toplar. Toplanan veriler [Azure Izleyici günlüklerinde](../logs/data-platform-logs.md)depolanır ve kolay toplama, filtreleme ve eğilim analizi etkinleştirir. Toplanan verileri SQL Insights [çalışma kitabı şablonundan](../visualize/workbooks-overview.md)görüntüleyebilir veya [günlük sorgularını](../logs/get-started-queries.md)kullanarak doğrudan verilere Delve yapabilirsiniz.

## <a name="pricing"></a>Fiyatlandırma
SQL Insights için doğrudan maliyet yoktur. Tüm maliyetler, verileri toplayan sanal makineler, verileri depolayan Log Analytics çalışma alanları ve verilerde yapılandırılan tüm uyarı kuralları tarafından gerçekleştirilir. 

**Sanal makineler**

Sanal makineler için, [sanal makineler fiyatlandırma sayfasında](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/)yayınlanan fiyatlandırmaya göre ücretlendirilirsiniz. Gerekli sanal makine sayısı, izlemek istediğiniz bağlantı dizeleri sayısına göre değişir. Her 100 bağlantı dizesi için 1 boyutlu Standard_B2s sanal makine ayırmanız önerilir. Daha fazla ayrıntı için bkz. [Azure sanal makine gereksinimleri](sql-insights-enable.md#azure-virtual-machine-requirements) .

**Log Analytics çalışma alanları**

Log Analytics çalışma alanları için, [Azure izleyici fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/monitor/)yayınlanan fiyatlandırmaya göre ücretlendirilirsiniz. SQL Insights tarafından kullanılan Log Analytics çalışma alanları, veri alma, veri saklama ve (isteğe bağlı olarak) veri dışa aktarma maliyetlerine tabi olacaktır. Tam ücretler, alınan, tutulan ve verilecek veri miktarına göre değişir. Daha sonra bu verilerin miktarı, veritabanı etkinliğinizdeki ve [izleme profilleriniz](sql-insights-enable.md#create-sql-monitoring-profile)tarafından tanımlanan koleksiyon ayarları temelinde farklılık gösterecektir.

**Uyarı kuralları**

Azure Izleyici 'deki uyarı kuralları için, [Azure izleyici fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/monitor/)yayınlanan fiyatlandırmaya göre ücretlendirilirsiniz. [SQL Insights ile uyarı oluşturmayı](sql-insights-alerts.md)seçerseniz, oluşturulan tüm uyarı kuralları ve gönderilen bildirimler için ücret ödersiniz.

## <a name="supported-versions"></a>Desteklenen sürümler 
SQL Insights, SQL Server aşağıdaki sürümlerini destekler:
- SQL Server 2012 ve üzeri

SQL Insights, aşağıdaki ortamlarda çalışan SQL Server destekler:
- Azure SQL Veritabanı
- Azure SQL Yönetilen Örnek
- Azure sanal makinelerinde SQL Server ( [SQL sanal makine](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) sağlayıcısına kayıtlı sanal makinelerde çalışan SQL Server)
- Azure VM 'Leri ( [SQL sanal makine](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) sağlayıcısına kayıtlı olmayan sanal makinelerde çalışan SQL Server)

SQL Insights, aşağıdakiler için destek veya sınırlı destek içermez:
- **Azure dışı örnekler**: Azure dışındaki sanal makinelerde çalışan SQL Server desteklenmez
- **Azure SQL veritabanı elastik havuzlar**: elastik havuzlar için ölçümler toplanamaz. Elastik havuzların içindeki veritabanları için ölçümler toplanamaz.
- **Azure SQL veritabanı düşük hizmet katmanları**: temel, S0, S1 ve S2 [hizmeti katmanlarında](../../azure-sql/database/resource-limits-dtu-single-databases.md) veritabanları için ölçümler toplanamaz
- **Azure SQL veritabanı sunucusuz katmanı**: sunucusuz işlem katmanını kullanan veritabanları için ölçümler toplanmaktadır. Ancak, ölçüm toplama işlemi otomatik duraklatma gecikmesi zamanlayıcısını sıfırlar ve veritabanının otomatik duraklatılmış duruma girmesini önler
- **İkincil çoğaltmalar**: ölçümler yalnızca, veritabanı başına tek bir ikincil çoğaltma için toplanabilir. Bir veritabanında 1 ' den fazla ikincil çoğaltma varsa, yalnızca 1 izlenebilir.
- **Azure Active Directory Ile kimlik doğrulaması**: izleme için desteklenen tek [KIMLIK doğrulama](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) yöntemi SQL kimlik doğrulamadır. Azure VM 'de SQL Server için, özel bir etki alanı denetleyicisindeki Active Directory kullanarak kimlik doğrulaması desteklenmez.  

## <a name="open-sql-insights"></a>SQL Insights 'ı aç
Azure portal **Azure izleyici** menüsünün **Öngörüler** bölümünde **SQL (Önizleme)** öğesini seçerek SQL Insights 'ı açın. İzlemekte olduğunuz SQL türüne yönelik deneyimi yüklemek için bir kutucuğa tıklayın.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Azure portal 'de SQL Insights.":::

## <a name="enable-sql-insights"></a>SQL Insights 'ı etkinleştir 
Bkz. SQL Insights 'ı etkinleştirme yönergeleri için [SQL Insights 'ı etkinleştirme](sql-insights-enable.md) .

## <a name="troubleshoot-sql-insights"></a>SQL Insights sorunlarını giderme 
Bkz. SQL Insights sorunlarını giderme yönergeleri için [SQL Insights sorunlarını giderme](sql-insights-troubleshoot.md) .

## <a name="data-collected-by-sql-insights"></a>SQL Insights tarafından toplanan veriler
SQL Insights, tüm izlemeyi uzaktan gerçekleştirir. SQL Server çalıştıran sanal makinelere hiçbir aracı yüklemeiyoruz. 

SQL Insights, SQL kaynaklarınızdan verileri uzaktan toplamak için adanmış izleme sanal makineleri kullanır. Her izleme sanal makinesinde [Azure izleyici Aracısı](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) ve iş yükü öngörüleri (wli) uzantısı yüklenir. WLI uzantısı açık kaynak [telegraf aracısını](https://www.influxdata.com/time-series-platform/telegraf/)içerir. SQL Insights, telegraf 'in [SQL Server eklentisi](https://www.influxdata.com/integration/microsoft-sql-server/)için veri toplama ayarlarını belirtmek üzere [veri toplama kurallarını](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) kullanır.

Azure SQL veritabanı, Azure SQL yönetilen örneği ve SQL Server için farklı veri kümeleri kullanılabilir. Aşağıdaki tablolar kullanılabilir verileri anlatmaktadır. Hangi veri kümelerinin toplanacağını ve [bir izleme profili oluştururken](sql-insights-enable.md#create-sql-monitoring-profile)koleksiyonun sıklığını özelleştirebilirsiniz.

Aşağıdaki tablolar aşağıdaki sütunlara sahiptir:
- **Kolay ad**: bir izleme profili oluştururken Azure Portal gösterildiği gibi sorgunun adı
- **Yapılandırma adı**: bir izleme profili düzenlenirken Azure Portal gösterildiği gibi sorgunun adı
- **Ad alanı**: Log Analytics çalışma alanında bulunan sorgunun adı. Bu tanımlayıcı, sütunundaki özellikte bulunan **ınsighstölçümlerini** tablosunda görüntülenir `Namespace` `Tags`
- **DMVs**: veri kümesini oluşturmak için kullanılan dinamik yönetilen görünümler
- **Varsayılan olarak etkin**: verilerin varsayılan olarak toplanıp toplanmayacağı
- **Varsayılan toplama sıklığı**: varsayılan olarak verilerin ne sıklıkta toplandığını

### <a name="data-for-azure-sql-database"></a>Azure SQL veritabanı için veriler 
| Kolay ad | Yapılandırma adı | Ad Alanı | DMV'ler | Varsayılan olarak etkin | Varsayılan toplama sıklığı |
|:---|:---|:---|:---|:---|:---|
| DB bekleme istatistikleri | Azuresddbwaitstats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | No | NA |
| DBO bekleme istatistikleri | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Yes | 60 saniye |
| Bellek yazıcıları | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 saniye |
| Veritabanı GÇ | Azuressqldbdatabaseıo | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | Yes | 60 saniye |
| Sunucu özellikleri | Azuresddbserverproperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>dosyasında. erişebileceğiniz<br>dosyasında. [database_service_objectives] | Yes | 60 saniye |
| Performans sayaçları | Azuresddbperformancecounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Yes | 60 saniye |
| Kaynak istatistikleri | Azuresddbresourcestats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Yes | 60 saniye |
| Kaynak idaresi | Azuressqldbresourceidare | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Yes | 60 saniye |
| İstekler | Azuresddbrequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Zamanlayıcılar| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA  |

### <a name="data-for-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği için veriler 

| Kolay ad | Yapılandırma adı | Ad Alanı | DMV'ler | Varsayılan olarak etkin | Varsayılan toplama sıklığı |
|:---|:---|:---|:---|:---|:---|
| Bekleme istatistikleri | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 saniye |
| Bellek yazıcıları | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 saniye |
| Veritabanı GÇ | Azuressqlmıdatabaseıo | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 saniye |
| Sunucu özellikleri | Azuressqlmiserverproperties | sqlserver_server_properties | sys.server_resource_stats | Yes | 60 saniye |
| Performans sayaçları | Azuressqlmıperformancecounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Yes | 60 saniye |
| Kaynak istatistikleri | Azuressqlmıresourcestats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Yes | 60 saniye |
| Kaynak idaresi | Azuressqlmıresourceidare | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Yes | 60 saniye |
| İstekler | Azuressqlmırequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Zamanlayıcılar | Azuressqlmischedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA |

### <a name="data-for-sql-server"></a>SQL Server için veriler

| Kolay ad | Yapılandırma adı | Ad Alanı | DMV'ler | Varsayılan olarak etkin | Varsayılan toplama sıklığı |
|:---|:---|:---|:---|:---|:---|
| Bekleme istatistikleri | Sqlserverwaitstatskategorize | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 saniye | 
| Bellek yazıcıları | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 saniye |
| Veritabanı GÇ | Sqlserverdatabaseıo | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 saniye |
| Sunucu özellikleri | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Yes | 60 saniye |
| Performans sayaçları | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Yes | 60 saniye |
| Birim alanı | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Yes | 60 saniye |
| SQL Server CPU | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Yes | 60 saniye |
| Zamanlayıcılar | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA |
| İstekler | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Kullanılabilirlik çoğaltması durumları | Sqlserverkullanılabilirliği Bilityreplicastates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | No | 60 saniye |
| Kullanılabilirlik veritabanı çoğaltmaları | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | No | 60 saniye |

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. SQL Insights 'ı etkinleştirme yönergeleri için [SQL Insights](sql-insights-enable.md) 'ı etkinleştirme
- SQL Insights hakkında sık sorulan sorular için bkz. [sık sorulan sorular](../faq.md#sql-insights-preview)
