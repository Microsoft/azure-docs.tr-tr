---
title: Event Grid kaynak olarak Azure Media Services
description: Azure Event Grid Media Services olaylar için belirtilen özellikleri açıklar
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: b5772a2332e1864d0b8df0d4e102006b29b6a61e
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120121"
---
# <a name="azure-media-services-as-an-event-grid-source"></a>Event Grid kaynak olarak Azure Media Services

Bu makale Media Services olaylar için şemaları ve özellikleri sağlar.

## <a name="job-related-event-types"></a>İşle ilgili olay türleri

Media Services, aşağıda açıklanan **işle ilgili**  olay türlerini yayar. **İşle ilgili** olaylar için iki kategori vardır: "Iş durumu değişikliklerini izleme" ve "Iş çıkış durumu değişikliklerini izleme". 

JobStateChange olayına abone olarak tüm olaylara kaydolabilirsiniz. Ya da yalnızca belirli olaylara abone olabilirsiniz (örneğin, Jobhatalı, JobFinished ve Jobiptal edildi gibi son durumlar).   

### <a name="monitoring-job-state-changes"></a>Iş durumu değişikliklerini izleme

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. Media. JobStateChange| Tüm Iş durumu değişiklikleri için bir olay alın. |
| Microsoft. Media. Jobzamanlandı| Zamanlanan duruma Iş geçişi yaptığında bir olay alın. |
| Microsoft. Media. JobProcessing| Iş durumu işlemeye geçiş yaptığında bir olay alın. |
| Microsoft. Media. Jobiptali| Durumu iptal etmek için Iş geçişleri olduğunda bir olay alın. |
| Microsoft. Media. Jobiptal edildi| Iş iptal edildi durumuna geçiş yaptığında bir olay alın. Bu, Iş çıkışlarını içeren son bir durumdur.|
| Microsoft. Media. Jobhatalı | Iş, hata durumuna geçiş yaptığında bir olay alır. Bu, Iş çıkışlarını içeren son bir durumdur.|

Aşağıdaki [şema örneklerine](#event-schema-examples) bakın.

### <a name="monitoring-job-output-state-changes"></a>İzleme işi çıkış durumu değişiklikleri

Bir iş birden çok iş çıkışı içerebilir (dönüşümü birden çok iş çıkışına sahip olacak şekilde yapılandırdıysanız) Bireysel iş çıkışının ayrıntılarını izlemek isterseniz, iş çıkışı değişiklik olayını dinleyin.

Her bir **Iş** **joverput** öğesinden daha yüksek bir düzeyde olacak, bu nedenle iş çıkışı olayları ilgili bir işin içinde harekete geçirilir. 

İçindeki hata iletileri, `JobFinished` `JobCanceled` `JobError` her bir iş çıkışı için toplanan sonuçları çıktı – bunların hepsi bittiğinde. Her görev tamamlandığında iş çıkış olayları ateşlenir. Örneğin, bir kodlama çıktılarınız varsa ve ardından bir video analizi çıkışı varsa, son JobFinished olayının toplanan verilerle tetiklamadan önce iş çıkış olayları olarak iki olay tetikleyerek bir işlem elde edersiniz.

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. Media. Joi Putstatechange| Tüm Iş çıkış durumu değişiklikleri için bir olay alın. |
| Microsoft. Media. Joi Putzamanlandı| Iş çıkışı zamanlanan duruma geçiş yaptığında bir olay alın. |
| Microsoft. Media. Joi Putprocessing| Iş çıkışı işleme durumuna geçiş yaptığında bir olay alın. |
| Microsoft. Media. Joi Putiptali| Durum iptal etmek için Iş çıktısı geçişleri olduğunda bir olay alın.|
| Microsoft. Media. Joi Putfinished| Iş çıkışı tamamlandı durumuna geçerse bir olay alın.|
| Microsoft. Media. Joi Putiptal edildi| Iş çıkışı iptal edildi durumuna geçerse bir olay alın.|
| Microsoft. Media. JobOutputErrored| Iş çıkışı hata durumuna geçerse bir olay alın.|

Aşağıdaki [şema örneklerine](#event-schema-examples) bakın.

### <a name="monitoring-job-output-progress"></a>İş çıkışı ilerlemesini izleme

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. Media. Joi Putprogress| Bu olay, %0 ' dan %100 ' e kadar iş işleme ilerlemesini yansıtır. Hizmet, ilerleme değerinde %5 veya daha fazla artış varsa veya son olaydan (sinyal) bu yana 30 saniyeden uzun süredir bir olay gönderilmeye çalışır. İlerleme değerinin %0 ' dan başlaması veya %100 ' e ulaşması ya da zaman içinde sabit bir hızda artması garanti edilmez. Bu olay işlemin tamamlandığını belirlemede kullanılmamalıdır; bunun yerine durum değişikliği olaylarını kullanmalısınız.|

Aşağıdaki [şema örneklerine](#event-schema-examples) bakın.

## <a name="live-event-types"></a>Canlı olay türleri

Media Services Ayrıca aşağıda açıklanan **canlı** olay türlerini de yayar. **Canlı** olaylar için iki kategori vardır: akış düzeyi olayları ve izleme düzeyi olayları. 

### <a name="stream-level-events"></a>Akış düzeyi olaylar

Akış düzeyi olaylar, akış veya bağlantı başına oluşturulur. Her olay `StreamId` , bağlantıyı veya akışı tanımlayan bir parametreye sahiptir. Her bir akışta veya bağlantıda farklı türlerin bir veya daha fazla izlemesi vardır. Örneğin, bir kodlayıcının bir bağlantısının bir ses izi ve dört video parçası olabilir. Akış olay türleri şunlardır:

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. Media. Liveeventconnectionreddedildi | Kodlayıcının bağlantı girişimi reddedildi. |
| Microsoft. Media. LiveEventEncoderConnected | Kodlayıcı canlı olayla bağlantı kurar. |
| Microsoft. Media. Liveeventencoderconnected bağlantısı kesildi | Kodlayıcı bağlantısı kesiliyor. |

Aşağıdaki [şema örneklerine](#event-schema-examples) bakın.

### <a name="track-level-events"></a>İzleme düzeyi olaylar

İzleme düzeyi olaylar iz başına getirilir. 

> [!NOTE]
> Tüm izleme düzeyi olaylar, canlı bir kodlayıcı bağlandıktan sonra tetiklenir.

İzleme düzeyi olay türleri şunlardır:

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. Media. Liveeventincomingdatachunkbırakılan | Medya sunucusu çok geç olduğu veya çakışan bir zaman damgasına sahip olduğu için veri öbeğini bırakıyor (yeni veri öbeğinin zaman damgası, önceki veri öbeğinin bitiş zamanından daha az). |
| Microsoft. Media. Liveeventincomingstreamalındı | Medya sunucusu akıştaki veya bağlantıdaki her bir parça için ilk veri öbeki alır. |
| Microsoft. Media. LiveEventIncomingStreamsOutOfSync | Medya sunucusu sesi algılar ve video akışları eşitlenmemiş. Kullanıcı deneyimi etkilenmediği için uyarı olarak kullanın. |
| Microsoft. Media. LiveEventIncomingVideoStreamsOutOfSync | Media Server, dış kodlayıcılardan gelen iki video akışının herhangi birini algılar. Kullanıcı deneyimi etkilenmediği için uyarı olarak kullanın. |
| Microsoft. Media. Liveeventingesthearti | Canlı olay çalışırken her bir parça için her 20 saniyede bir yayımlanır. Inest sistem durumu özetini sağlar.<br/><br/>Kodlayıcının ilk bağlantısı yapıldıktan sonra, sinyal olayı, kodlayıcının hala bağlı olup olmadığına bakılmaksızın her 20 saniyede bir yaymaya devam eder. |
| Microsoft. Media. Liveeventtrackdiscontinuityalgılanan | Media Server, gelen izlemede süreksizlik algılar. |

Aşağıdaki [şema örneklerine](#event-schema-examples) bakın.

## <a name="event-schema-examples"></a>Olay şeması örnekleri

### <a name="jobstatechange"></a>JobStateChange

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Aşağıdaki örnek, **Jobstatechange** olayının şemasını göstermektedir: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Aşağıdaki örnek, **Jobstatechange** olayının şemasını göstermektedir: 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "type": "Microsoft.Media.JobStateChange",
    "time": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "specversion": "1.0"
  }
]
```

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `previousState` | dize | Olaydan önceki işin durumu. |
| `state` | string | Bu olayda bildirim yapılacak işin yeni durumu. Örneğin, "zamanlandı: iş başlamaya hazırlanıyor" veya "tamamlandı: iş tamamlandı".|

Iş durumunun değerlerden biri olabilir: *sıraya alınmış*, *Zamanlanmış*, *işleme*, *tamamlandı*, *hata*, *iptal edildi*, *iptal etme*

> [!NOTE]
> *Kuyruğa* alma yalnızca **PreviousState** özelliğinde bulunur, ancak **durum** özelliğinde yer almıyor.

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>Jobzamanlandı, JobProcessing, Jobiptali

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Son olmayan her Iş durumu değişikliği için (örneğin, Jobzamanlandı, JobProcessing, Jobiptal), örnek şema şuna benzer:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, Jobiptal edildi, Jobhatalı

Her son Iş durumu değişikliği için (örneğin, JobFinished, Jobiptal, Jobhatalı), örnek şema şuna benzer:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Son olmayan her Iş durumu değişikliği için (örneğin, Jobzamanlandı, JobProcessing, Jobiptal), örnek şema şuna benzer:

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobProcessing",
  "time": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, Jobiptal edildi, Jobhatalı

Her son Iş durumu değişikliği için (örneğin, JobFinished, Jobiptal, Jobhatalı), örnek şema şuna benzer:

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobFinished",
  "time": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "specversion": "1.0"
}]
```

---


Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `outputs` | Dizi | Iş çıkışlarını alır.|

### <a name="joboutputstatechange"></a>Joi Putstatechange

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Aşağıdaki örnek, **Joi Putstatechange** olayının şemasını göstermektedir:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>Joi Putzamanlandı, Joiputprocessing, Joiputfinished, Joiputiptali, Joiputiptal edildi, JobOutputErrored

Her Joi put durumu değişikliği için örnek şema şuna benzer:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>Joi Putprogress

Örnek şema şuna benzer:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>Liveeventconnectionreddedildi

Aşağıdaki örnekte, **Liveeventconnectionreddedildi** olayının şeması gösterilmektedir: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Aşağıdaki örnek, **Joi Putstatechange** olayının şemasını göstermektedir:

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobOutputStateChange",
  "time": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>Joi Putzamanlandı, Joiputprocessing, Joiputfinished, Joiputiptali, Joiputiptal edildi, JobOutputErrored

Her Joi put durumu değişikliği için örnek şema şuna benzer:

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobOutputProcessing",
  "time": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "specversion": "1.0"
}]
```
### <a name="joboutputprogress"></a>Joi Putprogress

Örnek şema şuna benzer:

 ```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "type": "Microsoft.Media.JobOutputProgress",
  "time": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "specversion": "1.0"
}]
```

### <a name="liveeventconnectionrejected"></a>Liveeventconnectionreddedildi

Aşağıdaki örnekte, **Liveeventconnectionreddedildi** olayının şeması gösterilmektedir: 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "type": "Microsoft.Media.LiveEventConnectionRejected",
    "time": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "specversion": "1.0"
  }
]
```

---


Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `streamId` | dize | Akışın veya bağlantının tanımlayıcısı. Kodlayıcı veya müşteri, bu KIMLIĞI alma URL 'sine eklemekten sorumludur. |  
| `ingestUrl` | string | Canlı olay tarafından sunulan alma URL 'SI. |  
| `encoderIp` | string | Kodlayıcının IP 'si. |
| `encoderPort` | string | Bu akışın geldiği kodlayıcının bağlantı noktası. |
| `resultCode` | string | Bağlantının reddedilme nedeni. Sonuç kodları aşağıdaki tabloda listelenmiştir. |

Hata sonuç kodlarını [canlı olay hata kodlarında](../media-services/latest/live-event-error-codes-reference.md)bulabilirsiniz.

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Aşağıdaki örnekte, **Liveeventencoderconnected** olayının şeması gösterilmektedir: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Aşağıdaki örnekte, **Liveeventencoderconnected** olayının şeması gösterilmektedir: 

```json
[
  { 
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventEncoderConnected",
    "time": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "specversion": "1.0"
  }
]
```

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `streamId` | dize | Akışın veya bağlantının tanımlayıcısı. Kodlayıcı veya müşteri, bu KIMLIĞI alma URL 'SI içinde sağlamaktan sorumludur. |
| `ingestUrl` | string | Canlı olay tarafından sunulan alma URL 'SI. |
| `encoderIp` | string | Kodlayıcının IP 'si. |
| `encoderPort` | string | Bu akışın geldiği kodlayıcının bağlantı noktası. |

### <a name="liveeventencoderdisconnected"></a>Liveeventencoderconnected bağlantısı kesildi

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Aşağıdaki örnek **Liveeventencoderconnected** olayının şemasını gösterir: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Aşağıdaki örnek **Liveeventencoderconnected** olayının şemasını gösterir: 

```json
[
  { 
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventEncoderDisconnected",
    "time": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "specversion": "1.0"
  }
]
```

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `streamId` | dize | Akışın veya bağlantının tanımlayıcısı. Kodlayıcı veya müşteri, bu KIMLIĞI alma URL 'sine eklemekten sorumludur. |  
| `ingestUrl` | string | Canlı olay tarafından sunulan alma URL 'SI. |  
| `encoderIp` | string | Kodlayıcının IP 'si. |
| `encoderPort` | string | Bu akışın geldiği kodlayıcının bağlantı noktası. |
| `resultCode` | string | Kodlayıcı bağlantısının kesilmesi nedeni. Bu, düzgün bir şekilde kesilmesi veya bir hatadan olabilir. Sonuç kodları aşağıdaki tabloda listelenmiştir. |

Hata sonuç kodlarını [canlı olay hata kodlarında](../media-services/latest/live-event-error-codes-reference.md)bulabilirsiniz.

Düzgün olmayan bağlantıyı kesme sonuç kodları şunlardır:

| Sonuç kodu | Description |
| ----------- | ----------- |
| S_OK | Kodlayıcının bağlantısı başarıyla kesildi. |
| MPE_CLIENT_TERMINATED_SESSION | Kodlayıcı bağlantısı kesildi (RTMP). |
| MPE_CLIENT_DISCONNECTED | Kodlayıcı bağlantısı kesildi (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Kanal sıfırlama komutu alındı. |
| MPI_REST_API_CHANNEL_STOP | Kanal durdurma komutu alındı. |
| MPI_REST_API_CHANNEL_STOP | Kanal bakımda. |
| MPI_STREAM_HIT_EOF | EOF akışı kodlayıcı tarafından gönderilir. |

### <a name="liveeventincomingdatachunkdropped"></a>Liveeventıncomingdatachunkbırakılan

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Aşağıdaki örnek, **Liveeventincomingdatachunkbırakılan** olayının şemasını gösterir: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Aşağıdaki örnek, **Liveeventincomingdatachunkbırakılan** olayının şemasını gösterir: 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "type": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "time": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "specversion": "1.0"
  }
]
```

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `trackType` | dize | İzlemenin türü (ses/video). |
| `trackName` | string | İzlemenin adı. |
| `bitrate` | tamsayı | İzlemenin bit hızı. |
| `timestamp` | string | Atlanan veri öbeğinin zaman damgası. |
| `timescale` | string | Zaman damgasının zaman ölçeği. |
| `resultCode` | string | Veri öbeği bırakma nedeni. **FragmentDrop_OverlapTimestamp** veya **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>Liveeventincomingstreamalındı

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Aşağıdaki örnekte, **Liveeventincomingstreamreceived** olayının şeması gösterilmektedir: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Aşağıdaki örnekte, **Liveeventincomingstreamreceived** olayının şeması gösterilmektedir: 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "time": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "specversion": "1.0"
  }
]
```

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `trackType` | dize | İzlemenin türü (ses/video). |
| `trackName` | string | İzlemenin adı (kodlayıcı tarafından sağlandığı veya RTMP durumunda sunucu *TrackType_Bitrate* biçimde oluşturulur). |
| `bitrate` | tamsayı | İzlemenin bit hızı. |
| `ingestUrl` | string | Canlı olay tarafından sunulan alma URL 'SI. |
| `encoderIp` | string  | Kodlayıcının IP 'si. |
| `encoderPort` | string | Bu akışın geldiği kodlayıcının bağlantı noktası. |
| `timestamp` | string | Alınan veri öbeğinin ilk zaman damgası. |
| `timescale` | string | Zaman damgasının temsil edildiği zaman ölçeği. |

### <a name="liveeventincomingstreamsoutofsync"></a>Liveevenıncomingstreamsoutofsync

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Aşağıdaki örnekte, **Liveeventincomingstreamsoutofsync** olayının şeması gösterilmektedir: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Aşağıdaki örnekte, **Liveeventincomingstreamsoutofsync** olayının şeması gösterilmektedir: 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "time": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "specversion": "1.0"
  }
]
```

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `minLastTimestamp` | dize | Tüm parçalar arasındaki en az son zaman damgası (ses veya video). |
| `typeOfTrackWithMinLastTimestamp` | string | En az son zaman damgasıyla izleme (ses veya video) türü. |
| `maxLastTimestamp` | string | Tüm parçalar arasındaki tüm zaman damgalarının (ses veya video) en fazla sayısı. |
| `typeOfTrackWithMaxLastTimestamp` | string | En fazla son zaman damgasına sahip parça (ses veya video) türü. |
| `timescaleOfMinLastTimestamp`| string | "MinLastTimestamp" nin temsil edildiği zaman ölçeğini alır.|
| `timescaleOfMaxLastTimestamp`| string | "MaxLastTimestamp" nin temsil edildiği zaman ölçeğini alır.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>Liveevenıncomingvideostreamsoutofsync

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Aşağıdaki örnekte, **Liveeventincomingvideostreamsoutofsync** olayının şeması gösterilmektedir: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Aşağıdaki örnekte, **Liveeventincomingvideostreamsoutofsync** olayının şeması gösterilmektedir: 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "type": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "time": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "specversion": "1.0"
  }
]
```

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `firstTimestamp` | dize | Video türünde parçalar/kalite seviyelerinin biri için zaman damgası alındı. |
| `firstDuration` | string | İlk zaman damgasıyla veri öbeğinin süresi. |
| `secondTimestamp` | string  | Video türünde başka bir izleme/kalite düzeyi için zaman damgası alındı. |
| `secondDuration` | string | İkinci zaman damgasıyla veri öbeğinin süresi. |
| `timescale` | string | Zaman damgalarının ve sürenin zaman ölçeği.|

### <a name="liveeventingestheartbeat"></a>Liveeventingesthearti

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Aşağıdaki örnek, **Liveeventingestheartma** olayının şemasını göstermektedir: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)


Aşağıdaki örnek, **Liveeventingestheartma** olayının şemasını göstermektedir: 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIngestHeartbeat",
    "time": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "specversion": "1.0"
  }
]
```

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `trackType` | dize | İzlemenin türü (ses/video). |
| `trackName` | string | İzlemenin adı (kodlayıcı tarafından sağlandığı veya RTMP durumunda sunucu *TrackType_Bitrate* biçimde oluşturulur). |
| `bitrate` | tamsayı | İzlemenin bit hızı. |
| `incomingBitrate` | tamsayı | Kodlayıcıdan gelen veri öbeklerini temel alan hesaplanan bit hızı. |
| `lastTimestamp` | string | Son 20 saniye içindeki bir izleme için en son zaman damgası alındı. |
| `timescale` | string | Zaman damgalarının ifade edildiği zaman ölçeği. |
| `overlapCount` | tamsayı | Veri öbeklerinin sayısı, son 20 saniye içinde çakışan zaman damgalarına sahipti. |
| `discontinuityCount` | tamsayı | Son 20 saniye içinde gözlenen süreksizlik sayısı. |
| `nonIncreasingCount` | tamsayı | Son 20 saniye içinde, geçmişteki zaman damgalarına sahip veri öbeklerinin sayısı alındı. |
| `unexpectedBitrate` | bool | Beklenen ve gerçek bitoranlar, son 20 saniye içinde izin verilen sınırın üzerinde farklılık gösterir. Yalnızca ve yalnızca, ıncomingbit hızı >= 2 * bit hızı veya ıncomingbit hızı <= bit hızı/2 ya da ıncomingbit hızı = 0 olduğunda geçerlidir. |
| `state` | string | Canlı etkinliğin durumu. |
| `healthy` | bool | Alma sayısının, sayımlar ve bayraklara göre sağlıklı olup olmadığını gösterir. OverlapCount = 0 && discontinuityCount = 0 && Nonıncreasingcount = 0 && unexpectedBitrate = false olduğunda sağlıklı değeri true. |

### <a name="liveeventtrackdiscontinuitydetected"></a>Liveeventtrackdiscontinuityalgılandı

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Aşağıdaki örnek, **Liveeventtrackdiscontinuityalgılanan** olayının şemasını gösterir: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Aşağıdaki örnek, **Liveeventtrackdiscontinuityalgılanan** olayının şemasını gösterir: 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "time": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "specversion": "1.0"
  }
]
```

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `trackType` | dize | İzlemenin türü (ses/video). |
| `trackName` | string | İzlemenin adı (kodlayıcı tarafından sağlandığı veya RTMP durumunda sunucu *TrackType_Bitrate* biçimde oluşturulur). |
| `bitrate` | tamsayı | İzlemenin bit hızı. |
| `previousTimestamp` | string | Önceki parçanın zaman damgası. |
| `newTimestamp` | string | Geçerli parçanın zaman damgası. |
| `discontinuityGap` | string | İki zaman damgası üzerinde boşluk. |
| `timescale` | string | Hem zaman damgası hem de süreksizlik boşluğunu temsil eden zaman ölçeği. |

### <a name="common-event-properties"></a>Ortak olay özellikleri

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `topic` | dize | Olay Kılavuzu konusu. Bu özelliğin Media Services hesabının kaynak KIMLIĞI vardır. |
| `subject` | string | Media Services hesabının altındaki Media Services kanalının kaynak yolu. Konunun ve konunun bitiştirerek iş için kaynak KIMLIĞI verilmektedir. |
| `eventType` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. Örneğin, "Microsoft. Media. JobStateChange". |
| `eventTime` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | Olay verilerini Media Services. |
| `dataVersion` | string | Veri nesnesinin şema sürümü. Şema sürümünü yayımcı tanımlar. |
| `metadataVersion` | string | Olay meta verilerinin şema sürümü. Event Grid en üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `source` | dize | Olay Kılavuzu konusu. Bu özelliğin Media Services hesabının kaynak KIMLIĞI vardır. |
| `subject` | string | Media Services hesabının altındaki Media Services kanalının kaynak yolu. Konunun ve konunun bitiştirerek iş için kaynak KIMLIĞI verilmektedir. |
| `type` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. Örneğin, "Microsoft. Media. JobStateChange". |
| `time` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | Olay verilerini Media Services. |
| `specversion` | string | CloudEvents şema belirtimi sürümü. |


---

## <a name="next-steps"></a>Sonraki adımlar

[İş durumu değişikliği olaylarını Kaydet](../media-services/latest/monitoring/job-state-events-cli-how-to.md)

## <a name="see-also"></a>Ayrıca bkz.

- [Media Service olaylarını içeren EventGrid .NET SDK 'Sı](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services olaylarının tanımları](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Canlı olay hata kodları](../media-services/latest/live-event-error-codes-reference.md)
