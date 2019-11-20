---
title: Azure Izleyici 'yi kullanarak veri fabrikalarını izleme
description: Data Factory bilgi ile tanılama günlüklerini etkinleştirerek/Azure Data Factory işlem hatlarını izlemek için Azure Izleyici 'yi nasıl kullanacağınızı öğrenin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 67709ef96ffb8190812d625c04cd9749c0ebb900
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684628"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Azure Izleyici kullanarak veri fabrikalarını uyarma ve izleme

Bulut uygulamaları karmaşıktır ve birçok hareketli parçaya sahiptir. İzleyiciler, uygulamalarınızın sağlıklı durumda kalmasını ve çalışmasını sağlamaya yardımcı olmak için veri sağlar. İzleyicilerin olası sorunları önlemenize ve geçmişteki sorunları gidermenize yardımcı olur.

Uygulamalarınız hakkında derin Öngörüler elde etmek için izleme verilerini kullanabilirsiniz. Bu bilgi, uygulama performansını ve bakımlılığını iyileştirmenize yardımcı olur. Ayrıca, el ile müdahale gerektiren eylemleri otomatikleştirmenize de yardımcı olur.

Azure Izleyici, çoğu Azure hizmeti için temel düzeyde altyapı ölçümleri ve günlükleri sağlar. Azure tanılama günlükleri bir kaynak tarafından dağıtılır ve bu kaynağın çalışması hakkında zengin ve sık veriler sağlar. Azure Data Factory tanılama günlüklerini Izleyicisinde yazar.

Ayrıntılar için bkz. [Azure izleyiciye genel bakış](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Azure Data Factory verilerini tutma

Data Factory depolar işlem hattı-verileri yalnızca 45 gün boyunca çalıştırın. Bu verileri daha uzun süre tutmak istiyorsanız Izleyiciyi kullanın. Izleyici ile tanılama günlüklerini analiz için yönlendirebilirsiniz. Ayrıca, seçtiğiniz süre için fabrika bilgilerine sahip olmanız için bunları bir depolama hesabında tutabilirsiniz.

## <a name="diagnostic-logs"></a>Tanılama günlükleri

* Tanılama günlüklerinizi denetim veya el ile inceleme için bir depolama hesabına kaydedin. Bekletme süresini gün cinsinden belirtmek için tanılama ayarlarını kullanabilirsiniz.
* Günlüklerin Azure Event Hubs akışını yapın. Günlükler bir iş ortağı hizmetine veya Power BI gibi özel bir analiz çözümüne giriş haline gelir.
* Günlükleri Log Analytics analiz edin.

Günlükleri yayan kaynağın aboneliğinde olmayan bir depolama hesabı veya Olay Hub 'ı ad alanı kullanabilirsiniz. Ayarı yapılandıran kullanıcının her iki aboneliğe da uygun rol tabanlı erişim denetimi (RBAC) erişimi olmalıdır.

## <a name="set-up-diagnostic-logs"></a>Tanılama günlükleri ayarlama

### <a name="diagnostic-settings"></a>Tanılama ayarları

İşlem dışı kaynaklar için tanılama günlüklerini yapılandırmak için tanılama ayarlarını kullanın. Kaynak denetiminin ayarları aşağıdaki özelliklere sahiptir:

* Tanılama günlüklerinin nereye gönderileceğini belirtir. Azure depolama hesabı, Azure Olay Hub 'ı veya Izleme günlüklerini örnek olarak içerir.
* Hangi günlük kategorilerinin gönderileceğini belirtir.
* Her günlük kategorisinin bir depolama hesabında ne kadar süreyle tutulması gerektiğini belirtir.
* Sıfır günlük bir bekletme, günlüklerin süresiz olarak saklanacağı anlamına gelir. Aksi takdirde, değer 1 ile 2.147.483.647 arasında herhangi bir sayıda gün olabilir.
* Bekletme ilkeleri ayarlanır ancak günlükleri bir depolama hesabında depolamak devre dışı bırakılırsa, bekletme ilkelerinin etkisi yoktur. Örneğin, bu durum yalnızca Event Hubs veya Izleyici günlükleri seçeneklerinin seçildiği zaman gerçekleşebilir.
* Bekletme ilkeleri gün başına uygulanır. Günler arasındaki sınır, Eşgüdümlü Evrensel Saat (UTC) ile gece yarısı arasında gerçekleşir. Bir günün sonunda, bekletme ilkesinin ötesinde olan günlerdeki Günlükler silinir. Örneğin, bir güne ait bir bekletme ilkeniz varsa, bugünün başlangıcında dün olan Günlükler silinir.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Izleyici REST API aracılığıyla tanılama günlüklerini etkinleştirme

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Izleyici REST API bir tanılama ayarı oluşturun veya güncelleştirin

##### <a name="request"></a>İstek

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Üst bilgiler

* `{api-version}` yerine `2016-09-01` yazın.
* `{resource-id}`, tanılama ayarlarını düzenlemek istediğiniz kaynağın KIMLIĞIYLE değiştirin. Daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için Kaynak gruplarını kullanma](../azure-resource-manager/manage-resource-groups-portal.md).
* `Content-Type` üst bilgisini `application/json`olarak ayarlayın.
* Yetkilendirme üst bilgisini Azure Active Directory (Azure AD) ' dan aldığınız JSON Web belirtecine ayarlayın. Daha fazla bilgi için bkz. [Istekleri kimlik doğrulama](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Gövde

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Özellik | Tür | Açıklama |
| --- | --- | --- |
| **Storageaccountıd** |Dize | Tanılama günlükleri göndermek istediğiniz depolama hesabının kaynak KIMLIĞI. |
| **Servicebusruleıd** |Dize | ' In, akış tanılama günlükleri için Event Hubs oluşturulmasını istediğiniz hizmet veri yolu ad alanının hizmet veri yolu kuralı KIMLIĞI. Kural KIMLIĞI `{service bus resource ID}/authorizationrules/{key name}`biçimine sahiptir.|
| **Workspaceıd** | Karmaşık tür | Ölçüm zaman ve bekletme ilkelerine oluşan dizi. Bu özelliğin değeri boş. |
|**metrics**| Çağrılan işlem hattına geçirilecek işlem hattının parametre değerleri| Parametre adlarını bağımsız değişken değerleriyle eşleyen bir JSON nesnesi. |
| **açıldığında**| Karmaşık tür| Kaynak türü için bir tanılama günlüğü kategorisinin adı. Bir kaynak için tanılama günlük kategorilerinin listesini almak için, tanı ayarlarını al işlemini gerçekleştirin. |
| **alan**| Dize| Bir dizi günlük kategorisi ve bekletme ilkeleri. |
| **timeGrain** | Dize | ISO 8601 Duration biçiminde yakalanan ölçüm ayrıntı düzeyi. Özellik değeri bir dakika belirten `PT1M`olmalıdır. |
| **etkinletir**| Boole | Ölçüm veya günlük kategorisi koleksiyonunun bu kaynak için etkinleştirilip etkinleştirilmeyeceğini belirtir. |
| **retentionPolicy**| Karmaşık tür| Ölçüm veya günlük kategorisi için bekletme ilkesini açıklar. Bu özellik yalnızca depolama hesapları için kullanılır. |
|**miş**| 'Tir| Ölçüm veya günlüklerin saklanacağı gün sayısı. Özellik değeri 0 ise, Günlükler süresiz tutulur. Bu özellik yalnızca depolama hesapları için kullanılır. |

##### <a name="response"></a>Yanıt

200 TAMAM.


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Izleyici REST API Tanılama ayarları hakkında bilgi alın

##### <a name="request"></a>İstek

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Üst bilgiler

* `{api-version}` yerine `2016-09-01` yazın.
* `{resource-id}`, tanılama ayarlarını düzenlemek istediğiniz kaynağın KIMLIĞIYLE değiştirin. Daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için Kaynak gruplarını kullanma](../azure-resource-manager/manage-resource-groups-portal.md).
* `Content-Type` üst bilgisini `application/json`olarak ayarlayın.
* Yetkilendirme üst bilgisini Azure AD 'den aldığınız bir JSON Web belirtecine ayarlayın. Daha fazla bilgi için bkz. [Istekleri kimlik doğrulama](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Yanıt

200 TAMAM.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}

```
Daha fazla bilgi için bkz. [Tanılama ayarları](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Günlüklerin ve olayların şeması

### <a name="monitor-schema"></a>Şemayı izle

#### <a name="activity-run-log-attributes"></a>Etkinlik-günlük özniteliklerini Çalıştır

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Özellik | Tür | Açıklama | Örnek |
| --- | --- | --- | --- |
| **Düzey** |Dize | Tanılama günlüklerinin düzeyi. Etkinlik çalıştırma günlükleri için özellik değerini 4 olarak ayarlayın. | `4` |
| **ID** |Dize | Belirli bir isteği izlemeye yönelik benzersiz KIMLIK. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **ışınızda** | Dize | Zaman aralığı UTC biçimindeki olayın saati `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**Activityrunıd**| Dize| Etkinliğin çalıştırması KIMLIĞI. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**Ardışık düzen eylemsizlik kimliği**| Dize| İşlem hattı çalıştırmasının KIMLIĞI. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**RESOURCEID**| Dize | Data Factory kaynağıyla ilişkili KIMLIK. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**alan**| Dize | Tanılama günlüklerinin kategorisi. Özellik değerini `ActivityRuns`olarak ayarlayın. | `ActivityRuns` |
|**düzeyde**| Dize | Tanılama günlüklerinin düzeyi. Özellik değerini `Informational`olarak ayarlayın. | `Informational` |
|**operationName**| Dize | Etkinliğin durumuyla ilgili ad. Etkinlik başlangıç sinyalinise, özellik değeri `MyActivity -`. Etkinlik uç sinyaline ise, özellik değeri `MyActivity - Succeeded`. | `MyActivity - Succeeded` |
|**Ardışık Düzen adı**| Dize | İşlem hattının adı. | `MyPipeline` |
|**Özelliğinde**| Dize | Etkinliğin adı. | `MyActivity` |
|**start**| Dize | Etkinliğin Başlangıç saati, TimeSpan UTC biçiminde çalışır. | `2017-06-26T20:55:29.5007959Z`|
|**erer**| Dize | Etkinliğin bitiş saati, TimeSpan UTC biçiminde çalışır. Tanılama günlüğünde bir etkinliğin başlatıldığını ancak henüz bitmemiş olduğunu gösteriyorsa, özellik değeri `1601-01-01T00:00:00Z`. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>İşlem hattı-günlük özniteliklerini çalıştırma

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Özellik | Tür | Açıklama | Örnek |
| --- | --- | --- | --- |
| **Düzey** |Dize | Tanılama günlüklerinin düzeyi. Etkinlik çalıştırma günlükleri için özellik değerini 4 olarak ayarlayın. | `4` |
| **ID** |Dize | Belirli bir isteği izlemeye yönelik benzersiz KIMLIK. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **ışınızda** | Dize | Zaman aralığı UTC biçimindeki olayın saati `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**RunId**| Dize| İşlem hattı çalıştırmasının KIMLIĞI. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**RESOURCEID**| Dize | Data Factory kaynağıyla ilişkili KIMLIK. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**alan**| Dize | Tanılama günlüklerinin kategorisi. Özellik değerini `PipelineRuns`olarak ayarlayın. | `PipelineRuns` |
|**düzeyde**| Dize | Tanılama günlüklerinin düzeyi. Özellik değerini `Informational`olarak ayarlayın. | `Informational` |
|**operationName**| Dize | İşlem hattının, durumu ile birlikte adı. İşlem hattı çalıştırması tamamlandıktan sonra, özellik değeri `Pipeline - Succeeded`. | `MyPipeline - Succeeded`. |
|**Ardışık Düzen adı**| Dize | İşlem hattının adı. | `MyPipeline` |
|**start**| Dize | Etkinliğin Başlangıç saati, TimeSpan UTC biçiminde çalışır. | `2017-06-26T20:55:29.5007959Z`. |
|**erer**| Dize | Etkinliğin bitiş saati, TimeSpan UTC biçiminde çalışır. Tanılama günlüğünde bir etkinlik başlatılmış ancak henüz bitmemişse, özellik değeri `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|**durumlarına**| Dize | İşlem hattının son durumu. Olası özellik değerleri `Succeeded` ve `Failed`. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Tetikleme-günlük özniteliklerini Çalıştır

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Özellik | Tür | Açıklama | Örnek |
| --- | --- | --- | --- |
| **Düzey** |Dize | Tanılama günlüklerinin düzeyi. Etkinlik çalıştırma günlükleri için özellik değerini 4 olarak ayarlayın. | `4` |
| **ID** |Dize | Belirli bir isteği izlemeye yönelik benzersiz KIMLIK. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **ışınızda** | Dize | Zaman aralığı UTC biçimindeki olayın saati `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**Triggerıd**| Dize| Tetikleyici çalıştırmasının KIMLIĞI. | `08587023010602533858661257311` |
|**RESOURCEID**| Dize | Data Factory kaynağıyla ilişkili KIMLIK. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**alan**| Dize | Tanılama günlüklerinin kategorisi. Özellik değerini `PipelineRuns`olarak ayarlayın. | `PipelineRuns` |
|**düzeyde**| Dize | Tanılama günlüklerinin düzeyi. Özellik değerini `Informational`olarak ayarlayın. | `Informational` |
|**operationName**| Dize | Tetikleyicinin başarıyla tetikleyip tetiklenmediğini belirten, son durumu ile tetikleyicinin adı. Sinyal başarılı olduysa, özellik değeri `MyTrigger - Succeeded`. | `MyTrigger - Succeeded` |
|**triggerName**| Dize | Tetikleyicinin adı. | `MyTrigger` |
|**triggerType**| Dize | Tetikleyicinin türü. Olası özellik değerleri `Manual Trigger` ve `Schedule Trigger`. | `ScheduleTrigger` |
|**triggerEvent**| Dize | Tetikleyicinin olayı. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| Dize | Zaman aralığı UTC biçiminde tetikleyicinin tetiklemenin başlangıç saati. | `2017-06-26T20:55:29.5007959Z`|
|**durumlarına**| Dize | Tetikleyicinin başarıyla harekete geçirilip tetiklenmediğini gösteren nihai durum. Olası özellik değerleri `Succeeded` ve `Failed`. | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics şeması

Log Analytics şemayı Izleyiciden aşağıdaki özel durumlarla devralır:

* Her sütun adının ilk harfi büyük harfle ayarlanır. Örneğin, Monitor içindeki "Bağıntıkimliği" sütun adı, Log Analytics "Bağıntıkimliği" dır.
* "Düzey" sütunu yok.
* Dinamik "Özellikler" sütunu aşağıdaki dinamik JSON blob türü olarak korunur.

    | Azure Izleyici sütunu | Log Analytics sütunu | Tür |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dinamik |
    | $. Properties. Açıklamaları | Açıklamaları | Dinamik |
    | $. Properties. Girişinin | Girdi | Dinamik |
    | $. Properties. Çıktıların | Çıktı | Dinamik |
    | $. Properties. Hata. errorCode | Raporladı | int |
    | $. Properties. Hata. ileti | Hata | string |
    | $. Properties. Hatayla | Hata | Dinamik |
    | $. Properties. Öncül | Öncül | Dinamik |
    | $. Properties. Parametrelere | Parametreler | Dinamik |
    | $. Properties. SystemParameters | SystemParameters | Dinamik |
    | $. Properties. Lerimi | Etiketler | Dinamik |
    
## <a name="metrics"></a>Ölçümler

Izleyici ile Azure iş yüklerinizin performansı ve sistem durumu hakkında görünürlük elde edebilirsiniz. En önemli Izleyici verileri türü, performans sayacı olarak da adlandırılan ölçümdür. Ölçümler çoğu Azure kaynağı tarafından yayınlanır. İzleyici, izleme ve sorun giderme amacıyla bu ölçümleri yapılandırmak ve kullanmak için çeşitli yollar sağlar.

Azure Data Factory sürüm 2 aşağıdaki ölçümleri yayar.

| **Ölçüm**           | **Ölçüm görünen adı**         | **Birim** | **Toplama türü** | **Açıklama**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| Ardışık düzen Inesucceededçalıştırmaları | Başarılı işlem hattı çalışma ölçümleri | Sayı    | Toplam                | Bir dakika penceresi içinde başarılı olan işlem hattı çalıştırmalarının toplam sayısı. |
| Pipelinefailedçalıştırmaları   | Başarısız işlem hattı çalıştırmaları ölçümleri    | Sayı    | Toplam                | Bir dakika penceresinde başarısız olan işlem hattı çalıştırmalarının toplam sayısı.    |
| ActivitySucceededRuns | Başarılı etkinlik ölçümleri çalıştırıyor | Sayı    | Toplam                | Bir dakika penceresi içinde başarılı olan etkinlik çalıştırmalarının toplam sayısı.  |
| Activityfailedçalıştırmaları   | Başarısız etkinlik çalıştırma ölçümleri    | Sayı    | Toplam                | Bir dakika penceresi içinde başarısız olan etkinlik çalıştırmalarının toplam sayısı.     |
| TriggerSucceededRuns | Başarılı tetikleyici çalışan ölçümleri  | Sayı    | Toplam                | Bir dakika penceresi içinde başarılı olan tetikleyici çalıştırmalarının toplam sayısı.   |
| Triggerfailedçalıştırmaları    | Başarısız tetikleyici çalıştırma ölçümleri     | Sayı    | Toplam                | Bir dakika penceresi içinde başarısız olan tetikleyici çalıştırmalarının toplam sayısı.      |

Ölçümlere erişmek için [Azure izleyici veri platformundaki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)yönergeleri doldurun.

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Azure Izleyici ile Data Factory ölçümlerini izleme

İzlemek üzere verileri yönlendirmek için Izleyici ile Data Factory tümleştirme kullanabilirsiniz. Bu tümleştirme, aşağıdaki senaryolarda faydalıdır:

* Izlemek üzere Data Factory tarafından yayınlanan zengin bir ölçüm kümesine karmaşık sorgular yazmak istiyorsunuz. Bu sorgularda, Izleyici aracılığıyla özel uyarılar oluşturabilirsiniz.

* Veri fabrikaları genelinde izlemek istiyorsunuz. Birden çok veri fabrikasına ait verileri tek bir Izleyici çalışma alanına yönlendirebilirsiniz.

Bu özelliğin yedi dakikalık bir girişi ve gösterimi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Tanılama ayarlarını ve çalışma alanını yapılandırma

Veri fabrikanızın tanılama ayarlarını oluşturun veya ekleyin.

1. Portalda Izleyici ' ye gidin. **Tanılama ayarları** > **Ayarlar** ' ı seçin.

1. Tanılama ayarı ayarlamak istediğiniz veri fabrikasını seçin.

1. Seçili veri fabrikasında herhangi bir ayar yoksa, bir ayar oluşturmanız istenir. **Tanılamayı aç '** ı seçin.

   ![Hiçbir ayar yoksa bir tanılama ayarı oluştur](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Data Factory 'de mevcut ayarlar varsa, veri fabrikasında zaten yapılandırılmış ayarların bir listesini görürsünüz. **Tanılama ayarı Ekle**' yi seçin.

   ![Ayarlar varsa bir tanılama ayarı ekleyin](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Ayarınızı bir ad verin, **Log Analytics gönder**' i seçin ve sonra **Log Analytics çalışma**alanından bir çalışma alanı seçin.

    ![Ayarlarınızı adlandırın ve bir Log Analytics çalışma alanı seçin](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. **Kaydet**’i seçin.

Birkaç dakika sonra, bu veri fabrikasının ayarları listenizde yeni ayar görüntülenir. Tanılama günlükleri, yeni olay verileri oluşturulmasıyla hemen bu çalışma alanına akışlardır. Bir olay yayınlandığınızda ve Log Analytics göründüğünde 15 dakikaya kadar zaman çıkabilir.

* _Kaynağa özgü_ modda, Azure Data Factory Flow 'Dan _Adfardışık düzen eylemsizlik_, _Adftriggerrun_ve _adfactivityrun_ tablolarına yönelik tanılama günlükleri
* _Azure Tanılama_ modunda tanılama günlükleri _AzureDiagnostics_ tablosuna akıtılır

> [!NOTE]
> Bir Azure günlük tablosunda 500 ' den fazla sütun olabileceğinden kaynağa özgü mod ' u seçmeniz önerilir. Daha fazla bilgi için bkz. [Log Analytics bilinen sınırlamalar](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Azure Marketi 'nden Azure Data Factory Analytics 'i yükler

!["Azure Marketi" ne gidin, "analiz filtresi" yazın ve "Azure Data Factory analiz (Önizleme") seçeneğini belirleyin.](media/data-factory-monitor-oms/monitor-oms-image3.png)

!["Azure Data Factory Analytics (Önizleme)" hakkındaki ayrıntılar](media/data-factory-monitor-oms/monitor-oms-image4.png)

**Oluştur** ' u seçip **OMS çalışma** alanı ve **OMS çalışma alanı ayarları**' nı seçin.

![Yeni çözüm oluşturma](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory ölçümlerini izleme

Azure Data Factory Analytics 'in yüklenmesi, aşağıdaki ölçümlerin etkinleştirilebilmesi için varsayılan bir görünüm kümesi oluşturur:

- ADF çalıştırmaları-1) Data Factory tarafından işlem hattı çalıştırmaları
 
- ADF çalıştırmalar-2) Data Factory tarafından çalışan etkinlik çalıştırmaları

- ADF çalıştırmalar-3) Data Factory tarafından çalıştırılan tetikler

- ADF hataları-1) Data Factory göre Ilk 10 ardışık düzen hatası

- ADF hataları-2) Data Factory tarafından Ilk 10 etkinlik çalıştırılır

- ADF hataları-3) Ilk 10 Data Factory tarafından hata tetikleyin

- ADF Istatistikleri-1) türe göre etkinlik çalıştırmaları

- ADF Istatistikler-2) türe göre tetikleme çalıştırmaları

- ADF Istatistikleri-3) en fazla işlem hattı çalıştırma süresi

!["Çalışma kitapları (Önizleme)" ve "AzureDataFactoryAnalytics" vurgulanmış pencere](media/data-factory-monitor-oms/monitor-oms-image6.png)

Önceki ölçümleri görselleştirin, bu ölçümlerin arkasındaki sorgulara bakabilir, sorguları düzenleyebilir, uyarılar oluşturabilir ve başka işlemler yapabilirsiniz.

![Veri Fabrikası tarafından çalıştırılan işlem hattının grafik gösterimi "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (Önizleme), tanılama günlüklerini _kaynağa özgü_ hedef tablolarına gönderir. Şu tablolara yönelik sorgular yazabilirsiniz: _Adfardışık düzen eylemsizlik_, _Adftriggerrun_ve _adfactivityrun_.

## <a name="alerts"></a>Uyarılar

Azure portal oturum açın ve uyarı oluşturmak için **izleyici** > **uyarıları** ' nı seçin.

![Portal menüsündeki uyarılar](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Uyarı oluşturma

1. Yeni bir uyarı oluşturmak için **+ Yeni uyarı kuralı** ' nı seçin.

    ![Yeni uyarı kuralı](media/monitor-using-azure-monitor/alerts_image4.png)

1. Uyarı koşulunu tanımlayın.

    > [!NOTE]
    > **Kaynak türüne göre filtrele** aşağı açılan listesinden **Tümünü** seçtiğinizden emin olun.

    !["Kaynak Seç" bölmesini açan "hedef seç" > "uyarı koşulunu tanımlayın" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Sinyal mantığını yapılandırma" bölmesini açan "" ölçüt ekle ">" uyarı koşulunu tanımlayın "](media/monitor-using-azure-monitor/alerts_image6.png)

    !["Sinyal türünü yapılandırma" bölmesi](media/monitor-using-azure-monitor/alerts_image7.png)

1. Uyarı ayrıntılarını tanımlayın.

    ![Uyarı ayrıntıları](media/monitor-using-azure-monitor/alerts_image8.png)

1. Eylem grubunu tanımlayın.

    !["Yeni eylem grubu" vurgulanmış olarak bir kural oluşturun](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Yeni bir eylem grubu oluştur](media/monitor-using-azure-monitor/alerts_image10.png)

    ![E-posta, SMS, push ve ses yapılandırma](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Bir eylem grubu tanımlama](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Sonraki adımlar
[İşlem hatlarını programlama yoluyla izleme ve yönetme](monitor-programmatically.md)
