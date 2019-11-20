---
title: Metinden konuşmaya API başvurusu (REST)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Metin okuma REST API nasıl kullanacağınızı öğrenin. Bu makalede yetkilendirme seçenekleri, sorgu seçenekleri, bir isteği nasıl yapılandıracağınızı ve yanıt alabileceğinizi öğreneceksiniz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9fa0157bd458d2de028cab8ff9c836761e99562f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481205"
---
# <a name="text-to-speech-rest-api"></a>Metin okuma REST API

Konuşma Hizmetleri, [metin birleştirilmiş konuşmaya dönüştürmenize](#convert-text-to-speech) ve bir alan IÇIN bir REST API kümesi kullanarak bir bölge için [desteklenen seslerin bir listesini almanızı](#get-a-list-of-voices) sağlar. Kullanılabilir her uç nokta bir bölgeyle ilişkilendirilir. Kullanmayı planladığınız uç nokta/bölge için bir abonelik anahtarı gereklidir.

Metin okuma REST API, her biri yerel ayar ile tanımlanan belirli bir dili ve diyalekt destekleyen sinir ve standart metin okuma seslerini destekler.

* Tüm seslerin listesi için bkz. [dil desteği](language-support.md#text-to-speech).
* Bölgesel kullanılabilirlik hakkında daha fazla bilgi için bkz. [bölgeler](regions.md#text-to-speech).

> [!IMPORTANT]
> Maliyetler standart, özel ve sinir seslerine göre farklılık gösterir. Daha fazla bilgi için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Bu API 'yi kullanmadan önce şunları anlayın:

* Metin okuma REST API bir yetkilendirme üst bilgisi gerektirir. Bu, hizmete erişmek için bir belirteç değişimini doldurmanız gerektiği anlamına gelir. Daha fazla bilgi için bkz. [Kimlik doğrulaması](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Seslerin listesini alın

`voices/list` uç noktası, belirli bir bölgeye/uç noktaya ait seslerin tam listesini almanızı sağlar.

### <a name="regions-and-endpoints"></a>Bölgeler ve uç noktalar

| Bölge | Uç Nokta |
|--------|----------|
| Avustralya Doğu | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Güney Brezilya | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kanada Orta | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Orta ABD | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Doğu Asya | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Doğu ABD | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Doğu ABD 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Fransa Orta | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Hindistan Orta | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Doğu Japonya | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kore Orta | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Orta Kuzey ABD | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kuzey Avrupa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Orta Güney ABD | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Güneydoğu Asya | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Birleşik Krallık Güney | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Batı Avrupa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Batı ABD | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Batı ABD 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>İstek üst bilgileri

Bu tabloda, metinden konuşmaya istekleri için gerekli ve isteğe bağlı üstbilgiler listelenmektedir.

| Üst bilgi | Açıklama | Gerekli/Isteğe bağlı |
|--------|-------------|---------------------|
| `Authorization` | Öncesinde `Bearer`bir yetkilendirme belirteci. Daha fazla bilgi için bkz. [Kimlik doğrulaması](#authentication). | Gerekli |

### <a name="request-body"></a>İstek gövdesi

Bu uç noktaya yönelik `GET` istekleri için bir gövde gerekli değildir.

### <a name="sample-request"></a>Örnek istek

Bu istek yalnızca bir yetkilendirme üst bilgisi gerektirir.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Örnek yanıt

Bu yanıt, bir yanıtın yapısını göstermek için kesildi.

> [!NOTE]
> Ses kullanılabilirliği bölgeye/uç noktaya göre değişir.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>HTTP durum kodları

Her yanıt için HTTP durum kodu başarı veya genel hataları gösterir.

| HTTP durum kodu | Açıklama | Olası neden |
|------------------|-------------|-----------------|
| 200 | Tamam | İstek başarılı oldu. |
| 400 | Hatalı İstek | Gerekli bir parametre eksik, boş veya null. Ya da gerekli veya isteğe bağlı bir parametreye geçirilen değer geçersiz. Yaygın bir sorun çok uzun bir üst bilgi. |
| 401 | Yetkisiz | İstek yetkili değil. Abonelik anahtarınızın veya belirtecinizin geçerli olduğundan ve doğru bölgede bulunduğundan emin olun. |
| 429 | Çok fazla Istek | Aboneliğiniz için izin verilen kotayı veya isteklerin oranını aştınız. |
| 502 | Hatalı ağ geçidi | Ağ veya sunucu tarafı sorunu. Geçersiz üst bilgileri de gösterebilir. |


## <a name="convert-text-to-speech"></a>Metin okumayı dönüştürme

`v1` uç noktası, [konuşma birleştirme biçimlendirme dili (SSML)](speech-synthesis-markup.md)kullanarak metin okumayı dönüştürmenizi sağlar.

### <a name="regions-and-endpoints"></a>Bölgeler ve uç noktalar

Bu bölgeler REST API kullanarak metinden konuşmaya için desteklenir. Abonelik bölgeızla eşleşen uç noktayı seçtiğinizden emin olun.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>İstek üst bilgileri

Bu tabloda, metinden konuşmaya istekleri için gerekli ve isteğe bağlı üstbilgiler listelenmektedir.

| Üst bilgi | Açıklama | Gerekli/Isteğe bağlı |
|--------|-------------|---------------------|
| `Authorization` | Öncesinde `Bearer`bir yetkilendirme belirteci. Daha fazla bilgi için bkz. [Kimlik doğrulaması](#authentication). | Gerekli |
| `Content-Type` | Belirtilen metin için içerik türünü belirtir. Kabul edilen değer: `application/ssml+xml`. | Gerekli |
| `X-Microsoft-OutputFormat` | Ses çıkış biçimini belirtir. Kabul edilen değerlerin tüm listesi için bkz. [Ses çıkışları](#audio-outputs). | Gerekli |
| `User-Agent` | Uygulama adı. Belirtilen değer 255 karakterden az olmalıdır. | Gerekli |

### <a name="audio-outputs"></a>Ses çıkışları

Bu, her istekte `X-Microsoft-OutputFormat` üst bilgisi olarak gönderilen desteklenen ses biçimlerinin bir listesidir. Her biri bit hızı ve kodlama türü içerir. Konuşma Hizmetleri, 24 kHz, 16 kHz ve 8 kHz ses çıkışını destekler.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Seçtiğiniz ses ve çıkış biçiminizin farklı bit ücretleri varsa, ses gerektiği şekilde yeniden örneklenir. Ancak, 24 kHz ses `audio-16khz-16kbps-mono-siren` ve `riff-16khz-16kbps-mono-siren` çıktı biçimlerini desteklemez.

### <a name="request-body"></a>İstek gövdesi

Her bir `POST` isteğinin gövdesi, [konuşma birleştirme biçimlendirme dili (SSML)](speech-synthesis-markup.md)olarak gönderilir. SSML, metin okuma hizmeti tarafından döndürülen sentezleştirilmiş konuşmanın ses ve dilini seçmenizi sağlar. Desteklenen seslerin tüm listesi için bkz. [dil desteği](language-support.md#text-to-speech).

> [!NOTE]
> Özel bir ses kullanılıyorsa, bir isteğin gövdesi düz metin (ASCII veya UTF-8) olarak gönderilebilir.

### <a name="sample-request"></a>Örnek istek

Bu HTTP isteği, sesi ve dili belirtmek için SSML 'yi kullanır. Gövde 1.000 karakterden uzun olamaz.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-JessaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Dile özgü örnekler için hızlı başlangıçlarımıza bakın:

* [.NET Core,C#](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>HTTP durum kodları

Her yanıt için HTTP durum kodu başarı veya genel hataları gösterir.

| HTTP durum kodu | Açıklama | Olası neden |
|------------------|-------------|-----------------|
| 200 | Tamam | İstek başarılı oldu; yanıt gövdesi bir ses dosyasıdır. |
| 400 | Hatalı İstek | Gerekli bir parametre eksik, boş veya null. Ya da gerekli veya isteğe bağlı bir parametreye geçirilen değer geçersiz. Yaygın bir sorun çok uzun bir üst bilgi. |
| 401 | Yetkisiz | İstek yetkili değil. Abonelik anahtarınızın veya belirtecinizin geçerli olduğundan ve doğru bölgede bulunduğundan emin olun. |
| 413 | İstek varlığı çok büyük | SSML girişi 1024 karakterden daha uzun. |
| 415 | Desteklenmeyen medya türü | Yanlış `Content-Type` sağlanması mümkündür. `Content-Type` `application/ssml+xml`olarak ayarlanmalıdır. |
| 429 | Çok fazla Istek | Aboneliğiniz için izin verilen kotayı veya isteklerin oranını aştınız. |
| 502 | Hatalı ağ geçidi | Ağ veya sunucu tarafı sorunu. Geçersiz üst bilgileri de gösterebilir. |

HTTP durumu `200 OK`ise, yanıt gövdesi istenen biçimde bir ses dosyası içerir. Bu dosya aktarıldığından, bir arabelleğe kaydedildiğinden veya bir dosyaya kaydedildiğinden çalıştırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Konuşma deneme aboneliğinizi alın](https://azure.microsoft.com/try/cognitive-services/)
- [Akustik modelleri özelleştirme](how-to-customize-acoustic-models.md)
- [Dil modellerini özelleştirme](how-to-customize-language-model.md)
