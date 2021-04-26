---
title: Event Grid bir Azure Iletişim Hizmetleri hızlı başlangıç ile çağrı kaydetme ve indirme
titleSuffix: An Azure Communication Services quickstart
description: Bu hızlı başlangıçta, Event Grid kullanarak çağrıları kaydetmeyi ve indirmeyi öğreneceksiniz.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/14/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 493a35a627f458fe649931d9fabc175b0affc3a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730586"
---
# <a name="record-and-download-calls-with-event-grid"></a>Event Grid ile çağrıları kaydedin ve indirin

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Event Grid kullanarak Iletişim Hizmetleri çağrılarınızı kaydederek Azure Iletişim Hizmetleri ile çalışmaya başlayın.

## <a name="prerequisites"></a>Önkoşullar
- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Etkin bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- [`Microsoft.Azure.EventGrid`](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)NuGet paketi.

## <a name="create-a-webhook-and-subscribe-to-the-recording-events"></a>Web kancası oluşturma ve kayıt olaylarına abone olma
Çağrı kaydetme ve medya dosyası indirmelerini kolaylaştırmak için *Web kancaları* ve *olayları* kullanacağız. 

İlk olarak, bir Web kancası oluşturacağız. Iletişim Hizmetleri kaynağınız, olay tetiklendiğinde bu Web kancasını bilgilendirmek için Event Grid kullanır `recording` ve ardından kaydedilen medya indirilmeye hazırsa yeniden yapılır.

Bu olay bildirimlerini almak için kendi özel Web kancasını yazabilirsiniz. Bu Web kancasının, Web kancasını olay hizmetine başarıyla abone olmak için doğrulama kodu ile gelen iletilere yanıt vermesi önemlidir.

```
[HttpPost]
public async Task<ActionResult> PostAsync([FromBody] object request)
  {
   //Deserializing the request 
    var eventGridEvent = JsonConvert.DeserializeObject<EventGridEvent[]>(request.ToString())
        .FirstOrDefault();
    var data = eventGridEvent.Data as JObject;

    // Validate whether EventType is of "Microsoft.EventGrid.SubscriptionValidationEvent"
    if (string.Equals(eventGridEvent.EventType, EventTypes.EventGridSubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
   {
        var eventData = data.ToObject<SubscriptionValidationEventData>();
        var responseData = new SubscriptionValidationResponseData
        {
            ValidationResponse = eventData.ValidationCode
        };
        if (responseData.ValidationResponse != null)
        {
            return Ok(responseData);
        }
    }

    // Implement your logic here.
    ...
    ...
  }
```


Yukarıdaki kod, `Microsoft.Azure.EventGrid` NuGet paketine bağlıdır. Event Grid uç nokta doğrulaması hakkında daha fazla bilgi edinmek için, [uç nokta doğrulama belgelerini](https://docs.microsoft.com/azure/event-grid/receive-events#endpoint-validation) ziyaret edin

Daha sonra bu Web kancasını olaya abone yapacağız `recording` :

1. `Events`Azure Iletişim Hizmetleri kaynağınızın dikey penceresini seçin.
2. `Event Subscription`Aşağıda gösterildiği gibi seçin.
![Event Grid Kullanıcı arabirimini gösteren ekran görüntüsü](./media/call-recording/image1-event-grid.png)
3. Olay aboneliğini yapılandırın ve olarak öğesini seçin `Call Recording File Status Update` `Event Type` . `Webhook`Olarak öğesini seçin `Endpoint type` .
![Olay aboneliği oluştur](./media/call-recording/image2-create-subscription.png)
4. Web kancasının URL 'sini içine girin `Subscriber Endpoint` .
![Olaya abone ol](./media/call-recording/image3-subscribe-to-event.png)

Iletişim Hizmetleri kaynağınız bir çağrıyı kaydetmek için kullanıldığında Web kancasına artık bildirim alınacaktır.

## <a name="notification-schema"></a>Bildirim şeması
Kayıt indirilmek üzere kullanılabilir olduğunda, Iletişim Hizmetleri kaynağınız aşağıdaki olay şemasıyla bir bildirim sağlar. Kayıt için belge kimlikleri, `documentId` her birinin alanlarından getirilebilir `recordingChunk` .

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}

```

## <a name="download-the-recorded-media-files"></a>Kaydedilen medya dosyalarını indirin

İndirmek istediğimiz dosyanın belge KIMLIĞINI aldıktan sonra, HMAC kimlik doğrulaması kullanarak kayıtlı medyayı ve meta verileri indirmek için aşağıdaki Azure Iletişim Hizmetleri API 'Lerini çağıracağız.

En büyük kayıt dosyası boyutu 1,5 GB 'tır. Bu dosya boyutu aşıldığında kaydedici, kaydedilen medyayı otomatik olarak birden çok dosyaya bölecektir.

İstemci, tüm medya dosyalarını tek bir istek ile indirebilmelidir. Bir sorun varsa, istemci zaten indirilmiş olan kesimleri yeniden indirmeyi önlemek için bir Aralık üstbilgisiyle yeniden deneyebilir.

Kaydedilen medyayı indirmek için: 
- Yöntemidir `GET` 
- 'DEKI https://contoso.communication.azure.com/recording/download/{documentId}?api-version=2021-04-15-preview1

Kayıtlı medya meta verilerini indirmek için: 
- Yöntemidir `GET` 
- 'DEKI https://contoso.communication.azure.com/recording/download/{documentId}/metadata?api-version=2021-04-15-preview1


### <a name="authentication"></a>Kimlik Doğrulaması
Kayıtlı medya ve meta verileri indirmek için, Azure Iletişim Hizmetleri API 'Lerine karşı isteğin kimliğini doğrulamak için HMAC kimlik doğrulaması kullanın.

`HttpClient`Aşağıda belirtilen üstbilgileri kullanarak bir oluşturun ve gerekli üst bilgileri ekleyin `HmacAuthenticationUtils` :

```
  var client = new HttpClient();

  // Set Http Method
  var method = HttpMethod.Get;
  StringContent content = null;

  // Build request
  var request = new HttpRequestMessage
  {
      Method = method, // Http GET method
      RequestUri = new Uri(<Download_Recording_Url>), // Download recording Url
      Content = content // content if required for POST methods
  };

  // Question: Why do we need to pass String.Empty to CreateContentHash() method?
  // Answer: In HMAC authentication, the hash of the content is one of the parameters used to generate the HMAC token.
  // In our case our recording download APIs are GET methods and do not have any content/body to be passed in the request. 
  // However in this case we still need the SHA256 hash for the empty content and hence we pass an empty string. 


  string serializedPayload = string.Empty;

  // Hash the content of the request.
  var contentHashed = HmacAuthenticationUtils.CreateContentHash(serializedPayload);

  // Add HAMC headers.
  HmacAuthenticationUtils.AddHmacHeaders(request, contentHashed, accessKey, method);

  // Make a request to the Azure Communication Services APIs mentioned above
  var response = await client.SendAsync(request).ConfigureAwait(false);
```

#### <a name="hmacauthenticationutils"></a>HmacAuthenticationUtils 
Aşağıdaki yardımcı programlar HMAC iş akışınızı yönetmek için kullanılabilir.

**İçerik karması oluştur**

```
public static string CreateContentHash(string content)
{
    var alg = SHA256.Create();

    using (var memoryStream = new MemoryStream())
    using (var contentHashStream = new CryptoStream(memoryStream, alg, CryptoStreamMode.Write))
    {
        using (var swEncrypt = new StreamWriter(contentHashStream))
        {
            if (content != null)
            {
                swEncrypt.Write(content);
            }
        }
    }

    return Convert.ToBase64String(alg.Hash);
}
```

**HMAC üstbilgileri ekleme**

```
public static void AddHmacHeaders(HttpRequestMessage requestMessage, string contentHash, string accessKey)
{
    var utcNowString = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
    var uri = requestMessage.RequestUri;
    var host = uri.Authority;
    var pathAndQuery = uri.PathAndQuery;

    var stringToSign = $"{requestMessage.Method}\n{pathAndQuery}\n{utcNowString};{host};{contentHash}";
    var hmac = new HMACSHA256(Convert.FromBase64String(accessKey));
    var hash = hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign));
    var signature = Convert.ToBase64String(hash);
    var authorization = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";

    requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
    requestMessage.Headers.Add("Date", utcNowString);
    requestMessage.Headers.Add("Authorization", authorization);
}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bir Iletişim Hizmetleri aboneliğini temizleyip kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler. [Kaynakları Temizleme](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources)hakkında daha fazla bilgi edinin.


## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Web çağırma](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample) örneğimize göz atın
- [SDK yeteneklerini çağırma](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web) hakkında bilgi edinin
- [Nasıl çalıştığını çağırma](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types) hakkında daha fazla bilgi edinin
