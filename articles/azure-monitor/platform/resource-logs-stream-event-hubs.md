---
title: Azure Kaynak günlüklerini bir olay hub 'ına akış
description: Azure Kaynak günlüklerinin bir olay hub 'ına akışını nasıl sağlayacağınızı öğrenin.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 1d7a533658b6c72caae9649d7e5a9c4fad117245
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262420"
---
# <a name="stream-azure-resource-logs-to-azure-event-hubs"></a>Azure Kaynak günlüklerini Azure Event Hubs akış
Azure 'da [kaynak günlükleri](resource-logs-overview.md) , bir Azure kaynağının iç işlemi hakkında zengin, sık veriler sağlar. Bu makalede, üçüncü taraf Sıems ve diğer Log Analytics çözümleri gibi dış sistemlere veri göndermek için Olay Hub 'larına akış kaynak günlükleri açıklanır.


## <a name="what-you-can-do-with-resource-logs-sent-to-an-event-hub"></a>Bir olay hub 'ına gönderilen kaynak günlükleriyle yapabilecekleriniz
Aşağıdaki işlevleri sağlamak için Azure 'daki Olay Hub 'larına kaynak günlükleri akışı yapın:

* Günlük verilerini üçüncü taraf bir SıEM veya Log Analytics aracına yöneltmek için tüm kaynak günlüklerinizi tek bir olay hub 'ına akış halinde **kaydeder** .
* **Özel bir telemetri ve günlüğe kaydetme platformu oluşturma** – Olay Hub 'larının yüksek düzeyde ölçeklenebilir yayımla-abone olma yapısı, kaynak günlüklerini özel bir teletry platformunda esnek bir şekilde içe almanıza olanak sağlar. Ayrıntılar için bkz. [Event Hubs Azure 'Da küresel ölçekli telemetri platformunu tasarlama ve boyutlandırma](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .

* **Power BI veri akışı yaparak hizmet durumunu görüntüleyin** – tanılama verilerinizi Azure hizmetlerinizden neredeyse gerçek zamanlı içgörüler halinde dönüştürmek için Event Hubs, Stream Analytics ve Power BI kullanın. Stream Analytics [ve Power BI bakın: Bu çözüm hakkındaki ayrıntılar için veri](../../stream-analytics/stream-analytics-power-bi-dashboard.md) akışı için gerçek zamanlı analiz panosu.

    Aşağıdaki SQL kodu, ' deki tüm günlük verilerini bir Power BI tablosuna ayrıştırmak için kullanabileceğiniz bir örnek Stream Analytics sorgusudur:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Önkoşullar
Henüz bir tane yoksa [bir olay hub 'ı oluşturmanız](../../event-hubs/event-hubs-create.md) gerekir. Daha önce bu Event Hubs ad alanına kaynak günlükleri akıdıysanız, bu olay hub 'ı yeniden kullanılacaktır.

Ad alanı için paylaşılan erişim ilkesi, akış mekanizmanın sahip olduğu izinleri tanımlar. Event Hubs akışının yönetilmesi, gönderilmesi ve dinlemesi izinlerinin olması gerekir. Event Hubs ad alanınız için Yapılandır sekmesinin altındaki Azure portal paylaşılan erişim ilkeleri oluşturabilir veya değiştirebilirsiniz.

Tanılama ayarını akış içerecek şekilde güncelleştirmek için, bu Event Hubs yetkilendirme kuralında ListKey izninizin olması gerekir. Ayarı yapılandıran kullanıcının her iki aboneliğe ve her iki aboneliğe de aynı AAD kiracısına sahip olması koşuluyla, Event Hubs ad alanının, günlükleri yayan abonelikle aynı abonelikte olması gerekmez.

## <a name="create-a-diagnostic-setting"></a>Tanılama ayarı oluştur
Kaynak günlükleri varsayılan olarak toplanmaz. Azure kaynağı için bir tanılama ayarı oluşturarak bunları bir olay hub 'ına ve diğer hedeflere gönderin. Ayrıntılar için bkz. [Azure 'da günlükleri ve ölçümleri toplamak için tanılama ayarı oluşturma](diagnostic-settings.md) .

## <a name="stream-data-from-compute-resources"></a>İşlem kaynaklarından veri akışı
Bu makaledeki işlem, [Azure Kaynak günlüklerine genel bakış](diagnostic-settings.md)bölümünde açıklandığı gibi işlem dışı kaynaklar içindir.
Windows Azure Tanılama aracısını kullanarak Azure işlem kaynaklarından kaynak günlüklerini akışla. Ayrıntılar için [Event Hubs kullanarak bkz. Azure Tanılama verileri etkin yolda akışa](diagnostics-extension-stream-event-hubs.md) alma.


## <a name="consuming-log-data-from-event-hubs"></a>Olay Hub 'larından günlük verilerini kullanma
Olay Hub 'larından kaynak günlüklerini kullandığınızda, aşağıdaki tablodaki öğelerle birlikte JSON biçiminde olur.

| Öğe adı | Açıklama |
| --- | --- |
| kayıt |Bu yükteki tüm günlük olaylarının bir dizisi. |
| time |Olayın gerçekleştiği zaman. |
| category |Bu olay için günlük kategorisi. |
| resourceId |Bu olayı oluşturan kaynağın kaynak KIMLIĞI. |
| operationName |İşlemin adı. |
| level |İsteğe bağlı. Günlük olayı düzeyini gösterir. |
| properties |Etkinliğin özellikleri. Bunlar, bölümünde [ ]()açıklandığı gibi her bir Azure hizmeti için farklılık gösterir. |


Aşağıda Event Hubs örnek çıkış verileri verilmiştir:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Sonraki adımlar

* [Azure izleyici ile Azure Active Directory günlükleri akışı](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Azure Kaynak günlükleri hakkında daha fazla bilgi edinin](resource-logs-overview.md).
* [Event Hubs kullanmaya başlayın](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

