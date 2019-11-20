---
title: Metin okuma kapsayıcısı uç noktası sorgula
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491194"
---
Kapsayıcı, [REST tabanlı uç nokta API 'leri](../rest-text-to-speech.md)sağlar. Platform, çerçeve ve dil çeşitlemeleri için birçok [örnek kaynak kodu projesi](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) mevcuttur.

*Standart metin okuma* kapsayıcında, indirdiğiniz görüntü etiketinin yerel ve sesinden yararlanmalısınız. Örneğin, `latest` etiketini indirdiyseniz varsayılan yerel ayar `en-US` ve `JessaRUS` sestir. `{VOICE_NAME}` bağımsız değişkeni [`en-US-JessaRUS`](../language-support.md#standard-voices)olur. Aşağıda SSML örneğine bakın:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Ancak, *özel metin okuma* için [özel ses portalından](https://aka.ms/custom-voice-portal) **Ses/model** edinmeniz gerekir. Özel model adı, ses adı ile eşanlamlıdır. **Eğitim** sayfasına gidin ve `{VOICE_NAME}` bağımsız değişkeni olarak kullanılacak **ses/modeli** kopyalayın.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Özel ses modeli-ses adı":::

Aşağıda SSML örneğine bakın:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Birkaç üstbilgiyi ve bir veri yükünü sağlayan bir HTTP POST isteği oluşturalım. `{VOICE_NAME}` yer tutucusunu kendi değeri ile değiştirin.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Bu komut:

* `speech/synthesize/cognitiveservices/v1` uç noktası için HTTP POST isteği oluşturur.
* `audio/*` bir `Accept` üst bilgisini belirtir
* `application/ssml+xml`bir `Content-Type` üst bilgisini belirtir, daha fazla bilgi için bkz. [İstek gövdesi](../rest-text-to-speech.md#request-body).
* `riff-16khz-16bit-mono-pcm`bir `X-Microsoft-OutputFormat` üst bilgisini belirtir, daha fazla seçenek için [ses çıkışı](../rest-text-to-speech.md#audio-outputs)bölümüne bakın.
* `{VOICE_NAME}` bitiş noktasına verilen [konuşma Sensi biçimlendirme dili (SSML)](../speech-synthesis-markup.md) isteği gönderir.