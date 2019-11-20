---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 614d93a16b9149a217b5ff1004031e0a2d7337ca
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615040"
---
[Dayanıklı işlevler](../articles/azure-functions/durable-functions-overview.md)için yapılandırma ayarları.

### <a name="durable-functions-1x"></a>Dayanıklı İşlevler 1. x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2-0-host-json"></a>Dayanıklı İşlevler 2. x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "controlQueueBatchSize": 32,
      "partitionCount": 4,
      "controlQueueVisibilityTimeout": "00:05:00",
      "workItemQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "connectionStringName": "AzureWebJobsStorage",
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask"
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Görev hub 'ı adları bir harfle başlamalı ve yalnızca harf ve sayılardan oluşmalıdır. Belirtilmemişse, bir işlev uygulaması için varsayılan görev hub 'ı adı **Durablefunctionshub**olur. Daha fazla bilgi için bkz. [görev hub 'ları](../articles/azure-functions/durable-functions-task-hubs.md).

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Diğer [görev hub 'ı](../articles/azure-functions/durable-functions-task-hubs.md) adları, aynı depolama arka ucunu kullanıyor olsalar dahi, birden çok dayanıklı işlevler uygulamayı birbirinden yalıtmak için kullanılabilir.|
|controlQueueBatchSize|32|Denetim sırasından tek seferde çekilecek ileti sayısı.|
|partitionCount |4|Denetim kuyruğu için bölüm sayısı. 1 ile 16 arasında pozitif bir tamsayı olabilir.|
|controlQueueVisibilityTimeout |5 dakika|Sıradan çıkarılan denetim sırası iletilerinin görünürlük zaman aşımı.|
|Workıtemqueuevisibilitytimeout |5 dakika|Sıraya alınan iş öğesi sıra iletilerinin görünürlük zaman aşımı.|
|maxConcurrentActivityFunctions |10 x geçerli makinedeki işlemci sayısı|Tek bir konak örneğinde eşzamanlı olarak işlenebilecek etkinlik işlevlerinin maksimum sayısı.|
|maxConcurrentOrchestratorFunctions |10 x geçerli makinedeki işlemci sayısı|Tek bir konak örneğinde eşzamanlı olarak işlenebilecek Orchestrator işlevlerinin maksimum sayısı.|
|Maxqueuepollingınterval|30 saniye|*SS: DD: ss* biçiminde maksimum denetim ve iş öğesi kuyruğu yoklama aralığı. Daha yüksek değerler ileti işleme gecikmelerinin oluşmasına neden olabilir. Daha düşük değerler, daha yüksek depolama işlemleri nedeniyle depolama maliyetlerinin artmasına neden olabilir.|
|Azurestoraygeconnectionstringname |AzureWebJobsStorage|Temel Azure depolama kaynaklarını yönetmek için kullanılan Azure depolama bağlantı dizesine sahip uygulama ayarının adı.|
|trackingStoreConnectionStringName||Geçmiş ve örnekler tabloları için kullanılacak bağlantı dizesinin adı. Belirtilmezse, `azureStorageConnectionStringName` bağlantısı kullanılır.|
|trackingStoreNamePrefix||`trackingStoreConnectionStringName` belirtildiğinde geçmiş ve örnekler tabloları için kullanılacak ön ek. Ayarlanmamışsa, varsayılan ön ek değeri `DurableTask`olacaktır. `trackingStoreConnectionStringName` belirtilmemişse, geçmiş ve örnekler tabloları ön ek olarak `hubName` değerini kullanır ve `trackingStoreNamePrefix` ayarı yok sayılır.|
|traceInputsAndOutputs |false|İşlev çağrılarının giriş ve çıkışları takip edilip edilmeyeceğini belirten bir değer. İşlev yürütme olaylarının izlenirken, işlev çağrılarının seri hale getirilmiş giriş ve çıkışlarındaki bayt sayısını eklemek varsayılan davranıştır. Bu davranış, girişlerin ve çıktıların günlüğe kaydetmeksizin veya farkında olmadan hassas bilgileri açığa çıkarmadan nasıl göründüğünü öğrenmek için en az bilgi sağlar. Bu özelliğin true olarak ayarlanması, işlev girişlerinin ve çıktıların tüm içeriğini günlüğe kaydetmek için varsayılan işlev günlüğe kaydetmenin oluşmasına neden olur.|
|Günlüğe kaydetme Yevents|false|Application Insights için düzenleme yeniden yürütme olaylarının yazılacağını belirten bir değer.|
|eventGridTopicEndpoint ||Azure Event Grid özel konu uç noktasının URL 'SI. Bu özellik ayarlandığında, düzenleme yaşam döngüsü bildirim olayları bu uç noktada yayımlanır. Bu özellik uygulama ayarları çözümlemesini destekler.|
|eventGridKeySettingName ||`EventGridTopicEndpoint`' de Azure Event Grid özel konu ile kimlik doğrulaması için kullanılan anahtarı içeren uygulama ayarının adı.|
|eventGridPublishRetryCount|0|Event Grid konusunda yayımlama başarısız olursa kaç kez yeniden deneneceği.|
|Eventgridpublishretryınterval|5 dakika|Event Grid, *HH: mm: ss* biçiminde yeniden deneme aralığı yayımlar.|
|eventGridPublishEventTypes||Event Grid yayımlanacak olay türlerinin listesi. Belirtilmezse, tüm olay türleri yayımlanır. İzin verilen değerler `Started`, `Completed`, `Failed`, `Terminated`içerir.|

Bu ayarların birçoğu performansı iyileştirmek için kullanılır. Daha fazla bilgi için bkz. [performans ve ölçek](../articles/azure-functions/durable-functions-perf-and-scale.md).
