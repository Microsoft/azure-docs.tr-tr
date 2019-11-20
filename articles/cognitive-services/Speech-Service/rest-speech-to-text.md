---
title: Konuşmadan metne API başvurusu (REST)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşmayı metne REST API nasıl kullanacağınızı öğrenin. Bu makalede, sorgu seçenekleri, yetkilendirme seçenekleri hakkında bilgi edineceksiniz yapısı bir istek ve yanıt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 137ab722df280d17fe5ccc5c07acfd323feb6531
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091218"
---
# <a name="speech-to-text-rest-api"></a>Konuşmayı metne dönüştürme REST API'si

Konuşma [SDK 'sına](speech-sdk.md)alternatif olarak, konuşma Hizmetleri REST API kullanarak konuşmayı metne dönüştürmenize olanak tanır. Her erişilebilen bir uç noktaya bir bölge ile ilişkilidir. Uygulamanızı kullanmayı planladığınız uç nokta için bir abonelik anahtarı gerektirir.

Konuşmayı metne REST API kullanmadan önce, şunu anlayın:

* REST API ve doğrudan ses iletimi kullanan istekler yalnızca en fazla 60 saniyelik ses içerebilir.
* Konuşmayı metne REST API, yalnızca son sonuçları döndürür. Kısmi sonuçlar sağlanmaz.

Daha uzun bir ses gönderdiğinizde uygulamanız için bir gereklilik varsa, [konuşma SDK 'sını](speech-sdk.md) veya [toplu iş dökümü](batch-transcription.md)gibi dosya tabanlı REST API kullanmayı düşünün.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Bölgeler ve uç noktaları

Bu bölgeler, REST API kullanarak konuşma metin döküm için desteklenir. Eşleşen abonelik bölgenizi uç nokta seçtiğinizden emin olun.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)] 

## <a name="query-parameters"></a>Sorgu parametreleri

Bu parametreleri REST isteğinin sorgu dizesinde eklenebilir.

| Parametre | Açıklama | Gerekli / isteğe bağlı |
|-----------|-------------|---------------------|
| `language` | Tanınan konuşulan dil tanımlar. Bkz: [desteklenen diller](language-support.md#speech-to-text). | Gerekli |
| `format` | Sonuç biçimi belirtir. Kabul edilen değerler `simple` ve `detailed`. Basit sonuçlarında `RecognitionStatus`, `DisplayText`, `Offset`, ve `Duration`. Ayrıntılı yanıtlar, birden çok sonuçla güvenle değerleri ve dört farklı temsilleri içerir. Varsayılan ayar `simple`. | İsteğe bağlı |
| `profanity` | Tanıma sonuçları küfür nasıl ele alınacağını belirtir. Kabul edilen değerler `masked`, küfür ile `removed`, sonuçtaki tüm küfür kaldıran ve `raw`sonuçtaki küfür içeren. Varsayılan ayar `masked`. | İsteğe bağlı |

## <a name="request-headers"></a>İstek üst bilgileri

Bu tablo, Konuşmayı metne istekler için gerekli ve isteğe bağlı üst bilgileri listeler.

|Üst bilgi| Açıklama | Gerekli / isteğe bağlı |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Konuşma Hizmetleri abonelik anahtarınız. | Ya da bu üst bilgi veya `Authorization` gereklidir. |
| `Authorization` | Bir yetkilendirme belirteci word tarafından öncesinde `Bearer`. Daha fazla bilgi için bkz. [Kimlik doğrulaması](#authentication). | Ya da bu üst bilgi veya `Ocp-Apim-Subscription-Key` gereklidir. |
| `Content-type` | Sağlanan ses verisi codec ve biçim açıklar. Kabul edilen değerler `audio/wav; codecs=audio/pcm; samplerate=16000` ve `audio/ogg; codecs=opus`. | Gerekli |
| `Transfer-Encoding` | Öbekli ses, tek bir dosya yerine gönderilen veri olduğunu belirtir. Yalnızca ses verileri varsa bu üstbilgiyi kullanır. | İsteğe bağlı |
| `Expect` | Öbekli aktarım kullanıyorsanız, gönderme `Expect: 100-continue`. Konuşma Hizmetleri, ilk isteği onaylar ve ek verileri bekler.| Öbekli ses veri gönderen gereklidir. |
| `Accept` | Sağlanırsa, olmalıdır `application/json`. Konuşma Hizmetleri, JSON 'da sonuçlar sağlar. Bazı istek çerçeveleri uyumsuz bir varsayılan değer sağlar. `Accept`her zaman dahil etmek iyi bir uygulamadır. | İsteğe bağlı, ancak önerilir. |

## <a name="audio-formats"></a>Ses biçimleri

Ses HTTP gövdesi gönderilen `POST` isteği. Bu tabloda biçimlerden birinde olmalıdır:

| Biçimlendir | Codec bileşeni | Bit hızı | Örnek hızı |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 16 kHz, mono |
| OGG | GEÇERLİ | 16-bit | 16 kHz, mono |

>[!NOTE]
>Yukarıdaki biçimler, konuşma hizmetlerindeki REST API ve WebSocket aracılığıyla desteklenir. [Speech SDK'sı](speech-sdk.md) WAV PCM codec ile biçim şu anda yalnızca destekler.

## <a name="sample-request"></a>Örnek istek

Aşağıdaki örnek, ana bilgisayar adı ve gerekli üst bilgileri içerir. Hizmet bu örnekte yer almayan ses veri girmeniz gerektiğini unutmayın. Belirtildiği gibi daha önce parçalama, ancak gerekli değildir önerilir.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>HTTP durum kodları

Her yanıt için HTTP durum kodu, başarı veya sık karşılaşılan hataları gösterir.

| HTTP durum kodu | Açıklama | Olası neden |
|------------------|-------------|-----------------|
| 100 | Devam et | İlk istek kabul edildi. Kalan verileri göndermek ile devam edin. (İle öbekli aktarım kullanılır.) |
| 200 | Tamam | İstek başarılı oldu; yanıt gövdesi bir JSON nesnesidir. |
| 400 | Hatalı istek | Dil kodu sağlanmadı, desteklenen bir dil değil, geçersiz ses dosyası, vb. |
| 401 | Yetkisiz | Abonelik anahtarı veya yetkilendirme belirteci, belirtilen bölge veya geçersiz uç nokta geçersiz. |
| 403 | Yasak | Eksik abonelik anahtarı veya yetkilendirme belirteci. |

## <a name="chunked-transfer"></a>Öbekli aktarım

Öbekli aktarım (`Transfer-Encoding: chunked`), tanınma gecikmesini azaltmaya yardımcı olabilir. Konuşma hizmetlerinin iletilirken ses dosyasını işlemeye başlamasını sağlar. REST API, kısmi veya Ara sonuçlar sağlamaz.

Bu kod örneği, nasıl öbekler halinde ses gönderileceğini gösterir. Yalnızca ilk öbekte ses dosyanın üst bilgisi içermelidir. `request` HTTPWebRequest nesneyi uygun REST uç noktasına bağlanır. `audioFile` ses dosyası diskte yoludur.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Yanıt parametreleri

Sonuçları JSON olarak sağlanır. `simple` Biçimi bu üst düzey alanlar içerir.

| Parametre | Açıklama  |
|-----------|--------------|
|`RecognitionStatus`|Durumu gibi `Success` başarılı tanıma. Sonraki tabloya bakın.|
|`DisplayText`|Büyük harfler, noktalama, ters metin normalleştirmesinin ardından tanınan metin ("Doctor Smith" için "200" veya "Dr. Smith" için 200 gibi daha kısa formlara dönüştürme) ve küfür maskeleme. Yalnızca başarı sunar.|
|`Offset`|Tanınan konuşma tanıma ses akışı başlar süre (100 nanosaniyelik birimleri).|
|`Duration`|Ses akışı olarak tanınan konuşma süresi (100 nanosaniyelik birimlerindeki).|

`RecognitionStatus` Alan, bu değerleri içerebilir:

| Durum | Açıklama |
|--------|-------------|
| `Success` | Tanıma başarılı oldu ve `DisplayText` alanının olduğunu farkedin. |
| `NoMatch` | Konuşma Tanıma Ses akışında algılandı, ancak hiçbir hedef dil sözcükleri eşleştirilmiş olan. Genellikle kullanıcı Konuşmayı olandan farklı bir dil tanıma dilidir anlamına gelir. |
| `InitialSilenceTimeout` | Ses akışı başlangıcını yalnızca sessizlik ve konuşma için beklerken zaman aşımına hizmetini içeriyordu. |
| `BabbleTimeout` | Ses akışı başlangıcını yalnızca gürültü ve konuşma için beklerken zaman aşımına hizmetini içeriyordu. |
| `Error` | Tanıma hizmeti bir iç hatayla karşılaştı ve çalışmaya devam edemedi. Mümkün olduğunda yeniden deneyin. |

> [!NOTE]
> Ses yalnızca küfür oluşuyorsa ve `profanity` sorgu parametresi ayarlandığında `remove`, hizmeti bir konuşma sonuç döndürmez.

`detailed` biçimi `simple` biçimiyle aynı verileri, aynı tanınma sonucunun alternatif yorumlarının bir listesini `NBest`ile birlikte içerir. Bu sonuçlar en büyük olasılıkla en az büyük olasılıkla derecelendirilir. İlk giriş, ana tanıma sonucuyla aynıdır.  Kullanırken `detailed` biçimi `DisplayText` olarak sağlanan `Display` her sonucu için `NBest` listesi.

Her bir nesnenin `NBest` liste aşağıdakileri içerir:

| Parametre | Açıklama |
|-----------|-------------|
| `Confidence` | Güvenilirlik puanı 1.0 (tam güven) girişinin 0,0 (güven yok) |
| `Lexical` | Sözcük şeklinde tanınan metin: Gerçek sözcüklerin tanınır. |
| `ITN` | Ters metin normalleştirilmiş ("") kurallı tanınan metinle telefon numaraları, sayılar, kısaltmaları ("doktor smith" için "dr smith") ve uygulanan diğer dönüşümler. |
| `MaskedITN` | Edemezsiniz formun, istenmesi halinde uygulanan küfür maskeleme ile. |
| `Display` | Noktalama işaretleri ve eklenen büyük/küçük harf ile tanınan metin görüntüleme formu. Bu parametre aynı olan `DisplayText` biçimi ayarlandığında sağlanan `simple`. |

## <a name="sample-responses"></a>Örnek yanıt

`simple` tanıma için tipik bir yanıt:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

`detailed` tanıma için tipik bir yanıt:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Konuşma deneme aboneliğinizi alın](https://azure.microsoft.com/try/cognitive-services/)
- [Akustik modelleri özelleştirme](how-to-customize-acoustic-models.md)
- [Dil modellerini özelleştirme](how-to-customize-language-model.md)
