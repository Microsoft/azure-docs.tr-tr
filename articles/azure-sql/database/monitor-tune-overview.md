---
title: İzleme ve performans ayarlama
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde izleme ve performans ayarlama özelliklerine ve metodolojiye genel bakış.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 03/17/2021
ms.openlocfilehash: 4c9edaea75215bc4b471cd2e0ac50c4e69546bb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592143"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Veritabanı ve Azure SQL Yönetilen Örneği'nde izleme ve performansı ayarlama
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde bir veritabanının performansını izlemek için, belirli bir hizmet katmanını ve performans düzeyini seçerken seçtiğiniz veritabanı performansı düzeyine göre iş yükünüz tarafından kullanılan CPU ve GÇ kaynaklarını izlemeye başlayın. Bunu gerçekleştirmek için Azure SQL veritabanı ve Azure SQL yönetilen örneği, Azure portal görüntülenebilecek veya şu SQL Server yönetim araçlarından birini kullanarak kaynak ölçümleri yayar: [Azure Data Studio](/sql/azure-data-studio/what-is) ya da [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Azure SQL veritabanı, performansı artırmak için akıllı performans ayarlama önerilerini ve otomatik ayarlama seçeneklerini sağlamak üzere çeşitli veritabanı danışmanları sağlar. Ayrıca, Sorgu Performansı İçgörüleri tek ve havuza alınmış veritabanları için en fazla CPU ve GÇ kullanımından sorumlu sorgular hakkındaki ayrıntıları gösterir.

Azure SQL veritabanı ve Azure SQL yönetilen örneği, sorun gidermede ve veritabanlarınızın ve çözümlerin performansını en üst düzeye çıkarmak için yapay zeka tarafından desteklenen gelişmiş izleme ve ayarlama özellikleri sağlar. Bu [akıllı içgörüler](intelligent-insights-overview.md) ve diğer veritabanı kaynak günlüklerinin ve ölçümlerinin [akış dışa aktarılmasını](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) , özellikle de [SQL Analytics](../../azure-monitor/insights/azure-sql.md)'i kullanarak, tüketim ve analiz için birkaç hedefden birine yapılandırmayı seçebilirsiniz. Azure SQL Analytics, tüm veritabanlarınızın performansını ölçek ve birden çok abonelik arasında tek bir görünümde izlemek için gelişmiş bir bulut izleme çözümüdür. Dışarı aktarmak istediğiniz günlüklerin ve ölçümlerin bir listesi için bkz. [dışarı aktarma için tanılama telemetrisi](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

SQL Server, SQL veritabanı ve SQL yönetilen örneğinin, [sorgu deposu](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) ve [dinamik yönetim görünümleri (DMVs)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)gibi faydalanma konusunda kendi izleme ve tanılama özelliklerine sahiptir. Çeşitli performans sorunlarını izlemek için bkz. komut dosyaları için [DMVs kullanarak izleme](monitoring-with-dmvs.md) .

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure portal özellikleri izleme ve ayarlama

Azure portal, Azure SQL veritabanı ve Azure SQL yönetilen örneği, kaynak ölçümlerinin izlenmesini sağlar. Azure SQL veritabanı, veritabanı danışmanları sağlar ve Sorgu Performansı İçgörüleri sorgu ayarlama önerilerini ve sorgu performans analizini sağlar. Azure portal, [MANTıKSAL SQL sunucuları](logical-servers.md) ve bunların tek ve havuza alınmış veritabanları için otomatik ayarlamayı etkinleştirebilirsiniz.

> [!NOTE]
> Son derece düşük kullanımı olan veritabanları portalda gerçek kullanımdan daha az kullanım gösterebilir. Bir Double değeri en yakın tamsayıya dönüştürürken telemetri Yayınlanma yöntemi nedeniyle 0,5 'den daha az sayıda kullanım miktarları, yayılan Telemetriyi ayrıntı düzeyinde bir kaybına neden olan 0 ' a yuvarlanır. Ayrıntılar için bkz. [düşük veritabanı ve elastik havuz ölçümleri sıfıra yuvarlama](#low-database-and-elastic-pool-metrics-rounding-to-zero).

### <a name="monitor-with-sql-insights"></a>SQL Insights ile izleme

Azure [IZLEYICI SQL öngörüleri](../../azure-monitor/insights/sql-insights-overview.md) , Azure SQL VM 'lerinde Azure SQL yönetilen örnekleri, Azure SQL veritabanları ve SQL Server örneklerini izlemeye yönelik bir araçtır. Bu hizmet, dinamik yönetim görünümlerindeki (DMVs) verileri yakalamak ve verileri Azure Log Analytics yönlendirdiğinde, burada izlenebileceğiniz ve çözümlenebileceği bir uzak Aracı kullanır. Bu verileri, [Azure izleyici](../../azure-monitor/overview.md) 'den belirtilen görünümlerde görüntüleyebilir veya sorguları çalıştırmak ve eğilimleri çözümlemek için günlük verilerine doğrudan erişebilirsiniz. Azure Izleyici SQL öngörülerini kullanmaya başlamak için bkz. [SQL Insights 'ı etkinleştirme](../../azure-monitor/insights/sql-insights-enable.md).

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Azure SQL veritabanı ve Azure SQL yönetilen örnek kaynak izleme

Çeşitli kaynak ölçümlerini, **ölçüm** görünümündeki Azure Portal hızlı bir şekilde izleyebilirsiniz. Bu ölçümler, bir veritabanının işlemci, bellek veya GÇ kaynaklarının %100 ' ine ulaşmasını görmenizi sağlar. Yüksek DTU veya işlemci yüzdesi ve yüksek GÇ yüzdesi, iş yükünüzün daha fazla CPU veya GÇ kaynağına ihtiyacı olabileceğini gösterir. Ayrıca, iyileştirilmesi gereken sorguları da belirtebilir.

  ![Kaynak ölçümleri](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Azure SQL veritabanı 'nda veritabanı danışmanları

Azure SQL veritabanı, tek ve havuza alınmış veritabanları için performans ayarlama önerilerini sağlayan [veritabanı danışmanlarını](database-advisor-implement-performance-recommendations.md) içerir. Bu öneriler, Azure portal ve [PowerShell](/powershell/module/az.sql/get-azsqldatabaseadvisor)kullanılarak kullanılabilir. Ayrıca, [otomatik ayarlamayı](automatic-tuning-overview.md) ETKINLEŞTIREREK Azure SQL veritabanı 'nın bu ayarlama önerilerini otomatik olarak uygulayabilmesini sağlayabilirsiniz.

### <a name="query-performance-insight-in-azure-sql-database"></a>Azure SQL veritabanı 'nda Sorgu Performansı İçgörüleri

[Sorgu performansı içgörüleri](query-performance-insight-use.md) , tek ve havuza alınmış veritabanları için en çok kullanılan ve en uzun çalışan sorguların Azure Portal performansını gösterir.

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>Düşük veritabanı ve elastik havuz ölçümleri sıfıra yuvarlama

2020 Eylül 'den başlayarak, son derece düşük kullanımı olan veritabanları portalda gerçek kullanımdan daha az kullanım gösterebilir. Bir Double değeri en yakın tamsayıya dönüştürürken Telemetriyi bir katına çıkardığından, 0,5 'den daha az belirli kullanım miktarları 0 ' a yuvarlanır ve bu da yayılan Telemetriyi çok parçalı hale getirmenin bir kaybına neden olur.

Örneğin: şu dört veri noktasıyla 1 dakikalık bir pencere düşünün: 0,1, 0,1, 0,1, 0,1, bu düşük değerler 0, 0, 0, 0, 0, 0 ve ortalama 0 değerini gösterir. Veri noktalarından herhangi biri 0,5 ' den büyükse, örneğin: 0,1, 0,1, 0,9, 0,1, 0, 0, 1, 0 ' a yuvarlanır ve ortalama bir 0,25 gösterir.

Etkilenen veritabanı ölçümleri:
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

Etkilenen elastik havuz ölçümleri:
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>Performans sorunlarının akıllı değerlendirmelerini oluştur

Azure SQL veritabanı ve Azure SQL yönetilen örneği için [akıllı içgörüler](intelligent-insights-overview.md) , yapay zeka aracılığıyla veritabanı kullanımını sürekli olarak izlemek ve düşük performansa neden olan kötü etkinlikleri saptamak için yerleşik zeka kullanır. Akıllı İçgörüler sorgu yürütme bekleme süreleri, hatalar veya zaman aşımları temelinde veritabanları ile ilgili performans sorunlarını otomatik olarak algılar. Algılandıktan sonra, [sorunların akıllı değerlendirmesi](intelligent-insights-troubleshoot-performance.md)ile bir kaynak günlüğü (Sqlinsıghts olarak adlandırılır) oluşturan ayrıntılı bir analiz yapılır. Bu değerlendirme, veritabanı performans sorununun bir kök neden analizinden oluşur ve mümkün olduğunda performans iyileştirmeleri için öneriler içerir.

Akıllı İçgörüler, Azure yerleşik zekanın aşağıdaki değeri sağlayan benzersiz bir özelliğidir:

- Öngörülebilir izleme
- Özel performans öngörüleri
- Veritabanı performansı performansında erken algılama
- Algılanan sorunların kök neden analizi
- Performans geliştirme önerileri
- Yüzlerce binlerce veritabanında ölçeği genişletme özelliği
- DevOps kaynaklarına yönelik olumlu etki ve toplam sahip olma maliyeti

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Ölçüm ve kaynak günlüklerinin akış dışa aktarılmasını etkinleştirme

Akıllı İçgörüler kaynak günlüğü de dahil olmak üzere, [Tanılama telemetrinin akış dışa aktarılmasını](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) , birkaç hedefe göre etkinleştirebilir ve yapılandırabilirsiniz. Performans sorunlarını belirlemek ve çözmek üzere bu ek tanılama telemetrisini kullanmak için [SQL Analytics](../../azure-monitor/insights/azure-sql.md) ve diğer özellikleri kullanın.

Tanılama ayarlarını, tek veritabanları, havuza alınmış veritabanları, elastik havuzlar, yönetilen örnekler ve örnek veritabanları için aşağıdaki Azure kaynaklarından birine yönelik ölçüm kategorilerini ve kaynak günlüklerini akışa almak üzere yapılandırırsınız.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure Izleyici 'de Log Analytics çalışma alanı

Ölçümleri ve kaynak günlüklerini [Azure izleyici 'de bir Log Analytics çalışma alanına](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)akışını sağlayabilirsiniz. Burada akan veriler, performans raporları, uyarılar ve risk azaltma önerilerini içeren veritabanlarınızı akıllı olarak izlemeye yönelik yalnızca bir bulut izleme çözümü olan [SQL Analytics](../../azure-monitor/insights/azure-sql.md)tarafından tüketilebilir. Bir Log Analytics çalışma alanına akan veriler, toplanan diğer izleme verileriyle analiz edilebilir ve ayrıca uyarılar ve görselleştirmeler gibi diğer Azure Izleyici özelliklerinden yararlanmanızı sağlar.

### <a name="azure-event-hubs"></a>Azure Event Hubs

[Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)ölçümleri ve kaynak günlüklerini akışla aktarabilirsiniz. Aşağıdaki işlevleri sağlamak için Olay Hub 'larına yönelik tanılama telemetrisini akışa alma:

- **Günlüklerin üçüncü taraf günlük ve telemetri sistemlerine akışını yapın**

  Günlük verilerini bir üçüncü taraf SıEM veya Log Analytics aracına yöneltmek için tüm ölçümleri ve kaynak günlüklerinizi tek bir olay hub 'ına akışla.
- **Özel telemetri ve günlük platformu oluşturma**

  Olay Hub 'larının yüksek düzeyde ölçeklenebilir yayımla-abone ol yapısı, ölçümleri ve kaynak günlüklerini özel bir telemetri platformunda esnek bir şekilde içe almanızı sağlar. Ayrıntılar için bkz. [Event Hubs Azure 'Da küresel ölçekli telemetri platformunu tasarlama ve boyutlandırma](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **Power BI'a veri akışı yaparak hizmet durumunu görüntüleme**

  Tanılama verilerinizi Azure hizmetlerinizden neredeyse gerçek zamanlı içgörüler halinde dönüştürmek için Event Hubs, Stream Analytics ve Power BI kullanın. Bkz. [Stream Analytics ve Power BI: Bu çözümdeki Ayrıntılar için veri akışı için gerçek zamanlı analiz panosu](../../stream-analytics/stream-analytics-power-bi-dashboard.md) .

### <a name="azure-storage"></a>Azure Depolama

[Azure depolama](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)'ya ölçüm ve kaynak günlükleri akışı yapın. Önceki iki akış seçeneğinin maliyetinin bir bölümü boyunca çok miktarda tanılama telemetrisini arşivlemek için Azure Storage 'ı kullanın.

## <a name="use-extended-events"></a>Genişletilmiş olayları kullanma 

Ayrıca, Gelişmiş izleme ve sorun giderme için SQL Server [Genişletilmiş olayları](/sql/relational-databases/extended-events/extended-events) da kullanabilirsiniz. Genişletilmiş olaylar mimarisi, kullanıcıların sorunları gidermek veya bir performans sorunu tanımlamak için gereken kadar çok veya az veri toplamasına olanak sağlar. Azure SQL veritabanı 'nda genişletilmiş olayları kullanma hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda genişletilmiş olaylar](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Sonraki adımlar

- Tek ve havuza alınmış veritabanlarının akıllı performans önerileri hakkında daha fazla bilgi için bkz. [veritabanı Danışmanı performans önerileri](database-advisor-implement-performance-recommendations.md).
- Performans sorunlarının otomatik tanılama ve kök neden analizine sahip veritabanı performansını otomatik olarak izleme hakkında daha fazla bilgi için bkz. [Azure SQL akıllı içgörüler](intelligent-insights-overview.md).