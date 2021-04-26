---
title: LIP eşitleme için yüz poz olayları alma
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'Sı, belirli bir phoneme oluşturma sırasında LIP 'ler, Jaw ve dil listesi gibi gözlemlenen konuşmayla ilgili önemli pozları temsil etmek için kullanılan konuşma sensepsi ' nde viseme olayını destekler.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: e97c48d4e42627d0fc2caaa4f66e81b9a0cafa86
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643895"
---
# <a name="get-facial-pose-events"></a>Yüz poz olaylarını al

> [!NOTE]
> Viseme, şimdilik yalnızca `en-US-AriaNeural` Ses için geçerlidir.

Viseme, konuşulan dilde bir Fonem 'nin görsel açıklamasıdır.
Bir sözcük konuşurken yüzün ve ağız konumunu tanımlar.
Her bir viseme, belirli bir telefonun kümesi için önemli yüz pozları gösterir.
Visemes ve phonemes arasında bire bir yazışmalar yoktur.
Genellikle birkaç alfabesine tek bir Vime 'ya karşılık gelmektedir. bu şekilde, birkaç alfabesine, ve gibi üretildiğinde aynı şekilde görünür `s` `z` .
[Visemes ve alfabesine arasındaki eşleme tablosuna](#map-phonemes-to-visemes)bakın.

Visemes 'i kullanarak, daha doğal ve akıllı Haberler yayın Yardımcısı, etkileşimli oyun ve çizgi üzerine daha kolay bir şekilde ve daha sezgisel bir dil öğretimi oluşturabilirsiniz. İşitme zorluğu olan kişiler Ayrıca, animasyonlu bir yüzle birlikte, sesleri görsel olarak ve "LIP-oku" konuşma içeriğini de alabilir.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Konuşma SDK 'Sı ile viseme olaylarını alın

Viseme olaylarını oluşturmak için giriş metnini bir Fonem sırası kümesine ve bunlara karşılık gelen viseme dizilerini dönüştürüyoruz. Konuşma sesinde her bir viseme 'nin başlangıç saatini tahmin ediyoruz. Viseme olayları, her biri vinme 'nin göründüğü sesle bir uzaklığa sahip bir viseme kimlikleri dizisi içerir. Bu olaylar, giriş metnini konuşarak bir kişinin benzetimini yapan ağız animasyonlarını kullanabilir.

| Parametre | Açıklama |
|-----------|-------------|
| Viseme KIMLIĞI | Bir viseme belirten tamsayı numarası. Ingilizce (Birleşik Devletler) ' de, belirli bir fonetik kümesinin ağız şekillerini belirleyen 22 farklı visemes sunuyoruz. [Viseme kimliği ve alfabesine arasındaki eşleme tablosuna](#map-phonemes-to-visemes)bakın.  |
| Ses kayması | Her bir viseme 'nin başlangıç saati (Ticks) (100 nanosaniye). |

Viseme olaylarını almak için `VisemeReceived` konuşma SDK 'sında olaya abone olun.
Aşağıdaki kod parçacıkları, viseme olayının nasıl abone olunacağını göstermektedir.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>Alfabesine 'i cansemes 'e eşleyin

Visemes dile göre farklılık gösterir. Her dilin belirli telefonlara karşılık gelen bir dizi visemes vardır. Aşağıdaki tabloda, İngilizce (Birleşik Devletler) için uluslararası fonetik alfabe (IPA) alfabesine ve viseme kimlikleri arasındaki yazışma gösterilmektedir.

| IPA | Örnek | Viseme KIMLIĞI |
|-----|---------|-----------|
| ı   | **EA** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **bir** te | 4 |
| ɛ | **e** çok | 4 |
|æ  |   **bir** Active alanı        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au**         |3|
|ʊ  |   b **Oo**          |4|
|oʊ |   **o** ld           |8|
|u  |   **U** ber          |7|
|ʌ  |   **u** NCLE         |1|
|aɪ |   **i** CE           |11|
|aʊ |   **OU** t           |9|
|ɔɪ |   **oI** l           |10|
|Ju dili |   **Yu** ma          |[6, 7]|
|ə  |   **Go**           |1|
|ɪɹ |   **Ear**          |[6, 13]|
|ɛɹ |   **hava** düzlemi      |[4, 13]|
|ʊɹ |   c **'niz**          |[4, 13]|
|aɪ(ə)ɹ |   **IRE** Land   |[11, 13]|
|aʊ(ə)ɹ |   **saat** s     |[9, 13]|
|ɔɹ |   **veya** Değiştir        |[3, 13]|
|ɑɹ |   **ar** tist        |[2, 13]|
|ɝ  |   **kulak**         |[5, 13]|
|ɚ  |   Tüm **öğeler** GY       |[1, 13]|
|w  |   **b**.,**UE** de   |7|
|uygulanmaz  |   **y**, f **e** w     |6|
|p  |   **p** UT           |21|
|b  |   **b** IG           |21|
|t  |   **t** alk          |19|
|d  |   **d** IG           |19|
|k  |   **c** UT           |20|
|g  |   **g** o            |20|
|m  |   **d** at, s **a** Ash    |21|
|n  |   **n** o, s **n** ow      |19|
|ŋ  |   li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** Eğer         |18|
|θ  |   **' deki**          |17|
|ð  |   **TH**          |17|
|s  |   **s** It           |15|
|z  |   **z** AP           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   **J** tanışlar       |16|
|h  |   **h** ELP          |12|
|tʃ |   **ch** ın          |16|
|dʒ |   **j** oy           |16|
|l  |   **l** kimliği, g **l** ad     |14|
|ɹ  |   **r** Ed, b **r**    |13|


## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma SDK başvuru belgeleri](speech-sdk.md)
