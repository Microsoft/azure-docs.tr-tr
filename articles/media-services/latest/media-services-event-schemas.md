---
title: Media Services olayları Azure Event Grid şemaları
description: Media Services olayları Azure Event Grid ile sağlanan özellikleri tanımlar
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: 2d1e648a9ea33beb1347a4a635388ee04e46215b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449766"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Media Services olayları Azure Event Grid şemaları

Bu makalede, Media Services olaylarını şemaları ve özellikleri sağlar.

Örnek betikler ve öğreticiler listesi için bkz: [Media Services olay kaynağı](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="job-related-event-types"></a>İş ilgili olay türleri

Media Services yayan **iş** ilgili olay türleri aşağıda açıklanmıştır. İçin iki kategorisi vardır **iş** ilgili olaylar: "İzleme iş durumu" ve "İzleme iş Çıkış durumu değiştirir". 

Tüm olaylar için JobStateChange olaya abone olarak kaydedebilirsiniz. Veya yalnızca belirli olaylar (örneğin, son durumlarını JobErrored JobFinished ve JobCanceled gibi) için abone olabilirsiniz. 

### <a name="monitoring-job-state-changes"></a>İş durumu değişiklikleri izleme

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Bir olay için tüm iş durumu değişiklikleri alırsınız. |
| Microsoft.Media.JobScheduled| İşi zamanlanmış duruma geçtiğinde bir olay alırsınız. |
| Microsoft.Media.JobProcessing| İş işleme durumu için geçiş yaptığında bir olay alırsınız. |
| Microsoft.Media.JobCanceling| İş durumu iptal etmek için geçiş yaptığında bir olay alırsınız. |
| Microsoft.Media.JobFinished| İş bitti durumuna geçtiğinde bir olay alırsınız. Bu iş çıkışları içeren son bir durumdur.|
| Microsoft.Media.JobCanceled| İş iptal edilmiş duruma geçtiğinde bir olay alırsınız. Bu iş çıkışları içeren son bir durumdur.|
| Microsoft.Media.JobErrored| İş hata durumuna geçtiğinde bir olay alırsınız. Bu iş çıkışları içeren son bir durumdur.|

Bkz: [Şeması Örnekleri](#event-schema-examples) anlatılmaktadır.

### <a name="monitoring-job-output-state-changes"></a>Durum değişikliklerini İzleme işi çıkışı

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Bir olay durumu değişiklikleri tüm iş çıktısı alırsınız. |
| Microsoft.Media.JobOutputScheduled| Zamanlanmış durumu geçişleri iş çıkışı, bir olay alırsınız. |
| Microsoft.Media.JobOutputProcessing| Bir olay işleme durumu için geçiş işi çıktısını alın. |
| Microsoft.Media.JobOutputCanceling| İş iptal ediliyor durumunda geçiş çıkışı, bir olay alırsınız.|
| Microsoft.Media.JobOutputFinished| İş çıkışı geçişleri durumu tamamlandığında bir olay alırsınız.|
| Microsoft.Media.JobOutputCanceled| Proje çıkış geçişi için durum iptal ettiğinizde, bir olay alırsınız.|
| Microsoft.Media.JobOutputErrored| Hata durumu geçişleri iş çıkışı, bir olay alırsınız.|

Bkz: [Şeması Örnekleri](#event-schema-examples) anlatılmaktadır.

### <a name="monitoring-job-output-progress"></a>İlerleme İzleme işi çıkışı

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Bu olay işleme ilerleme durumu %0 ile % 100'den iş yansıtır. %5 olmuştur ya da büyük artış ilerleme değeri veya son olayın (sinyal aralığı) itibaren 30 saniyeden uzun süredir olay göndermek hizmet çalışır. %0 başlatmak için ya da % 100 ulaşmaya devam eden değer garanti edilmez ya da sabit bir fiyat karşılığında zamanla artacağını garanti edilir. Bu olayın işlenmesi tamamlandı-durum değişikliği olayları kullanmalısınız belirlemek için kullanılmamalıdır.|

Bkz: [Şeması Örnekleri](#event-schema-examples) anlatılmaktadır.

## <a name="live-event-types"></a>Canlı etkinlik türleri

Medya Hizmetleri de yayan **canlı** olay türleri aşağıda açıklanmıştır. İçin iki kategorisi vardır **canlı** olayları: akış düzeyinde olaylar ve izleme düzeyi olaylar. 

### <a name="stream-level-events"></a>Stream düzeyinde olaylar

Stream düzeyinde olaylar, akış veya bağlantı oluşturulur. Her olayda bir `StreamId` bağlantı veya akış tanımlayan bir parametre. Her akış veya bağlantı farklı türde bir veya daha fazla parça vardır. Örneğin, bir bağlantıdan bir kodlayıcı, bir ses kaydı ve dört video parçaları sahip olabilir. Akış olayı türü şunlardır:

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | Kodlayıcı'nın bağlantı girişimini reddetti. |
| Microsoft.Media.LiveEventEncoderConnected | Kodlayıcı Canlı etkinlik ile bağlantı kurar. |
| Microsoft.Media.LiveEventEncoderDisconnected | Kodlayıcı bağlantısını keser. |

Bkz: [Şeması Örnekleri](#event-schema-examples) anlatılmaktadır.

### <a name="track-level-events"></a>İzleme düzeyi olayları

İzleme düzeyi olaylar, parça oluşturulur. 

> [!NOTE]
> Gerçek zamanlı bir kodlayıcı bağlandıktan sonra tüm izleme düzeyi olaylar oluşturulur.

İzleme düzeyi olay türleri şunlardır:

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Medya sunucusu çok geç veya çakışan bir zaman damgası olduğundan veri öbeği düşene (yeni veri öbeğin zaman damgası olan önceki verileri öbek, son saatten daha az). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Medya sunucusu, her parça için ilk veri öbeği stream ya da bağlantı alır. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Ses medya sunucusu algılar ve video akışları eşitlenmiş halde değil. Kullanıcı deneyimi olmayan etkilenebilir, çünkü bir uyarı olarak kullanın. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Medya sunucusu herhangi bir dış kodlayıcıdan gelen iki video akışları eşitlenmemiş algılar. Kullanıcı deneyimi olmayan etkilenebilir, çünkü bir uyarı olarak kullanın. |
| Microsoft.Media.LiveEventIngestHeartbeat | Canlı etkinlik çalıştırıldığında, her parça için her 20 saniyede yayımladı. Sağlar sistem durumu özetini alın.<br/><br/>Kodlayıcı başlangıçta bağlı sonra Kodlayıcı veya hala bağlı olup olmadığını her 20 saniye yaymak sinyal olay devam eder. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Medya sunucusu süreksizlik gelen izde algılar. |

Bkz: [Şeması Örnekleri](#event-schema-examples) anlatılmaktadır.

## <a name="event-schema-examples"></a>Olay Şeması Örnekleri

### <a name="jobstatechange"></a>JobStateChange

Aşağıdaki örnek, şemasını gösterir **JobStateChange** olay: 

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

Veri nesnesi, aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| previousState | string | Olay önce iş durumu. |
| state | string | Bu durumda bildirilmesini işi yeni durumu. Örneğin, "zamanlandı: İşi başlatmak hazır"veya" tamamlandı: İş tamamlandı".|

Burada iş durumu değerlerden biri olabilir: *Kuyruğa Alınan*, *zamanlanmış*, *işleme*, *tamamlandı*, *hata*, *iptal*, *İptal ediliyor*

> [!NOTE]
> *Kuyruğa Alınan* yalnızca bulunması geçiyor **previousState** özelliği de **durumu** özelliği.

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Her olmayan son iş durumu değişikliği için (örneğin, JobScheduled, JobProcessing, JobCanceling), şema örneği aşağıdaki gibi görünür:

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

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

Her son iş durumu değişikliği için (örneğin, JobFinished, JobCanceled, JobErrored), şema örneği aşağıdaki gibi görünür:

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

Veri nesnesi, aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| outputs | Array | İş çıkışları alır.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

Aşağıdaki örnek, şemasını gösterir **JobOutputStateChange** olay:

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

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Her JobOutput durum değişikliği için şema örneği aşağıdaki gibi görünür:

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
### <a name="joboutputprogress"></a>JobOutputProgress

Şema örneği, aşağıdakine benzer olacaktır:

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

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Aşağıdaki örnek, şemasını gösterir **LiveEventConnectionRejected** olay: 

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

Veri nesnesi, aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| streamıd | string | Bağlantı ve akış tanımlayıcısı. Alma URL'si bu kimliği eklemek için Kodlayıcı veya müşteri sorumludur. |  
| ingestUrl | string | Canlı etkinliği tarafından sağlanan URL alın. |  
| encoderIp | string | Kodlayıcı IP'si. |
| encoderPort | string | Gelen bu akış nereden geldiğini bir kodlayıcının bağlantı noktası. |
| resultCode | string | Bunun nedeni bağlantı reddedildi. Sonuç kodları, aşağıdaki tabloda listelenmiştir. |

Sonuç kodları şunlardır:

| Sonuç kodu | Açıklama |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Yanlış alma URL'si |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Kodlayıcı IP içinde IP hazır değilse izin verilenler listesi yapılandırılmış |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Kodlayıcı stream hakkında meta veri gönderin. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Belirtilen codec desteklenmez. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | Alma ve üst bilgisi bu akış için önce bir parça aldı. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | Belirtilen kalitelerini sayısı izin verilen maksimum sınırı aşıyor. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | Toplam hızı maksimum izin verilen sınırı aşıyor. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | RTMP kodlayıcıdan video veya ses FLVTag için zaman damgası geçersiz. |

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

Aşağıdaki örnek, şemasını gösterir **LiveEventEncoderConnected** olay: 

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

Veri nesnesi, aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| streamıd | string | Bağlantı ve akış tanımlayıcısı. Kodlayıcı veya müşteri bu kimliği alma URL'si sağlamaktan sorumludur. |
| ingestUrl | string | Canlı etkinliği tarafından sağlanan URL alın. |
| encoderIp | string | Kodlayıcı IP'si. |
| encoderPort | string | Gelen bu akış nereden geldiğini bir kodlayıcının bağlantı noktası. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Aşağıdaki örnek, şemasını gösterir **LiveEventEncoderDisconnected** olay: 

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

Veri nesnesi, aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| streamıd | string | Bağlantı ve akış tanımlayıcısı. Alma URL'si bu kimliği eklemek için Kodlayıcı veya müşteri sorumludur. |  
| ingestUrl | string | Canlı etkinliği tarafından sağlanan URL alın. |  
| encoderIp | string | Kodlayıcı IP'si. |
| encoderPort | string | Gelen bu akış nereden geldiğini bir kodlayıcının bağlantı noktası. |
| resultCode | string | Kesme Kodlayıcı nedeni. Normal bağlantıyı kes olabilir veya bir hata oluştu. Sonuç kodları, aşağıdaki tabloda listelenmiştir. |

Hata sonuç kodları şunlardır:

| Sonuç kodu | Açıklama |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | RTMP oturumu için izin verilen süre sınırı boşta kaldıktan sonra zaman aşımına uğradı. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | RTMP kodlayıcıdan video veya ses FLVTag için zaman damgası geçersiz. |
| MPE_CAPACITY_LIMIT_REACHED | Çok hızlı veri gönderen Kodlayıcı. |
| Bilinmeyen hata kodları | Bu hata kodları karma eşleme girişleri çoğaltmak için bellek hatası değişebilir. |

Normal bağlantıyı kes sonuç kodları şunlardır:

| Sonuç kodu | Açıklama |
| ----------- | ----------- |
| S_OK | Kodlayıcı bağlantısı başarıyla kesildi. |
| MPE_CLIENT_TERMINATED_SESSION | Kodlayıcı (RTMP) bağlantısı kesildi. |
| MPE_CLIENT_DISCONNECTED | Kodlayıcı (FMP4) bağlantısı kesildi. |
| MPI_REST_API_CHANNEL_RESET | Kanal sıfırlama komutu aldı. |
| MPI_REST_API_CHANNEL_STOP | Kanal Durdur komutu aldı. |
| MPI_REST_API_CHANNEL_STOP | Kanal bakıma alınıyor. |
| MPI_STREAM_HIT_EOF | EOF stream Kodlayıcı tarafından gönderilir. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

Aşağıdaki örnek, şemasını gösterir **LiveEventIncomingDataChunkDropped** olay: 

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

Veri nesnesi, aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| trackType | string | İzleme türü (Ses / Video). |
| trackName | string | İzleme adı. |
| bitrate | integer | İzleme hızı. |
| timestamp | string | Bırakılan veri öbeğin zaman damgası. |
| timescale | string | Zaman damgası ölçeğini. |
| resultCode | string | Verileri öbek açılan açıklaması. **FragmentDrop_OverlapTimestamp** veya **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

Aşağıdaki örnek, şemasını gösterir **LiveEventIncomingStreamReceived** olay: 

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

Veri nesnesi, aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| trackType | string | İzleme türü (Ses / Video). |
| trackName | string | İzleme adı (ya da sunucu Kodlayıcı tarafından veya, RTMP olması durumunda, oluşturur sağlanan *TrackType_Bitrate* biçimi). |
| bitrate | integer | İzleme hızı. |
| ingestUrl | string | Canlı etkinliği tarafından sağlanan URL alın. |
| encoderIp | string  | Kodlayıcı IP'si. |
| encoderPort | string | Gelen bu akış nereden geldiğini bir kodlayıcının bağlantı noktası. |
| timestamp | string | Alınan verileri öbek ilk zaman damgası. |
| timescale | string | Ölçeği, zaman damgası gösterilir. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

Aşağıdaki örnek, şemasını gösterir **LiveEventIncomingStreamsOutOfSync** olay: 

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

Veri nesnesi, aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| minLastTimestamp | string | Son zaman damgaları tüm parçalar (ses veya video) arasında en az. |
| typeOfTrackWithMinLastTimestamp | string | En düşük son zaman damgası ile izleme (ses veya video) türü. |
| maxLastTimestamp | string | Tüm parçaları (ses veya video) arasında tüm zaman damgaları en fazla. |
| typeOfTrackWithMaxLastTimestamp | string | En son zaman damgası ile izleme (ses veya video) türü. |
| timescaleOfMinLastTimestamp| string | "MinLastTimestamp" temsil edilen bir zaman ölçeğine göre alır.|
| timescaleOfMaxLastTimestamp| string | "MaxLastTimestamp" temsil edilen bir zaman ölçeğine göre alır.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

Aşağıdaki örnek, şemasını gösterir **LiveEventIncomingVideoStreamsOutOfSync** olay: 

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

Veri nesnesi, aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| firstTimestamp | string | Zaman damgası türü video izler/kalite düzeylerinden birini alındı. |
| firstDuration | string | İlk zaman damgası ile verileri öbek süresi. |
| secondTimestamp | string  | Zaman damgası, bazı diğer izleme/kalite düzeyini video türü alındı. |
| secondDuration | string | İkinci zaman damgası ile verileri öbek süresi. |
| timescale | string | Zaman damgaları ve süresi ölçeğini.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

Aşağıdaki örnek, şemasını gösterir **LiveEventIngestHeartbeat** olay: 

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

Veri nesnesi, aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| trackType | string | İzleme türü (Ses / Video). |
| trackName | string | İzleme adı (ya da sunucu Kodlayıcı tarafından veya, RTMP olması durumunda, oluşturur sağlanan *TrackType_Bitrate* biçimi). |
| bitrate | integer | İzleme hızı. |
| incomingBitrate | integer | Kodlayıcıdan gelen veri öbekleri göre hesaplanan hızı. |
| lastTimestamp | string | Son 20 saniye cinsinden bir parçası için alınan son zaman damgası. |
| timescale | string | Ölçeği zaman damgaları cinsinden ifade edilir. |
| overlapCount | integer | Veri öbeği sayısı son 20 saniye cinsinden zaman damgaları çakışan. |
| discontinuityCount | integer | Son 20 saniye içinde gözlemlenen discontinuities sayısı. |
| nonIncreasingCount | integer | Geçmişteki zaman damgalı veri öbeği sayısı son 20 saniye içinde alınmadı. |
| unexpectedBitrate | bool | Son 20 saniye cinsinden izin verilenden fazla sınırı tarafından beklenen ve gerçek bit hızlarına dönüştürme farklıysa. True ise ve yalnızca, incomingBitrate olan > = 2 * bit hızı veya incomingBitrate < = hızı/2 veya IncomingBitrate = 0. |
| state | string | Canlı etkinlik durumu. |
| healthy | bool | Belirtir olup olmadığını alma sayıları ve bayrakları göre kötü durumda. Sağlıklı true ise, overlapCount 0 = & & discontinuityCount 0 = & & nonIncreasingCount 0 = & & unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

Aşağıdaki örnek, şemasını gösterir **LiveEventTrackDiscontinuityDetected** olay: 

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

Veri nesnesi, aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| trackType | string | İzleme türü (Ses / Video). |
| trackName | string | İzleme adı (ya da sunucu Kodlayıcı tarafından veya, RTMP olması durumunda, oluşturur sağlanan *TrackType_Bitrate* biçimi). |
| bitrate | integer | İzleme hızı. |
| previousTimestamp | string | Önceki parça zaman damgası. |
| newTimestamp | string | Zaman damgası geçerli parça. |
| discontinuityGap | string | Yukarıdaki iki zaman damgaları arasındaki boşluk. |
| timescale | string | Hangi zaman damgası hem süreksizlik boşluk ölçeğinde temsil edilir. |

### <a name="common-event-properties"></a>Ortak olay özellikleri

Bir olay aşağıdaki üst düzey veri vardır:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| topic | string | EventGrid konu. Bu özellik, Media Services hesabı kaynak kimliği vardır. |
| subject | string | Media Services kanalın Media Services hesabı altında kaynak yolu. İş için kaynak kimliği konusu ve konu verin birleştiriliyor. |
| eventType | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. Örneğin, "Microsoft.Media.JobStateChange". |
| eventTime | string | Olayın oluşturulduğu zamandan, sağlayıcının UTC saatini temel alan. |
| id | string | Olayın benzersiz tanımlayıcısı. |
| data | object | Media Services olay verileri. |
| dataVersion | string | Veri nesnesinin şema sürümü. Yayımcı, şema sürümü tanımlar. |
| metadataVersion | string | Olay meta verilerinin şema sürümü. Event Grid, şemanın en üst düzey özellikleri tanımlar. Event Grid, bu değeri sağlar. |

## <a name="next-steps"></a>Sonraki adımlar

[İş durumu değişikliği olayları için kaydolun](job-state-events-cli-how-to.md)

## <a name="see-also"></a>Ayrıca bkz.

- [Medya hizmeti olayları içerdiği EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services olaylarını tanımları](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
