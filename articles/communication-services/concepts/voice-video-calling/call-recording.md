---
title: Azure Iletişim Hizmetleri çağrı kaydına genel bakış
titleSuffix: An Azure Communication Services concept document
description: Çağrı kaydı özelliğine ve API 'Lere genel bir bakış sağlar.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/13/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df9638588b4d677a7ceb80bafbe7157bb5c2df42
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730606"
---
# <a name="calling-recording-overview"></a>Kayda genel bakış çağrısı

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

> [!NOTE]
> Birçok ülke ve eyalet, PSTN, ses ve video çağrılarının kaydı için geçerli olan yasalar ve yönetmeliklere sahiptir ve bu, genellikle kullanıcıların iletişimleri kaydına izin vermesini gerektirir. Yetenek kaydı yeteneklerini yasalar ile uyumlu olarak kullanmanın sorumluluğundadır. Kayıtlı iletişimin taraflarından, her katılımcıya uygun yasalar ile uyumlu bir onay almalısınız.

> [!NOTE]
> Kişisel verilerin bakımından oluşan düzenlemeler, Kullanıcı verilerini dışarı aktarma özelliği gerektirir. Bu gereksinimleri desteklemek için, meta veri dosyalarını kaydetme dizideki her bir çağrı katılımcısı için participantId içerir `participants` . `participants`Bir çağrıda katılımcıları belirlemek için, iç Kullanıcı kimlikleriniz ile dizideki MRA 'ya çapraz başvuru yapabilirsiniz. Başvuru için aşağıda bir kayıt meta veri dosyası örneği verilmiştir.

Çağrı kaydı, kaydı başlatmak, durdurmak, duraklatmak ve devam ettirmeye yönelik bir API kümesi sağlar. Bu API 'Lere, sunucu tarafı iş mantığından veya Kullanıcı eylemleri tarafından tetiklenen olaylar aracılığıyla erişilebilir. Kayıtlı medya çıkışı `MP4 Audio+Video` , ekiplerin medya kaydetmek için kullandığı biçim biçiminde olur. Medya ve meta verilerle ilgili bildirimler Event Grid aracılığıyla dağıtılır. Kayıtlar, bir tercih edilen uzun süreli depolama çözümüne alma ve taşıma işlemleri için yerleşik geçici depolamada 48 saat boyunca depolanır. 

## <a name="run-time-control-apis"></a>Çalışma zamanı denetimi API 'Leri
Çalışma zamanı denetimi API 'Leri, bir grup çağrısı oluşturma ve konuşmayı kaydetme gibi iç iş mantığı tetikleriyle veya sunucu uygulamasına kaydın başlamasını bildiren kullanıcı tarafından tetiklenen bir eylemden kaydı yönetmek için kullanılabilir. Her iki senaryoda da `<conversation-id>` belirli bir toplantıyı veya aramayı kaydetmek için gereklidir. 

#### <a name="getting-conversation-id-from-a-server-initiated-call"></a>Sunucu tarafından başlatılan çağrıdan konuşma KIMLIĞI alınıyor

Bir `ConversationId` olay aracılığıyla döndürülür `Microsoft.Communication.CallLegStateChanged` . Bu olay bildirimi, bir çağrı kurulduktan sonra yayınlanır. `data.ConversationId`Alanda bulunabilir. Bu değer, `{conversationId}` çalışma zamanı denetimi API 'lerinde doğrudan parametre olarak kullanılabilir:
```
      {
        "id": null,
        "topic": null,
        "subject": "callLeg/<callLegId>/callState",
        "data": {
---->       "ConversationId": "<conversation-id>",    <----
            "CallLegId": "<callLegId>",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-04-14T16:32:34.1115003Z",
        "metadataVersion": null,
        "dataVersion": null
    }
```
                                                            
#### <a name="getting-the-conversation-id-from-a-user-triggered-event-on-the-client"></a>İstemcideki Kullanıcı tarafından tetiklenen bir olaydan konuşma KIMLIĞINI alma

JavaScript kitaplığından, ' `@azure/communication-calling` ı almak için bir çağrı çağırma kurulduktan sonra, `let result = call.info.getConversationUrl()` `conversationUrl` **`conversationUrl` `{conversationId}` çalışma zamanı denetimi API 'lerinde kullanımı için Base64Url kodlayın**. Kodlama, olay sunucuya veya sunucu tarafına gönderilmeden önce istemcide yapılabilir.

`conversationUrl`  Yalnızca Base64 kodlamasıyla (ör. BTOA) karıştırılmamalıdır.                                                            

### <a name="start-recording"></a>Kaydı başlat

#### <a name="request"></a>İstek

**HTTP**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```
POST /conversations/{conversationId}/Recordings
Content-Type: application/json

{
  "operationContext": "string", // developer provided string for correlation context on each operation
  "recordingStateCallbackUri": "string"
}
```
**C# SDK’sı**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// start call recording
StartRecordingResponse startRecordingResponse = await conversationClient.StartRecordingAsync(
    conversationId: "<conversation-id>"
    operationContext: "<operation-context>", /// developer provided string for correlation context on each operation
    recordingStateCallbackUri: "<recording-state-callback-uri>").ConfigureAwait(false);

string recordingId = startRecordingResponse.RecordingId;
```

#### <a name="response"></a>Yanıt

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 404 Not found
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="get-call-recording-state"></a>Çağrı kayıt durumunu al

#### <a name="request"></a>İstek

**HTTP**
<!-- {
  "blockType": "request",
  "name": "get-recording-state"
}-->
```http
GET /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
}
```
**C# SDK’sı**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// get recording state
GetCallRecordingStateResponse recordingState = await conversationClient.GetRecordingStateAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>).ConfigureAwait(false);
```
#### <a name="response"></a>Yanıt

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingState": "active"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="stop-recording"></a>Kaydı durdur
#### <a name="request"></a>İstek
**HTTP**
<!-- {
  "blockType": "request",
  "name": "stop-recording"
}-->
```
DELETE /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK’sı**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// stop recording
StopRecordingResponse response = conversationClient.StopRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Yanıt
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="pause-recording"></a>Kaydı Duraklat
Çağrı kaydını duraklatma ve sürdürme, bir çağrı veya toplantının bir bölümünü kaydetmeyi atlayıp tek bir dosyaya kaydetmeyi sürdürmenizi sağlar. 
#### <a name="request"></a>İstek
**HTTP**
<!-- {
  "blockType": "request",
  "name": "pause-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Pause
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK’sı**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// pause recording
PauseRecordingResponse response = conversationClient.PauseRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Yanıt
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="resume-recording"></a>Kaydı sürdürür
#### <a name="request"></a>İstek
**HTTP**
<!-- {
  "blockType": "request",
  "name": "resume-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Resume
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK’sı**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// resume recording
ResumeRecordingResponse response = conversationClient.ResumeRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Yanıt
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

## <a name="media-output-types"></a>Medya çıktı türleri
Çağrı kaydı Şu anda karışık ses + video MP4 çıkış biçimini desteklemektedir. Çıktı medyası, Microsoft ekipleri kaydıyla oluşturulan toplantı kayıtları ile eşleşiyor.

| Kanal türü | İçerik biçimi | Video | Ses |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | MP4 | Varsayılan kutucuk düzenlemede tüm katılımcıların 1920x1080 8 FPS videosu | 16kHz MP4A tüm katılımcıların karma sesi |

## <a name="event-grid-notifications"></a>Event Grid bildirimleri
Event Grid bildirimi `Microsoft.Communication.RecordingFileStatusUpdated` , kayıt işlemi tamamlandıktan sonra genellikle 1-2 dakika sonra (örneğin, Toplantı sona erdikten, kayıt durdurulduğunda) bir kayıt hazırlanırsa yayımlanır. Olay bildirimlerini kaydetme, hem kayıtlı medyayı hem de bir kayıt meta veri dosyasını almak için kullanılabilecek bir belge KIMLIĞI içerir:

- <Azure_Communication_Service_Endpoint>/Recording/Download/{belgeentid}
- <Azure_Communication_Service_Endpoint>/Recording/Download/{belgeentid}/Metadata

Olay Kılavuzu bildirimlerinin işlenmesine ve kayıt ve meta veri dosyalarının indirilebileceği örnek kod [burada](../../quickstarts/voice-video-calling/download-recording-file-sample.md)bulunabilir. 

### <a name="notification-schema"></a>Bildirim şeması
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="file-download"></a>Dosya Indirme

> Azure Iletişim Hizmetleri, kayıtlar için kısa süreli medya depolaması sağlar. **Korumak istediğiniz tüm kayıtlı içerikleri 48 saat içinde dışarı aktarın.** 48 saat sonra kayıtlar artık kullanılabilir olmayacaktır.

### <a name="download-recording"></a>Kaydı indirme
#### <a name="request"></a>İstek
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording"
}-->
```http
GET /recording/download/{documentId}
Content-Type: application/json

{
}
```
#### <a name="response"></a>Yanıt
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```
HTTP/1.1 200 Success
Content-Type: video/mp4

{
string // Recording file bytes
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
### <a name="download-recording-metadata"></a>Kayıt meta verilerini indir
#### <a name="request"></a>İstek
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording-metadata"
}-->
```http
GET /recording/download/{documentId}/metadata
Content-Type: application/json

{
}
```
#### <a name="response"></a>Yanıt
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "resourceId": "string",
  "callId": "string",
  "chunkDocumentId": "string",
  "chunkIndex": 0,
  "chunkStartTime": "string",
  "chunkDuration": 0,
  "pauseResumeIntervals": [
    {
      "startTime": "string",
      "duration": 0
    }
  ],
  "recordingInfo": {
    "contentType": "string",
    "channelType": "string",
    "format": "string",
    "audioConfiguration": {
      "sampleRate": "string",
      "bitRate": 0,
      "channels": 0
    },
    "videoConfiguration": {
      "longerSideLength": 0,
      "shorterSideLength": 0,
      "framerate": 0,
      "bitRate": 0
    }
  },
  "participants": [
    {
      "participantId": "string"
    }
  ]
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
