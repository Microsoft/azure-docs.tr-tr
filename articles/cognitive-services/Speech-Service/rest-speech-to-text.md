---
title: Konuşmadan metne API başvurusu (REST)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşmayı metne REST API nasıl kullanacağınızı öğrenin. Bu makalede yetkilendirme seçenekleri, sorgu seçenekleri, bir isteği nasıl yapılandıracağınızı ve yanıt alabileceğinizi öğreneceksiniz.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c041d64b3dbd0c38979f4d8d0fa563a72f6a4b1
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168204"
---
# <a name="speech-to-text-rest-api"></a>Konuşmayı metne dönüştürme REST API'si

Konuşmadan metne iki farklı REST API 'si vardır. Her API özel amacını sunar ve farklı uç noktaları kümelerini kullanır.

Konuşmadan metne REST API 'Leri şunlardır:
- [Okuma-metin REST API v 3.0](#speech-to-text-rest-api-v30) , [toplu iş dökümü](batch-transcription.md) ve [özel konuşma tanıma](custom-speech-overview.md)için kullanılır. v 3.0, [v 2.0 'ın bir ardıldır](./migrate-v2-to-v3.md).
- [Kısa seste konuşmadan metne REST API](#speech-to-text-rest-api-for-short-audio) , [konuşma SDK 'sının](speech-sdk.md)bir alternatifi olarak çevrimiçi döküm için kullanılır. Bu API kullanan istekler, istek başına yalnızca en fazla 60 saniyelik ses aktarabilir. 

## <a name="speech-to-text-rest-api-v30"></a>Konuşmayı metne REST API v 3.0

Okuma-metin REST API v 3.0, [toplu iş dökümü](batch-transcription.md) ve [özel konuşma tanıma](custom-speech-overview.md)için kullanılır. REST aracılığıyla çevrimiçi dökümle iletişim kurması gerekiyorsa, [kısa ses Için konuşmayı metne REST API](#speech-to-text-rest-api-for-short-audio)kullanın.

REST API v 3.0 kullanarak şunları yapın:
- İş arkadaşlarınızın oluşturduğunuz bir modele erişmesini istiyorsanız veya bir modeli birden fazla bölgeye dağıtmak istediğiniz durumlarda, diğer aboneliklerdeki modelleri kopyalayın
- Bir kapsayıcıdan (toplu döküm) veri oluşturma ve birden çok ses dosyası URL 'Si sağlama
- SAS URI 'Si kullanımı üzerinden Azure depolama hesaplarından veri yükleme
- Bu uç nokta için Günlükler isteniyorsa uç nokta başına günlükleri al
- Şirket içi kapsayıcılar ayarlama amacıyla oluşturduğunuz modellerin bildirimini isteyin

REST API v 3.0, şu özelliklere sahiptir:
- **Bildirimler-Web kancaları**— hizmetin tüm çalışan işlemleriyle artık Web kancası bildirimleri desteklenir. REST API v 3.0, Web Kancalarınızı bildirimlerin gönderileceği yere kaydetmenizi sağlamak için çağrılar sağlar
- **Uç noktaların arkasındaki modelleri güncelleştirme** 
- **Birden çok veri kümesiyle model uyarlama**— akustik, dil ve Söyleniş verilerinin birden çok veri kümesi birleşimini kullanarak bir modeli uyarlayın
- **Kendi depolama alanınızı getir**— Günlükler, döküm dosyaları ve diğer veriler için kendi depolama hesaplarınızı kullanın

Toplu Iş dökümü [Bu makalele](batch-transcription.md)birlikte REST API v 3.0 kullanma örneklerine bakın.

Konuşmayı metne REST API v 2.0 kullanıyorsanız, [Bu kılavuzdaki](./migrate-v2-to-v3.md)v 3.0 'a nasıl geçirebileceğiniz konusuna bakın.

Tam konuşmadan metne REST API v 3.0 başvurusunu [buraya](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)bakın.

## <a name="speech-to-text-rest-api-for-short-audio"></a>Kısa ses için konuşmaya metne REST API

Konuşma [SDK 'sına](speech-sdk.md)alternatif olarak, konuşma hizmeti bir REST API kullanarak konuşmayı metne dönüştürmenize olanak tanır.
Kısa ses için REST API çok sınırlıdır ve yalnızca [konuşma SDK 'sının](speech-sdk.md) olmaması durumunda kullanılmalıdır.

Kısa ses için konuşmayı metne REST API kullanmadan önce aşağıdakileri göz önünde bulundurun:

* Kısa ses için REST API kullanan istekler ve doğrudan ses iletimi, en fazla 60 saniyelik ses içerebilir.
* Kısa seste konuşmadan metne REST API yalnızca nihai sonuçları döndürür. Kısmi sonuçlar sağlanmaz.

Daha uzun bir ses gönderiyorsanız uygulamanız için bir gereksinimdir, [konuşma SDK 'sını](speech-sdk.md) veya [konuşmayı metne REST API v 3.0](#speech-to-text-rest-api-v30)kullanmayı düşünün.

> [!TIP]
> Azure Kamu ve Azure Çin uç noktaları için [Bu makaleye](sovereign-clouds.md) bakın.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

### <a name="regions-and-endpoints"></a>Bölgeler ve uç noktalar

Kısa ses için REST API uç noktası şu biçimdedir:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

`<REGION_IDENTIFIER>`Bu tablodaki aboneliğinizin bölgesiyle eşleşen tanımlayıcıyla değiştirin:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Bir 4xx HTTP hatası almamak için dil parametresi URL 'ye eklenmelidir. Örneğin, Batı ABD uç noktası kullanılarak dil ABD Ingilizcesi olarak ayarlanmıştır: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="query-parameters"></a>Sorgu parametreleri

Bu parametreler REST isteğinin sorgu dizesine dahil edilebilir.

| Parametre | Açıklama | Gerekli/Isteğe bağlı |
|-----------|-------------|---------------------|
| `language` | Tanınmakta olan konuşulan dili tanımlar. [Desteklenen diller](language-support.md#speech-to-text)bölümüne bakın. | Gerekli |
| `format` | Sonuç biçimini belirtir. Kabul edilen değerler `simple` şunlardır `detailed` . Basit sonuçlar,,, `RecognitionStatus` `DisplayText` ve içerir `Offset` `Duration` . Ayrıntılı yanıtlar, görüntü metninin dört farklı temsilini içerir. Varsayılan ayar `simple` değeridir. | İsteğe Bağlı |
| `profanity` | Tanıma sonuçlarında küfür nasıl işleneceğini belirtir. Kabul edilen değerler `masked` , küfür ile bir `removed` bütün küfür kaldıran, ya da `raw` sonuçtaki küfür da dahil olmak üzere yıldız işaretiyle değiştirilir. Varsayılan ayar `masked` değeridir. | İsteğe Bağlı |
| `cid` | Özel modeller oluşturmak için [özel konuşma tanıma portalını](./custom-speech-overview.md) kullanırken, **dağıtım** SAYFASıNDA bulunan **uç nokta kimlikleri** aracılığıyla özel modeller kullanabilirsiniz. Sorgu dizesi parametresinin bağımsız değişkeni olarak **uç nokta kimliğini** kullanın `cid` . | İsteğe Bağlı |

### <a name="request-headers"></a>İstek üst bilgileri

Bu tabloda, konuşma-metin istekleri için gerekli ve isteğe bağlı üstbilgiler listelenmektedir.

|Üst bilgi| Description | Gerekli/Isteğe bağlı |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Konuşma hizmeti abonelik anahtarınız. | Bu üst bilgi ya da `Authorization` gerekli. |
| `Authorization` | Bir yetkilendirme belirteci öncesinde kelimedir `Bearer` . Daha fazla bilgi için bkz. [Kimlik doğrulaması](#authentication). | Bu üst bilgi ya da `Ocp-Apim-Subscription-Key` gerekli. |
| `Pronunciation-Assessment` | Tanınma sonuçlarında telaffuz puanlarını göstermek için parametreleri belirtir; doğruluk, akıcı, tamamlanma, vb. göstergeler ile konuşma girişi için telaffuz kalitesini değerlendirir. Bu parametre, birden çok ayrıntılı parametre içeren Base64 kodlamalı JSON 'dir. Bu üstbilgiyi derlemek için [telaffuz değerlendirmesi parametrelerine](#pronunciation-assessment-parameters) bakın. | İsteğe Bağlı |
| `Content-type` | Belirtilen ses verilerinin biçimini ve codec 'ini açıklar. Kabul edilen değerler `audio/wav; codecs=audio/pcm; samplerate=16000` şunlardır `audio/ogg; codecs=opus` . | Gerekli |
| `Transfer-Encoding` | Tek bir dosya yerine, öbekli ses verilerinin gönderileceğini belirtir. Yalnızca ses verilerini parçalama durumunda bu üstbilgiyi kullanın. | İsteğe Bağlı |
| `Expect` | Öbekli aktarım kullanılıyorsa, gönderin `Expect: 100-continue` . Konuşma hizmeti, ilk isteği ve bekleek verileri onaylar.| Öbekli ses verileri gönderiyorsanız gereklidir. |
| `Accept` | Sağlanmışsa, olmalıdır `application/json` . Konuşma hizmeti, sonuçları JSON ile sağlar. Bazı istek çerçeveleri uyumsuz bir varsayılan değer sağlar. Her zaman dahil etmek iyi bir uygulamadır `Accept` . | İsteğe bağlı, ancak önerilir. |

### <a name="audio-formats"></a>Ses biçimleri

HTTP isteğinin gövdesinde ses gönderilir `POST` . Bu tablodaki biçimlerden birinde olmalıdır:

| Biçimlendir | Bileşeni | Bit hızı | Örnek hız  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 kbps | 16 kHz, mono |
| OGG    | OPUS 'LAR  | 256 kpbs | 16 kHz, mono |

>[!NOTE]
>Yukarıdaki biçimler, konuşma hizmetindeki kısa ses ve WebSocket için REST API aracılığıyla desteklenir. [Konuşma SDK 'sı](speech-sdk.md) Şu anda, PCM codec ve [DIĞER biçimlere](how-to-use-codec-compressed-audio-input-streams.md)sahip WAV biçimini desteklemektedir.

### <a name="pronunciation-assessment-parameters"></a>Telaffuz değerlendirme parametreleri

Bu tabloda, telaffuz değerlendirmesi için gerekli ve isteğe bağlı parametreler listelenmektedir.

| Parametre | Açıklama | Gerekli mi? |
|-----------|-------------|---------------------|
| ReferenceText | Telaffuz tarafından değerlendirilecek metin. | Gerekli |
| GradingSystem | Puan ayarlaması için nokta sistemi. `FivePoint`Sistem, 0-5 kayan nokta puanı verir ve `HundredMark` bir 0-100 kayan nokta puanı verir. Varsayılan: `FivePoint`. | İsteğe Bağlı |
| Ayrıntı düzeyi | Değerlendirme ayrıntı düzeyi. Kabul edilen değerler, tam metin ve sözcük düzeyindeki puanı gösteren ve tam metin düzeyinde puan gösteren, `Phoneme` Word ve Fonem düzeyindeki puanı gösteren kabul edilir `Word` `FullText` . Varsayılan ayar `Phoneme` değeridir. | İsteğe Bağlı |
| Boyut | Çıkış ölçütünü tanımlar. Kabul edilen değerler `Basic` yalnızca doğruluk puanı ' nı gösterir, `Comprehensive` daha fazla boyutlara ilişkin puanları gösterir (örneğin, tam metin düzeyinde, akıcı puan ve tamamlayıcı puanı, sözcük düzeyinde hata türü). Farklı puan boyutlarının tanımlarını ve sözcük hata türlerini görmek için [yanıt parametrelerini](#response-parameters) denetleyin. Varsayılan ayar `Basic` değeridir. | İsteğe Bağlı |
| EnableMiscue | Hatalı işaret hesaplamasını etkinleştirilir. Bu etkinken, bulunan sözcükler başvuru metniyle karşılaştırılır ve karşılaştırmaya göre atlama/ekleme ile işaretlenir. Kabul edilen değerler `False` şunlardır `True` . Varsayılan ayar `False` değeridir. | İsteğe Bağlı |
| ScenarioId | Özelleştirilmiş bir nokta sistemini gösteren bir GUID. | İsteğe Bağlı |

Aşağıda, telaffuz değerlendirmesi parametrelerini içeren bir JSON örneği verilmiştir:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

Aşağıdaki örnek kod, üst bilgiyle telaffuz değerlendirmesi parametrelerinin nasıl oluşturulacağını gösterir `Pronunciation-Assessment` :

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

Ses verilerinin, gecikme süresini önemli ölçüde azaltan akış (öbekli) karşıya yükleme işlemini kesinlikle öneririz. Akışı etkinleştirme hakkında bilgi için bkz. [farklı programlama dillerinde örnek kod](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment) .

>[!NOTE]
> Telaffuz değerlendirmesi özelliği şu anda `en-US` tüm [konuşmadan metin bölgelerinde](regions.md#speech-to-text)kullanılabilen dili desteklemektedir. Ve dillerini destekler, ve `en-GB` `zh-CN` bölgelerinde bulunan önizleme aşamasındadır `westus` `eastasia` `centralindia` .

### <a name="sample-request"></a>Örnek istek

Aşağıdaki örnekte konak adı ve gerekli üst bilgiler yer alır. Hizmetin bu örneğe dahil olmayan ses verilerini de beklediğini unutmayın. Daha önce belirtildiği gibi, öbek oluşturma önerilir, ancak gerekli değildir.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

Telaffuz değerlendirmesini etkinleştirmek için aşağıdaki üst bilgiyi ekleyebilirsiniz. Bu üstbilgiyi derlemek için [telaffuz değerlendirmesi parametrelerine](#pronunciation-assessment-parameters) bakın.

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

### <a name="http-status-codes"></a>HTTP durum kodu

Her yanıt için HTTP durum kodu başarı veya genel hataları gösterir.

| HTTP durum kodu | Açıklama | Olası neden |
|------------------|-------------|-----------------|
| `100` | Devam et | İlk istek kabul edildi. Verilerin geri kalanını göndermeye devam edin. (Öbekli aktarımlı olarak kullanılır) |
| `200` | Tamam | İstek başarılı oldu; yanıt gövdesi bir JSON nesnesidir. |
| `400` | Hatalı istek | Dil kodu sağlanmadı, desteklenen bir dil değil, geçersiz ses dosyası, vb. |
| `401` | Yetkisiz | Belirtilen bölgede veya geçersiz uç noktada abonelik anahtarı veya yetkilendirme belirteci geçersiz. |
| `403` | Yasak | Abonelik anahtarı veya yetkilendirme belirteci eksik. |

### <a name="chunked-transfer"></a>Öbekli aktarım

Öbekli aktarım ( `Transfer-Encoding: chunked` ), tanınma gecikmesini azaltmaya yardımcı olabilir. Konuşma hizmetinin, aktarım sırasında ses dosyasını işlemeye başlamasını sağlar. Kısa ses REST API kısmi veya geçici sonuçlar sağlamıyor.

Bu kod örneği, öbekte nasıl ses gönderileceğini gösterir. Yalnızca ilk öbek, ses dosyasının üst bilgisini içermelidir. `request` , `HttpWebRequest` uygun REST uç noktasına bağlı bir nesnedir. `audioFile` diskteki bir ses dosyasının yoludur.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>Yanıt parametreleri

Sonuçlar JSON olarak sağlanır. `simple`Biçim, bu üst düzey alanları içerir.

| Parametre | Açıklama  |
|-----------|--------------|
|`RecognitionStatus`|Başarılı tanıma gibi durum `Success` . Sonraki tabloya bakın.|
|`DisplayText`|Büyük harfler, noktalama, ters metin normalleştirmesinin ardından tanınan metin ("Doctor Smith" için "200" veya "Dr. Smith" için 200 gibi daha kısa formlara dönüştürme) ve küfür maskeleme. Yalnızca başarılı olduğunda sunun.|
|`Offset`|Tanınan konuşmanın ses akışında başladığı zaman (100-nanosaniyelik birimi).|
|`Duration`|Ses akışındaki tanınan konuşmanın süresi (100-nanosaniyelik birimi).|

`RecognitionStatus`Alan şu değerleri içerebilir:

| Durum | Açıklama |
|--------|-------------|
| `Success` | Tanıma başarılı oldu ve `DisplayText` alan var. |
| `NoMatch` | Ses akışında konuşma algılandı, ancak hedef dilden hiçbir sözcük eşleşmedi. Genellikle, tanınma dilinin kullanıcının konuşmadan farklı bir dil olduğu anlamına gelir. |
| `InitialSilenceTimeout` | Ses akışının başlangıcı yalnızca sessizlik içeriyordu ve hizmet konuşmayı beklerken zaman aşımına uğradı. |
| `BabbleTimeout` | Ses akışının başlangıcı yalnızca gürültü içeriyordu ve hizmet konuşmayı beklerken zaman aşımına uğradı. |
| `Error` | Tanıma hizmeti bir iç hatayla karşılaştı ve devam edemedi. Mümkünse yeniden deneyin. |

> [!NOTE]
> Ses yalnızca küfür içeriyorsa ve `profanity` sorgu parametresi olarak ayarlanırsa `remove` , hizmet bir konuşma sonucu döndürmez.

`detailed`Biçim, tanınan sonuçların ek biçimlerini içerir.
`detailed`Biçimini kullanırken, `DisplayText` `Display` listedeki her sonuç için olarak olarak sağlanır `NBest` .

`NBest`Listedeki nesne şunları içerebilir:

| Parametre | Açıklama |
|-----------|-------------|
| `Confidence` | 0,0 (güven yok) ile 1,0 arasındaki girdinin Güvenirlik puanı (tam güven) |
| `Lexical` | Tanınan metnin sözlü biçimi: tanınan gerçek sözcükler. |
| `ITN` | Tanınan metnin, telefon numarası, sayı, kısaltmalar ("Doctor Smith" ile "Dr Smith") ve uygulanan diğer dönüşümler içeren ters metin normalleştirilmiş ("kurallı") biçimi. |
| `MaskedITN` | İsteniyorsa, uygunsuz bir maskeleme uygulanmış ıTYPEFORM. |
| `Display` | Tanınan metnin noktalama ve büyük harfleri eklenmiş olan görüntüleme formu. Bu parametre, `DisplayText` biçim olarak ayarlandığında belirtilen şekilde aynıdır `simple` . |
| `AccuracyScore` | Konuşmayı telaffuz doğruluğu. Doğruluk, alfabesine 'in yerel konuşmacı söylenişi ile ne kadar yakın olduğunu gösterir. Word ve tam metin düzeyi doğruluk puanı, Fonem düzeyi doğruluk puanı ' ndan toplanır. |
| `FluencyScore` | Verilen konuşmayı akıcı olarak. Akıcı bir şekilde, konuşmanın bir yerel konuşmacının sözcükler arasında sessiz kesmeler kullanımıyla ne kadar yakın olduğunu gösterir. |
| `CompletenessScore` | Sözcük girişine başvuru metin girişi olarak bulunan sözcüklerin oranının hesaplanmasıyla belirlenen konuşma. |
| `PronScore` | Verilen konuşmayı gösteren telaffuz kalitesini belirten genel puan. Bu `AccuracyScore` , `FluencyScore` ve `CompletenessScore` ağırlığıyla toplanır. |
| `ErrorType` | Bu değer, ile karşılaştırıldığında bir sözcüğün atlanıp atlanmadığını, ekleneceğini veya hatalı bir şekilde olduğunu gösterir `ReferenceText` . Olası değerler şunlardır `None` (Yani bu sözcükte hata yok), `Omission` `Insertion` ve `Mispronunciation` . |

### <a name="sample-responses"></a>Örnek yanıtlar

Tanıma için tipik bir yanıt `simple` :

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Tanıma için tipik bir yanıt `detailed` :

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
      }
  ]
}
```

Telaffuz değerlendirmesi ile tipik bir yanıt:

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
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Ücretsiz bir Azure hesabı oluşturma](https://azure.microsoft.com/free/cognitive-services/)
- [Akustik modelleri özelleştirme](./how-to-custom-speech-train-model.md)
- [Dil modellerini özelleştirme](./how-to-custom-speech-train-model.md)
- [Toplu Iş dökümü hakkında bilgi edinin](batch-transcription.md)

