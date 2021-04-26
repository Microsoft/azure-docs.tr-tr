---
title: Metinden konuşmaya API başvurusu (REST)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Metin okuma REST API nasıl kullanacağınızı öğrenin. Bu makalede yetkilendirme seçenekleri, sorgu seçenekleri, bir isteği nasıl yapılandıracağınızı ve yanıt alabileceğinizi öğreneceksiniz.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 0065b6f4a7039e2883bca6acd5cf659be7b71069
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717684"
---
# <a name="text-to-speech-rest-api"></a>Metin okuma REST API'si

Konuşma hizmeti, [metin birleştirilmiş konuşmaya dönüştürmenize](#convert-text-to-speech) ve bir alan IÇIN bir REST API kümesi kullanarak bir bölge için [desteklenen seslerin bir listesini almanızı](#get-a-list-of-voices) sağlar. Kullanılabilir her uç nokta bir bölgeyle ilişkilendirilir. Kullanmayı planladığınız uç nokta/bölge için bir abonelik anahtarı gereklidir.

Metin okuma REST API, her biri yerel ayar ile tanımlanan belirli bir dili ve diyalekt destekleyen sinir ve standart metin okuma seslerini destekler.

* Tüm seslerin listesi için bkz. [dil desteği](language-support.md#text-to-speech).
* Bölgesel kullanılabilirlik hakkında daha fazla bilgi için bkz. [bölgeler](regions.md#text-to-speech).

> [!IMPORTANT]
> Maliyetler standart, özel ve sinir seslerine göre farklılık gösterir. Daha fazla bilgi için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Bu API 'yi kullanmadan önce şunları anlayın:

* Metin okuma REST API bir yetkilendirme üst bilgisi gerektirir. Bu, hizmete erişmek için bir belirteç değişimini doldurmanız gerektiği anlamına gelir. Daha fazla bilgi için bkz. [Kimlik doğrulaması](#authentication).

> [!TIP]
> Azure Kamu ve Azure Çin uç noktaları için [Bu makaleye](sovereign-clouds.md) bakın.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Seslerin listesini alın

`voices/list`Uç nokta, belirli bir bölgeye/uç noktaya ait seslerin tam listesini almanızı sağlar.

### <a name="regions-and-endpoints"></a>Bölgeler ve uç noktalar

| Region | Uç Nokta |
|--------|----------|
| Doğu Avustralya | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Güney Brezilya | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Orta Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Central US | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Doğu Asya | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Doğu ABD | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Doğu ABD 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Orta Fransa | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Hindistan Orta | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Doğu Japonya | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Güney Kore - Orta | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Orta Kuzey ABD | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kuzey Avrupa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Güney Afrika - Kuzey | `https://southafricanorth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Orta Güney ABD | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Güneydoğu Asya | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Güney Birleşik Krallık | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Orta Batı ABD | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| West Europe | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Batı ABD | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Batı ABD 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [Önizlemede bulunan sesler](language-support.md#neural-voices-in-preview) yalnızca şu 3 bölgede kullanılabilir: Doğu ABD, Batı Avrupa ve Güneydoğu Asya.

### <a name="request-headers"></a>İstek üst bilgileri

Bu tabloda, metinden konuşmaya istekleri için gerekli ve isteğe bağlı üstbilgiler listelenmektedir.

| Üst bilgi | Description | Gerekli/Isteğe bağlı |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Konuşma hizmeti abonelik anahtarınız. | Bu üst bilgi ya da `Authorization` gerekli. |
| `Authorization` | Bir yetkilendirme belirteci öncesinde kelimedir `Bearer` . Daha fazla bilgi için bkz. [Kimlik doğrulaması](#authentication). | Bu üst bilgi ya da `Ocp-Apim-Subscription-Key` gerekli. |



### <a name="request-body"></a>İstek gövdesi

`GET`Bu uç noktaya yönelik istekler için bir gövde gerekli değildir.

### <a name="sample-request"></a>Örnek istek

Bu istek yalnızca bir yetkilendirme üst bilgisi gerektirir.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>Örnek yanıt

Bu yanıt, bir yanıtın yapısını göstermek için kesildi.

> [!NOTE]
> Ses kullanılabilirliği bölgeye/uç noktaya göre değişir.

```json
[

    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)",
    "DisplayName": "Aria",
    "LocalName": "Aria",
    "ShortName": "en-US-AriaNeural",
    "Gender": "Female",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },

  ...

     {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

  ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...

]
```

### <a name="http-status-codes"></a>HTTP durum kodu

Her yanıt için HTTP durum kodu başarı veya genel hataları gösterir.

| HTTP durum kodu | Açıklama | Olası neden |
|------------------|-------------|-----------------|
| 200 | Tamam | İstek başarılı oldu. |
| 400 | Hatalı İstek | Gerekli bir parametre eksik, boş veya null. Ya da gerekli veya isteğe bağlı bir parametreye geçirilen değer geçersiz. Yaygın bir sorun çok uzun bir üst bilgi. |
| 401 | Yetkisiz | İstek yetkili değil. Abonelik anahtarınızın veya belirtecinizin geçerli olduğundan ve doğru bölgede bulunduğundan emin olun. |
| 429 | Çok fazla Istek | Aboneliğiniz için izin verilen kotayı veya isteklerin oranını aştınız. |
| 502 | Hatalı ağ geçidi    | Ağ veya sunucu tarafı sorunu. Geçersiz üst bilgileri de gösterebilir. |


## <a name="convert-text-to-speech"></a>Metin okumayı dönüştürme

`v1`Uç nokta, [konuşma birleştirme biçimlendirme DILI (SSML)](speech-synthesis-markup.md)kullanarak metin okumayı dönüştürmenizi sağlar.

### <a name="regions-and-endpoints"></a>Bölgeler ve uç noktalar

Bu bölgeler REST API kullanarak metinden konuşmaya için desteklenir. Abonelik bölgeızla eşleşen uç noktayı seçtiğinizden emin olun.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>İstek üst bilgileri

Bu tabloda, metinden konuşmaya istekleri için gerekli ve isteğe bağlı üstbilgiler listelenmektedir.

| Üst bilgi | Description | Gerekli/Isteğe bağlı |
|--------|-------------|---------------------|
| `Authorization` | Bir yetkilendirme belirteci öncesinde kelimedir `Bearer` . Daha fazla bilgi için bkz. [Kimlik doğrulaması](#authentication). | Gerekli |
| `Content-Type` | Belirtilen metin için içerik türünü belirtir. Kabul edilen değer: `application/ssml+xml` . | Gerekli |
| `X-Microsoft-OutputFormat` | Ses çıkış biçimini belirtir. Kabul edilen değerlerin tüm listesi için bkz. [Ses çıkışları](#audio-outputs). | Gerekli |
| `User-Agent` | Uygulama adı. Belirtilen değer 255 karakterden az olmalıdır. | Gerekli |

### <a name="audio-outputs"></a>Ses çıkışları

Bu, her istekte üst bilgi olarak gönderilen desteklenen ses biçimlerinin bir listesidir `X-Microsoft-OutputFormat` . Her biri bit hızı ve kodlama türü içerir. Konuşma hizmeti, 24 kHz, 16 kHz ve 8 kHz ses çıkışını destekler.

```output
raw-16khz-16bit-mono-pcm            riff-16khz-16bit-mono-pcm
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
raw-48khz-16bit-mono-pcm            riff-48khz-16bit-mono-pcm
raw-8khz-8bit-mono-mulaw            riff-8khz-8bit-mono-mulaw
raw-8khz-8bit-mono-alaw             riff-8khz-8bit-mono-alaw
audio-16khz-32kbitrate-mono-mp3     audio-16khz-64kbitrate-mono-mp3
audio-16khz-128kbitrate-mono-mp3    audio-24khz-48kbitrate-mono-mp3
audio-24khz-96kbitrate-mono-mp3     audio-24khz-160kbitrate-mono-mp3
audio-48khz-96kbitrate-mono-mp3     audio-48khz-192kbitrate-mono-mp3
raw-16khz-16bit-mono-truesilk       raw-24khz-16bit-mono-truesilk
webm-16khz-16bit-mono-opus          webm-24khz-16bit-mono-opus
ogg-16khz-16bit-mono-opus           ogg-24khz-16bit-mono-opus
ogg-48khz-16bit-mono-opus
```

> [!NOTE]
> Seçtiğiniz ses ve çıkış biçiminizin farklı bit ücretleri varsa, ses gerektiği şekilde yeniden örneklenir.
> OGG-24khz-16bit-mono-Opus, [Opus codec](https://opus-codec.org/downloads/) bileşeniyle çözülebilir

### <a name="request-body"></a>İstek gövdesi

Her isteğin gövdesi, `POST` [konuşma birleştirme biçimlendirme DILI (SSML)](speech-synthesis-markup.md)olarak gönderilir. SSML, metin okuma hizmeti tarafından döndürülen sentezleştirilmiş konuşmanın ses ve dilini seçmenizi sağlar. Desteklenen seslerin tüm listesi için bkz. [dil desteği](language-support.md#text-to-speech).

> [!NOTE]
> Özel bir ses kullanılıyorsa, bir isteğin gövdesi düz metin (ASCII veya UTF-8) olarak gönderilebilir.

### <a name="sample-request"></a>Örnek istek

Bu HTTP isteği, sesi ve dili belirtmek için SSML 'yi kullanır. Gövde uzunluğu uzunsa ve elde edilen ses 10 dakikayı aşarsa, 10 dakikaya kesilir. Diğer bir deyişle, ses uzunluğu 10 dakikayı aşamaz.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP durum kodu

Her yanıt için HTTP durum kodu başarı veya genel hataları gösterir.

| HTTP durum kodu | Açıklama | Olası neden |
|------------------|-------------|-----------------|
| 200 | Tamam | İstek başarılı oldu; yanıt gövdesi bir ses dosyasıdır. |
| 400 | Hatalı İstek | Gerekli bir parametre eksik, boş veya null. Ya da gerekli veya isteğe bağlı bir parametreye geçirilen değer geçersiz. Yaygın bir sorun çok uzun bir üst bilgi. |
| 401 | Yetkisiz | İstek yetkili değil. Abonelik anahtarınızın veya belirtecinizin geçerli olduğundan ve doğru bölgede bulunduğundan emin olun. |
| 415 | Desteklenmeyen medya türü | Yanlış `Content-Type` sağlanmış olabilir. `Content-Type` olarak ayarlanmalıdır `application/ssml+xml` . |
| 429 | Çok fazla Istek | Aboneliğiniz için izin verilen kotayı veya isteklerin oranını aştınız. |
| 502 | Hatalı ağ geçidi    | Ağ veya sunucu tarafı sorunu. Geçersiz üst bilgileri de gösterebilir. |

HTTP durumu ise `200 OK` , yanıt gövdesi istenen biçimde bir ses dosyası içerir. Bu dosya aktarıldığından, bir arabelleğe kaydedildiğinden veya bir dosyaya kaydedildiğinden çalıştırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Ücretsiz bir Azure hesabı oluşturma](https://azure.microsoft.com/free/cognitive-services/)
- [Uzun biçimli ses için zaman uyumsuz sensıs](./long-audio-api.md)
- [Özel Ses’i kullanmaya başlama](how-to-custom-voice.md)
