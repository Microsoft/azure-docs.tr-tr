---
title: Konuşma SDK 'sını izleme bellek kullanımı-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma hizmeti SDK 'Sı, konuşmadan metne ve metinden konuşmaya dönüştürmeye kadar birçok programlama dilini destekler ve konuşma çevirisi de sağlar. Bu makalede, SDK 'da yerleşik olarak bulunan bellek yönetimi araçları ele alınmaktadır.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c7e74ce8f4d0b7889d2e5bcd117eaa43e2b6991b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "88934149"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Konuşma SDK 'Sı bellek kullanımını izleme

Konuşma SDK 'Sı, bir dizi birlikte çalışabilirlik katmanı aracılığıyla birden çok programlama dilinde tasarlanan yerel bir kod tabanına dayalıdır. Dile özgü her projeksiyon, nesne yaşam döngüsünü yönetmek için doğru şekilde doğru özelliklere sahiptir. Ayrıca, konuşma SDK 'Sı, nesne günlüğü ve nesne limitleriyle kaynak kullanımını izlemek için bellek yönetimi araçları 'nı içerir. 

## <a name="how-to-read-object-logs"></a>Nesne günlüklerini okuma

[Konuşma SDK günlüğü etkinse](how-to-use-logging.md), geçmiş nesne gözlemlemeyi etkinleştirmek için izleme etiketleri dağıtılır. Bu Etiketler şunları içerir: 

* `TrackHandle` veya `StopTracking` 
* Nesne türü
* Nesnenin türünü ve izlenmekte olan geçerli sayıyı izleyen geçerli nesne sayısı.

Örnek bir günlük aşağıda verilmiştir: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Uyarı eşiği ayarlama

Uyarı eşiği oluşturma seçeneğiniz vardır ve bu eşik aşılırsa (günlüğe kaydetmenin etkin olduğu varsayılarak) bir uyarı iletisi kaydedilir. Uyarı iletisi, var olan tüm nesnelerin, sayılarıyla birlikte bir dökümünü içerir. Bu bilgiler, sorunları daha iyi anlamak için kullanılabilir. 

Bir uyarı eşiğini etkinleştirmek için, bir nesne üzerinde belirtilmesi gerekir `SpeechConfig` . Bu nesne, yeni bir tanıyıcı oluşturulduğunda denetlenir. Aşağıdaki örneklerde, adlı bir örneği oluşturduğunuzu varsayalım `SpeechConfig` `config` :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> Bu özellik için varsayılan değer 10.000 ' dir.

## <a name="set-an-error-threshold"></a>Hata eşiği ayarlama 

Konuşma SDK 'sını kullanarak, belirli bir zamanda izin verilen en fazla nesne sayısını ayarlayabilirsiniz. Bu ayar etkinleştirilirse, en fazla sayı isabet edildiğinde, yeni tanıyıcı nesneleri oluşturma girişimleri başarısız olur. Varolan nesneler çalışmaya devam edecektir.

Örnek bir hata aşağıda verilmiştir:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Bir hata eşiğini etkinleştirmek için, bir nesne üzerinde belirtilmelidir `SpeechConfig` . Bu nesne, yeni bir tanıyıcı oluşturulduğunda denetlenir. Aşağıdaki örneklerde, adlı bir örneği oluşturduğunuzu varsayalım `SpeechConfig` `config` :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> Bu özellik için varsayılan değer, bir veri türü için platforma özgü en büyük değerdir `size_t` . Tipik bir tanıma, 7 ila 10 iç nesne arasında tüketir.

## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma SDK 'Sı hakkında daha fazla bilgi edinin](speech-sdk.md)