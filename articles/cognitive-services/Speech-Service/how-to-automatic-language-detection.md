---
title: Konuşmayı metne dönüştürme için otomatik dil algılamayı kullanma
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'Sı, konuşmadan metne yönelik otomatik dil algılamayı destekler. Bu özellik kullanılırken, belirtilen ses, belirtilen dil listesi ile karşılaştırılır ve en büyük olasılıkla eşleşme belirlenir. Döndürülen değer daha sonra, konuşma için kullanılan dil modelini seçmek için kullanılabilir.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
ms.openlocfilehash: a72f477e64c856c545801533c131c397de627c00
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74110170"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Konuşmayı metne dönüştürme için otomatik dil algılama

Otomatik dil algılama, sunulan dillerin listesine kıyasla konuşma SDK 'sına geçirilen sesle ilgili en büyük olasılıkla eşleşmeyi saptamak için kullanılır. Otomatik dil algılama tarafından döndürülen değer daha sonra konuşmayı metne dönüştürme için dil modelini seçmek ve daha doğru bir döküm sağlamak için kullanılır. Hangi dillerin kullanılabilir olduğunu görmek için bkz. [dil desteği](language-support.md).

Bu makalede, `SpeechRecognizer` nesnesini oluşturmak ve algılanan dili almak için `AutoDetectSourceLanguageConfig` kullanmayı öğreneceksiniz.

> [!IMPORTANT]
> Bu özellik yalnızca için C++ konuşma SDK 'Sı ve Java için konuşma SDK 'sı için kullanılabilir.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Konuşma SDK 'Sı ile otomatik dil algılama

Otomatik dil algılama 'nın Şu anda algılama başına iki dilin hizmet tarafı sınırı vardır. `AudoDetectSourceLanguageConfig` nesneniz oluşturulurken bu sınırlamayı aklınızda bulundurun. Aşağıdaki örneklerde bir `AutoDetectSourceLanguageConfig`oluşturup `SpeechRecognizer`oluşturmak için kullanacaksınız.

> [!TIP]
> Ayrıca, metinde konuşma yaparken kullanılacak özel bir model de belirtebilirsiniz. Daha fazla bilgi için bkz. [Otomatik dil algılama için özel model kullanma](#use-a-custom-model-for-automatic-language-detection).

Aşağıdaki kod parçacıkları, uygulamalarınızda otomatik dil algılamayı nasıl kullanacağınızı göstermektedir:

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Otomatik dil algılama için özel model kullanma

Konuşma hizmeti modellerini kullanarak dil algılamayı ayarlamanın yanı sıra, gelişmiş bir tanıma için özel bir model de belirtebilirsiniz. Özel bir model sağlanmazsa, hizmet varsayılan dil modelini kullanacaktır.

Aşağıdaki kod parçacıkları, konuşma hizmeti çağrınızla özel bir modelin nasıl belirtildiğini gösterir. Algılanan dil `en-US`, varsayılan model kullanılır. Algılanan dil `fr-FR`, özel model için uç nokta kullanılır:

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

## <a name="next-steps"></a>Sonraki adımlar

- [Konuşma SDK başvuru belgeleri](speech-sdk.md)
