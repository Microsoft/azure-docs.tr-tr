---
title: Veri değişikliği-LUSıS
titleSuffix: Azure Cognitive Services
description: Verilerin Language Understanding tahminlerden önce nasıl değiştirilebileceğinizi öğrenin (LUSıS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: 734389c92ede88d336df60a1a79a738d2abcfa92
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703171"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Söylenişi verilerini tahmine göre veya tahmin sırasında değiştirme
LUO, tahmine göre veya tahmin sırasında zaman içinde değişiklik yapmak için yollar sağlar. Bunlar, [yazımı düzeltmeyi](luis-tutorial-bing-spellcheck.md)ve önceden oluşturulmuş [datetimeV2](luis-reference-prebuilt-datetimev2.md)için saat dilimi sorunlarını düzeltmeyi içerir. 

## <a name="correct-spelling-errors-in-utterance"></a>Söylenişi 'te yazım hatalarını düzeltme
LUO, yazıdaki yazım hatalarını düzeltmek için [Bing yazım denetimi API'si v7](../Bing-Spell-Check/overview.md) kullanır. LUSıS bu hizmetle ilişkili anahtara ihtiyaç duyuyor. Anahtarı oluşturun ve sonra anahtar [noktasında](https://go.microsoft.com/fwlink/?linkid=2092356)QueryString parametresi olarak anahtarı ekleyin. 

Ayrıca, [anahtarı girerek](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) **Test** panelinde yazım hatalarını düzeltebilirsiniz. Anahtar, test paneli için tarayıcıda bir oturum değişkeni olarak tutulur. Yazımı düzeltilmesini istediğiniz her tarayıcı oturumunda anahtarı test paneline ekleyin. 

Test panelinde ve uç noktada anahtar [kullanım](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) kotasına doğru anahtar kullanımı. LUO, metin uzunluğu için Bing Yazım Denetimi sınırlarını uygular. 

Uç nokta, yazım düzeltmelerinin çalışması için iki params gerektirir:

|Larına|Değer|
|--|--|
|`spellCheck`|Boole değeri|
|`bing-spell-check-subscription-key`|[Bing yazım denetimi API'si v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) uç noktası anahtarı|

[Bing yazım denetimi API'si v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) bir hata algıladığında, orijinal söylenişi ve düzeltilen söylenişi, uç noktadan tahmine göre birlikte döndürülür.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)
 
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

### <a name="endpoint-querystring-parameter"></a>Endpoint QueryString parametresi
Saat dilimi, kullanıcının saat dilimine `timezoneOffset` parametresi kullanılarak [uç noktaya](https://go.microsoft.com/fwlink/?linkid=2092356) eklenerek düzeltilir. @No__t-0 değeri, süreyi değiştirecek pozitif veya negatif bir sayı olmalıdır.  

|Larına|Değer|
|--|--|
|`timezoneOffset`|dakika cinsinden pozitif veya negatif sayı|

### <a name="daylight-savings-example"></a>Günışığından tasarruf örneği
Gün ışığından yararlanma süresini ayarlamak için döndürülen önceden oluşturulmuş datetimeV2 gerekiyorsa, [uç nokta](https://go.microsoft.com/fwlink/?linkid=2092356) sorgusu için dakikalar içinde `timezoneOffset` QueryString parametresini kullanmanız gerekir.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 tahmin uç noktası isteği](#tab/V2)

60 dakika ekle: 

https://{Region}. api. bilişsel. Microsoft. com/lusıs/v 2.0/Apps/{AppID}? q = ışıkları açık duruma getirin mi? **Timezonekayması = 60**& verbose = {boolean} & SpellCheck = {boolean} & hazırlama = {boolean} & Bing-Yazım-Denetim-abonelik-anahtar = {string} & günlük = {Boolean}

60 dakika kaldır: 

https://{Region}. api. bilişsel. Microsoft. com/lusıs/v 2.0/Apps/{AppID}? q = ışıkları açık duruma getirin mi? **Timezonekayması =-60**& verbose = {boolean} & SpellCheck = {boolean} & hazırlama = {boolean} & Bing-yazım denetimi-abonelik-anahtar = {string} & günlük = {Boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 tahmin uç noktası isteği](#tab/V3)

60 dakika ekle:

https://{Region}. api. bilişsel. Microsoft. com/LUG/v 3.0-Önizleme/uygulamalar/{AppID}/yuvalar/üretim/tahmin? sorgu = ışıkları açık duruma getirin mi? **Timezonekayması = 60**& SpellCheck = {boolean} & Bing-yazım denetimi-abonelik-anahtar = {string} & günlük = {Boolean}

60 dakika kaldır: 

https://{Region}. api. bilişsel. Microsoft. com/LUG/v 3.0-Önizleme/uygulamalar/{AppID}/yuvalar/üretim/tahmin? sorgu = ışıkları açık duruma getirin mi? **Timezonekayması =-60**& SpellCheck = {boolean} & Bing-yazım denetimi-abonelik-anahtar = {dize} & Log = {Boolean}

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#kod, Timezonesapmasını doğru değerini belirler
Aşağıdaki C# kod, sistem saatine göre doğru `timezoneOffset` ' ü belirleyebilmek Için [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) sınıfının [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) metodunu kullanır:

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bu öğreticide yazım hatalarını düzeltin](luis-tutorial-bing-spellcheck.md)
