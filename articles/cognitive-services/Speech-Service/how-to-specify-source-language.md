---
title: Konuşmayı metne dönüştürme için kaynak dilini belirtme
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'Sı, konuşmayı metne dönüştürürken kaynak dilini belirtmenize olanak tanır. Bu makalede, konuşma hizmetinin kaynak dili bilmesini ve özel bir model hedefi sağlamasını sağlamak için FromConfig ve SourceLanguageConfig yöntemlerinin nasıl kullanılacağı açıklanır.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 94b8fb026b61b52e8096cf54e1db30a6c260c04b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109962"
---
# <a name="specify-source-language-for-speech-to-text"></a>Konuşmadan metne yönelik kaynak dilini belirtin

Bu makalede, konuşma tanıma için konuşma SDK 'sına geçilen bir ses girişi için kaynak dilin nasıl belirtildiğini öğreneceksiniz. Ayrıca, geliştirilmiş tanıma için özel bir konuşma modeli belirtmek üzere örnek kod sağlanır.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>İçinde kaynak dilini belirtmeC#

İlk adım `SpeechConfig`oluşturmaktır:

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Sonra, `SpeechRecognitionLanguage`ile sesinizin kaynak dilini belirtin:

```csharp
speechConfig.SpeechRecognitionLanguage = "de-DE";
```

Tanıma için özel bir model kullanıyorsanız, uç noktayı `EndpointId`belirtebilirsiniz:

```csharp
speechConfig.EndpointId = "The Endpoint ID for your custom model.";
```

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>İçinde kaynak dilini belirtmeC++

Bu örnekte, kaynak dili açıkça `FromConfig` yöntemi kullanılarak bir parametre olarak sağlanır.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Bu örnekte, kaynak dili `SourceLanguageConfig`kullanılarak sunulmaktadır. Ardından `sourceLanguageConfig`, `recognizer`oluşturulurken `FromConfig` bir parametre olarak geçirilir.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Bu örnekte, kaynak dili ve özel uç nokta `SourceLanguageConfig`kullanılarak sağlanır. `sourceLanguageConfig`, `recognizer`oluşturulurken `FromConfig` bir parametre olarak geçirilir.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` ve `SetEndpointId`, ve Java 'daki C++ `SpeechConfig` sınıfından kullanım dışı yöntemlerdir. Bu yöntemlerin kullanımı önerilmez ve bir `SpeechRecognizer`oluşturulurken kullanılmamalıdır.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Java 'da kaynak dilini belirtme

Bu örnekte, yeni bir `SpeechRecognizer`oluşturulurken kaynak dili açık olarak sağlanır.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Bu örnekte, kaynak dili `SourceLanguageConfig`kullanılarak sunulmaktadır. Ardından `sourceLanguageConfig` yeni bir `SpeechRecognizer`oluşturulurken parametre olarak geçirilir.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Bu örnekte, kaynak dili ve özel uç nokta `SourceLanguageConfig`kullanılarak sağlanır. Ardından `sourceLanguageConfig` yeni bir `SpeechRecognizer`oluşturulurken parametre olarak geçirilir.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` ve `setEndpointId`, ve Java 'daki C++ `SpeechConfig` sınıfından kullanım dışı yöntemlerdir. Bu yöntemlerin kullanımı önerilmez ve bir `SpeechRecognizer`oluşturulurken kullanılmamalıdır.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Python 'da kaynak dilini belirtme

İlk adım `speech_config`oluşturmaktır:

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

Sonra, `speech_recognition_language`ile sesinizin kaynak dilini belirtin:

```Python
speech_config.speech_recognition_language="de-DE"
```

Tanıma için özel bir model kullanıyorsanız, uç noktayı `endpoint_id`belirtebilirsiniz:

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>JavaScript 'te kaynak dilini belirtme

İlk adım `SpeechConfig`oluşturmaktır:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Sonra, `speechRecognitionLanguage`ile sesinizin kaynak dilini belirtin:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Tanıma için özel bir model kullanıyorsanız, uç noktayı `endpointId`belirtebilirsiniz:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Amaç-C ' d e kaynak dilini belirtme

İlk adım `speechConfig`oluşturmaktır:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Sonra, `speechRecognitionLanguage`ile sesinizin kaynak dilini belirtin:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Tanıma için özel bir model kullanıyorsanız, uç noktayı `endpointId`belirtebilirsiniz:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Ayrıca bkz.

* Konuşma için desteklenen dillerin ve yerel ayarların listesi için bkz. [dil desteği](language-support.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma SDK başvuru belgeleri](speech-sdk.md)
