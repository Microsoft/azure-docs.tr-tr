---
title: İş yükünü izleme-Azure portal
description: Azure portal kullanarak SYNAPSE SQL 'i izleme
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4f4c50588a67e2e69d0975c9f4414242ecf23617
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568276"
---
# <a name="monitor-workload---azure-portal"></a>İş yükünü izleme-Azure portal

Bu makalede, iş yükünüzü izlemek için Azure portal nasıl kullanılacağı açıklanır. Bu, [SYNAPSE SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)için Log Analytics kullanarak sorgu yürütmeyi ve iş yükü eğilimlerini araştırmak üzere Azure izleyici günlüklerini ayarlamayı içerir.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
- SQL havuzu: bir SQL havuzu için günlükleri toplayacağız. Sağlanmış bir SQL havuzu yoksa, [SQL havuzu oluşturma](./load-data-from-azure-blob-storage-using-copy.md)' daki yönergelere bakın.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics çalışma alanı oluşturma

Log Analytics çalışma alanları için gezinme dikey penceresine gidin ve bir çalışma alanı oluşturun

![Log Analytics çalışma alanları](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Ekran görüntüsü, Ekle ' yi seçebileceğiniz Log Analytics çalışma alanlarını gösterir.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Ekran görüntüsü, değer girebileceğiniz Log Analytics çalışma alanını gösterir.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Çalışma alanları hakkında daha fazla bilgi için aşağıdaki [belgeleri](../../azure-monitor/logs/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)ziyaret edin.

## <a name="turn-on-resource-logs"></a>Kaynak günlüklerini aç

SQL havuzunuzdaki günlükleri göstermek için tanılama ayarlarını yapılandırın. Günlükler, en sık kullanılan performans sorunlarını giderme DMVs ile eşdeğer telemetri görünümlerinden oluşur. Şu anda Şu görünümler desteklenir:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![Kaynak günlüklerini etkinleştirme](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Günlükler Azure depolama, Stream Analytics veya Log Analytics dağıtılabilir. Bu öğretici için Log Analytics ' yi seçin.

![Günlükleri belirtin](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Sorguları Log Analytics karşı Çalıştır

Log Analytics çalışma alanınıza giderek şunları yapabilirsiniz:

- Günlük sorgularını kullanarak günlükleri çözümleyin ve sorguları yeniden kullanım için kaydedin
- Sorguları yeniden kullanmak üzere Kaydet
- Günlük uyarıları oluşturma
- Sorgu sonuçlarını panoya sabitleme

Günlük sorgularının özellikleri hakkında ayrıntılı bilgi için aşağıdaki [belgeleri](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)ziyaret edin.

![Log Analytics çalışma alanı Düzenleyicisi](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Log Analytics çalışma alanı sorguları](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Örnek günlük sorguları

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>Sonraki adımlar

Azure izleyici günlüklerini ayarlayıp yapılandırdığınıza göre, [Azure panoları](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) takımınızın genelinde paylaşılacak şekilde özelleştirin.