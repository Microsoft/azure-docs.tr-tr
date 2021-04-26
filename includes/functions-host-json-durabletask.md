---
title: include dosyası
description: include dosyası
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d1b2cbefe64add5048d4e3c7946277b1461a5434
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607103"
---
[Dayanıklı işlevler](../articles/azure-functions/durable/durable-functions-overview.md)için yapılandırma ayarları.

> [!NOTE]
> Tüm Dayanıklı İşlevler ana sürümleri Azure Işlevleri çalışma zamanının tüm sürümlerinde desteklenir. Ancak, yapılandırmadaki host.jsşeması, Azure Işlevleri çalışma zamanının sürümüne ve kullandığınız Dayanıklı İşlevler uzantı sürümüne bağlı olarak biraz farklıdır. Aşağıdaki örnekler, 2,0 ve 3,0 Azure Işlevleri ile kullanım içindir. Her iki örnekte de, Azure Işlevleri 1,0 kullanıyorsanız, kullanılabilir ayarlar aynıdır, ancak host.js"durableTask" bölümü, "Uzantılar" altında bir alan yerine yapılandırma üzerindeki host.jsköküne gitmelidir.

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Dayanıklı İşlevler 2. x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "useLegacyPartitionManagement": true,
      "workItemQueueVisibilityTimeout": "00:05:00",
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
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useAppLease": true,
    "useGracefulShutdown": false
  }
 }
}

```

### <a name="durable-functions-1x"></a>Dayanıklı İşlevler 1. x

```json
{
  "extensions": {
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
}
```

Görev hub 'ı adları bir harfle başlamalı ve yalnızca harf ve sayılardan oluşmalıdır. Belirtilmemişse, bir işlev uygulaması için varsayılan görev hub 'ı adı **Durablefunctionshub** olur. Daha fazla bilgi için bkz. [görev hub 'ları](../articles/azure-functions/durable/durable-functions-task-hubs.md).

|Özellik  |Varsayılan | Description |
|---------|---------|----------|
|hubName|DurableFunctionsHub|Diğer [görev hub 'ı](../articles/azure-functions/durable/durable-functions-task-hubs.md) adları, aynı depolama arka ucunu kullanıyor olsalar dahi, birden çok dayanıklı işlevler uygulamayı birbirinden yalıtmak için kullanılabilir.|
|controlQueueBatchSize|32|Denetim sırasından tek seferde çekilecek ileti sayısı.|
|controlQueueBufferThreshold| **Tüketim planı**: 32 <br> **Adanmış/Premium plan**: 256 |Aynı anda bellekte ara belleğe alınmış olan denetim sırası iletilerinin sayısı, bu noktada dağıtıcı herhangi bir ek ileti kuyruğa almadan önce bekleyeceği süre.|
|partitionCount |4|Denetim kuyruğu için bölüm sayısı. 1 ile 16 arasında pozitif bir tamsayı olabilir.|
|controlQueueVisibilityTimeout |5 dakika|Sıradan çıkarılan denetim sırası iletilerinin görünürlük zaman aşımı.|
|Workıtemqueuevisibilitytimeout |5 dakika|Sıraya alınan iş öğesi sıra iletilerinin görünürlük zaman aşımı.|
|maxConcurrentActivityFunctions | **Tüketim planı**: 10 <br> **Adanmış/Premium plan**: 10X geçerli makinedeki işlemci sayısı|Tek bir konak örneğinde eşzamanlı olarak işlenebilecek etkinlik işlevlerinin maksimum sayısı.|
|maxConcurrentOrchestratorFunctions | **Tüketim planı**: 5 <br> **Adanmış/Premium plan**: 10X geçerli makinedeki işlemci sayısı |Tek bir konak örneğinde eşzamanlı olarak işlenebilecek Orchestrator işlevlerinin maksimum sayısı.|
|Maxqueuepollingınterval|30 saniye|*SS: DD: ss* biçiminde maksimum denetim ve iş öğesi kuyruğu yoklama aralığı. Daha yüksek değerler ileti işleme gecikmelerinin oluşmasına neden olabilir. Daha düşük değerler, daha yüksek depolama işlemleri nedeniyle depolama maliyetlerinin artmasına neden olabilir.|
|Azurestoraygeconnectionstringname |AzureWebJobsStorage|Temel Azure depolama kaynaklarını yönetmek için kullanılan Azure depolama bağlantı dizesine sahip uygulama ayarının adı.|
|trackingStoreConnectionStringName||Geçmiş ve örnekler tabloları için kullanılacak bağlantı dizesinin adı. Belirtilmemişse, `connectionStringName` (dayanıklı 2. x) veya `azureStorageConnectionStringName` (dayanıklı 1. x) bağlantısı kullanılır.|
|trackingStoreNamePrefix||Belirtildiğinde, geçmiş ve örnekleri tabloları için kullanılacak ön ek `trackingStoreConnectionStringName` . Ayarlanmamışsa, varsayılan ön ek değeri olacaktır `DurableTask` . `trackingStoreConnectionStringName`Belirtilmemişse, geçmiş ve örnekler tabloları `hubName` ön ekler olarak değeri kullanır ve için herhangi bir ayar `trackingStoreNamePrefix` yok sayılır.|
|traceInputsAndOutputs |yanlış|İşlev çağrılarının giriş ve çıkışları takip edilip edilmeyeceğini belirten bir değer. İşlev yürütme olaylarının izlenirken, işlev çağrılarının seri hale getirilmiş giriş ve çıkışlarındaki bayt sayısını eklemek varsayılan davranıştır. Bu davranış, girişlerin ve çıktıların günlüğe kaydetmeksizin veya farkında olmadan hassas bilgileri açığa çıkarmadan nasıl göründüğünü öğrenmek için en az bilgi sağlar. Bu özelliğin true olarak ayarlanması, işlev girişlerinin ve çıktıların tüm içeriğini günlüğe kaydetmek için varsayılan işlev günlüğe kaydetmenin oluşmasına neden olur.|
|Günlüğe kaydetme Yevents|yanlış|Application Insights için düzenleme yeniden yürütme olaylarının yazılacağını belirten bir değer.|
|eventGridTopicEndpoint ||Azure Event Grid özel konu uç noktasının URL 'SI. Bu özellik ayarlandığında, düzenleme yaşam döngüsü bildirim olayları bu uç noktada yayımlanır. Bu özellik uygulama ayarları çözümlemesini destekler.|
|eventGridKeySettingName ||Üzerinde Azure Event Grid özel konu ile kimlik doğrulaması için kullanılan anahtarı içeren uygulama ayarının adı `EventGridTopicEndpoint` .|
|eventGridPublishRetryCount|0|Event Grid konusunda yayımlama başarısız olursa kaç kez yeniden deneneceği.|
|Eventgridpublishretryınterval|5 dakika|Event Grid, *HH: mm: ss* biçiminde yeniden deneme aralığı yayımlar.|
|eventGridPublishEventTypes||Event Grid yayımlanacak olay türlerinin listesi. Belirtilmezse, tüm olay türleri yayımlanır. İzin verilen değerler şunlardır,,, `Started` `Completed` `Failed` `Terminated` .|
|useAppLease|true|Olarak ayarlandığında `true` , uygulamalar, görev hub 'ı iletilerini işlemeden önce uygulama düzeyi blob kirası almak için gerekli olacaktır. Daha fazla bilgi için [olağanüstü durum kurtarma ve coğrafi dağıtım](../articles/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution.md) belgelerine bakın. V 2.3.0 'dan başlayarak kullanılabilir.
|useLegacyPartitionManagement|yanlış|Olarak ayarlandığında `false` , ölçekleme sırasında yinelenen işlev yürütme olasılığını azaltan bir bölüm yönetimi algoritması kullanır.  V 2.3.0 'dan başlayarak kullanılabilir.|
|useGracefulShutdown|yanlış|Önizle İşlem sırasında işlev yürütmelerinin başarısız olmasına neden olan konak kapanmalarının olasılığını azaltmak için düzgün bir şekilde kapatmayı etkinleştirin.|

Bu ayarların birçoğu performansı iyileştirmek için kullanılır. Daha fazla bilgi için bkz. [performans ve ölçek](../articles/azure-functions/durable/durable-functions-perf-and-scale.md).
