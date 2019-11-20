---
title: Web kancaları-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Web kancaları, içeri aktarmalar, uyarlama, doğruluk testleri veya uzun süre çalışan dosyaların dökümü gibi uzun süre çalışan işlemlerle ilgilenirken çözümünüzü iyileştirmek için ideal HTTP çağrılardır.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558803"
---
# <a name="webhooks-for-speech-services"></a>Konuşma Hizmetleri için Web kancaları

Web kancaları, uygulamanızın kullanılabilir hale geldiğinde konuşma hizmetlerinden verileri kabul etmesine izin veren HTTP geri çağırmaları gibidir. Web kancalarını kullanarak, yanıt için sürekli yoklama gereksinimini ortadan kaldırarak REST API 'lerimizin kullanımını iyileştirebilirsiniz. Sonraki birkaç bölümde, konuşma Hizmetleri ile Web kancalarını kullanmayı öğreneceksiniz.

## <a name="supported-operations"></a>Desteklenen işlemler

Konuşma Hizmetleri, uzun süre çalışan tüm işlemler için Web kancalarını destekler. Aşağıda listelenen işlemlerin her biri, tamamlandıktan sonra HTTP geri çağırması tetikleyebilir.

* Dataımportcompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* Döküm tamamlama
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Sonra bir Web kancası oluşturalım.

## <a name="create-a-webhook"></a>Web kancası oluşturma

Çevrimdışı bir döküm için Web kancası oluşturalım. Senaryo: bir Kullanıcı, toplu Iş dökümü API 'SI ile zaman uyumsuz olarak almak istedikleri, uzun süre çalışan bir ses dosyasına sahiptir.

Web kancaları, https://\<Region\>. Cris.ai/api/speechtotext/v2.1/Transcriptions/Hooks öğesine bir post isteği yapılarak oluşturulabilir.

İsteğin yapılandırma parametreleri JSON olarak sağlanır:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
Toplu Iş dökümü API 'sine gönderilen tüm POST istekleri bir `name`gerektirir. `description` Ve`properties` parametreleri isteğe bağlıdır.

`Active` Özelliği, Web kancası kaydını silip yeniden oluşturmaya gerek kalmadan URL 'nize geri çağırma ve kapatma yapmak için kullanılır. İşlem tamamlandıktan sonra yalnızca bir kez geri çağrı yapmanız gerekiyorsa, Web kancasını silip `Active` özelliği yanlış olarak değiştirin.

Olay türü `TranscriptionCompletion` , olaylar dizisinde verilmiştir. Bir döküm bir Terminal durumuna (`Succeeded` veya `Failed`) geldiğinde uç noktanıza geri çağrı yapılır. Kayıtlı URL 'ye geri çağrılırken, istek kayıtlı olay türlerinden birini içeren bir `X-MicrosoftSpeechServices-Event` üst bilgi içerir. Kayıtlı olay türü başına bir istek vardır.

Abone olabileceğiniz bir olay türü vardır. `Ping` Bu olay türüdür. Bu türe sahip bir istek, ping URL 'SI kullanılırken Web kancası oluşturma işlemi tamamlandıktan sonra URL 'ye gönderilir (aşağıya bakın).  

Yapılandırmada, `url` özelliği gereklidir. POST istekleri bu URL 'ye gönderilir. , `secret` Bir SHA256 karması oluşturmak için, gizli anahtar olarak bir HMAC anahtarı olarak kullanılır. Karma, kayıtlı URL 'ye geri `X-MicrosoftSpeechServices-Signature` çağrılırken üst bilgi olarak ayarlanır. Bu üst bilgi Base64 kodlandı.

Bu örnek, kullanarak C#bir yükün nasıl doğrulandığını göstermektedir:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
Bu kod parçacığında `secret` , kodu çözülür ve onaylanır. Ayrıca, Web kancası olay türünün değiştirilmiş olduğunu fark edeceksiniz. Şu anda tamamlandı dökümü başına bir olay vardır. Kod, her olay için beş kez yeniden dener (bir ikinci gecikmeyle) ve vermeden önce.

### <a name="other-webhook-operations"></a>Diğer Web kancası işlemleri

Tüm kayıtlı Web kancalarını almak için: AL https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Belirli bir Web kancasını almak için: AL https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Belirli bir Web kancasını kaldırmak için: SILMELI https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> Yukarıdaki örnekte, bölge ' westus ' olur. Bu, Azure portal konuşma Hizmetleri kaynağını oluşturduğunuz bölge ile değiştirilmelidir.

Posta https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping gövdesi: boş

Kayıtlı URL 'ye bir POST isteği gönderir. İstek, ping değeri `X-MicrosoftSpeechServices-Event` olan bir üstbilgi içeriyor. Web kancası bir gizli dizi ile kaydedilmişse, gizliliğe sahip bir SHA256 `X-MicrosoftSpeechServices-Signature` karması ile HMAC anahtarı olarak gizli anahtar içeren bir üst bilgi içerecektir. Karma Base64 kodlandı.

Posta https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test gövdesi: boş

Sistemde abone olan olay türü (döküm) için bir varlık varsa ve uygun durumdaysa, kayıtlı URL 'ye bir POST isteği gönderir. Yük, Web kancasını çağıran son varlıktan oluşturulacaktır. Hiçbir varlık yoksa GÖNDERI 204 ile yanıt verir. Bir test isteği yapılırsa, 200 ile yanıt verir. İstek gövdesi, Web kancası 'nun abone olduğu belirli bir varlık için GET isteğindeki ile aynı şekildir (örneğin, döküm için). İstek, `X-MicrosoftSpeechServices-Event` daha önce açıklandığı gibi `X-MicrosoftSpeechServices-Signature` ve üst bilgilerine sahip olacaktır.

### <a name="run-a-test"></a>Test çalıştırma

Web sitesi https://bin.webhookrelay.com kullanılarak hızlı bir test gerçekleştirilebilir. Buradan, belgede daha önce açıklanan bir Web kancası oluşturmak için HTTP GÖNDERISINI parametre olarak geçirmek üzere geri çağrı URL 'Leri elde edebilirsiniz.

' Demet oluştur ' düğmesine tıklayın ve ekrandaki yönergeleri izleyerek bir kanca elde edin. Ardından bu sayfada sunulan bilgileri kullanarak konuşmayı konuşma hizmetine kaydedin. Geçiş iletisinin yükü: bir dökümü tamamlamaya yanıt olarak, aşağıdaki gibi görünür:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
İleti, kayıt URL 'sini ve bu kaydı silmek için kullanılan modelleri içerir.

## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma deneme aboneliğinizi alın](https://azure.microsoft.com/try/cognitive-services/)
