---
title: Telaffuz değerlendirmesi için konuşma SDK 'sını kullanma
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'Sı, konuşma girişi değerlendirir, doğruluk, akıcı, tamamlayıcı, vb. göstergelerini içeren telaffuz değerlendirmesini destekler.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 2d1b5e490b7c8212e6103e3d169c1b5491d01dde
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167439"
---
# <a name="pronunciation-assessment"></a>Telaffuz değerlendirmesi

Söyleniş değerlendirmesi konuşma söylenişini değerlendirir ve konuşmayla ilgili doğruluk ve akıcı bir ses elde etmenizi sağlar.
Telaffuz değerlendirmesi sayesinde, konuşma, anında geri bildirim alabilir ve telaffuz hale gelecek ve güvenle sunabilmeleri için söylenişlerini geliştirebilirler.
Eğitimciler, birden çok konuşmacıdaki söylenişi gerçek zamanlı olarak değerlendirmek için özelliğini kullanabilir.

Bu makalede, `PronunciationAssessmentConfig` `PronunciationAssessmentResult` konuşma SDK 'sını kullanarak ayarlamayı ve almayı öğreneceksiniz.

> [!NOTE]
> Telaffuz değerlendirmesi özelliği şu anda `en-US` tüm [konuşmadan metin bölgelerinde](regions.md#speech-to-text-text-to-speech-and-translation)kullanılabilen dili desteklemektedir. Ve dillerini destekler, ve `en-GB` `zh-CN` bölgelerinde bulunan önizleme aşamasındadır `westus` `eastasia` `centralindia` .

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Konuşma SDK 'Sı ile telaffuz değerlendirmesi

Aşağıdaki örneklerde bir oluşturacak `PronunciationAssessmentConfig` ve ardından bir ' a uygulayacaksınız `SpeechRecognizer` .

Aşağıdaki kod parçacıkları, uygulamalarınızda otomatik dil algılamayı nasıl kullanacağınızı göstermektedir:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
        var wordLevelResult = pronunciationAssessmentResult.detailResult.Words;
},
{});
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Telaffuz değerlendirmesi yapılandırma parametreleri

Bu tablo, telaffuz değerlendirmesi için yapılandırma parametrelerini listeler.

| Parametre | Açıklama | Gerekli mi? |
|-----------|-------------|---------------------|
| ReferenceText | Telaffuz tarafından değerlendirilecek metin. | Gerekli |
| GradingSystem | Puan ayarlaması için nokta sistemi. `FivePoint`Sistem, 0-5 kayan nokta puanı verir ve `HundredMark` bir 0-100 kayan nokta puanı verir. Varsayılan: `FivePoint`. | İsteğe Bağlı |
| Ayrıntı düzeyi | Değerlendirme ayrıntı düzeyi. Kabul edilen değerler, tam metin ve sözcük düzeyindeki puanı gösteren ve tam metin düzeyinde puan gösteren, `Phoneme` Word ve Fonem düzeyindeki puanı gösteren kabul edilir `Word` `FullText` . Varsayılan: `Phoneme`. | İsteğe Bağlı |
| EnableMiscue | Hatalı işaret hesaplamasını etkinleştirilir. Bu etkinken, bulunan sözcükler başvuru metniyle karşılaştırılır ve karşılaştırmaya göre atlama/ekleme ile işaretlenir. Kabul edilen değerler `False` şunlardır `True` . Varsayılan: `False`. | İsteğe Bağlı |
| ScenarioId | Özelleştirilmiş bir nokta sistemini gösteren bir GUID. | İsteğe Bağlı |

### <a name="pronunciation-assessment-result-parameters"></a>Söyleniş değerlendirmesi sonuç parametreleri

Bu tablo, Söyleniş değerlendirmesinin sonuç parametrelerini listeler.

| Parametre | Açıklama |
|-----------|-------------|
| `AccuracyScore` | Konuşmayı telaffuz doğruluğu. Doğruluk, alfabesine 'in yerel konuşmacı söylenişi ile ne kadar yakın olduğunu gösterir. Word ve tam metin düzeyi doğruluk puanı, Fonem düzeyi doğruluk puanı ' ndan toplanır. |
| `FluencyScore` | Verilen konuşmayı akıcı olarak. Akıcı bir şekilde, konuşmanın bir yerel konuşmacının sözcükler arasında sessiz kesmeler kullanımıyla ne kadar yakın olduğunu gösterir. |
| `CompletenessScore` | Sözcük girişine başvuru metin girişi olarak bulunan sözcüklerin oranının hesaplanmasıyla belirlenen konuşma. |
| `PronunciationScore` | Verilen konuşmayı gösteren telaffuz kalitesini belirten genel puan. Bu `AccuracyScore` , `FluencyScore` ve `CompletenessScore` ağırlığıyla toplanır. |
| `ErrorType` | Bu değer, ile karşılaştırıldığında bir sözcüğün atlanıp atlanmadığını, ekleneceğini veya hatalı bir şekilde olduğunu gösterir `ReferenceText` . Olası değerler şunlardır `None` (Yani bu sözcükte hata yok), `Omission` `Insertion` ve `Mispronunciation` . |

### <a name="sample-responses"></a>Örnek yanıtlar

JSON ile tipik bir telaffuz değerlendirmesi sonucu:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronunciationAssessment":
        {
            "PronScore" : 84.4,
            "AccuracyScore" : 100.0,
            "FluencyScore" : 74.0,
            "CompletenessScore" : 100.0,
        },
        "Words": [
            {
              "Word" : "Good",
              "Offset" : 500000,
              "Duration" : 2700000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            },
            {
              "Word" : "morning",
              "Offset" : 5300000,
              "Duration" : 900000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

<!-- TODO: update JavaScript sample links after release -->

* Telaffuz değerlendirmesi ve video [tanıtımı](https://www.youtube.com/watch?v=cBE8CUHOFHQ) [öğreticisini](https://www.youtube.com/watch?v=zFlwm7N4Awc) izleyin

* [Telaffuz değerlendirmesi gösterisini](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment/BrowserJS) deneyin

::: zone pivot="programming-language-csharp"
* Söyleniş değerlendirmesi için GitHub 'daki [örnek koda](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949) bakın.
::: zone-end

::: zone pivot="programming-language-cpp"
* Söyleniş değerlendirmesi için GitHub 'daki [örnek koda](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633) bakın.
::: zone-end

::: zone pivot="programming-language-java"
* Söyleniş değerlendirmesi için GitHub 'daki [örnek koda](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697) bakın.
::: zone-end

::: zone pivot="programming-language-python"
* Söyleniş değerlendirmesi için GitHub 'daki [örnek koda](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576) bakın.
::: zone-end

::: zone pivot="programming-language-objectivec"
* Söyleniş değerlendirmesi için GitHub 'daki [örnek koda](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642) bakın.
::: zone-end

* [Konuşma SDK başvuru belgeleri](speech-sdk.md)

* [Ücretsiz bir Azure hesabı oluşturma](https://azure.microsoft.com/free/cognitive-services/)
