---
title: 'Öğretici: bir kod satırı olmadan Azure Veri Gezgini izleme verilerini alma'
description: Bu öğreticide, verileri tek bir kod satırı olmadan Azure Veri Gezgini izlemeyi ve verileri sorgulamayı öğreneceksiniz.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 11/17/2019
ms.openlocfilehash: 97faa445a286574aa5fc05d084d21c0740bc8a8b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173849"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Öğretici: Azure Veri Gezgini veri alma ve sorgu izleme 

Bu öğretici, tanılama ve etkinlik günlüklerinden bir Azure Veri Gezgini kümesine kod yazmadan veri alma hakkında öğretir. Bu basit alma yöntemiyle, veri analizi için Azure Veri Gezgini 'yi sorgulamaya hızlı bir başlangıç yapabilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure Veri Gezgini veritabanında tablolar ve alma eşlemesi oluşturun.
> * Alınan verileri bir güncelleştirme ilkesi kullanarak biçimlendirin.
> * Bir [Olay Hub 'ı](/azure/event-hubs/event-hubs-about) oluşturun ve Azure Veri Gezgini bağlayın.
> * Azure Izleyici [Tanılama ölçümleri ve günlükleri](/azure/azure-monitor/platform/diagnostic-settings) ve [etkinlik günlüklerinden](/azure/azure-monitor/platform/activity-logs-overview)bir olay hub 'ına veri akışı.
> * Alınan verileri Azure Veri Gezgini kullanarak sorgulayın.

> [!NOTE]
> Tüm kaynakları aynı Azure konumunda veya bölgede oluşturun. 

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* [Azure Veri Gezgini kümesi ve veritabanı](create-cluster-database-portal.md). Bu öğreticide, veritabanı adı *TestDatabase*' dir.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Azure Izleyici veri sağlayıcısı: Tanılama ölçümleri ve Günlükler ve etkinlik günlükleri

Azure Izleyici tanılama ölçümleri ve günlükleri ve etkinlik günlükleri tarafından belirtilen verileri görüntüleyin ve anlayın. Bu veri şemalarını temel alan bir giriş işlem hattı oluşturacaksınız. Günlükteki her olayın bir kayıt dizisi olduğunu unutmayın. Bu kayıt dizisi daha sonra öğreticide bölünecektir.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Tanılama ölçümleri ve Günlükler ve etkinlik günlükleri örnekleri

Azure tanılama ölçümleri ve günlükleri ve etkinlik günlükleri bir Azure hizmeti tarafından dağıtılır ve bu hizmetin çalışması hakkındaki verileri sağlar. 

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Tanılama ölçümleri](#tab/diagnostic-metrics)
#### <a name="example"></a>Örnek

Tanılama ölçümleri 1 dakikalık bir zaman çizgisi ile toplanır. Sorgu süresi üzerinde bir Azure Veri Gezgini metrik olay şeması örneği aşağıda verilmiştir:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Tanılama günlükleri](#tab/diagnostic-logs)
#### <a name="example"></a>Örnek

Aşağıda Azure Veri Gezgini [Tanılama alma günlüğüne](using-diagnostic-logs.md#diagnostic-logs-schema)bir örnek verilmiştir:

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logstabactivity-logs"></a>[Etkinlik günlükleri](#tab/activity-logs)
#### <a name="example"></a>Örnek

Azure etkinlik günlükleri, aboneliğinizdeki kaynaklarda gerçekleştirilen işlemlere ilişkin Öngörüler sağlayan abonelik düzeyi günlüklerdir. Aşağıda, erişimi denetlemeye yönelik bir etkinlik günlüğü olayına bir örnek verilmiştir:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Azure Veri Gezgini bir giriş işlem hattı ayarlama

Azure Veri Gezgini işlem hattının kurulması, [tablo oluşturma ve veri](/azure/data-explorer/ingest-sample-data#ingest-data)alımı gibi çeşitli adımları içerir. Ayrıca verileri işleyebilir, eşleyebilirsiniz ve güncelleştirebilirsiniz.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Azure Veri Gezgini Web Kullanıcı arabirimine bağlanma

Azure Veri Gezgini *TestDatabase* veritabanınızda, Azure Veri Gezgini Web Kullanıcı arabirimini açmak için **sorgula** ' yı seçin.

![Sorgu sayfası](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Hedef tabloları oluşturma

Azure Izleyici günlüklerinin yapısı tablosal değildir. Verileri işleyebilir ve her bir olayı bir veya daha fazla kayıtta genişletebilirsiniz. Ham veriler, etkinlik günlükleri için *Activitylogsrawrecords* adlı bir ara tabloya alınır ve tanılama ölçümleri ve Günlükler Için *diagnosticrawrecords* . Bu sırada, veriler değiştirilecek ve genişletilir. Bir güncelleştirme ilkesi kullanarak, genişletilmiş veriler etkinlik günlükleri için *activitylogs* tablosuna, tanılama ölçümleri Için *diagnosticölçümler* ve tanılama günlükleri için *diagnosticlogs* ' a alınacaktır. Bu, tanılama ölçümlerini ve günlüklerini almak için, etkinlik günlüklerini ve üç ayrı tabloyu oluşturmak üzere iki ayrı tablo oluşturmanız gerektiği anlamına gelir.

Azure Veri Gezgini veritabanında hedef tabloları oluşturmak için Azure Veri Gezgini Web Kullanıcı arabirimini kullanın.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Tanılama ölçümleri](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Tanılama ölçümleri için tablo oluşturma

1. *TestDatabase* veritabanında, tanılama ölçümleri kayıtlarını depolamak Için *diagnosticölçümler* adlı bir tablo oluşturun. Aşağıdaki `.create table` denetim komutunu kullanın:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Tabloyu oluşturmak için **Çalıştır** ' ı seçin.

    ![Sorgu çalıştırma](media/ingest-data-no-code/run-query.png)

1. Aşağıdaki sorguyu kullanarak veri işleme için *TestDatabase* veritabanında *diagnosticrawrecords* adlı ara veri tablosunu oluşturun. Tabloyu oluşturmak için **Çalıştır** ' ı seçin.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Ara tablo için sıfır [bekletme ilkesi](/azure/kusto/management/retention-policy) ayarlayın:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Tanılama günlükleri](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Tanılama günlükleri için tablo oluşturma 

1. *TestDatabase* veritabanında, tanılama günlüğü kayıtlarını depolamak Için *diagnosticlogs* adlı bir tablo oluşturun. Aşağıdaki `.create table` denetim komutunu kullanın:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Tabloyu oluşturmak için **Çalıştır** ' ı seçin.

1. Aşağıdaki sorguyu kullanarak veri işleme için *TestDatabase* veritabanında *diagnosticrawrecords* adlı ara veri tablosunu oluşturun. Tabloyu oluşturmak için **Çalıştır** ' ı seçin.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Ara tablo için sıfır [bekletme ilkesi](/azure/kusto/management/retention-policy) ayarlayın:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logstabactivity-logs"></a>[Etkinlik günlükleri](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Etkinlik günlükleri için tablo oluşturma 

1. Etkinlik günlüğü kayıtlarını almak için *TestDatabase* veritabanında *activitylogs* adlı bir tablo oluşturun. Tabloyu oluşturmak için aşağıdaki Azure Veri Gezgini sorgusunu çalıştırın:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Veri işleme için *TestDatabase* veritabanında *Activitylogsrawrecords* adlı ara veri tablosunu oluşturun:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Ara tablo için sıfır [bekletme ilkesi](/azure/kusto/management/retention-policy) ayarlayın:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Tablo eşlemeleri oluşturma

 Veri biçimi `json`olduğundan, veri eşleme gereklidir. `json` eşleme her JSON yolunu bir tablo sütunu adıyla eşleştirir.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Tanılama ölçümleri/tanılama günlükleri](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Tanılama ölçümlerini ve günlüklerini tabloyla eşleyin

Tanılama ölçüsünü ve günlük verilerini tabloya eşlemek için aşağıdaki sorguyu kullanın:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logstabactivity-logs"></a>[Etkinlik günlükleri](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Etkinlik günlüklerini tabloyla eşleme

Etkinlik günlüğü verilerini tabloyla eşlemek için aşağıdaki sorguyu kullanın:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Ölçüm ve günlük verileri için güncelleştirme ilkesi oluşturma

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Tanılama ölçümleri](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Tanılama ölçümleri için veri güncelleştirme ilkesi oluşturma

1. Koleksiyondaki her bir değerin ayrı bir satır alması için, tanılama ölçümü kayıtlarının toplanmasını genişleten bir [işlev](/azure/kusto/management/functions) oluşturun. [`mv-expand`](/azure/kusto/query/mvexpandoperator) işlecini kullanın:
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events.time),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events.count),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. [Güncelleştirme ilkesini](/azure/kusto/concepts/updatepolicy) hedef tabloya ekleyin. Bu ilke, sorguyu *Diagnosticrawrecords* ara veri tablosundaki yeni verileri otomatik olarak çalıştırır ve sonuçları *diagnosticölçümler* tablosuna alır:

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True"}]'
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Tanılama günlükleri](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Tanılama günlükleri için veri güncelleştirme ilkesi oluştur

1. Koleksiyondaki her bir değerin ayrı bir satır alması için tanılama günlüğü kayıtlarının toplanmasını genişleten bir [işlev](/azure/kusto/management/functions) oluşturun. Azure Veri Gezgini kümesinde alma günlüklerini etkinleştireceksiniz ve alma [günlükleri şemasını](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema)kullanacaksınız. Başarılı ve başarısız alma için bir tablo oluşturacaksınız, bazı alanlar başarılı alma işlemi için boş olur (örneğin, hata kodu). [`mv-expand`](/azure/kusto/query/mvexpandoperator) işlecini kullanın:

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events.time),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. [Güncelleştirme ilkesini](/azure/kusto/concepts/updatepolicy) hedef tabloya ekleyin. Bu ilke, sorguyu *Diagnosticrawrecords* ara veri tablosundaki yeni verileri otomatik olarak çalıştırır ve sonuçları *diagnosticlogs* tablosuna alır:

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True"}]'
    ```

# <a name="activity-logstabactivity-logs"></a>[Etkinlik günlükleri](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Etkinlik günlükleri için veri güncelleştirme ilkesi oluştur

1. Koleksiyondaki her değerin ayrı bir satır alması için etkinlik günlüğü kayıtlarının toplanmasını genişleten bir [işlev](/azure/kusto/management/functions) oluşturun. [`mv-expand`](/azure/kusto/query/mvexpandoperator) işlecini kullanın:

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events.time),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. [Güncelleştirme ilkesini](/azure/kusto/concepts/updatepolicy) hedef tabloya ekleyin. Bu ilke, sorguyu *Activitylogsrawrecords* ara veri tablosundaki yeni alınan verilerde otomatik olarak çalıştırır ve sonuçları *activitylogs* tablosuna alır:

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Azure Event Hubs ad alanı oluşturma

Azure Tanılama ayarları, ölçüm ve günlüklerin bir depolama hesabına veya bir olay hub 'ına verilmesini sağlar. Bu öğreticide, ölçümleri ve günlükleri bir olay hub 'ı aracılığıyla yönlendiririz. Aşağıdaki adımlarda, tanılama ölçümleri ve Günlükler için bir Event Hubs ad alanı ve bir olay hub 'ı oluşturacaksınız. Azure Izleyici, etkinlik günlükleri için Event hub *Insights-işletimsel günlüklerini* oluşturacak.

1. Azure portal bir Azure Resource Manager şablonu kullanarak bir olay hub 'ı oluşturun. Bu makaledeki adımların geri kalanını izlemek için, **Azure 'A dağıt** düğmesine sağ tıklayın ve sonra **Yeni pencerede aç**' ı seçin. **Azure 'A dağıt** düğmesi sizi Azure Portal götürür.

    [![Azure 'a dağıt düğmesi](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Tanılama günlükleri için bir Event Hubs ad alanı ve bir olay hub 'ı oluşturun.

    ![Olay Hub 'ı oluşturma](media/ingest-data-no-code/event-hub.png)

1. Formu aşağıdaki bilgilerle doldurun. Aşağıdaki tabloda listelenmeyen tüm ayarlar için varsayılan değerleri kullanın.

    **Ayar** | **Önerilen değer** | **Açıklama**
    |---|---|---|
    | **Abonelik** | *Aboneliğiniz* | Olay hub'ınız için kullanmak istediğiniz Azure aboneliğini seçin.|
    | **Kaynak grubu** | *test-resource-group* | Yeni bir kaynak grubu oluşturun. |
    | **Konum** | Gereksinimlerinize en uygun bölgeyi seçin. | Event Hubs ad alanını diğer kaynaklarla aynı konumda oluşturun.
    | **Ad alanı adı** | *AzureMonitoringData* | Ad alanınızı tanımlayan benzersiz bir ad seçin.
    | **Olay Hub 'ı adı** | *DiagnosticData* | Olay hub'ı benzersiz bir kapsayıcı kapsamı sunan ad alanında bulunur. |
    | **Tüketici grubu adı** | *adxpipeline* | Bir tüketici grubu adı oluşturun. Tüketici grupları birden fazla tüketici uygulamasının ayrı olay akışı görünümüne sahip olmasını sağlar. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Azure Izleyici ölçümlerini ve günlüklerini Olay Hub 'ınıza bağlama

Artık tanılama ölçümlerini ve günlüklerinizi ve etkinlik günlüklerinizi Olay Hub 'ına bağlamanız gerekir.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Tanılama ölçümleri/tanılama günlükleri](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Tanılama ölçümlerini ve günlüklerini Olay Hub 'ınıza bağlama

Ölçümlerin dışarı aktarılacağı kaynağı seçin. Birçok kaynak türü Event Hubs ad alanı, Azure Key Vault, Azure IoT Hub ve Azure Veri Gezgini kümeleri de dahil olmak üzere tanılama verilerini dışa aktarmayı destekler. Bu öğreticide, kaynağı olarak bir Azure Veri Gezgini kümesi kullanacağız, sorgu performans ölçümlerini ve Alım sonuçları günlüklerini inceleyeceğiz.

1. Azure portal kusto kümenizi seçin.
1. **Tanılama ayarları**' nı seçin ve sonra **tanılamayı aç** bağlantısını seçin. 

    ![Tanılama ayarları](media/ingest-data-no-code/diagnostic-settings.png)

1. **Tanılama ayarları** bölmesi açılır. Aşağıdaki adımları uygulayın:
   1. Tanılama günlük verilerinize *Adxexporteddata*adı verin.
   1. **Günlük**altında hem **SucceededIngestion** hem de **failedingestion** onay kutularını seçin.
   1. **Ölçüm**altında **sorgu performansı** onay kutusunu seçin.
   1. **Bir olay hub 'ının akışını** seçin onay kutusu.
   1. **Yapılandır**' ı seçin.

      ![Tanılama ayarları bölmesi](media/ingest-data-no-code/diagnostic-settings-window.png)

1. **Olay Hub 'ı Seç** bölmesinde, tanılama günlüklerinden veri aktarma işlemini oluşturduğunuz Olay Hub 'ına yapılandırın:
    1. **Olay Hub 'ı ad alanı seç** listesinde *AzureMonitoringData*' yi seçin.
    1. **Olay Hub 'ı adını seçin** listesinde, *diagnosticdata*öğesini seçin.
    1. **Olay Hub 'ı ilke adı Seç** listesinde **Rootmanagersharedaccesskey**' i seçin.
    1. **Tamam**’ı seçin.

1. **Kaydet**’i seçin.

# <a name="activity-logstabactivity-logs"></a>[Etkinlik günlükleri](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Etkinlik günlüklerini Olay Hub 'ınıza bağlama

1. Azure portal sol menüsünde **etkinlik günlüğü**' nü seçin.
1. **Etkinlik günlüğü** penceresi açılır. **Olay Hub 'ına Aktar ' ı**seçin.

    ![Etkinlik günlüğü penceresi](media/ingest-data-no-code/activity-log.png)

1. **Etkinlik günlüğünü dışarı aktar** penceresi açılır:
 
    ![Etkinlik günlüğünü dışarı aktar penceresi](media/ingest-data-no-code/export-activity-log.png)

1. **Etkinlik günlüğünü dışarı aktar** penceresinde aşağıdaki adımları uygulayın:
      1. Aboneliğinizi seçin.
      1. **Bölgeler** listesinde **Tümünü Seç**' i seçin.
      1. **Bir olay hub 'ına aktar** onay kutusunu seçin.
      1. **Olay Hub 'ı Seç** bölmesini açmak için **Service Bus ad alanı seç** ' i seçin.
      1. **Olay Hub 'ı Seç** bölmesinde, aboneliğinizi seçin.
      1. **Olay Hub 'ı ad alanı seç** listesinde *AzureMonitoringData*' yi seçin.
      1. **Olay Hub 'ı ilke adı Seç** listesinde, varsayılan olay hub 'ı ilke adını seçin.
      1. **Tamam**’ı seçin.
      1. Pencerenin sol üst köşesinde **Kaydet**' i seçin.
   *Insights-işletimsel-logs* adlı bir olay hub 'ı oluşturulur.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Bkz. Olay Hub 'larınız için veri akışı

1. Bağlantı tanımlanana kadar birkaç dakika bekleyin ve Olay Hub 'ına etkinlik günlüğü dışarı aktarma işlemi tamamlanmıştır. Oluşturduğunuz Olay Hub 'larını görmek için Event Hubs ad alanına gidin.

    ![Oluşturulan olay hub 'ları](media/ingest-data-no-code/event-hubs-created.png)

1. Bkz. Olay Hub 'ınıza veri akışı.

    ![Olay Hub 'ı verileri](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Bir olay hub 'ını Azure 'a bağlama Veri Gezgini

Artık tanılama ölçümleri ve günlükleriniz ve etkinlik günlükleriniz için veri bağlantıları oluşturmanız gerekir.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Tanılama ölçümleri ve Günlükler ve etkinlik günlükleri için veri bağlantısı oluşturma

1. *Kustodocs*adlı Azure Veri Gezgini kümenizde Sol menüdeki **veritabanları** ' nı seçin.
1. **Veritabanları** penceresinde *TestDatabase* veritabanınızı seçin.
1. Sol taraftaki menüden **veri**alımı ' nı seçin.
1. **Veri** alma penceresinde **+ veri bağlantısı ekle**' ye tıklayın.
1. **Veri bağlantısı** penceresinde, aşağıdaki bilgileri girin:

    ![Olay Hub 'ı veri bağlantısı](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Tanılama ölçümleri/tanılama günlükleri](#tab/diagnostic-metrics+diagnostic-logs) 

1. **Veri bağlantısı** penceresinde aşağıdaki ayarları kullanın:

    Veri kaynağı:

    **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | **Veri bağlantısı adı** | *DiagnosticsLogsConnection* | Azure Veri Gezgini'nde oluşturmak istediğiniz bağlantının adı.|
    | **Olay Hub 'ı ad alanı** | *AzureMonitoringData* | Önceden seçtiğiniz ve ad alanınızı tanımlayan ad. |
    | **Olay Hub 'ı** | *DiagnosticData* | Oluşturduğunuz olay hub'ı. |
    | **Tüketici grubu** | *adxpipeline* | Oluşturduğunuz olay hub'ında tanımlanan tüketici grubu. |
    | | |

    Hedef tablo:

    İki yönlendirme seçeneği vardır: *statik* ve *dinamik*. Bu öğreticide, tablo adını, veri biçimini ve eşlemeyi belirttiğiniz statik yönlendirme (varsayılan) kullanacaksınız. **Verilerimde yönlendirme bilgileri var** seçeneğini işaretlemeyin.

     **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | **Tablo** | *DiagnosticRawRecords* | *TestDatabase* veritabanında oluşturduğunuz tablo. |
    | **Veri biçimi** | *JSON* | Tabloda kullanılan biçim. |
    | **Sütun eşleme** | *Diagnosticrawmisinizmapping* | Gelen JSON verilerini *Diagnosticrawrecords* tablosunun sütun adlarıyla ve veri türleriyle eşleyen *TestDatabase* veritabanında oluşturduğunuz eşleme.|
    | | |

1. **Oluştur**'u seçin.  

# <a name="activity-logstabactivity-logs"></a>[Etkinlik günlükleri](#tab/activity-logs)

1. **Veri bağlantısı** penceresinde aşağıdaki ayarları kullanın:

    Veri kaynağı:

    **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | **Veri bağlantısı adı** | *ActivityLogsConnection* | Azure Veri Gezgini'nde oluşturmak istediğiniz bağlantının adı.|
    | **Olay Hub 'ı ad alanı** | *AzureMonitoringData* | Önceden seçtiğiniz ve ad alanınızı tanımlayan ad. |
    | **Olay Hub 'ı** | *Öngörüler-işletimsel-Günlükler* | Oluşturduğunuz olay hub'ı. |
    | **Tüketici grubu** | *$Default* | Varsayılan Tüketici grubu. Gerekirse, farklı bir tüketici grubu oluşturabilirsiniz. |
    | | |

    Hedef tablo:

    İki yönlendirme seçeneği vardır: *statik* ve *dinamik*. Bu öğreticide, tablo adını, veri biçimini ve eşlemeyi belirttiğiniz statik yönlendirme (varsayılan) kullanacaksınız. **Verilerimde yönlendirme bilgileri var** seçeneğini işaretlemeyin.

     **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | **Tablo** | *ActivityLogsRawRecords* | *TestDatabase* veritabanında oluşturduğunuz tablo. |
    | **Veri biçimi** | *JSON* | Tabloda kullanılan biçim. |
    | **Sütun eşleme** | *Activitylogsrawmisinizmapping* | Gelen JSON verilerini *Activitylogsrawrecords* tablosunun sütun adlarıyla ve veri türleriyle eşleyen *TestDatabase* veritabanında oluşturduğunuz eşleme.|
    | | |

1. **Oluştur**'u seçin.  
---

## <a name="query-the-new-tables"></a>Yeni tabloları sorgulama

Artık veri akışı olan bir işlem hattına sahipsiniz. Küme aracılığıyla alma işlemi varsayılan olarak 5 dakika sürer, bu nedenle sorguya başlamadan önce verilerin birkaç dakika boyunca akmasını sağlar.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Tanılama ölçümleri](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Tanılama ölçümleri tablosunu sorgulama

Aşağıdaki sorgu, Azure Veri Gezgini 'de tanılama ölçümü kayıtlarından sorgu süresi verilerini analiz eder:

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Sorgu sonuçları:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Tanılama günlükleri](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Tanılama günlükleri tablosunu sorgulama

Bu işlem hattı bir olay hub 'ı aracılığıyla alma işlemi üretir. Bu giriş sonuçlarının sonuçlarını gözden geçireceğiz.
Aşağıdaki sorgu, her Aralık için `Database`, `Table` ve `IngestionSourcePath` bir örneği de dahil olmak üzere dakikada kaç alma yapıldığını analiz eder:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Sorgu sonuçları:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logstabactivity-logs"></a>[Etkinlik günlükleri](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Etkinlik günlükleri tablosunu sorgulama

Aşağıdaki sorgu Azure Veri Gezgini etkinlik günlüğü kayıtlarından verileri analiz eder:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Sorgu sonuçları:

|   |   |
| --- | --- |
|   |  Ort (ort) |
|   | 768,333 |
| | |

---

## <a name="next-steps"></a>Sonraki adımlar

* Azure Veri Gezgini 'de ayıkladığınız verilere Azure [Veri Gezgini yönelik yazma sorgularını](write-queries.md)kullanarak daha fazla sorgu yazmayı öğrenin.
* [Tanılama günlüklerini kullanarak Azure Veri Gezgini alma işlemlerini izleme](using-diagnostic-logs.md)
* [Küme durumunu izlemek için ölçümleri kullanma](using-metrics.md)
