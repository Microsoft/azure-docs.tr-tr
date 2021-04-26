---
title: Gerçek zamanlı konuşma dökümü hızlı başlangıç-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'Sı ile gerçek zamanlı konuşma dökümünü nasıl kullanacağınızı öğrenin. Konuşma dökümü, konuşma hizmetine ses akışı yaparak, birden fazla katılımcı ekleme, kaldırma ve tanımanıza olanak sağlayan toplantılar ve diğer konuşmalar gerçekleştirmenize olanak tanır.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 48cd4c7996eabad7293aa2429c76b8943e0ab3da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100368481"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>Gerçek zamanlı konuşma dökümü ile çalışmaya başlama

Konuşma **SDK 'sı konuşma API 'si** , veya kullanarak konuşma hizmetine ses akışı yaparak, birden fazla katılımcı ekleme, kaldırma ve tanıma olanağı sunarak toplantılar ve diğer konuşmalar oluşturmanızı sağlar `PullStream` `PushStream` . İlk olarak REST API kullanarak her katılımcı için ses imzaları oluşturun ve ardından, konuşmaları oluşturmak için SDK ile birlikte ses imzalarını kullanın. Daha fazla bilgi için konuşmaya [genel bakış](conversation-transcription.md) bölümüne bakın.

## <a name="limitations"></a>Sınırlamalar

* Yalnızca şu abonelik bölgelerinde kullanılabilir: `centralus` , `eastasia` , `eastus` , `westeurope`
* 7 MIC dairesel çok mikrofonlu dizi gerektirir. Microphone dizisinin [belirtimizi](./speech-devices-sdk-microphone.md)karşılaması gerekir.
* [Konuşma cihazları SDK 'sı](speech-devices-sdk.md) , uygun cihazlar ve görüşme dökümünü gösteren örnek bir uygulama sağlar.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede bir Azure hesabınız ve konuşma hizmeti aboneliğiniz olduğunu varsaymaktadır. Hesabınız ve aboneliğiniz yoksa [konuşma hizmetini ücretsiz deneyin](overview.md#try-the-speech-service-for-free).

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Zaman uyumsuz konuşma dökümü](how-to-async-conversation-transcription.md) 
>  [Roobo cihazı örnek kodu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java) 
>  [Azure Kinect Dev Kit örnek kodu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)