---
title: Ölçüm ve kaynak günlüklerinin akış dışa aktarılmasını yapılandırma
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nin akıllı tanılama analizini, kaynak kullanımı ve sorgu yürütme istatistikleri hakkında bilgi depolamak için tercih ettiğiniz hedefe dahil olmak üzere ölçüm ve kaynak günlüklerinin akış dışa aktarılmasını nasıl yapılandıracağınızı öğrenin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 04/06/2020
ms.openlocfilehash: 1de2c1ff02c799d04f2ab2c81e83dda5001a531f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592727"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>Azure SQL veritabanı ve SQL yönetilen örnek tanılama telemetrisine akış vermeyi yapılandırma
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bu makalede, analiz için birkaç hedefden birine dışarı aktarabilirsiniz. Azure SQL veritabanı için performans ölçümleri ve kaynak günlükleri hakkında bilgi edineceksiniz. Azure portal, PowerShell, Azure CLı, REST API ve Azure Resource Manager şablonları aracılığıyla bu tanılama telemetrisini akışa alma işlemini nasıl yapılandıracağınızı öğreneceksiniz.

Ayrıca, bu tanılama telemetrisini akışındaki ve bu seçenekler arasından seçim yapabileceğiniz hedefler hakkında bilgi edineceksiniz. Hedef seçenekleriniz şunlardır:

- [Log Analytics ve SQL Analytics](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure Depolama](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>Dışarı aktarma için tanılama telemetrisi

Dışarı aktarmak için kullanabileceğiniz tanılama telemetrisi arasındaki en önemli nokta Akıllı İçgörüler (Sqlinsıghts) günlüğssdır. [Akıllı içgörüler](intelligent-insights-overview.md) , yapay zeka aracılığıyla veritabanı kullanımını sürekli olarak izlemek ve zayıf performansa neden olan olayları saptamak için yerleşik zeka kullanır. Algılandıktan sonra, sorunun akıllı değerlendirmesiyle Akıllı İçgörüler bir günlük üreten ayrıntılı bir analiz gerçekleştirilir. Bu değerlendirme, veritabanı performans sorununun bir kök neden analizinden oluşur ve mümkün olduğunda performans iyileştirmeleri için öneriler içerir. İçeriğini görüntülemek için bu günlüğün akış dışarı aktarmayı yapılandırmanız gerekir.

Akıllı İçgörüler günlüğünün dışa aktarılmasını akışa ek olarak, çeşitli performans ölçümlerini ve ek veritabanı günlüklerini de dışarı aktarabilirsiniz. Aşağıdaki tabloda, çeşitli hedeflerden birine akış verme için yapılandırabileceğiniz performans ölçümleri ve kaynak günlükleri açıklanmaktadır. Bu tanılama telemetrisi, tek veritabanları, elastik havuzlar ve havuza alınmış veritabanları, yönetilen örnekler ve örnek veritabanları için yapılandırılabilir.

| Veritabanları için tanılama telemetrisi | Azure SQL veritabanı desteği | Azure SQL Yönetilen Örneği desteği |
| :------------------- | ----- | ----- |
| [Temel ölçümler](#basic-metrics): DTU/CPU YÜZDESI, DTU/CPU sınırı, fiziksel veri okuma yüzdesi, günlük yazma yüzdesi, başarılı/başarısız/engellenen güvenlik duvarı bağlantıları, oturum yüzdesi, çalışan yüzdesi, depolama, depolama alanı yüzdesi ve XTP depolama yüzdesi içerir. | Yes | Hayır |
| [Örnek ve uygulama gelişmiş](#advanced-metrics): tempdb sistem veritabanı verilerini ve günlük dosyası boyutunu ve kullanılan tempdb yüzde günlük dosyasını içerir. | Yes | Hayır |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): CPU kullanımı ve sorgu süresi istatistikleri gibi sorgu çalışma zamanı istatistikleri hakkındaki bilgileri içerir. | Yes | Yes |
| [Querystorewaitstatistics](#query-store-wait-statistics): CPU, günlük ve kilitleme gibi sorgu bekleme istatistikleri (sorgularınızın ne kadar bekledikleriniz) hakkındaki bilgileri içerir. | Yes | Yes |
| [Hatalar](#errors-dataset): BIR veritabanındaki SQL hatalarıyla ilgili bilgileri içerir. | Yes | Yes |
| [Databasewaitstatistics](#database-wait-statistics-dataset): bir veritabanının farklı bekleme türlerini beklerken ne kadar zaman harcadığını gösteren bilgiler içerir. | Yes | Hayır |
| [Zaman aşımları](#time-outs-dataset): bir veritabanındaki zaman aşımları hakkında bilgi içerir. | Yes | Hayır |
| [Bloklar](#blockings-dataset): bir veritabanındaki olayları engelleme hakkında bilgi içerir. | Yes | Hayır |
| [Kilitlenmeler](#deadlocks-dataset): bir veritabanındaki kilitlenme olayları hakkında bilgi içerir. | Yes | Hayır |
| Otomatik [ayarlama: bir](#automatic-tuning-dataset)veritabanı için otomatik ayarlama önerileri hakkındaki bilgileri içerir. | Yes | Hayır |
| [Sqlinsıghts](#intelligent-insights-dataset): bir veritabanının performansına akıllı içgörüler içerir. Daha fazla bilgi için bkz. [akıllı içgörüler](intelligent-insights-overview.md). | Yes | Yes |

> [!NOTE]
> Tanılama ayarları, ana, msdb, model, kaynak ve tempdb veritabanları gibi **sistem veritabanları** için yapılandırılamaz.

## <a name="streaming-export-destinations"></a>Akış dışa aktarma hedefleri

Bu tanılama telemetrisi, analiz için aşağıdaki Azure kaynaklarından birine akışla eklenebilir.

- **[Log Analytics çalışma alanı](#stream-into-sql-analytics)**:

  [Log Analytics çalışma alanına](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) akan veriler [SQL Analytics](../../azure-monitor/insights/azure-sql.md)tarafından tüketilebilir. SQL Analytics, veritabanlarının performans raporları, uyarılar ve risk azaltma önerilerini içeren akıllı bir şekilde izlenmesini sağlayan bir yalnızca bulut izleme çözümüdür. Bir Log Analytics çalışma alanına akan veriler, toplanan diğer izleme verileriyle analiz edilebilir ve ayrıca uyarılar ve görselleştirmeler gibi diğer Azure Izleyici özelliklerinden yararlanmanızı sağlar
- **[Azure Event Hubs](#stream-into-event-hubs)**:

  [Azure Olay Hub 'ına](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)akan veriler aşağıdaki işlevleri sağlar:

  - Günlük **kaydını** üçüncü taraf BIR SIEM veya Log Analytics aracına yöneltmek için tüm ölçümleri ve kaynak günlüklerinizi tek bir olay hub 'ına akışa alma.
  - **Özel bir telemetri ve günlüğe kaydetme platformu oluşturun**: Olay Hub 'larının yüksek düzeyde ölçeklenebilir yayımla-abone olma yapısı, ölçümleri ve kaynak günlüklerini özel bir telemetri platformunda esnek bir şekilde içe almanıza olanak sağlar. Ayrıntılar için bkz. [Event Hubs Azure 'Da küresel ölçekli telemetri platformunu tasarlama ve boyutlandırma](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
  - **Power BI veri akışı yaparak hizmet durumunu görüntüleyin**: Tanılama verilerinizi Azure hizmetlerinizden neredeyse gerçek zamanlı içgörüler halinde dönüştürmek için Event Hubs, Stream Analytics ve Power BI kullanın. Bkz. [Stream Analytics ve Power BI: Bu çözümdeki Ayrıntılar için veri akışı için gerçek zamanlı analiz panosu](../../stream-analytics/stream-analytics-power-bi-dashboard.md) .
- **[Azure depolama](#stream-into-azure-storage)**:

  [Azure depolama](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) 'ya akan veriler, önceki iki akış seçeneğinin maliyetinin bir bölümü boyunca çok miktarda tanılama telemetrisini arşivlemenize olanak sağlar.

Bu hedeflerin birine akan bu tanılama telemetrisi, daha kolay performans izleme için kaynak kullanımını ve sorgu yürütme istatistiklerini ölçmek için kullanılabilir.

![Diyagramda, bilgileri Azure SQL Analytics, Olay Hub 'ına ve depolamaya ileten Azure Tanılama telemetri gönderen yönetilen örneklerde birçok SQL veritabanı ve veritabanı gösterilmektedir.](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Tanılama telemetrinin akış dışa aktarılmasını etkinleştirin ve yapılandırın

Aşağıdaki yöntemlerden birini kullanarak ölçümleri ve tanılama telemetrisi günlüğünü etkinleştirebilirsiniz:

- Azure portal
- PowerShell
- Azure CLI
- Azure İzleyici REST API'si
- Azure Resource Manager şablonu

> [!NOTE]
> Güvenlik telemetrisinden denetim günlüğü akışını etkinleştirmek için bkz. [Azure izleyici günlüklerinde ve azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242), [Veritabanınız için denetim ayarlama](./auditing-overview.md#setup-auditing) ve günlükleri denetleme.

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Tanılama telemetrisini akışa alma işlemini yapılandırma

Tanılama telemetrinin akışını etkinleştirmek ve yapılandırmak için Azure portal **Tanılama ayarları** menüsünü kullanabilirsiniz. Ayrıca, tanılama telemetrinin akışını yapılandırmak için PowerShell, Azure CLı, [REST API](/rest/api/monitor/diagnosticsettings)ve [Kaynak Yöneticisi şablonlarını](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md) kullanabilirsiniz. Tanılama telemetrisini akışa almak için aşağıdaki hedefleri ayarlayabilirsiniz: Azure depolama, Azure Event Hubs ve Azure Izleyici günlükleri.

> [!IMPORTANT]
> Tanılama telemetrinin akış dışa aktarılması varsayılan olarak etkin değildir.

Azure portal tanılama telemetrinin akış dışa aktarılmasını yapılandırmaya yönelik adım adım yönergeler ve PowerShell ve Azure CLı ile aynı şekilde çalışmak için betikler için aşağıdaki sekmelerden birini seçin.

# <a name="azure-portal"></a>[Azure portalı](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Azure SQL veritabanı 'nda elastik havuzlar

Aşağıdaki tanılama telemetrisini toplamak için bir elastik havuz kaynağı ayarlayabilirsiniz:

| Kaynak | Telemetri izleme |
| :------------------- | ------------------- |
| **Elastik havuz** | [Temel ölçümler](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) EDTU/CPU yüzdesi, EDTU/CPU sınırı, fiziksel veri okuma yüzdesi, günlük yazma yüzdesi, oturum yüzdesi, çalışan yüzdesi, depolama, depolama yüzdesi, depolama sınırı ve XTP depolama yüzdesi içerir. |

Esnek havuzlar ve havuza alınmış veritabanları için tanılama telemetrinin akışını yapılandırmak için her birini ayrı ayrı yapılandırmanız gerekir:

- Elastik havuz için tanılama telemetrinin akışını etkinleştirme
- Elastik havuzdaki her veritabanı için tanılama telemetrinin akışını etkinleştirme

Elastik havuz kapsayıcısının, tek tek havuza alınmış her veritabanı telemetrisinden ayrı bir telemetri vardır.

Esnek havuz kaynağı için tanılama telemetrinin akışını etkinleştirmek için şu adımları izleyin:

1. Azure portal ' deki **elastik havuz** kaynağına gidin.
2. **Tanılama ayarları**' nı seçin.
3. Önceki ayarlar yoksa **tanılamayı aç** ' ı seçin veya önceki bir ayarı düzenlemek Için **ayarı Düzenle** ' yi seçin.

   ![Elastik havuzlar için tanılamayı etkinleştir](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. Kendi başvurunuz için bir ayar adı girin.
5. Akış Tanılama verileri için bir hedef kaynak seçin: **depolama hesabına Arşivle**, **bir olay hub 'ına akış** veya **Log Analytics gönderme**.
6. Log Analytics için, **Yapılandır** ' ı seçin ve yeni çalışma **alanı oluştur ' u seçerek yeni** bir çalışma alanı oluşturun veya mevcut bir çalışma alanı seçin.
7. Elastik havuz tanılama telemetrisi için onay kutusunu seçin: **temel** ölçümler.
   ![Elastik havuzlar için tanılamayı yapılandırma](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. **Kaydet**’i seçin.
9. Ayrıca, sonraki bölümde açıklanan adımları izleyerek izlemek istediğiniz elastik havuzda bulunan her bir veritabanı için tanılama telemetrinin akışını yapılandırın.

> [!IMPORTANT]
> Esnek havuz için tanılama telemetrisini yapılandırmanın yanı sıra, esnek havuzdaki her veritabanı için de tanılama telemetrisini yapılandırmanız gerekir.

### <a name="databases-in-azure-sql-database"></a>Azure SQL veritabanı 'ndaki veritabanları

Aşağıdaki tanılama telemetrisini toplamak için bir veritabanı kaynağı ayarlayabilirsiniz:

| Kaynak | Telemetri izleme |
| :------------------- | ------------------- |
| **Tek veya havuza alınmış veritabanı** | [Temel ölçümler](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) DTU YÜZDESI, DTU kullanımı, DTU SıNıRı, CPU yüzdesi, fiziksel veri okuma yüzdesi, günlük yazma yüzdesi, başarılı/başarısız/engellenen güvenlik duvarı bağlantıları, oturum yüzdesi, çalışan yüzdesi, depolama, depolama alanı yüzdesi, XTP depolama yüzdesi ve kilitlenmeleri içerir. |

Tek veya havuza alınmış bir veritabanı için tanılama telemetrinin akışını etkinleştirmek için şu adımları izleyin:

1. Azure **SQL veritabanı** kaynağına gidin.
2. **Tanılama ayarları**' nı seçin.
3. Önceki ayarlar yoksa **tanılamayı aç** ' ı seçin veya önceki bir ayarı düzenlemek Için **ayarı Düzenle** ' yi seçin. Tanılama telemetrisini akışa almak için en fazla üç paralel bağlantı oluşturabilirsiniz.
4. Tanılama verilerinin paralel akışını birden çok kaynağa yapılandırmak için **Tanılama ayarı Ekle** ' yi seçin.

   ![Tek ve havuza alınmış veritabanları için tanılamayı etkinleştirme](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. Kendi başvurunuz için bir ayar adı girin.
6. Akış Tanılama verileri için bir hedef kaynak seçin: **depolama hesabına Arşivle**, **bir olay hub 'ına akış** veya **Log Analytics gönderme**.
7. Standart, olay tabanlı izleme deneyimi için, veritabanı tanılama günlüğü telemetrisi için aşağıdaki onay kutularını seçin: **Sqlinsıghts**, **automatictuning**, **QueryStoreRuntimeStatistics**, **querystorewaitstatistics**, **hatalar**, **databasewaitstatistics**, **zaman aşımları**, **bloklar** ve **kilitlenmeler**.
8. Gelişmiş, tek dakikalık tabanlı izleme deneyimi için **temel** ölçümler onay kutusunu seçin.

   ![Azure SQL veritabanı için tanılamayı yapılandırma](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. **Kaydet**’i seçin.
10. İzlemek istediğiniz her veritabanı için bu adımları tekrarlayın.

> [!TIP]
> İzlemek istediğiniz her bir tek ve havuza alınmış veritabanı için bu adımları yineleyin.

### <a name="instances-in-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği örnekleri

Aşağıdaki tanılama telemetrisini toplamak için bir yönetilen örnek kaynağı ayarlayabilirsiniz:

| Kaynak | Telemetri izleme |
| :------------------- | ------------------- |
| **Yönetilen örnek** | [Resourceusagestats](#resource-usage-stats-for-managed-instances) sanal çekirdekler sayısını, ortalama CPU YÜZDESINI, GÇ isteklerini, okunan/yazılan bayt, ayrılmış depolama alanını ve kullanılan depolama alanını içerir. |

Yönetilen örnek ve örnek veritabanları için tanılama telemetrinin akışını yapılandırmak için, her birini ayrı ayrı yapılandırmanız gerekir:

- Yönetilen örnek için tanılama telemetrinin akışını etkinleştir
- Her örnek veritabanı için tanılama telemetrinin akışını etkinleştir

Yönetilen örnek kapsayıcısının her bir örnek veritabanının telemetrisinden ayrı kendi telemetrisi vardır.

Yönetilen örnek kaynağı için tanılama telemetrinin akışını etkinleştirmek için şu adımları izleyin:

1. Azure portal **yönetilen örnek** kaynağına gidin.
2. **Tanılama ayarları**' nı seçin.
3. Önceki ayarlar yoksa **tanılamayı aç** ' ı seçin veya önceki bir ayarı düzenlemek Için **ayarı Düzenle** ' yi seçin.

   ![Yönetilen örnek için tanılamayı etkinleştir](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. Kendi başvurunuz için bir ayar adı girin.
5. Akış Tanılama verileri için bir hedef kaynak seçin: **depolama hesabına Arşivle**, **bir olay hub 'ına akış** veya **Log Analytics gönderme**.
6. Log Analytics için, **Yapılandır** ' ı seçin ve yeni çalışma **alanı oluştur ' u seçerek yeni** bir çalışma alanı oluşturun veya mevcut bir çalışma alanını kullanın.
7. Örnek tanılama telemetrisi: **Resourceusagestats** için onay kutusunu seçin.

   ![Yönetilen örnek için tanılamayı yapılandırma](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. **Kaydet**’i seçin.
9. Ayrıca, sonraki bölümde açıklanan adımları izleyerek, izlemek istediğiniz yönetilen örnek içindeki her örnek veritabanı için tanılama telemetrinin akışını yapılandırın.

> [!IMPORTANT]
> Yönetilen bir örnek için tanılama telemetrisini yapılandırmaya ek olarak, her örnek veritabanı için de tanılama telemetrisini yapılandırmanız gerekir.

### <a name="databases-in-azure-sql-managed-instance"></a>Azure SQL yönetilen örneğindeki veritabanları

Aşağıdaki tanılama telemetrisini toplamak için bir örnek veritabanı kaynağı ayarlayabilirsiniz:

| Kaynak | Telemetri izleme |
| :------------------- | ------------------- |
| **Örnek veritabanı** | [Resourceusagestats](#resource-usage-stats-for-managed-instances) sanal çekirdekler sayısını, ortalama CPU YÜZDESINI, GÇ isteklerini, okunan/yazılan bayt, ayrılmış depolama alanını ve kullanılan depolama alanını içerir. |

Bir örnek veritabanı için tanılama telemetrinin akışını etkinleştirmek için şu adımları izleyin:

1. Yönetilen örnek içinde **örnek veritabanı** kaynağına gidin.
2. **Tanılama ayarları**' nı seçin.
3. Önceki ayarlar yoksa **tanılamayı aç** ' ı seçin veya önceki bir ayarı düzenlemek Için **ayarı Düzenle** ' yi seçin.
   - Veri akışı tanılama telemetrisine kadar en fazla üç (3) paralel bağlantı oluşturabilirsiniz.
   - Tanılama verilerinin paralel akışını birden çok kaynağa yapılandırmak için **+ Tanılama ayarı Ekle** ' yi seçin.

   ![Örnek veritabanları için tanılamayı etkinleştir](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. Kendi başvurunuz için bir ayar adı girin.
5. Akış Tanılama verileri için bir hedef kaynak seçin: **depolama hesabına Arşivle**, **bir olay hub 'ına akış** veya **Log Analytics gönderme**.
6. Veritabanı tanılama telemetrisi için onay kutularını seçin: **Sqlinsıghts**, **QueryStoreRuntimeStatistics**, **Querystorewaitstatistics** ve **hatalar**.
   ![Örnek veritabanları için tanılamayı yapılandırma](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. **Kaydet**’i seçin.
8. İzlemek istediğiniz her örnek veritabanı için bu adımları tekrarlayın.

> [!TIP]
> İzlemek istediğiniz her örnek veritabanı için bu adımları tekrarlayın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

PowerShell kullanarak ölçümleri ve tanılama günlük kaydını etkinleştirebilirsiniz.

- Bir depolama hesabında ölçüm ve kaynak günlüklerinin depolanmasını etkinleştirmek için şu komutu kullanın:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  Depolama hesabı KIMLIĞI, hedef depolama hesabının kaynak KIMLIĞIDIR.

- Ölçüm ve kaynak günlüklerinin bir olay hub 'ına akışını etkinleştirmek için şu komutu kullanın:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Azure Service Bus kural kimliği, şu biçime sahip bir dizedir:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Ölçüm ve kaynak günlüklerinin Log Analytics çalışma alanına gönderilmesini sağlamak için şu komutu kullanın:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- Log Analytics çalışma alanının kaynak kimliğini almak için aşağıdaki komutu kullanabilirsiniz:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

Bu parametreleri bir arada kullanarak birden fazla çıkış seçeneği oluşturabilirsiniz.

**Birden çok Azure kaynağını yapılandırmak için**

Birden çok aboneliği desteklemek için PowerShell komut dosyasını kullanarak PowerShell 'i [kullanarak Azure Kaynak ölçümleri günlüğünü etkinleştirin](/archive/blogs/msoms/enable-azure-resource-metrics-logging-using-powershell).

\<$WSID\> `Enable-AzureRMDiagnostics.ps1` Birden çok kaynaktan alınan tanılama verilerini çalışma alanına göndermek için betiği yürütürken çalışma alanı kaynak kimliğini bir parametre olarak belirtin.

- Tanılama verilerinize ilişkin hedefin çalışma alanı KIMLIĞINI almak için \<$WSID\> aşağıdaki betiği kullanın:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  \<subID\> \<RG_NAME\> Kaynak grubu adı ile ve \<WS_NAME\> çalışma alanı ADıYLA birlikte abonelik kimliğiyle değiştirin.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı kullanarak ölçümleri ve tanılama günlük kaydını etkinleştirebilirsiniz.

> [!IMPORTANT]
> Tanılama günlüğünü etkinleştirme betikleri Azure CLı v 1.0 için desteklenir. Azure CLı v 2.0 şu anda desteklenmiyor.

- Bir depolama hesabında ölçüm ve kaynak günlüklerinin depolanmasını etkinleştirmek için şu komutu kullanın:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  Depolama hesabı KIMLIĞI, hedef depolama hesabının kaynak KIMLIĞIDIR.

- Ölçüm ve kaynak günlüklerinin bir olay hub 'ına akışını sağlamak için şu komutu kullanın:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  Service Bus kural KIMLIĞI Şu biçimdeki bir dizedir:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Ölçüm ve kaynak günlüklerinin bir Log Analytics çalışma alanına gönderilmesini sağlamak için şu komutu kullanın:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

Bu parametreleri bir arada kullanarak birden fazla çıkış seçeneği oluşturabilirsiniz.

---

## <a name="stream-into-sql-analytics"></a>SQL Analytics 'e akış

Azure SQL veritabanı ve Azure SQL yönetilen örnek ölçümleri ve bir Log Analytics çalışma alanına akan kaynak günlükleri Azure SQL Analytics tarafından tüketilebilir. Azure SQL Analytics, tek veritabanlarının, elastik havuzların ve havuza alınan veritabanlarının performansını ve yönetilen örnekleri ve örnek veritabanlarını ölçeklendirmekte ve birden çok aboneliğe göre izleyen bir bulut çözümüdür. Performans ölçümlerini toplamanıza ve görselleştirmenize yardımcı olabilir ve performans sorunlarını gidermek için yerleşik zeka sahiptir.

![Azure SQL Analytics genel bakış](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Yüklemeye genel bakış

Aşağıdaki adımları gerçekleştirerek, bir veritabanı ve veritabanı koleksiyonları koleksiyonunu Azure SQL Analytics izleyebilirsiniz:

1. Azure Marketi 'nden bir Azure SQL Analytics çözümü oluşturun.
2. Çözümde bir Log Analytics çalışma alanı oluşturun.
3. Tanılama telemetrisini çalışma alanına akışa almak için veritabanlarını yapılandırın.

Azure portal Tanılama Ayarları sekmesindeki yerleşik **Log Analytics gönder** seçeneğini kullanarak bu tanılama telemetrinin akış dışa aktarılmasını yapılandırabilirsiniz. [PowerShell cmdlet 'leri](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), [Azure CLI](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry), [Azure izleyici REST API](/rest/api/monitor/diagnosticsettings)veya [Kaynak Yöneticisi şablonları](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md)aracılığıyla tanılama ayarlarını kullanarak da bir Log Analytics çalışma alanına akışı etkinleştirebilirsiniz.

### <a name="create-an-azure-sql-analytics-resource"></a>Azure SQL Analytics kaynağı oluşturma

1. Azure Market 'te Azure SQL Analytics arayın ve seçin.

   ![Portalda Azure SQL Analytics arama](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. Çözümün genel bakış ekranında **Oluştur** ' u seçin.

3. Azure SQL Analytics formunu, gereken ek bilgilerle girin: çalışma alanı adı, abonelik, kaynak grubu, konum ve fiyatlandırma katmanı.

   ![Portalda Azure SQL Analytics yapılandırma](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. Onaylamak için **Tamam** ' ı seçin ve ardından **Oluştur**' u seçin.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Kaynakları, ölçümleri ve kaynak günlüklerini kaydedecek şekilde yapılandırma

Tek ve havuza alınmış veritabanları, elastik havuzlar, yönetilen örnekler ve örnek veritabanları için tanılama telemetri akışını ayrı olarak yapılandırmanız gerekir. Kaynak kaydı ölçümlerini Azure portal kullanarak yapılandırmanın en kolay yolu. Ayrıntılı adımlar için bkz. [Tanılama telemetrinin akış dışa aktarılmasını yapılandırma](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>İzleme ve uyarma için Azure SQL Analytics kullanma

Veritabanı kaynaklarınızı görüntülemek için SQL Analytics 'i hiyerarşik bir pano olarak kullanabilirsiniz.

- Azure SQL Analytics nasıl kullanacağınızı öğrenmek için bkz. [SQL Analytics kullanarak izleme](../../azure-monitor/insights/azure-sql.md).
- SQL Analytics 'te için uyarıları ayarlamayı öğrenmek için bkz. [veritabanı, elastik havuzlar ve yönetilen örnekler için uyarı oluşturma](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Event Hubs'a akış sağlama

Azure SQL veritabanı ve Azure SQL yönetilen örnek ölçümleri ve kaynak günlüklerini, Azure portal **bir olay hub** 'ı seçeneğinde yerleşik akışı kullanarak Event Hubs ' a akışını sağlayabilirsiniz. Ayrıca, PowerShell cmdlet 'leri, Azure CLı veya Azure Izleyici REST API aracılığıyla tanılama ayarlarını kullanarak Service Bus kuralı KIMLIĞINI etkinleştirebilirsiniz. Olay Hub 'ının, veritabanınız ve sunucunuz ile aynı bölgede olduğundan emin olun.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Event Hubs ölçümler ve kaynak günlükleriyle ne yapmalı?

Seçili veriler Event Hubs akışa alındıktan sonra, Gelişmiş izleme senaryolarına olanak tanımak için bir adım daha yakınına sahip olursunuz. Event Hubs, bir olay işlem hattının ön kapısı olarak davranır. Veriler bir olay hub 'ına toplandıktan sonra, gerçek zamanlı bir analiz sağlayıcısı veya bir depolama bağdaştırıcısı kullanılarak dönüştürülebilir ve depolanabilir. Event Hubs, bir olay akışının üretimini bu olayların kullanımından ayırır. Bu şekilde olay tüketicileri, olaylara kendi zamanlamalarıyla erişebilirler. Event Hubs hakkında daha fazla bilgi için bkz.

- [Azure Event Hubs nedir?](../../event-hubs/event-hubs-about.md)
- [Event Hubs kullanmaya başlayın](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Event Hubs için akan ölçümleri kullanabilirsiniz:

- **Power BI, etkin yol verilerini akışa alarak hizmet durumunu görüntüleyin**

  Event Hubs, Stream Analytics ve Power BI kullanarak ölçümler ve tanılama verilerinizi Azure hizmetlerinizden neredeyse gerçek zamanlı içgörüler halinde kolayca dönüştürebilirsiniz. Bir olay hub 'ı ayarlama, Stream Analytics ile verileri işleme ve çıkış olarak Power BI kullanma hakkında genel bilgi için bkz. [Stream Analytics ve Power BI](../../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Üçüncü taraf günlüğe kaydetme ve telemetri akışlarına yönelik akış günlükleri**

  Event Hubs akışı kullanarak, ölçümlerinizi ve kaynak günlüklerinizi çeşitli üçüncü taraf izleme ve Log Analytics çözümlerine aktarabilirsiniz.

- **Özel telemetri ve günlük platformu oluşturma**

  Zaten özel olarak oluşturulmuş bir telemetri platformudur veya bir tane oluşturmayı düşünülüyor musunuz? Yüksek düzeyde ölçeklenebilir yayımla-abone ol Event Hubs, ölçümleri ve kaynak günlüklerini esnek bir şekilde almanızı sağlar. [Küresel ölçekli bir telemetri platformunda Event Hubs kullanmak için bkz. Dan Rosanova Kılavuzu](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Azure depolama 'ya akış

Azure portal yerleşik Arşivi kullanarak Azure depolama 'da ölçümleri ve kaynak günlüklerini depolama **hesabı** seçeneğinde saklayabilirsiniz. Ayrıca, PowerShell cmdlet 'leri, Azure CLı veya Azure Izleyici REST API aracılığıyla tanılama ayarlarını kullanarak depolamayı etkinleştirebilirsiniz.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Depolama hesabındaki ölçüm ve kaynak günlüklerinin şeması

Ölçümleri ve kaynak günlükleri toplamayı ayarladıktan sonra, ilk veri satırları kullanılabilir olduğunda seçtiğiniz depolama hesabında bir depolama kapsayıcısı oluşturulur. Blobların yapısı şu şekilde olur:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ya da daha basit:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Örneğin, temel ölçümler için bir blob adı şu olabilir:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Elastik bir havuzdan veri depolamak için bir blob adı şöyle görünür:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Veri bekletme ilkesi ve fiyatlandırma

Event Hubs veya depolama hesabı ' nı seçerseniz bir bekletme ilkesi belirleyebilirsiniz. Bu ilke seçilen bir zaman aralığından daha eski olan verileri siler. Log Analytics belirtirseniz, bekletme ilkesi seçili fiyatlandırma katmanına bağlıdır. Bu durumda, sunulan ücretsiz veri alma birimleri her ay çeşitli veritabanlarının ücretsiz olarak izlenmesini etkinleştirebilir. Ücretsiz birimlerin fazla olması halinde tüm tanılama telemetrisi tüketimi maliyette bulunabilir.

> [!IMPORTANT]
> Yoğun iş yüklerine sahip etkin veritabanları, boşta veritabanlarından daha fazla veri alma. Daha fazla bilgi için bkz. [Log Analytics fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/).

Azure SQL Analytics kullanıyorsanız, Azure SQL Analytics gezinti menüsünde **OMS çalışma alanı** ' nı ve ardından **kullanım** ve **tahmini maliyetler**' i seçerek veri alımı kullanımınızı izleyebilirsiniz.

## <a name="metrics-and-logs-available"></a>Ölçümler ve Günlükler kullanılabilir

Tek veritabanları, havuza alınmış veritabanları, elastik havuzlar, yönetilen örnek ve örnek veritabanları için kullanılabilen izleme telemetrisi, makalenin bu bölümünde belgelenmiştir. SQL Analytics içinde toplanan izleme telemetrisi, [Azure izleyici günlük sorguları](../../azure-monitor/logs/get-started-queries.md) dilini kullanarak kendi özel analiz ve uygulama geliştirme için kullanılabilir.

### <a name="basic-metrics"></a>Temel ölçümler

Kaynağa göre temel ölçümler hakkında daha fazla bilgi için aşağıdaki tablolara bakın.

> [!NOTE]
> Temel ölçümler seçeneği önceden tüm ölçümler olarak bilinirdi. Yapılan değişiklik yalnızca adlandırma amaçlıdır ve izlenen ölçümlerde hiçbir değişiklik yapılmadı. Bu değişiklik, gelecekte ek ölçüm kategorilerinin tanıtılmasıyla izin verecek şekilde başlatıldı.

#### <a name="basic-metrics-for-elastic-pools"></a>Elastik havuzlar için temel ölçümler

|**Kaynak**|**Ölçümler**|
|---|---|
|Elastik havuz|eDTU yüzdesi, eDTU kullanımı, eDTU sınırı, CPU yüzdesi, fiziksel veri okuma yüzdesi, günlük yazma yüzdesi, oturum yüzdesi, çalışan yüzdesi, depolama, depolama yüzdesi, depolama sınırı, XTP depolama yüzdesi |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Tek ve havuza alınmış veritabanları için temel ölçümler

|**Kaynak**|**Ölçümler**|
|---|---|
|Tek ve havuza alınmış veritabanı|DTU yüzdesi, DTU kullanımı, DTU sınırı, CPU yüzdesi, fiziksel veri okuma yüzdesi, günlük yazma yüzdesi, başarılı/başarısız/engellenen güvenlik duvarı bağlantıları, oturum yüzdesi, çalışan yüzdesi, depolama, depolama yüzdesi, XTP depolama yüzdesi ve kilitlenmeler |

### <a name="advanced-metrics"></a>Gelişmiş ölçümler

Gelişmiş ölçümler hakkındaki ayrıntılar için aşağıdaki tabloya bakın.

|**Ölçüm**|**Ölçüm görünen adı**|**Açıklama**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|SQL işlem çekirdeği yüzdesi|SQL işlemi için işletim sistemi tarafından ölçülen CPU kullanım yüzdesi.|
|sqlserver_process_memory_percent<sup>1</sup> |SQL işlem belleği yüzdesi|SQL işlemi için işletim sistemi tarafından ölçülen bellek kullanım yüzdesi.|
|tempdb_data_size<sup>2</sup>| Tempdb veri dosyası boyutu kilobayt |Tempdb veri dosyası boyutu kilobayt.|
|tempdb_log_size<sup>2</sup>| Tempdb günlük dosyası boyutu kilobayt |Tempdb günlük dosyası boyutu kilobayt.|
|tempdb_log_used_percent<sup>2</sup>| Kullanılan tempdb günlüğü yüzdesi |Kullanılan tempdb günlüğü yüzdesi.|

<sup>1</sup> Bu ölçüm, DTU tabanlı satın alma modelleri Için 2 sanal çekirdek ve üzeri Ile Vcore satın alma modeli veya 200 DTU ve üzeri kullanan veritabanlarında kullanılabilir.

<sup>2</sup> Bu ölçüm, 2 sanal çekirdek ve üzeri Ile Vcore satın alma modelini kullanan veritabanları IÇIN, DTU tabanlı satın alma modelleri IÇIN 200 DTU ve üzeri ile kullanılabilir. Bu ölçüm, hiper ölçekli veritabanları veya veri ambarları için şu anda kullanılamıyor.

### <a name="basic-logs"></a>Temel Günlükler

Tüm Günlükler için kullanılabilen telemetri ayrıntıları aşağıdaki tablolarda belgelenmiştir. Daha fazla bilgi için bkz. [desteklenen tanılama telemetrisi](#diagnostic-telemetry-for-export).

#### <a name="resource-usage-stats-for-managed-instances"></a>Yönetilen örnekler için kaynak kullanım istatistikleri

|Özellik|Açıklama|
|---|---|
|TenantId|Kiracı KIMLIĞINIZ |
|SourceSystem|Always: Azure|
|TimeGenerated [UTC]|Günlüğün kaydedildiği zaman damgası |
|Tür|Always: AzureDiagnostics |
|ResourceProvider|Kaynak sağlayıcının adı. Always: MICROSOFT. SQL |
|Kategori|Kategorinin adı. Always: ResourceUsageStats |
|Kaynak|Kaynağın adı |
|ResourceType|Kaynak türünün adı. Always: MANAGEDıNSTANCES |
|SubscriptionId|Veritabanı için abonelik GUID 'SI |
|adlı yönetilen örnek,|Veritabanı için kaynak grubunun adı |
|LogicalServerName_s|Yönetilen örnek adı |
|ResourceId|Kaynak URI 'SI |
|SKU_s|SQL yönetilen örnek Ürün SKU 'SU |
|virtual_core_count_s|Kullanılabilir sanal çekirdek sayısı |
|avg_cpu_percent_s|Ortalama CPU yüzdesi |
|reserved_storage_mb_s|Yönetilen örnekteki ayrılmış depolama kapasitesi |
|storage_space_used_mb_s|Yönetilen örnekte kullanılan depolama alanı |
|io_requests_s|IOPS sayısı |
|io_bytes_read_s|IOPS bayt okuma |
|io_bytes_written_s|Bayt yazılan ıOPS |

#### <a name="query-store-runtime-statistics"></a>Sorgu deposu çalışma zamanı istatistikleri

|Özellik|Açıklama|
|---|---|
|TenantId|Kiracı KIMLIĞINIZ |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Günlüğün kaydedildiği zaman damgası |
|Tür|Always: AzureDiagnostics |
|ResourceProvider|Kaynak sağlayıcının adı. Always: MICROSOFT. SQL |
|Kategori|Kategorinin adı. Always: QueryStoreRuntimeStatistics |
|OperationName|İşlemin adı. Always: QueryStoreRuntimeStatisticsEvent |
|Kaynak|Kaynağın adı |
|ResourceType|Kaynak türünün adı. Always: sunucular/VERITABANLARı |
|SubscriptionId|Veritabanı için abonelik GUID 'SI |
|adlı yönetilen örnek,|Veritabanı için kaynak grubunun adı |
|LogicalServerName_s|Veritabanı için sunucu adı |
|ElasticPoolName_s|Veritabanı için elastik havuzun adı (varsa) |
|DatabaseName_s|Veritabanının adı |
|ResourceId|Kaynak URI 'SI |
|query_hash_s|Sorgu karması |
|query_plan_hash_s|Sorgu planı karması |
|statement_sql_handle_s|Ekstre SQL tanıtıcısı |
|interval_start_time_d|1900-1-1 arasındaki onay işareti sayısında aralığın DateTimeOffset değerini Başlat |
|interval_end_time_d|1900-1-1 'den onay işareti sayısı cinsinden aralığın bitiş aralığı. |
|logical_io_writes_d|Toplam mantıksal GÇ yazma sayısı |
|max_logical_io_writes_d|Yürütme başına en fazla mantıksal GÇ yazma sayısı |
|physical_io_reads_d|Toplam fiziksel GÇ okuma sayısı |
|max_physical_io_reads_d|Yürütme başına en fazla mantıksal GÇ okuma sayısı |
|logical_io_reads_d|Toplam mantıksal GÇ okuma sayısı |
|max_logical_io_reads_d|Yürütme başına en fazla mantıksal GÇ okuma sayısı |
|execution_type_d|Yürütme türü |
|count_executions_d|Sorgunun yürütmelerinin sayısı |
|cpu_time_d|Sorgu tarafından mikrosaniye cinsinden tüketilen toplam CPU süresi |
|max_cpu_time_d|Mikrosaniye cinsinden tek bir yürütme ile en fazla CPU süresi tüketicisi |
|dop_d|Paralellik derecenin toplamı |
|max_dop_d|Tek yürütme için kullanılan en yüksek paralellik derecesi |
|rowcount_d|Döndürülen toplam satır sayısı |
|max_rowcount_d|Tek yürütmede döndürülen en fazla satır sayısı |
|query_max_used_memory_d|KB cinsinden kullanılan toplam bellek miktarı |
|max_query_max_used_memory_d|KB cinsinden tek bir yürütme tarafından kullanılan maksimum bellek miktarı |
|duration_d|Mikrosaniye cinsinden toplam yürütme süresi |
|max_duration_d|Tek yürütmenin en fazla yürütme süresi |
|num_physical_io_reads_d|Toplam fiziksel okuma sayısı |
|max_num_physical_io_reads_d|Yürütme başına en fazla fiziksel okuma sayısı |
|log_bytes_used_d|Kullanılan günlük baytlarının toplam miktarı |
|max_log_bytes_used_d|Yürütme başına kullanılan günlük baytlarının maksimum miktarı |
|query_id_d|Sorgu deposundaki sorgunun KIMLIĞI |
|plan_id_d|Sorgu deposundaki planın KIMLIĞI |

[Sorgu deposu çalışma zamanı istatistikleri verileri](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)hakkında daha fazla bilgi edinin.

#### <a name="query-store-wait-statistics"></a>Sorgu deposu bekleme istatistikleri

|Özellik|Açıklama|
|---|---|
|TenantId|Kiracı KIMLIĞINIZ |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Günlüğün kaydedildiği zaman damgası |
|Tür|Always: AzureDiagnostics |
|ResourceProvider|Kaynak sağlayıcının adı. Always: MICROSOFT. SQL |
|Kategori|Kategorinin adı. Always: QueryStoreWaitStatistics |
|OperationName|İşlemin adı. Always: QueryStoreWaitStatisticsEvent |
|Kaynak|Kaynağın adı |
|ResourceType|Kaynak türünün adı. Always: sunucular/VERITABANLARı |
|SubscriptionId|Veritabanı için abonelik GUID 'SI |
|adlı yönetilen örnek,|Veritabanı için kaynak grubunun adı |
|LogicalServerName_s|Veritabanı için sunucu adı |
|ElasticPoolName_s|Veritabanı için elastik havuzun adı (varsa) |
|DatabaseName_s|Veritabanının adı |
|ResourceId|Kaynak URI 'SI |
|wait_category_s|Bekleme kategorisi |
|is_parameterizable_s|Sorgu parametreleştirilebilir mi |
|statement_type_s|Deyimin türü |
|statement_key_hash_s|Ekstre anahtarı karması |
|exec_type_d|Yürütme türü |
|total_query_wait_time_ms_d|Belirli bir bekleme kategorisindeki sorgunun toplam bekleme süresi |
|max_query_wait_time_ms_d|Belirli bir bekleme kategorisinde tek tek yürütmede sorgunun en fazla bekleme süresi |
|query_param_type_d|0 |
|query_hash_s|Sorgu deposunda sorgu karması |
|query_plan_hash_s|Sorgu deposunda sorgu planı karması |
|statement_sql_handle_s|Sorgu deposundaki ekstre tanıtıcısı |
|interval_start_time_d|1900-1-1 arasındaki onay işareti sayısında aralığın DateTimeOffset değerini Başlat |
|interval_end_time_d|1900-1-1 'den onay işareti sayısı cinsinden aralığın bitiş aralığı. |
|count_executions_d|Sorgunun yürütmelerinin sayısı |
|query_id_d|Sorgu deposundaki sorgunun KIMLIĞI |
|plan_id_d|Sorgu deposundaki planın KIMLIĞI |

[Sorgu deposu bekleme istatistikleri verileri](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)hakkında daha fazla bilgi edinin.

#### <a name="errors-dataset"></a>Hatalar veri kümesi

|Özellik|Açıklama|
|---|---|
|TenantId|Kiracı KIMLIĞINIZ |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Günlüğün kaydedildiği zaman damgası |
|Tür|Always: AzureDiagnostics |
|ResourceProvider|Kaynak sağlayıcının adı. Always: MICROSOFT. SQL |
|Kategori|Kategorinin adı. Always: hatalar |
|OperationName|İşlemin adı. Always: ErrorEvent |
|Kaynak|Kaynağın adı |
|ResourceType|Kaynak türünün adı. Always: sunucular/VERITABANLARı |
|SubscriptionId|Veritabanı için abonelik GUID 'SI |
|adlı yönetilen örnek,|Veritabanı için kaynak grubunun adı |
|LogicalServerName_s|Veritabanı için sunucu adı |
|ElasticPoolName_s|Veritabanı için elastik havuzun adı (varsa) |
|DatabaseName_s|Veritabanının adı |
|ResourceId|Kaynak URI 'SI |
|İleti|Düz metinde hata iletisi |
|user_defined_b|Hata Kullanıcı tanımlı biti |
|error_number_d|Hata kodu |
|Önem derecesi|Hatanın önem derecesi |
|state_d|Hatanın durumu |
|query_hash_s|Varsa, başarısız sorgunun sorgu karması |
|query_plan_hash_s|Varsa, başarısız sorgunun sorgu planı karması |

[SQL hata iletileri](/sql/relational-databases/errors-events/database-engine-events-and-errors)hakkında daha fazla bilgi edinin.

#### <a name="database-wait-statistics-dataset"></a>Veritabanı bekleme istatistikleri veri kümesi

|Özellik|Açıklama|
|---|---|
|TenantId|Kiracı KIMLIĞINIZ |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Günlüğün kaydedildiği zaman damgası |
|Tür|Always: AzureDiagnostics |
|ResourceProvider|Kaynak sağlayıcının adı. Always: MICROSOFT. SQL |
|Kategori|Kategorinin adı. Always: DatabaseWaitStatistics |
|OperationName|İşlemin adı. Always: DatabaseWaitStatisticsEvent |
|Kaynak|Kaynağın adı |
|ResourceType|Kaynak türünün adı. Always: sunucular/VERITABANLARı |
|SubscriptionId|Veritabanı için abonelik GUID 'SI |
|adlı yönetilen örnek,|Veritabanı için kaynak grubunun adı |
|LogicalServerName_s|Veritabanı için sunucu adı |
|ElasticPoolName_s|Veritabanı için elastik havuzun adı (varsa) |
|DatabaseName_s|Veritabanının adı |
|ResourceId|Kaynak URI 'SI |
|wait_type_s|Bekleme türünün adı |
|start_utc_date_t [UTC]|Ölçülen dönem başlangıç zamanı |
|end_utc_date_t [UTC]|Ölçülen dönem bitiş zamanı |
|delta_max_wait_time_ms_d|Yürütme başına en fazla beklenen süre |
|delta_signal_wait_time_ms_d|Toplam sinyal bekleme süresi |
|delta_wait_time_ms_d|Dönemdeki toplam bekleme süresi |
|delta_waiting_tasks_count_d|Bekleyen görev sayısı |

[Veritabanı bekleme istatistikleri](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)hakkında daha fazla bilgi edinin.

#### <a name="time-outs-dataset"></a>Zaman aşımları veri kümesi

|Özellik|Açıklama|
|---|---|
|TenantId|Kiracı KIMLIĞINIZ |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Günlüğün kaydedildiği zaman damgası |
|Tür|Always: AzureDiagnostics |
|ResourceProvider|Kaynak sağlayıcının adı. Always: MICROSOFT. SQL |
|Kategori|Kategorinin adı. Always: zaman aşımları |
|OperationName|İşlemin adı. Always: TimeoutEvent |
|Kaynak|Kaynağın adı |
|ResourceType|Kaynak türünün adı. Always: sunucular/VERITABANLARı |
|SubscriptionId|Veritabanı için abonelik GUID 'SI |
|adlı yönetilen örnek,|Veritabanı için kaynak grubunun adı |
|LogicalServerName_s|Veritabanı için sunucu adı |
|ElasticPoolName_s|Veritabanı için elastik havuzun adı (varsa) |
|DatabaseName_s|Veritabanının adı |
|ResourceId|Kaynak URI 'SI |
|error_state_d|Hata durum kodu |
|query_hash_s|Varsa sorgu karması |
|query_plan_hash_s|Varsa sorgu planı karması |

#### <a name="blockings-dataset"></a>Blok veri kümesi

|Özellik|Açıklama|
|---|---|
|TenantId|Kiracı KIMLIĞINIZ |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Günlüğün kaydedildiği zaman damgası |
|Tür|Always: AzureDiagnostics |
|ResourceProvider|Kaynak sağlayıcının adı. Always: MICROSOFT. SQL |
|Kategori|Kategorinin adı. Always: bloklar |
|OperationName|İşlemin adı. Always: BlockEvent |
|Kaynak|Kaynağın adı |
|ResourceType|Kaynak türünün adı. Always: sunucular/VERITABANLARı |
|SubscriptionId|Veritabanı için abonelik GUID 'SI |
|adlı yönetilen örnek,|Veritabanı için kaynak grubunun adı |
|LogicalServerName_s|Veritabanı için sunucu adı |
|ElasticPoolName_s|Veritabanı için elastik havuzun adı (varsa) |
|DatabaseName_s|Veritabanının adı |
|ResourceId|Kaynak URI 'SI |
|lock_mode_s|Sorgu tarafından kullanılan kilit modu |
|resource_owner_type_s|Kilidin sahibi |
|blocked_process_filtered_s|Engellenen işlem raporu XML 'i |
|duration_d|Mikrosaniye cinsinden kilit süresi |

#### <a name="deadlocks-dataset"></a>Kilitlenmeler veri kümesi

|Özellik|Açıklama|
|---|---|
|TenantId|Kiracı KIMLIĞINIZ |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC] |Günlüğün kaydedildiği zaman damgası |
|Tür|Always: AzureDiagnostics |
|ResourceProvider|Kaynak sağlayıcının adı. Always: MICROSOFT. SQL |
|Kategori|Kategorinin adı. Always: kilitlenmeler |
|OperationName|İşlemin adı. Always: DeadlockEvent |
|Kaynak|Kaynağın adı |
|ResourceType|Kaynak türünün adı. Always: sunucular/VERITABANLARı |
|SubscriptionId|Veritabanı için abonelik GUID 'SI |
|adlı yönetilen örnek,|Veritabanı için kaynak grubunun adı |
|LogicalServerName_s|Veritabanı için sunucu adı |
|ElasticPoolName_s|Veritabanı için elastik havuzun adı (varsa) |
|DatabaseName_s|Veritabanının adı |
|ResourceId|Kaynak URI 'SI |
|deadlock_xml_s|Kilitlenme rapor XML 'i |

#### <a name="automatic-tuning-dataset"></a>Otomatik ayarlama veri kümesi

|Özellik|Açıklama|
|---|---|
|TenantId|Kiracı KIMLIĞINIZ |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Günlüğün kaydedildiği zaman damgası |
|Tür|Always: AzureDiagnostics |
|ResourceProvider|Kaynak sağlayıcının adı. Always: MICROSOFT. SQL |
|Kategori|Kategorinin adı. Always: otomatik olarak ayarlama |
|Kaynak|Kaynağın adı |
|ResourceType|Kaynak türünün adı. Always: sunucular/VERITABANLARı |
|SubscriptionId|Veritabanı için abonelik GUID 'SI |
|adlı yönetilen örnek,|Veritabanı için kaynak grubunun adı |
|LogicalServerName_s|Veritabanı için sunucu adı |
|LogicalDatabaseName_s|Veritabanının adı |
|ElasticPoolName_s|Veritabanı için elastik havuzun adı (varsa) |
|DatabaseName_s|Veritabanının adı |
|ResourceId|Kaynak URI 'SI |
|RecommendationHash_s|Otomatik ayarlama önerisinin benzersiz karması |
|OptionName_s|Otomatik ayarlama işlemi |
|Schema_s|Veritabanı şeması |
|Table_s|Etkilenen tablo |
|IndexName_s|Dizin adı |
|IndexColumns_s|Sütun adı |
|IncludedColumns_s|Dahil edilen sütunlar |
|EstimatedImpact_s|Otomatik ayarlama önerisi JSON ' inin tahmini etkisi |
|Event_s|Otomatik ayarlama olayının türü |
|Timestamp_t|Son güncelleme zaman damgası |

#### <a name="intelligent-insights-dataset"></a>Akıllı İçgörüler veri kümesi

[Akıllı içgörüler günlük biçimi](intelligent-insights-use-diagnostics-log.md)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

Günlüğe kaydetmeyi etkinleştirme ve çeşitli Azure hizmetleri tarafından desteklenen ölçümleri ve günlük kategorilerini anlama hakkında bilgi edinmek için bkz.:

- [Microsoft Azure ölçümlerine genel bakış](../../azure-monitor/data-platform.md)
- [Azure platform günlüklerine genel bakış](../../azure-monitor/essentials/platform-logs-overview.md)

Event Hubs hakkında bilgi edinmek için şunu okuyun:

- [Azure Event Hubs nedir?](../../event-hubs/event-hubs-about.md)
- [Event Hubs kullanmaya başlayın](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Log Analytics 'ten telemetri temelinde uyarıları ayarlamayı öğrenmek için bkz.:

- [Azure SQL veritabanı ve Azure SQL yönetilen örneği için uyarı oluşturma](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)