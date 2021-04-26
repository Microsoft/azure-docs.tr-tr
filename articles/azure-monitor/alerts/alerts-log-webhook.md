---
title: Azure uyarılarında günlük uyarıları için Web kancası eylemleri
description: Web kancası eylemi ve kullanılabilir özelleştirmeler ile günlük uyarı gönderimlerini yapılandırmayı açıklar
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1834fb7478fbb9ed435dac4f1e4b43f83e5d2db1
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383578"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Günlük uyarı kuralları için web kancası eylemleri

[Günlük uyarısı](alerts-log.md) , [Web kancası eylem gruplarını yapılandırmayı](./action-groups.md#webhook)destekler. Bu makalede, hangi özelliklerin kullanılabildiğini ve özel bir JSON Web kancasının nasıl yapılandırılacağını açıklayacağız.

> [!NOTE]
> Özel JSON tabanlı Web kancası API sürümünde şu anda desteklenmiyor `2020-05-01-preview` .

> [!NOTE]
> Web kancası tümleştirmelerinizin [ortak uyarı şemasını](../alerts/alerts-common-schema.md) kullanmanız önerilir. Ortak uyarı şeması, Azure Izleyici 'deki tüm uyarı hizmetlerinde tek bir Genişletilebilir ve birleştirilmiş uyarı yüküne sahip olmanın avantajlarından yararlanır. Özel bir JSON yükü tanımlanmış olan günlük uyarıları kuralları için ortak uyarı şemasının etkinleştirilmesi, yük şemasını [burada](../alerts/alerts-common-schema-definitions.md#log-alerts)açıklanan birine geri döndürür. Bu, özel bir JSON yükünün tanımlanmış olmasını istiyorsanız web kancasının ortak uyarı şemasını kullanamayacağı anlamına gelir. Ortak şema etkin olan uyarılar, uyarı başına 256 KB üst boyut sınırına sahiptir, daha büyük uyarı arama sonuçlarını içermez. Arama sonuçları dahil edilmemişse, `LinkToFilteredSearchResultsAPI` `LinkToSearchResultsAPI` sorgu SONUÇLARıNA Log Analytics API aracılığıyla erişmek için veya kullanmanız gerekir.

## <a name="webhook-payload-properties"></a>Web kancası yük özellikleri

Web kancası eylemleri tek bir HTTP POST isteği çağırabilmesini sağlar. Çağrılan hizmet Web kancalarını desteklemelidir ve aldığı yükü nasıl kullanacağınızı bilmelidir.

Varsayılan Web kancası eylem özellikleri ve özel JSON parametresi adları:

| Parametre | Değişken | Açıklama |
|:--- |:--- |:--- |
| *Alertrutaname* |#alertrulename |Uyarı kuralının adı. |
| *Önem Derecesi* |#severity |Tetiklenen günlük uyarısı için önem derecesi kümesi. |
| *AlertThresholdOperator* |#thresholdoperator |Uyarı kuralı için eşik işleci. |
| *AlertThresholdValue* |#thresholdvalue |Uyarı kuralının eşik değeri. |
| *LinkToSearchResults* |#linktosearchresults |Uyarıyı oluşturan sorgudaki kayıtları döndüren analiz portalına bağlantı. |
| *Linktosearchresultsapı* |#linktosearchresultsapi |Uyarıyı oluşturan sorgudaki kayıtları döndüren analiz API 'sine bağlantı. |
| *Linktofilteredsearchresultsuı* |#linktofilteredsearchresultsui |Uyarıyı oluşturan boyut değer birleşimlerine göre filtrelenmiş sorgudan kayıtları döndüren analiz portalına bağlantı. |
| *Linktofilteredsearchresultsapı* |#linktofilteredsearchresultsapi |Uyarıyı oluşturan boyut değer birleşimlerine göre filtrelenmiş sorgudan kayıtları döndüren analiz API 'sine bağlantı. |
| *Ayarlanan resultcount* |#searchresultcount |Arama sonuçlarındaki kayıt sayısı. |
| *Arama aralığı bitiş zamanı* |#searchintervalendtimeutc |UTC olarak sorgunun bitiş saati, aa/gg/yyyy HH: mm: ss "/PM biçiminde. |
| *Arama aralığı* |#searchinterval |Uyarı kuralı için ss: DD: ss biçiminde zaman penceresi. |
| *Arama aralığı başlangıçsaati* |#searchintervalstarttimeutc |Sorgu için, aa/gg/yyyy HH: mm: ss/PM biçimindeki UTC olarak başlangıç zamanı. 
| *SearchQuery* |#searchquery |Uyarı kuralı tarafından kullanılan günlük arama sorgusu. |
| *SearchResults* |"Includesearchresults": true|Sorgu tarafından JSON tablosu olarak döndürülen kayıtlar, ilk 1.000 kayıtla sınırlıdır. "Includesearchresults": true, özel bir JSON Web kancası tanımına en üst düzey özellik olarak eklenir. |
| *Boyutlarına* |"Includedimensions": true|Bu uyarıyı JSON bölümü olarak tetikleyen boyut değer birleşimleri. "Includedimensions": true, özel bir JSON Web kancası tanımına en üst düzey özellik olarak eklenir. |
| *Uyarı türü*| #alerttype | [Ölçüm ölçümü veya sonuç sayısı](./alerts-unified-log.md#measure)olarak yapılandırılan günlük uyarısı kuralının türü.|
| *Workspaceıd* |#workspaceid |Log Analytics çalışma alanınızın KIMLIĞI. |
| *Uygulama Kimliği* |#applicationid |Application Insights uygulamanızın KIMLIĞI. |
| *Abonelik kimliği* |#subscriptionid |Kullanılan Azure aboneliğinizin KIMLIĞI. |

## <a name="custom-webhook-payload-definition"></a>Özel Web kancası yük tanımı

Yukarıdaki parametreleri kullanarak özel bir JSON yükü almak için **Web kancası için özel JSON yükünü dahil et** ' i kullanabilirsiniz. Ek özellikler de oluşturabilirsiniz.
Örneğin, *metin* adlı tek bir parametre içeren aşağıdaki özel yükü belirtebilirsiniz. Bu Web kancası çağrısının çağrı yaptığı hizmet bu parametreyi bekler:

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Bu örnek yük, Web kancasına gönderildiğinde aşağıdakine benzer bir şekilde çözümlenir:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Özel bir Web kancası içindeki değişkenlerin bir JSON Kasası içinde belirtilmesi gerekir. Örneğin, yukarıdaki Web kancası örneğinde "#searchresultcount" başvurmak, uyarı sonuçlarına göre çıkış olur.

Arama sonuçlarını eklemek için, özel JSON 'da en üst düzey özellik olarak **ıncludesearchresults** ekleyin. Arama sonuçları JSON yapısına dahildir, bu nedenle sonuçlara özel tanımlanmış alanlarda başvurulamaz. 

> [!NOTE]
> **Web kancası için özel JSON yükünü dahil et** seçeneğinin yanındaki **Web kancasını görüntüle** seçeneği, sağlandıklarındaki önizlemeyi görüntüler. Gerçek veriler içermez, ancak kullanılacak JSON şeması temsilcisidir. 

## <a name="sample-payloads"></a>Örnek yükleri
Bu bölümde, günlük uyarıları için Web kancaları için örnek yükleri gösterilmektedir. Örnek yükleri, yük standart olduğunda ve özel olduğunda örnekleri içerir.

### <a name="log-alert-for-log-analytics"></a>Log Analytics için günlük uyarısı
Aşağıdaki örnek yük, Log Analytics dayalı uyarılar için kullanılan standart bir Web kancası eylemi içindir:

> [!NOTE]
> [Eski Log Analytics uyarı API](./api-alerts.md)'Sindeki [geçerli scheduledqueryrules API 'sine geçiş](../alerts/alerts-log-api-switch.md) yaptıysanız, "önem derecesi" alan değeri değişir.

```json
{
   "schemaId":"Microsoft.Insights/LogAlert",
   "data":{
      "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
      "AlertRuleName":"AcmeRule",
      "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
      "SearchIntervalStartTimeUtc":"2018-03-26T08:10:40Z",
      "SearchIntervalEndtimeUtc":"2018-03-26T09:10:40Z",
      "AlertThresholdOperator":"Greater Than",
      "AlertThresholdValue":0,
      "ResultCount":2,
      "SearchIntervalInSeconds":3600,
      "LinkToSearchResults":"https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
      "LinkToFilteredSearchResultsUI":"https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
      "LinkToSearchResultsAPI":"https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
      "LinkToFilteredSearchResultsAPI":"https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
      "Description":"log alert rule",
      "Severity":"Warning",
      "AffectedConfigurationItems":[
         "INC-Gen2Alert"
      ],
      "Dimensions":[
         {
            "name":"Computer",
            "value":"INC-Gen2Alert"
         }
      ],
      "SearchResult":{
         "tables":[
            {
               "name":"PrimaryResult",
               "columns":[
                  {
                     "name":"$table",
                     "type":"string"
                  },
                  {
                     "name":"Computer",
                     "type":"string"
                  },
                  {
                     "name":"TimeGenerated",
                     "type":"datetime"
                  }
               ],
               "rows":[
                  [
                     "Fabrikam",
                     "33446677a",
                     "2018-02-02T15:03:12.18Z"
                  ],
                  [
                     "Contoso",
                     "33445566b",
                     "2018-02-02T15:16:53.932Z"
                  ]
               ]
            }
         ]
      },
      "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
      "AlertType":"Metric measurement"
   }
}
```

### <a name="log-alert-for-application-insights"></a>Application Insights için günlük uyarısı
Aşağıdaki örnek yük, Application Insights kaynaklarına dayalı günlük uyarıları için kullanıldığında standart bir Web kancası içindir:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>Diğer kaynak günlükleri için günlük Uyarısı (API sürümünden `2020-05-01-preview` )

> [!NOTE]
> Şu anda API sürümü `2020-05-01-preview` ve kaynak merkezli günlük uyarıları için ek ücret alınmaz.  Önizlemede olan özellikler için fiyatlandırma, gelecekte duyurulacak ve faturalandırma başlamadan önce bir bildirim sunulacaktır. Bildirim döneminden sonra yeni API sürümü ve kaynak merkezli günlük uyarılarını kullanmaya devam etmeyi tercih etmeniz gerekir, ilgili ücret üzerinden faturalandırılırsınız.

Aşağıdaki örnek yük, diğer kaynak günlüklerine dayanan (çalışma alanları ve Application Insights hariç) günlük uyarıları için kullanıldığında standart Web kancasına yöneliktir:

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>Özel bir JSON yükünden oluşan günlük uyarısı
Örneğin, yalnızca uyarı adını ve arama sonuçlarını içeren özel bir yük oluşturmak için şu yapılandırmayı kullanın: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Aşağıdaki örnek yük, herhangi bir günlük uyarısı için özel bir Web kancası eylemine yöneliktir:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```

## <a name="next-steps"></a>Sonraki adımlar
- [Azure uyarılarında günlük uyarıları](./alerts-unified-log.md)hakkında bilgi edinin.
- [Azure 'da günlük uyarılarını yönetmeyi](alerts-log.md)anlayın.
- [Azure 'da eylem grupları](./action-groups.md)oluşturun ve yönetin.
- [Application Insights](../logs/log-query-overview.md)hakkında daha fazla bilgi edinin.
- [Günlük sorguları](../logs/log-query-overview.md)hakkında daha fazla bilgi edinin.
