---
title: Veri değişikliği-LUSıS
description: Verilerin Language Understanding tahminlerden önce nasıl değiştirilebileceğinizi öğrenin (LUSıS)
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 621a41f743b751a8c24bf6f6ad8497fb5c79775c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026019"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Söylenişi verilerini tahmine göre veya tahmin sırasında değiştirme
LUO, tahmine göre veya tahmin sırasında zaman içinde değişiklik yapmak için yollar sağlar. Bunlar, [yazımı düzeltmeyi](luis-tutorial-bing-spellcheck.md)ve önceden oluşturulmuş [datetimeV2](luis-reference-prebuilt-datetimev2.md)için saat dilimi sorunlarını düzeltmeyi içerir.

## <a name="correct-spelling-errors-in-utterance"></a>Söylenişi 'te yazım hatalarını düzeltme


### <a name="v3-runtime"></a>V3 çalışma zamanı

Deterance 'i LUSıS 'e göndermeden önce yazım düzeltmeleri için metin ön işleme. Doğru tahminlere sahip olduğunuzdan emin olmak için doğru yazılarla örnek söylemeleri kullanın.

[Bing yazım denetimi](../bing-spell-check/overview.md) kullanarak lusıs 'e göndermeden önce metni düzeltin.

### <a name="prior-to-v3-runtime"></a>V3 çalışma zamanından önce

LUO, yazıdaki yazım hatalarını düzeltmek için [Bing yazım denetimi API'si v7](../Bing-Spell-Check/overview.md) kullanır. LUSıS bu hizmetle ilişkili anahtara ihtiyaç duyuyor. Anahtarı oluşturun ve sonra anahtar [noktasında](https://go.microsoft.com/fwlink/?linkid=2092356)QueryString parametresi olarak anahtarı ekleyin.

Uç nokta, yazım düzeltmelerinin çalışması için iki params gerektirir:

|Param|Değer|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[Bing yazım denetimi API'si v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) uç noktası anahtarı|

[Bing yazım denetimi API'si v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) bir hata algıladığında, orijinal söylenişi ve düzeltilen söylenişi, uç noktadan tahmine göre birlikte döndürülür.

#### <a name="v2-prediction-endpoint-response"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>İzin verilen sözcüklerin listesi
LUSıS 'de kullanılan Bing yazım denetimi API 'SI, yazım denetimi değişiklikleri sırasında yoksayılacak sözcüklerin listesini desteklemez. Sözcüklerin veya kısaltmalardan oluşan bir listeye izin vermeniz gerekiyorsa, duyun amaç tahmini için, duyun, duyun, duyun bir tahmin için gönderilmesi için istemci uygulamadaki söyliği işleyin.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Önceden oluşturulmuş datetimeV2 varlığının saat dilimini değiştirme
Bir LUSıS uygulaması önceden oluşturulmuş [datetimeV2](luis-reference-prebuilt-datetimev2.md) varlığını kullandığında, tahmin yanıtında bir tarih saat değeri döndürülebilir. İsteğin saat dilimi, döndürülecek doğru tarih/saati belirlemekte kullanılır. İstek bir bot 'tan veya başka bir merkezi uygulamadan geliyorsa, lusıs 'e geçmeden önce, LUSıS 'nin kullandığı saat dilimini düzeltin.

### <a name="v3-prediction-api-to-alter-timezone"></a>Saat dilimini değiştirmek için v3 tahmin API 'SI

V3 'de, `datetimeReference` saat dilimi sapmasını belirler. [V3 tahminleri](luis-migration-api-v3.md#v3-post-body)hakkında daha fazla bilgi edinin.

### <a name="v2-prediction-api-to-alter-timezone"></a>Saat dilimini değiştirmek için v2 tahmin API 'SI
Zaman dilimi, kullanıcının saat dilimine `timezoneOffset` API sürümüne göre parametresi kullanılarak uç noktaya eklenerek düzeltilir. Parametrenin değeri, zaman değiştirmek için dakika cinsinden pozitif veya negatif bir sayı olmalıdır.

#### <a name="v2-prediction-daylight-savings-example"></a>V2 tahmini gün ışığından yararlanma örneği
Gün ışığından yararlanma süresini ayarlamak için döndürülen önceden oluşturulmuş datetimeV2 gerekiyorsa, [uç nokta](https://go.microsoft.com/fwlink/?linkid=2092356) sorgusu için dakika cinsinden bir +/-değeri ile QueryString parametresini kullanmanız gerekir.

60 dakika ekle:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

60 dakika kaldır:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>V2 tahmini C# kodu parametrenin doğru değerini belirler

Aşağıdaki C# kodu, sistem saatine göre doğru fark değerini öğrenmek için [TimeZoneInfo](/dotnet/api/system.timezoneinfo) sınıfının [FindSystemTimeZoneById](/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) metodunu kullanır:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bu öğreticide yazım hatalarını düzeltin](luis-tutorial-bing-spellcheck.md)