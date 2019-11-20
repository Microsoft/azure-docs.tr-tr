---
title: Azure uyarılarında günlük uyarıları için Web kancası eylemleri
description: Bu makalede Log Analytics çalışma alanı veya Application Insights kullanarak bir günlük uyarı kuralının nasıl oluşturulacağı, uyarının verileri bir HTTP Web kancası olarak nasıl gönderdiği ve olası farklı özelleştirmelerin ayrıntıları açıklanmaktadır.
author: yanivlavi
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 3a072ae64104f8fded49ff6a00f5b58902c39903
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838580"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Günlük uyarısı kuralları için Web kancası eylemleri
[Azure 'da bir günlük uyarısı oluşturulduğunda](alerts-log.md), bir veya daha fazla eylem gerçekleştirmek için [eylem gruplarını kullanarak yapılandırma](action-groups.md) seçeneğiniz vardır. Bu makalede, kullanılabilen farklı Web kancası eylemleri açıklanmakta ve özel bir JSON tabanlı Web kancasının nasıl yapılandırılacağı gösterilmektedir.

> [!NOTE]
> Web kancası tümleştirmelerinizin [ortak uyarı şemasını](https://aka.ms/commonAlertSchemaDocs) da kullanabilirsiniz. Ortak uyarı şeması, Azure Izleyici 'deki tüm uyarı hizmetlerinde tek bir Genişletilebilir ve Birleşik bir uyarı yüküne sahip olmanın avantajlarından yararlanır. lütfen genel uyarı şemasının günlük uyarıları için özel JSON seçeneğini gözlemleyemediğini unutmayın. , Uyarı kuralı düzeyinde yapmış olabileceğiniz özelleştirmeleri ne olursa olsun, ortak uyarı şeması yüküne erteler. [Ortak uyarı şeması tanımları hakkında bilgi edinin.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Web kancası eylemleri

Web kancası eylemleriyle, tek bir HTTP POST isteği aracılığıyla bir dış işlem çağırabilirsiniz. Çağrılan hizmet Web kancalarını desteklemelidir ve aldığı yükün nasıl kullanılacağını belirlemelidir.

Web kancası eylemleri aşağıdaki tablodaki özellikleri gerektirir.

| Özellik | Açıklama |
|:--- |:--- |
| **Web kancası URL 'SI** |Web kancasının URL 'SI. |
| **Özel JSON yükü** |Bu seçenek, uyarı oluşturma sırasında seçildiğinde Web kancası ile gönderilecek özel yük. Daha fazla bilgi için bkz. [günlük uyarılarını yönetme](alerts-log.md).|

> [!NOTE]
> Günlük uyarısı için **özel JSON yükünü dahil et** seçeneğinin yanı sıra Web kancasını **görüntüle** düğmesi, sunulan özelleştirmenin örnek Web kancası yükünü görüntüler. Gerçek veriler içermez ancak günlük uyarıları için kullanılan JSON şemasının temsilcisidir. 

Web kancaları, dış hizmete gönderilen verilerin JSON 'de biçimlendirildiği bir URL ve bir yük içerir. Varsayılan olarak, yük aşağıdaki tablodaki değerleri içerir. Bu yükü kendi özel bir biriyle değiştirmeyi tercih edebilirsiniz. Bu durumda, her bir parametre için tablodaki değişkenleri, değerlerini özel yükünüzü içerecek şekilde kullanın.


| Parametre | Değişken | Açıklama |
|:--- |:--- |:--- |
| *Alertrutaname* |#alertrulename |Uyarı kuralının adı. |
| *İnin* |#severity |Tetiklenen günlük uyarısı için önem derecesi kümesi. |
| *AlertThresholdOperator* |#thresholdoperator |Uyarı kuralı için ' den büyük veya küçüktür kullanan eşik işleci. |
| *AlertThresholdValue* |#thresholdvalue |Uyarı kuralının eşik değeri. |
| *LinkToSearchResults* |#linktosearchresults |Uyarıyı oluşturan sorgudaki kayıtları döndüren analiz portalına bağlantı. |
| *Ayarlanan resultcount* |#searchresultcount |Arama sonuçlarındaki kayıt sayısı. |
| *Arama aralığı bitiş zamanı* |#searchintervalendtimeutc |UTC olarak sorgunun bitiş saati, aa/gg/yyyy HH: mm: ss "/PM biçiminde. |
| *Arama aralığı* |#searchinterval |Uyarı kuralı için ss: DD: ss biçiminde zaman penceresi. |
| *Arama aralığı başlangıçsaati* |#searchintervalstarttimeutc |Sorgu için, aa/gg/yyyy HH: mm: ss/PM biçimindeki UTC olarak başlangıç zamanı. 
| *SearchQuery* |#searchquery |Uyarı kuralı tarafından kullanılan günlük arama sorgusu. |
| *SearchResults* |"Includesearchresults": true|Sorgu tarafından JSON 1.000 tablosu olarak döndürülen kayıtlar, "ıncludesearchresults": true, özel bir JSON Web kancası tanımına en üst düzey özellik olarak eklenirse. |
| *Uyarı türü*| #alerttype | [Ölçüm ölçümü](alerts-unified-log.md#metric-measurement-alert-rules) veya [sonuç sayısı](alerts-unified-log.md#number-of-results-alert-rules)olarak yapılandırılan günlük uyarısı kuralının türü.|
| *Workspaceıd* |#workspaceid |Log Analytics çalışma alanınızın KIMLIĞI. |
| *Uygulama KIMLIĞI* |#applicationid |Application Insights uygulamanızın KIMLIĞI. |
| *Abonelik KIMLIĞI* |#subscriptionid |Kullanılan Azure aboneliğinizin KIMLIĞI. 

> [!NOTE]
> *Linktosearchresults* , *SearchQuery*, *arama aralığı başlangıçsaati*ve analiz bölümünde görüntülenmek üzere Azure Portal URL 'sindeki *arama aralığı bitiş zamanı* gibi parametreleri geçirir. Azure portal yaklaşık 2.000 karakter uzunluğunda bir URI boyut sınırına sahiptir. Parametreler, parametre değerleri sınırı aşarsa, uyarılarda belirtilen *bağlantıları açmaz.* Sonuçları analiz portalında görüntülemek için el ile bilgi girebilirsiniz. Ya da sonuçları programlı bir şekilde almak için [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) veya [Log Analytics REST API](/rest/api/loganalytics/) kullanabilirsiniz. 

Örneğin, *metin*adlı tek bir parametre içeren aşağıdaki özel yükü belirtebilirsiniz. Bu Web kancası çağrısının çağrı yaptığı hizmet bu parametreyi bekler.

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
Özel bir Web kancası içindeki tüm değişkenlerin bir JSON Kasası içinde belirtilmesi gerekir, örneğin "#searchinterval" gibi, sonuçta elde edilen Web kancası içinde "00:05:00" gibi değişken veriler de vardır.

Arama sonuçlarını özel bir yüke dahil etmek için, **ıncludesearchresults** 'un JSON yükünde en üst düzey özellik olarak ayarlandığından emin olun. 

## <a name="sample-payloads"></a>Örnek yükleri
Bu bölümde, günlük uyarıları için Web kancaları için örnek yükleri gösterilmektedir. Örnek yükleri, yük standart olduğunda ve özel olduğunda örnekleri içerir.

### <a name="standard-webhook-for-log-alerts"></a>Günlük uyarıları için standart Web kancası 
Bu örneklerin her ikisinde de yalnızca iki sütun ve iki satır içeren bir kukla yük vardır.

#### <a name="log-alert-for-log-analytics"></a>Log Analytics için günlük uyarısı
Aşağıdaki örnek yük, Log Analytics dayalı uyarılar için kullanılan *Özel BIR JSON seçeneği olmadan* standart Web kancası eylemi içindir:

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
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
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> "Önem" alanı değeri, Log Analytics üzerindeki günlük uyarıları için [API tercihinizi](alerts-log-api-switch.md) değiştirdiyseniz değişebilir.


#### <a name="log-alert-for-application-insights"></a>Application Insights için günlük uyarısı
Aşağıdaki örnek yük, Application Insights dayalı günlük uyarıları için kullanıldığında *Özel BIR JSON seçeneği olmadan* standart Web kancasına sahiptir:
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
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
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Özel JSON yüküyle günlük uyarısı
Örneğin, yalnızca uyarı adını ve arama sonuçlarını içeren özel bir yük oluşturmak için aşağıdakileri kullanabilirsiniz: 

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
- [Azure uyarılarında günlük uyarıları](alerts-unified-log.md)hakkında bilgi edinin.
- [Azure 'da günlük uyarılarını yönetmeyi](alerts-log.md)anlayın.
- [Azure 'da eylem grupları](action-groups.md)oluşturun ve yönetin.
- [Application Insights](../../azure-monitor/app/analytics.md)hakkında daha fazla bilgi edinin.
- [Günlük sorguları](../log-query/log-query-overview.md)hakkında daha fazla bilgi edinin. 

