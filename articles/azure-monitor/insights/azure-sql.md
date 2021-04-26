---
title: Azure Izleyici 'de çözüm Azure SQL Analytics | Microsoft Docs
description: Azure SQL Analytics çözümü, Azure SQL veritabanlarınızı yönetmenize yardımcı olur
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 09/19/2020
ms.reviewer: carlrab
ms.openlocfilehash: 54ef88e65925ba9c7e9fe2e44ef0c76fbc9ceb04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717494"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Azure SQL Analytics kullanarak Azure SQL veritabanını izleme (Önizleme)

![Azure SQL Analytics simgesi](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics, tek bir görünümdeki tüm Azure SQL veritabanlarınızın ve birden çok aboneliğin performansını izlemeye yönelik gelişmiş bir bulut izleme çözümüdür. Azure SQL Analytics performans sorunlarını gidermek için yerleşik zeka ile önemli performans ölçümlerini toplayıp görselleştirir.

Bu toplanan ölçümleri kullanarak, özel izleme kuralları ve uyarılar oluşturabilirsiniz. Azure SQL Analytics, uygulama yığınınızdaki her katmandaki sorunları belirlemenize yardımcı olur. Tek bir Log Analytics çalışma alanındaki tüm Azure SQL veritabanlarınızla ilgili verileri sunmak için Azure Izleyici görünümleri ile birlikte Azure tanılama ölçümlerini kullanır. Azure Izleyici, yapılandırılmış ve yapılandırılmamış verileri toplamanıza, ilişkilendirmenize ve görselleştirmenize yardımcı olur.

Azure SQL Analytics çözümü kullanmaya ve tipik kullanım senaryolarına yönelik uygulamalı bir genel bakış için bkz. gömülü video:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Bağlı kaynaklar

Azure SQL Analytics, tüm Azure SQL veritabanlarınızda tanılama telemetrisi akışını destekleyen bir yalnızca bulut izleme çözümüdür. Azure SQL Analytics Azure Izleyici 'ye bağlanmak için aracıları kullanmadığından, şirket içinde veya sanal makinelerde barındırılan SQL Server izlemeyi desteklemez.

| Bağlı Kaynak | Desteklenir | Description |
| --- | --- | --- |
| [Tanılama ayarları](../essentials/diagnostic-settings.md) | **Evet** | Azure ölçümü ve günlük verileri Azure Izleyici günlüklerine doğrudan Azure tarafından gönderilir. |
| [Azure depolama hesabı](../essentials/resource-logs.md#send-to-log-analytics-workspace) | No | Azure Izleyici, bir depolama hesabındaki verileri okuyamıyor. |
| [Windows aracıları](../agents/agent-windows.md) | No | Doğrudan Windows aracıları Azure SQL Analytics tarafından kullanılmaz. |
| [Linux aracıları](../vm/quick-collect-linux-computer.md) | No | Doğrudan Linux aracıları Azure SQL Analytics tarafından kullanılmaz. |
| [System Center Operations Manager yönetim grubu](../agents/om-agents.md) | No | Operations Manager aracısından Azure Izleyici 'ye doğrudan bağlantı Azure SQL Analytics tarafından kullanılmaz. |

## <a name="azure-sql-analytics-options"></a>Azure SQL Analytics seçenekleri

Aşağıdaki tabloda, biri Azure SQL veritabanı ve diğeri de Azure SQL yönetilen örnek veritabanları için Azure SQL Analytics panosunun iki sürümü için desteklenen seçenekler özetlenmektedir.

| Azure SQL Analytics seçeneği | Description | SQL veritabanı desteği | SQL Yönetilen Örneği desteği |
| --- | ------- | ----- | ----- |
| Türe göre kaynak | İzlenen tüm kaynakları sayan perspektif. | Yes | Yes |
| Insights | Akıllı İçgörüler, performans için hiyerarşik ayrıntıya gitme sağlar. | Yes | Yes |
| Hatalar | Veritabanlarında gerçekleşen SQL hatalarına hiyerarşik ayrıntıya gitme sağlar. | Yes | Yes |
| Zaman aşımları | Veritabanlarında gerçekleşen SQL zaman aşımları için hiyerarşik ayrıntıya gitme sağlar. | Yes | Hayır |
| Blok | Veritabanlarında gerçekleşen SQL blokları için hiyerarşik ayrıntıya gitme sağlar. | Yes | Hayır |
| Veritabanı bekleme süresi | Veritabanı düzeyinde SQL bekleme istatistikleri için hiyerarşik ayrıntıya gitme sağlar. Bekleme türü başına düşen toplam bekleme süresinin ve bekleme zamanının özetlerini içerir. |Yes | Hayır |
| Sorgu süresi | Sorgu süresi, CPU kullanımı, veri GÇ kullanımı, günlük GÇ kullanımı gibi sorgu yürütme istatistiklerinde hiyerarşik ayrıntıya gitme sağlar. | Yes | Yes |
| Sorgu beklemeleri | Bekleme kategorisine göre sorgu bekleme istatistikleri için hiyerarşik ayrıntıya gitme sağlar. | Yes | Yes |

## <a name="configuration"></a>Yapılandırma

Log Analytics çalışma alanınıza Azure SQL Analytics (Önizleme) eklemek için [Çözüm Galerisi Azure izleyici çözümlerini ekleme](./solutions.md) bölümünde açıklanan işlemi kullanın.

### <a name="configure-azure-sql-database-to-stream-diagnostics-telemetry"></a>Azure SQL veritabanını, tanılama telemetrisini akışa almak için yapılandırma

Çalışma alanınızda Azure SQL Analytics çözüm oluşturduktan sonra, tanılama telemetrisini Azure SQL Analytics akışa almak için izlemek istediğiniz **her kaynağı yapılandırmanız** gerekir. Bu sayfadaki ayrıntılı yönergeleri izleyin:

- [Tanılama telemetrisini Azure SQL Analytics akışa](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)almak için veritabanınızın Azure tanılama etkinleştirin.

Yukarıdaki sayfada, tek bir Azure SQL Analytics çalışma alanındaki birden çok Azure aboneliğini tek bir cam bölmesi olarak izleme desteğinin etkinleştirilmesi hakkında yönergeler de sağlanmaktadır.

## <a name="using-azure-sql-analytics"></a>Azure SQL Analytics kullanma

Çalışma alanınıza Azure SQL Analytics eklediğinizde, Azure SQL Analytics kutucuk çalışma alanınıza eklenir ve genel bakış ' da görünür. Kutucuk içeriğini yüklemek için Özeti görüntüle bağlantısını seçin.

![Azure SQL Analytics Özet kutucuğu](./media/azure-sql/azure-sql-sol-tile-01.png)

Bir kez yüklendikten sonra kutucuk, SQL veritabanı 'nda veritabanlarının ve elastik havuzların sayısını ve Azure SQL Analytics tanılama telemetrisini aldığından SQL yönetilen örneğindeki örnekleri ve örnek veritabanlarını gösterir.

![Azure SQL Analytics kutucuğu](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics iki ayrı görünüm sağlar--SQL veritabanı 'nı izlemek için bir tane ve SQL yönetilen örneği izlemeye yönelik diğer görünüm.

SQL veritabanı için Azure SQL Analytics izleme panosunu görüntülemek için kutucuğun üst kısmına tıklayın. SQL yönetilen örneği için Azure SQL Analytics izleme panosunu görüntülemek için kutucuğun alt kısmına tıklayın.

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL Analytics verileri görüntüleme

Pano, farklı perspektiflerle izlenen tüm veritabanlarına genel bakış içerir. Farklı perspektiflerin çalışması için, SQL kaynaklarınızın Log Analytics çalışma alanına akışını sağlamak üzere uygun ölçümleri veya günlükleri etkinleştirmeniz gerekir.

Bazı ölçümler veya Günlükler Azure Izleyici 'ye akışı yoksa, Azure SQL Analytics içindeki kutucuklar izleme bilgileriyle doldurulmaz.

### <a name="sql-database-view"></a>SQL veritabanı görünümü

Veritabanı için Azure SQL Analytics kutucuğu seçildikten sonra, izleme panosu gösterilir.

![İzleme panosunu gösteren ekran görüntüsü.](./media/azure-sql/azure-sql-sol-overview.png)

Kutucukların herhangi birini seçtiğinizde, belirli bir perspektife bir ayrıntıya gitme raporu açılır. Perspektif seçildikten sonra, ayrıntıya gitme raporu açılır.

![Ayrıntıya gitme raporunu belirli bir perspektife gösteren ekran görüntüsü.](./media/azure-sql/azure-sql-sol-metrics.png)

Bu görünümdeki her bakış, abonelik, sunucu, elastik havuz ve veritabanı düzeylerinde özetler sağlar. Bunlara ek olarak, her bir perspektifte, sağda rapora özgü bir perspektif gösterilmektedir. Listeden abonelik, sunucu, havuz veya veritabanı seçildiğinde ayrıntıya gitme devam eder.

### <a name="sql-managed-instance-view"></a>SQL yönetilen örnek görünümü

Veritabanları için Azure SQL Analytics kutucuğu seçildikten sonra, izleme panosu gösterilir.

![Azure SQL Analytics genel bakış](./media/azure-sql/azure-sql-sol-overview-mi.png)

Kutucukların herhangi birini seçtiğinizde, belirli bir perspektife bir ayrıntıya gitme raporu açılır. Perspektif seçildikten sonra, ayrıntıya gitme raporu açılır.

SQL yönetilen örnek görünümünü seçtiğinizde, örnek kullanımı, örnek veritabanları ve yönetilen örnek genelinde yürütülen sorgularda bulunan telemetri hakkındaki ayrıntıları gösterir.

![Azure SQL Analytics zaman aşımları](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Akıllı İçgörüler raporu

Azure SQL veritabanı [akıllı içgörüler](../../azure-sql/database/intelligent-insights-overview.md) , tüm Azure SQL veritabanlarının performansı ile neler olduğunu bilmenizi sağlar. Toplanan tüm Akıllı İçgörüler öngörülere göre görselleştirilebilir ve erişilebilir.

![Azure SQL Analytics Öngörüler](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Elastik havuzlar ve veritabanı raporları

Esnek havuzların ve veritabanlarının her ikisi de kaynak için belirtilen zamanda toplanan tüm verileri gösteren özel raporlara sahiptir.

![Azure SQL Analytics veritabanı](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL elastik havuzu](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Sorgu raporları

Sorgu süresi ve sorgu perspektifleri bekler, sorgu raporu aracılığıyla herhangi bir sorgunun performansını ilişkilendirebilmeniz gerekir. Bu rapor, farklı veritabanları genelinde sorgu performansını karşılaştırır ve seçili sorgu için yavaş olan veritabanlarının yerini almanızı kolaylaştırır.

![Azure SQL Analytics sorguları](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>İzinler

Azure SQL Analytics kullanmak için, kullanıcıların Azure 'da okuyucu rolüne en az bir izin verilmesi gerekir. Ancak, bu rol, kullanıcıların sorgu metnini görmesine veya herhangi bir otomatik ayarlama eylemi gerçekleştirmesine izin vermez. Azure 'da, en yüksek ölçüde Azure SQL Analytics kullanılmasına izin veren daha fazla izin verilen roller sahip, katkıda bulunan, SQL DB katılımcısı veya SQL Server katılımcısı. Ayrıca, portalda yalnızca Azure SQL Analytics kullanmak için gerekli izinleri olan ve diğer kaynakları yönetmeye yönelik erişim olmadan özel bir rol oluşturmayı düşünmek isteyebilirsiniz.

### <a name="creating-a-custom-role-in-portal"></a>Portalda özel bir rol oluşturma

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bazı kuruluşların Azure 'da kesin izin denetimleri uyguladığını tanıma, "SQL Analytics Izleme operatörü" adlı özel bir rolün, en uzun ölçüde Azure SQL Analytics kullanması gereken minimum okuma ve yazma izinlerine sahip Azure portal, aşağıdaki PowerShell betiğini bulun.

Aşağıdaki betikteki "{SubscriptionID}" öğesini Azure abonelik KIMLIĞINIZLE değiştirin ve Azure 'da bir sahip veya katkıda bulunan rolü olarak oturum açmış betiği yürütün.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Yeni rol oluşturulduktan sonra, Azure SQL Analytics kullanmak için özel izinler vermeniz gereken her kullanıcıya bu rolü atayın.

## <a name="analyze-data-and-create-alerts"></a>Verileri çözümleme ve uyarı oluşturma

Azure SQL Analytics veri analizi, özel sorgulama ve raporlamanın [Log Analytics diline](../logs/get-started-queries.md) bağlıdır. [Ölçüm ve günlüklerde](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#metrics-and-logs-available)bulunan özel sorgulama için veritabanı kaynağından toplanan kullanılabilir verilerin açıklamasını bulun.

Azure SQL Analytics otomatik uyarı, bir koşulun karşılanmasıyla ilgili uyarıyı tetikleyen bir Log Analytics sorgusunun yazılmasına bağlıdır. Azure SQL Analytics ' de uyarı ayarlantırabileceği Log Analytics sorgularda birkaç örnek aşağıda bulabilirsiniz.

### <a name="creating-alerts-for-azure-sql-database"></a>Azure SQL veritabanı için uyarı oluşturma

Azure SQL veritabanı kaynaklarından gelen verilerle kolayca [uyarı oluşturabilirsiniz](../alerts/alerts-metric.md) . Bir günlük uyarısıyla kullanabileceğiniz bazı yararlı [günlük sorguları](../logs/log-query-overview.md) aşağıda verilmiştir:

#### <a name="high-cpu"></a>Yüksek CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Bu uyarıyı ayarlamanın ön gerekliliği, izlenen veritabanlarının Azure SQL Analytics için temel ölçümleri akışıdır.
> - Bunun yerine yüksek DTU sonuçları elde etmek için MetricName değeri cpu_percent dtu_consumption_percent ile değiştirin.

#### <a name="high-cpu-on-elastic-pools"></a>Esnek havuzlarda yüksek CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Bu uyarıyı ayarlamanın ön gerekliliği, izlenen veritabanlarının Azure SQL Analytics için temel ölçümleri akışıdır.
> - Bunun yerine yüksek DTU sonuçları elde etmek için MetricName değeri cpu_percent dtu_consumption_percent ile değiştirin.

#### <a name="storage-in-average-above-95-in-the-last-1-hr"></a>Son 1 saat içinde %95 üzerinde ortalama depolama alanı

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - Bu uyarıyı ayarlamanın ön gerekliliği, izlenen veritabanlarının Azure SQL Analytics için temel ölçümleri akışıdır.
> - Bu sorgu, sorgunun bazı veritabanlarında mevcut olduğunu belirten bir uyarı (> 0 sonuç) olduğunda bir uyarının tetikleneceği bir uyarı kuralının ayarlanmasını gerektirir. Çıktı, tanımlanan time_range içinde storage_threshold üzerinde olan veritabanı kaynaklarının bir listesidir.
> - Çıktı, tanımlanan time_range içinde storage_threshold üzerinde olan veritabanı kaynaklarının bir listesidir.

#### <a name="alert-on-intelligent-insights"></a>Akıllı Öngörüler üzerinde uyarı

> [!IMPORTANT]
> Bir veritabanının iyi hale getirilmesi ve Akıllı İçgörüler üretilmediğinden, bu sorgu bir hata iletisiyle başarısız olur: ' rootCauseAnalysis_s ' adlı skaler ifade çözümlenemedi. Bu davranış, veritabanı için akıllı öngörü bulunmayan tüm durumlarda beklenir.

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - Bu uyarıyı ayarlamanın ön gerekliliği, izlenen veritabanlarının Azure SQL Analytics için Sqlinsıghts tanılama günlüğünü açmasını sağlar.
> - Bu sorgu, yinelenen sonuçları önlemek için alert_run_interval aynı sıklık ile çalışacak şekilde ayarlanmış bir uyarı kuralının ayarlanmasını gerektirir. Sorgudan, bir sorgu (> 0 sonuç) varsa, uyarının tetiklenmesi için ayarlanmış olması gerekir.
> - Alert_run_interval, koşulun Sqlinsıghts Azure SQL Analytics günlüğünü akışa almak üzere yapılandırılan veritabanlarında oluşup olmadığını denetlemek için zaman aralığını belirlemek üzere ' i özelleştirin.
> - İnsights_string, Öngörüler kök nedeni analiz metninin çıkışını yakalamak için özelleştirin. Bu, mevcut Öngörüler arasından kullanabileceğiniz Azure SQL Analytics Kullanıcı arabiriminde görüntülenen aynı metindir. Alternatif olarak, aboneliğinizde oluşturulan tüm öngörülerin metnini görmek için aşağıdaki sorguyu kullanabilirsiniz. Öngörüler üzerinde uyarıları ayarlamaya yönelik ayrı dizeleri toplamak için sorgunun çıkışını kullanın.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-sql-managed-instance"></a>SQL yönetilen örneği için uyarı oluşturma

#### <a name="storage-is-above-90"></a>Depolama %90 üzerinde

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - Bu uyarıyı ayarlamanın ön gerekliliği, izlenen yönetilen örnek Azure SQL Analytics için ResourceUsageStats günlüğü akışının etkin olmasını sağlar.
> - Bu sorgu, sorgunun yönetilen örnekte mevcut olduğunu belirten, sorgudan bir uyarı (> 0 sonuç) olduğunda bir uyarının tetikleneceği bir uyarı kuralının ayarlanmasını gerektirir. Çıktı, yönetilen örnekteki depolama yüzdesi tüketimidir.

#### <a name="cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Son 1 hr 'da CPU ortalama tüketim %95 üzerinde

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - Bu uyarıyı ayarlamanın ön gerekliliği, izlenen yönetilen örnek Azure SQL Analytics için ResourceUsageStats günlüğü akışının etkin olmasını sağlar.
> - Bu sorgu, sorgunun yönetilen örnekte mevcut olduğunu belirten, sorgudan bir uyarı (> 0 sonuç) olduğunda bir uyarının tetikleneceği bir uyarı kuralının ayarlanmasını gerektirir. Çıktı, yönetilen örnekteki tanımlı dönemdeki ortalama CPU kullanımı yüzdesi tüketimidir.

### <a name="pricing"></a>Fiyatlandırma

Azure SQL Analytics kullanımı ücretsizdir, her ay ayrılan boş veri alma birimlerinin üzerinde tanılama telemetrinin tüketimi geçerlidir, bkz. [Log Analytics fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor). Sunulan ücretsiz veri alma birimleri, her ay birkaç veritabanının ücretsiz olarak izlenmesini sağlar. Daha yoğun iş yüklerine sahip daha fazla veri ve boştaki veritabanlarına kıyasla daha fazla etkin veritabanı. Azure SQL Analytics gezinti menüsünde OMS çalışma alanı ' nı ve ardından kullanım ve tahmini maliyetler ' i seçerek Azure SQL Analytics veri alımı kullanımını kolayca izleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Ayrıntılı Azure SQL verilerini görüntülemek için Azure Izleyici 'de [günlük sorguları](../logs/log-query-overview.md) ' nı kullanın.
- Azure SQL verilerini gösteren [kendi panolarınızı oluşturun](../visualize/tutorial-logs-dashboards.md) .
- Belirli Azure SQL olayları gerçekleştiğinde [uyarılar oluşturun](../alerts/alerts-overview.md) .

