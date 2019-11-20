---
title: Bing Konuşma WebSocket protokolü | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: WebSockets tabanlı Bing Konuşma Protokolü belgeleri
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e7f51d49624d5019bec058a2d12f6ca2f1366938
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966881"
---
# <a name="bing-speech-websocket-protocol"></a>Bing Konuşma WebSocket protokolü

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Konuşma, konuşulan sesi metne dönüştürmek için kullanılabilen en gelişmiş algoritmaları sunan bulut tabanlı bir platformdur. Bing Konuşma protokolü, istemci uygulamaları ile hizmet arasındaki [Bağlantı kurulumunu](#connection-establishment) ve Istemci[kaynaklı iletileri](#client-originated-messages) ve [hizmet kaynaklı iletileri](#service-originated-messages) arasında değiş tokuş edilen konuşma tanıma iletilerini tanımlar ). Ayrıca, [telemetri iletileri](#telemetry-schema) ve [hata işleme](#error-handling) açıklanır.

## <a name="connection-establishment"></a>Bağlantı kurma

Konuşma hizmeti protokolü, WebSocket standart belirtim [IETF RFC 6455](https://tools.ietf.org/html/rfc6455)' i izler. WebSocket bağlantısı, istemcinin HTTP semantiğini kullanmak yerine bir WebSocket 'e bağlantıyı yükseltme isteğini belirten HTTP üstbilgileri içeren http istekleri olarak başlatılır. Sunucu, bir http `101 Switching Protocols` yanıtı döndürerek WebSocket bağlantısına katılmak için Soline işaret eden durumu gösterir. Bu el sıkışma 'nın Exchange sonrasında, hem istemci hem de hizmet, bilgileri gönderip almak için ileti tabanlı bir protokol kullanmaya başlar.

WebSocket anlaşmasını başlatmak için, istemci uygulaması hizmete bir HTTPS GET isteği gönderir. Bu, konuşmaya özgü diğer üst bilgilerle birlikte standart WebSocket yükseltme üst bilgilerini içerir.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

Hizmet şu şekilde yanıt verir:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Tüm konuşma istekleri [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) şifrelemesini gerektirir. Şifrelenmemiş konuşma isteklerinin kullanımı desteklenmez. Aşağıdaki TLS sürümü destekleniyor:

* TLS 1,2

### <a name="connection-identifier"></a>Bağlantı tanımlayıcısı

Konuşma hizmeti, tüm istemcilerin bağlantıyı tanımlamak için benzersiz bir KIMLIK içermesini gerektirir. Bir WebSocket el sıkışması başlatıldığında istemciler *X-ConnectionID* üst *bilgisini içermelidir.* *X-ConnectionID* üst bilgisi, bir [evrensel benzersiz tanımlayıcı](https://en.wikipedia.org/wiki/Universally_unique_identifier) (UUID) değeri olmalıdır. *X-ConnectionID*içermeyen WebSocket yükseltme istekleri *x-ConnectionID* üst bilgisi için bir değer belirtmeyin veya geçerli bir UUID değeri içermez, hizmet tarafından http `400 Bad Request` yanıtıyla reddedilir.

### <a name="authorization"></a>Authorization

Standart WebSocket el sıkışma üst bilgilerine ek olarak, konuşma istekleri bir *Yetkilendirme* üst bilgisi gerektirir. Bu üst bilgiyi içermeyen bağlantı istekleri, hizmet tarafından http `403 Forbidden` yanıtıyla reddedilir.

*Yetkilendirme* üst bilgisinde JSON Web token (JWT) erişim belirteci bulunmalıdır.

Geçerli JWT erişim belirteçlerini almak için kullanılan API anahtarlarının nasıl abone olunacağı ve alınacağı hakkında daha fazla bilgi için bilişsel [Hizmetler aboneliği](https://azure.microsoft.com/try/cognitive-services/) sayfasına bakın.

API anahtarı, belirteç hizmetine geçirilir. Örneğin:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Belirteç erişimi için aşağıdaki üst bilgi bilgileri gereklidir.

| Name | Biçimi | Açıklama |
|----|----|----|
| Ocp-Apim-Subscription-Key | ASCII | Abonelik anahtarınız |

Belirteç hizmeti, JWT erişim belirtecini olarak `text/plain`döndürür. Ardından JWT, el sıkışma olarak `Base64 access_token` dize `Bearer`önekli bir *Yetkilendirme* üstbilgisi olarak geçirilir. Örneğin:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Tanımlama bilgileri

İstemciler, [RFC 6265](https://tools.ietf.org/html/rfc6265)' de BELIRTILDIĞI gibi http tanımlama *bilgilerini desteklemelidir.*

### <a name="http-redirection"></a>HTTP yeniden yönlendirme

İstemcilerin [http protokol belirtimi](https://www.w3.org/Protocols/rfc2616/rfc2616.html)tarafından belirtilen standart yeniden yönlendirme mekanizmalarını desteklemesi *gerekir* .

### <a name="speech-endpoints"></a>Konuşma uç noktaları

İstemciler, konuşma hizmeti 'nin uygun bir uç *noktasını kullanmalıdır.* Uç nokta tanıma modunu ve dilini temel alır. Tabloda bazı örnekler gösterilmektedir.

| Mod | `Path` | Hizmet URI'si |
| -----|-----|-----|
| Etkileşimli | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Konuşma | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Etme | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Daha fazla bilgi için bkz. [hizmet URI 'si](../GetStarted/GetStartedREST.md#service-uri) sayfası.

### <a name="report-connection-problems"></a>Bağlantı sorunlarını raporla

İstemciler bir bağlantı kurulurken karşılaşılan tüm sorunları anında raporlemelidir. Başarısız bağlantıları raporlamak için ileti Protokolü [bağlantı hatası telemetrisi](#connection-failure-telemetry)' nde açıklanmaktadır.

### <a name="connection-duration-limitations"></a>Bağlantı süresi sınırlamaları

Tipik Web hizmeti HTTP bağlantılarıyla karşılaştırıldığında, en son WebSocket bağlantıları *uzun* bir süredir. Konuşma hizmeti, hizmete yönelik WebSocket bağlantısı süresine ilişkin sınırlamalar getirir:

 * Etkin WebSocket bağlantısı için en uzun süre 10 dakikadır. Hizmetin veya istemcinin bu bağlantı üzerinden WebSocket iletileri göndermesi durumunda bir bağlantı etkin olur. Sınıra ulaşıldığında hizmet uyarı vermeden bağlantıyı sonlandırır. İstemciler, bağlantının en fazla bağlantı ömrü boyunca etkin kalmasını gerektirmeyen kullanıcı senaryoları geliştirmelidir.

 * Etkin olmayan WebSocket bağlantısı için en uzun süre 180 saniyedir. Hizmet veya istemci bağlantı üzerinden bir WebSocket iletisi gönderdiyse bağlantı devre dışı bırakılır. Etkin olmayan en fazla kullanım süresine ulaşıldığında hizmet, etkin olmayan WebSocket bağlantısını sonlandırır.

## <a name="message-types"></a>İleti türleri

İstemci ve hizmet arasında bir WebSocket bağlantısı kurulduktan sonra, hem istemci hem de hizmet ileti gönderebilir. Bu bölümde bu WebSocket iletilerinin biçimi açıklanmaktadır.

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) , WebSocket iletilerinin bir metin veya ikili kodlama kullanarak veri aktarabileceği belirtir. İki kodlama farklı, hat üzeri biçimleri kullanır. Her biçim, ileti yükünün verimli kodlama, iletim ve kod çözme için iyileştirilmiştir.

### <a name="text-websocket-messages"></a>Metin WebSocket iletileri

Metin WebSocket iletileri, bir üst bilgi bölümünün ve HTTP iletileri için kullanılan tanıdık çift satır başı yeni satır çiftiyle ayrılmış bir gövde bölümünden oluşan bir yük taşır. HTTP iletileri gibi metin WebSocket iletileri, bir tek satır başı yeni satır çiftiyle ayrılmış şekilde *ad: değer* biçiminde üst bilgiler belirtir. Metin WebSocket iletisine eklenen tüm metinlerin [UTF-8](https://tools.ietf.org/html/rfc3629) kodlaması kullanması *gerekir* .

Metin WebSocket iletileri üstbilgi *yolunda*bir ileti yolu belirtmelidir. Bu üstbilginin değeri, bu belgede daha sonra tanımlanan konuşma Protokolü ileti türlerinden biri olmalıdır.

### <a name="binary-websocket-messages"></a>İkili WebSocket iletileri

İkili WebSocket iletileri ikili bir yük taşır. Konuşma hizmeti protokolünde ses, ikili WebSocket iletileri kullanılarak hizmete iletilir ve hizmetten alınır. Diğer tüm iletiler metin WebSocket iletilerdir.

Metin WebSocket iletileri gibi, ikili WebSocket iletileri bir üst bilgi ve gövde bölümünden oluşur. İkili WebSocket iletisinin ilk 2 baytı, [büyük endian](https://en.wikipedia.org/wiki/Endianness) düzeninde başlık bölümünün 16 bitlik tamsayı boyutunu belirtir. En küçük başlık bölümünün boyutu 0 bayttır. En büyük boyut 8.192 bayttır. İkili *WebSocket iletilerinin üst* bilgilerindeki metinde [US-ASCII](https://tools.ietf.org/html/rfc20) kodlaması kullanılmalıdır.

Bir ikili WebSocket iletisindeki üstbilgiler, metin WebSocket iletileriyle aynı biçimde kodlanır. *Ad: değer* biçimi, tek satır başı bir yeni satır çifti ile ayrılmıştır. İkili WebSocket iletileri üstbilgi *yolunda*bir ileti yolu belirtmelidir. Bu üstbilginin değeri, bu belgede daha sonra tanımlanan konuşma Protokolü ileti türlerinden biri olmalıdır.

Konuşma hizmeti protokolünde hem metin hem de ikili WebSocket iletileri kullanılır.

## <a name="client-originated-messages"></a>İstemci kaynaklı iletiler

Bağlantı kurulduktan sonra, istemci ve hizmet iletileri gönderilmeye başlayabilir. Bu bölümde, istemci uygulamalarının konuşma hizmetine gönderdikleri iletilerin biçimi ve yükü açıklanmaktadır. [Hizmet kaynaklı iletiler](#service-originated-messages) bölümü, konuşma hizmetinde olan ve istemci uygulamalarına gönderilen iletileri gösterir.

İstemci tarafından hizmetlere `speech.config`gönderilen ana iletiler, `audio`ve `telemetry` iletilerdir. Her iletiyi ayrıntılı bir şekilde kabul etmeden önce, tüm bu iletiler için gereken yaygın ileti üstbilgileri açıklanmaktadır.

### <a name="required-message-headers"></a>Gerekli ileti üstbilgileri

İstemci kaynaklı tüm iletiler için aşağıdaki üstbilgiler gereklidir.

| Üstbilgi | Value |
|----|----|
| `Path` | Bu belgede belirtilen ileti yolu |
| X-RequestId | "Hiçbir tire" biçiminde UUID |
| X-Timestamp | ISO 8601 biçiminde istemci UTC saat zaman damgası |

#### <a name="x-requestid-header"></a>X-RequestId üstbilgisi

İstemci kaynaklı istekler, *X-RequestId* ileti üst bilgisi tarafından benzersiz şekilde tanımlanır. Bu üst bilgi, istemci kaynaklı tüm iletiler için gereklidir. *X-RequestId* üstbilgi değeri, "No-Dash" BIÇIMINDE bir UUID olmalıdır, örneğin, *123e4567e89b12d3a456426655440000*. Standart olarak *123e4567-e89b-12d3-A456-426655440000* *biçiminde olamaz.* *X-RequestId* üstbilgisi olmayan veya UUID 'ler için yanlış biçimi kullanan bir üstbilgi değeri olan istekler, hizmetin WebSocket bağlantısını sonlanmasına neden olur.

#### <a name="x-timestamp-header"></a>X-timestamp üst bilgisi

Bir *istemci uygulaması tarafından* konuşma hizmetine gönderilen her Ileti bir *X zaman damgası* üst bilgisi içermelidir. Bu üstbilginin değeri, istemcinin iletiyi gönderdiği zaman değeridir. *X zaman damgası* üstbilgisi olmayan ya da yanlış biçimi kullanan bir üst bilgi değeri olan istekler, hizmetin WebSocket bağlantısını sonlanmasına neden olur.

*X-timestamp* başlık değeri, ' yyyy'-'mm'-'dd'T'HH ': ' mm ': ' ss '. ' biçiminde olmalıdır. fffffffZ ', ' fffffff ' saniyenin bir kesri. Örneğin, ' 12,5 ', ' 12 + 5/10 saniye ' anlamına gelir ve ' 12,526 ', ' 12 Plus 526/1000 saniye ' anlamına gelir. Bu biçim [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumludur ve standart http *Tarih* üstbilgisinin aksine milisaniyelik çözüm sağlayabilir. İstemci uygulamaları, zaman damgalarını en yakın milisaniyeye yuvarlayabilirler. İstemci uygulamaları, cihaz saatinin bir [ağ zaman Protokolü (NTP) sunucusunu](https://en.wikipedia.org/wiki/Network_Time_Protocol)kullanarak saati doğru bir şekilde izlediğinden emin olmalıdır.

### <a name="message-speechconfig"></a>İleti`speech.config`

Konuşma hizmetinin olası en iyi konuşma tanımayı sağlaması için uygulamanızın özelliklerini bilmeleri gerekir. Gerekli özellikler verileri, uygulamanızı destekleyen cihaz ve işletim sistemi hakkında bilgiler içerir. Bu bilgileri `speech.config` iletide sağlarsınız.

İstemciler *,* konuşma hizmetine `speech.config` bağlantı kurulduktan sonra ve herhangi `audio` bir ileti göndermeden önce bir ileti göndermelidir. Bağlantı başına yalnızca bir `speech.config` ileti göndermeniz gerekir.

| Alan | Açıklama |
|----|----|
| WebSocket ileti kodlaması | Text |
| Body | JSON yapısı olarak yük |

#### <a name="required-message-headers"></a>Gerekli ileti üstbilgileri

| Üst bilgi adı | Value |
|----|----|
| `Path` | `speech.config` |
| X-Timestamp | ISO 8601 biçiminde istemci UTC saat zaman damgası |
| Content-Type | Uygulama/JSON; charset = UTF-8 |

Konuşma hizmeti protokolündeki istemci kaynaklı tüm iletilerde olduğu gibi, `speech.config` ileti hizmete gönderildiğinde istemcinin UTC saat saatini kaydeden bir *X zaman damgası* üst bilgisi içermelidir. Bu ileti *belirli* bir konuşma isteğiyle ilişkili olmadığından iletibirX-RequestIdüstbilgisi`speech.config` gerektirmez.

#### <a name="message-payload"></a>İleti yükü
`speech.config` İletinin yükü uygulama hakkında bilgi içeren bir JSON yapısıdır. Aşağıdaki örnekte bu bilgiler gösterilmektedir. İstemci ve cihaz bağlamı bilgileri, JSON yapısının *bağlam* öğesine dahildir.

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>Sistem öğesi

`speech.config` İletinin System. Version öğesi, istemci uygulaması veya cihazı tarafından kullanılan konuşma SDK yazılımının sürümünü içerir. Değer, *ana. ikincil. Build. Branch*biçimindedir. *Dal* bileşenini, uygulanabilir değilse atlayabilirsiniz.

##### <a name="os-element"></a>OS öğesi

| Alan | Açıklama | Kullanım |
|-|-|-|
| os.platform | Uygulamayı barındıran işletim sistemi platformu, örneğin, Windows, Android, iOS veya Linux |Gerekli |
| os.name | İşletim sistemi ürün adı, örneğin, deni veya Windows 10 | Gerekli |
| os.version | *Ana. ikincil. Build. Branch* biçimindeki işletim sisteminin sürümü | Gerekli |

##### <a name="device-element"></a>Cihaz öğesi

| Alan | Açıklama | Kullanım |
|-|-|-|
| device.manufacturer | Cihaz donanım üreticisi | Gerekli |
| device.model | Cihaz modeli | Gerekli |
| device.version | Cihaz üreticisi tarafından sunulan cihaz yazılımı sürümü. Bu değer, cihazın üretici tarafından izlenebilecek bir sürümünü belirtir. | Gerekli |

### <a name="message-audio"></a>İleti`audio`

Konuşma özellikli istemci uygulamaları ses akışını bir dizi ses öbeklerine dönüştürerek konuşma hizmetine ses gönderir. Her ses yığını, hizmet tarafından yapılacak bir konuşma sesinin bir kesimini taşır. Tek bir ses öbeğinin en büyük boyutu 8.192 bayttır. Ses akışı iletileri *Ikili WebSocket iletilerdir*.

İstemciler, `audio` hizmete bir ses öbeği göndermek için iletisini kullanır. İstemciler öbeklerdeki mikrofonlardan ses okur ve bu öbekleri bu öbeklerin konuşma hizmetine dökümünü gönderir. İlk `audio` ileti, sesin hizmet tarafından desteklenen kodlama biçimlerinden birine uygun olduğunu doğru şekilde belirten iyi biçimlendirilmiş bir üst bilgi içermelidir. Ek `audio` iletiler yalnızca mikrofondan okunan ikili ses akışı verilerini içerir.

İstemciler isteğe bağlı olarak sıfır `audio` uzunlukta bir gövde içeren bir ileti gönderebilir. Bu ileti, istemciye istemcinin kullanıcının konuşmayı durdurduğunu, söylenişi 'in bittiğini ve mikrofonun kapalı olduğunu bildirir.

Konuşma hizmeti, yeni bir `audio` istek/yanıt döngüsünün başlangıcını veya *dönüşü*bildirmek için benzersiz bir istek tanımlayıcısı içeren ilk iletiyi kullanır. Hizmet yeni bir istek tanımlayıcısına `audio` sahip bir ileti aldıktan sonra, önceki herhangi bir sırayla ilişkili sıraya alınmış veya gönderilmemiş iletileri atar.

| Alan | Açıklama |
|-------------|----------------|
| WebSocket ileti kodlaması | Binary |
| Body | Ses öbeğinin ikili verileri. En büyük boyut 8.192 bayttır. |

#### <a name="required-message-headers"></a>Gerekli ileti üstbilgileri

Tüm `audio` iletiler için aşağıdaki üstbilgiler gereklidir.

| Üstbilgi         |  Value     |
| ------------- | ---------------- |
| `Path` | `audio` |
| X-RequestId | "Hiçbir tire" biçiminde UUID |
| X-Timestamp | ISO 8601 biçiminde istemci UTC saat zaman damgası |
| Content-Type | Ses içerik türü. Tür, *ses/x-WAV* (PCM) ya da *Ses/Silk* (Silk) olmalıdır. |

#### <a name="supported-audio-encodings"></a>Desteklenen ses kodlamaları

Bu bölümde, konuşma hizmeti tarafından desteklenen ses codec bileşenleri açıklanmaktadır.

##### <a name="pcm"></a>PCM

Konuşma hizmeti, sıkıştırılmamış darbe Kodu Modülasyonu (PCM) sesini kabul eder. Ses, [WAV](https://en.wikipedia.org/wiki/WAV) biçiminde gönderilir, bu nedenle ilk ses öbeği geçerli bir [Kaynak Değişim dosyası biçimi](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (RIFF) üst *bilgisi içermelidir.* Bir istemci, geçerli bir RIFF üstbilgisi içermeyen bir ses öbeğini kullanmaya *başladığında,* hizmet isteği reddeder ve WebSocket bağlantısını sonlandırır.

PCM Audio, örnek başına 16 bit ve bir kanal (*Riff-16khz-16bit-mono-PCM*) Ile 16 kHz üzerinde *örneklenmelidir* . Konuşma hizmeti, stereo ses akışlarını desteklemez ve belirtilen bit hızını, örnek hızı veya kanal sayısını kullanmayan ses akışlarını reddeder.

##### <a name="opus"></a>Opus 'lar

Opus, açık, ücretsiz ve çok yönlü bir ses codec 'tir. Konuşma hizmeti, `32000` veya `16000`sabit bit hızında Opus 'yi destekler. Şu anda yalnızca Opus `audio/ogg` kapsayıcısı,MIMEtürütarafındanbelirtilenşekildedesteklenmektedir.`OGG`

Opus 'yi kullanmak için [JavaScript örneğini](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) değiştirin ve `RecognizerSetup` yöntemi döndürecek şekilde değiştirin.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>Konuşma sonunu Algıla

İnsanlar, konuşma bittiğinde açıkça sinyal vermez. Konuşmayı giriş olarak kabul eden herhangi bir uygulama, ses akışındaki konuşma sonunu işlemek için iki seçeneğe sahiptir: hizmet konuşma sonu algılama ve istemci konuşma sonu algılama. Bu iki seçenek de, hizmet okuma sonu algılama genellikle daha iyi bir kullanıcı deneyimi sağlar.

##### <a name="service-end-of-speech-detection"></a>Hizmet konuşma sonu algılama

İdeal ve ücretsiz konuşma deneyimini oluşturmak için, uygulamalar, kullanıcının konuşmayı ne zaman tamamladığını algılamasına izin verir. İstemcileri, hizmet sessizlik tespit edinceye ve bir `speech.endDetected` iletiyle geri yanıt verinceye kadar mikrofondan ses öbekleri olarak ses gönderir.

##### <a name="client-end-of-speech-detection"></a>İstemci konuşma sonu algılama

Kullanıcının konuşma sonuna bir şekilde sinyal vermesini sağlayan istemci uygulamalar, hizmete bu sinyali verebilir. Örneğin, bir istemci uygulamasında kullanıcının basması için bir "Durdur" veya "sessiz" düğmesi olabilir. İstemci uygulamaları, konuşmaya yönelik bir gövde içeren bir *Ses* öbek iletisi gönderir. Konuşma hizmeti bu iletiyi gelen ses akışının sonu olarak yorumlar.

### <a name="message-telemetry"></a>İleti`telemetry`

İstemci uygulamaları, konuşma hizmeti 'ni etkinleştirme hakkında telemetri göndererek her bir sırayla kabul *etmelidir* . Ön uç bildirimi, konuşma hizmeti 'nin, isteğin tamamlanması için gereken tüm iletilerin ve yanıtının istemci tarafından düzgün bir şekilde alındığından emin olmasını sağlar. Ayrıca, ön uç bildirimi, konuşma hizmeti 'nin istemci uygulamalarının beklendiği gibi çalıştığını doğrulamasına izin verir. Konuşma özellikli uygulamanızda sorun gidermeye yardımcı olması gerekiyorsa bu bilgiler çok değerlidir.

İstemciler `telemetry` ileti`turn.end` aldıktan sonra bir ileti göndererek bir iletiyi bir daha çabuk göndermesi gerekir. İstemciler en kısa sürede kabul `turn.end` etmeye çalışır. Bir istemci uygulaması, son uca bildirim kuramazsa, konuşma hizmeti bir hata ile bağlantıyı sonlandırmayabilir. İstemciler, `telemetry` *X-RequestId* değeri tarafından tanımlanan her istek ve yanıt için yalnızca bir ileti göndermelidir.

| Alan | Açıklama |
| ------------- | ---------------- |
| WebSocket ileti kodlaması | Text |
| `Path` | `telemetry` |
| X-Timestamp | ISO 8601 biçiminde istemci UTC saat zaman damgası |
| Content-Type | `application/json` |
| Body | Açma ile ilgili istemci bilgilerini içeren bir JSON yapısı |

`telemetry` İleti gövdesinin şeması [telemetri şeması](#telemetry-schema) bölümünde tanımlanmıştır.

#### <a name="telemetry-for-interrupted-connections"></a>Kesintiye uğramış bağlantılar için telemetri

Ağ bağlantısı, bir hata nedeniyle herhangi bir nedenle başarısız olursa ve istemci hizmetten `turn.end` bir *ileti almazsa,* istemci bir `telemetry` ileti gönderir. Bu ileti, istemci hizmete bir sonraki bağlantı kurduğunda başarısız isteği açıklar. İstemcilerin `telemetry` iletiyi göndermek için hemen bir bağlantı denemesi gerekmez. İleti istemcide ara belleğe alınmış olabilir ve gelecekte Kullanıcı tarafından istenen bağlantı üzerinden gönderilebilir. Başarısız isteğin iletisi, başarısız istekten *X-RequestId* değeri kullanmalıdır. `telemetry` Bağlantı kurulmadan, diğer iletiler için gönderilmesini veya almayı beklemeden hizmete gönderilebilir.

## <a name="service-originated-messages"></a>Hizmet kaynaklı iletiler

Bu bölümde, konuşma hizmetindeki ve istemciye gönderilen iletiler açıklanmaktadır. Konuşma hizmeti, istemci yeteneklerini bir kayıt defteri tutar ve her istemci için gereken iletileri oluşturur, bu nedenle tüm istemciler burada açıklanan tüm iletileri almaz. Kısaltma için, iletilere *yol* üstbilgisinin değeri tarafından başvurulur. Örneğin, bir konuşma. varsayım iletisi olarak *yol* değeri `speech.hypothesis` olan bir WebSocket metin iletisine başvurduk.

### <a name="message-speechstartdetected"></a>İleti`speech.startDetected`

İleti `speech.startDetected` , konuşma hizmetinin ses akışında konuşma algıladığını gösterir.

| Alan | Açıklama |
| ------------- | ---------------- |
| WebSocket ileti kodlaması | Text |
| `Path` | `speech.startDetected` |
| Content-Type | Uygulama/JSON; charset = UTF-8 |
| Body | Konuşma başlangıcı algılandığında koşullar hakkında bilgi içeren JSON yapısı. Bu yapıdaki *konum* alanı, akış başlangıcına göre ses akışında konuşma algılandığında sapmayı (100-nanosaniyelik Units) belirtir. |

#### <a name="sample-message"></a>Örnek ileti

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>İleti`speech.hypothesis`

Konuşma tanıma sırasında, konuşma hizmeti, hizmeti tanınan sözcükler hakkında düzenli olarak hipotezleri oluşturur. Konuşma hizmeti bu hipotezleri istemciye yaklaşık 300 milisaniyede bir gönderilir. Yalnızca Kullanıcı konuşma deneyimini geliştirmek için uygundur. `speech.hypothesis` Bu iletilerdeki metnin içeriğine veya doğruluğuna hiçbir bağımlılık götürememelidir.

 İleti `speech.hypothesis` , bazı metin işleme yeteneğine sahip olan istemciler için geçerlidir ve devam eden bir tanıma anında konuşma yapan kişiye neredeyse gerçek zamanlı geri bildirim sağlamak istiyor.

| Alan | Açıklama |
| ------------- | ---------------- |
| WebSocket ileti kodlaması | Text |
| `Path` | `speech.hypothesis` |
| X-RequestId | "Hiçbir tire" biçiminde UUID |
| Content-Type | uygulama/json |
| Body | Konuşma varsayım JSON yapısı |

#### <a name="sample-message"></a>Örnek ileti

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

, Tümcecik, ses akışının başlangıcına göre tanındığında, göreli konum (100-nanosaniyelik units *) öğesini belirtir* .

*Duration* öğesi, bu konuşma tümceciğinin süresini (100-nanosaniyelik birimi) belirtir.

İstemciler, konuşma varsayımlarında bulunan sıklık, zamanlama veya metin ya da herhangi bir iki konuşma hipotezleri metin tutarlılığı hakkında varsayımlara sahip olmamalıdır. Hipotezleri, hizmette yer alma işleminin yalnızca anlık görüntüleridir. Bunlar, bir dökümü kararlı bir şekilde temsil etmez. Örneğin, ilk bir konuşma varsayım "ince eğlence" sözcüklerini içerebilir ve ikinci varsayım "komik bul" sözcüklerini içerebilir. Konuşma hizmeti, konuşma varsayımsunda metinde hiçbir işlem (örneğin, büyük harf, noktalama) gerçekleştirmez.

### <a name="message-speechphrase"></a>İleti`speech.phrase`

Konuşma hizmeti, değişmeyecek bir tanıma sonucu üretmek için yeterli bilgiye sahip olduğunu belirlediğinde, hizmet bir `speech.phrase` ileti üretir. Konuşma hizmeti, kullanıcının bir cümleyi veya tümceciği tamamladığını algıladıktan sonra bu sonuçları üretir.

| Alan | Açıklama |
| ------------- | ---------------- |
| WebSocket ileti kodlaması | Text |
| `Path` | `speech.phrase` |
| Content-Type | uygulama/json |
| Body | Konuşma tümceciği JSON yapısı |

Konuşma tümceciği JSON şeması aşağıdaki alanları içerir `RecognitionStatus`:, `DisplayText`, `Offset`, ve `Duration`. Bu alanlar hakkında daha fazla bilgi için bkz. [Transcriptıon yanıtları](../concepts.md#transcription-responses).

#### <a name="sample-message"></a>Örnek ileti

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>İleti`speech.endDetected`

`speech.endDetected` İleti, istemci uygulamanın hizmete akış sesi durdurmasını gerektiğini belirtir.

| Alan | Açıklama |
| ------------- | ---------------- |
| WebSocket ileti kodlaması | Text |
| `Path` | `speech.endDetected` |
| Body | Konuşma sonu algılandığında sapmayı içeren JSON yapısı. Bu konum, tanıma için kullanılan sesin başlangıcından itibaren 100-nanosaniyelik birimi arasındaki sapmayı temsil eder. |
| Content-Type | Uygulama/JSON; charset = UTF-8 |

#### <a name="sample-message"></a>Örnek ileti

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

, Tümcecik, ses akışının başlangıcına göre tanındığında, göreli konum (100-nanosaniyelik units *) öğesini belirtir* .

### <a name="message-turnstart"></a>İleti`turn.start`

, `turn.start` Hizmetin perspektifinden bir başlangıç olduğunu bildirir. `turn.start` İleti her zaman istek için aldığınız ilk yanıt iletisidir. `turn.start` İleti almazsanız, hizmet bağlantısı durumunun geçersiz olduğunu varsayalım.

| Alan | Açıklama |
| ------------- | ---------------- |
| WebSocket ileti kodlaması | Text |
| `Path` | `turn.start` |
| Content-Type | Uygulama/JSON; charset = UTF-8 |
| Body | JSON yapısı |

#### <a name="sample-message"></a>Örnek ileti

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

`turn.start` İletinin gövdesi, her açma için bağlam içeren bir JSON yapısıdır. *Bağlam* öğesi bir *servicetag* özelliği içeriyor. Bu özellik, hizmetin sırasıyla ilişkilendirildiği bir etiket değerini belirtir. Bu değer, uygulamanızda hata giderme konusunda yardım almak istiyorsanız Microsoft tarafından kullanılabilir.

### <a name="message-turnend"></a>İleti`turn.end`

, `turn.end` Hizmetin perspektifinden sonuna kadar sinyal gönderir. `turn.end` İleti her zaman istek için aldığınız Son Yanıt iletisidir. İstemciler, bu iletinin alındı bilgisini Temizleme etkinlikleri için bir sinyal olarak kullanabilir ve boşta durumuna geçiş yapabilir. `turn.end` İleti almazsanız, hizmet bağlantısı durumunun geçersiz olduğunu varsayalım. Bu durumlarda, mevcut hizmet bağlantısını kapatın ve yeniden bağlayın.

| Alan | Açıklama |
| ------------- | ---------------- |
| WebSocket ileti kodlaması | Text |
| `Path` | `turn.end` |
| Body | Yok. |

#### <a name="sample-message"></a>Örnek ileti

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Telemetri şeması

*Telemetri* iletisinin gövdesi, bir açma veya denenen bağlantıyla ilgili istemci bilgilerini IÇEREN bir JSON yapısıdır. Yapı, istemci olayları gerçekleştiğinde kayıt yapan istemci zaman damgalarının oluşur. Her zaman damgası, "X-timestamp Header" başlıklı bölümde açıklandığı gibi ISO 8601 biçiminde olmalıdır. JSON yapısındaki gerekli alanları belirtmeyen veya doğru zaman damgası biçimini kullanmayan *telemetri* iletileri hizmetin istemciye olan bağlantıyı sonlanmasına neden olabilir. İstemciler tüm gerekli alanlar için geçerli *değerler sağlamalıdır.* İstemciler uygun olan her durumda isteğe bağlı alanlar için *değer sağlamalıdır.* Bu bölümdeki örneklerde gösterilen değerler yalnızca gösterim amaçlıdır.

Telemetri şeması aşağıdaki bölümlere ayrılmıştır: alınan ileti zaman damgaları ve ölçümleri. Her parçanın biçimi ve kullanımı aşağıdaki bölümlerde belirtilmiştir.

### <a name="received-message-time-stamps"></a>Alınan ileti zaman damgaları

İstemciler, hizmete başarıyla bağlandıktan sonra aldıkları tüm iletiler için giriş saati değerlerini içermelidir. Bu değerler, istemcinin ağdan her iletiyi *aldığı* zamanı kaydeder. Değer başka bir zaman içermemelidir. Örneğin, istemci *ileti üzerinde işlem* yaptığı zamanı kaydetmez. Alınan ileti zaman damgaları bir *ad: değer* çiftleri dizisinde belirtilmiştir. Çiftin adı iletinin *yol* değerini belirtir. Çiftin değeri, iletinin alındığı istemci saatini belirtir. Ya da, belirtilen ada sahip birden fazla ileti alınmışsa, çiftin değeri, bu mesajların ne zaman alındığını gösteren zaman damgalarının bir dizisidir.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

İstemciler, JSON gövdesindeki iletiler için zaman damgaları ekleyerek, hizmet tarafından gönderilen tüm iletilerin alındığını kabul *etmelidir* . Bir istemci bir iletinin alındığını kabul edemezse, hizmet bağlantıyı sonlandırabilir.

### <a name="metrics"></a>Ölçümler

İstemciler, bir isteğin ömrü boyunca gerçekleşen olaylar hakkında bilgi içermelidir. Aşağıdaki ölçümler desteklenir: `Connection`, `Microphone`, ve `ListeningTrigger`.

### <a name="metric-connection"></a>Ölçüt`Connection`

`Connection` Ölçüm, istemci tarafından yapılan bağlantı denemelerinde ayrıntıları belirler. Ölçüm, WebSocket bağlantısının başladığı ve bittiği zaman damgalarını içermelidir. Ölçüm yalnızca *bir bağlantının ilk kümesi için*gereklidir. `Connection` Bu bilgileri içermesi için sonraki dönüşler gerekli değildir. Bir istemci bir bağlantı oluşturulmadan önce birden çok bağlantı denemesi yaptığında, *Tüm* bağlantı denemeleri hakkında bilgi dahil edilmelidir. Daha fazla bilgi için bkz. [bağlantı hatası telemetrisi](#connection-failure-telemetry).

| Alan | Açıklama | Kullanım |
| ----- | ----------- | ----- |
| Name | `Connection` | Gerekli |
| Id | Bu bağlantı isteği için *X-ConnectionID* üst bilgisinde kullanılan bağlantı tanımlayıcı değeri | Gerekli |
| Start | İstemcinin bağlantı isteğini gönderdiği saat | Gerekli |
| End | İstemcinin bağlantının başarıyla kurulduğu veya hata durumlarında, reddedildiği, reddettiği veya başarısız olduğu konusunda bildirim alma zamanı | Gerekli |
| Hata | Oluşan hatanın açıklaması (varsa). Bağlantı başarılı olduysa, istemciler bu alanı atmalıdır. Bu alanın uzunluk üst sınırı 50 karakterdir. | Hata durumları için gerekli, aksi belirtilmedikçe |

Hata açıklaması en çok 50 karakter uzunluğunda olmalı ve ideal olarak aşağıdaki tabloda listelenen değerlerden biri olmalıdır. Hata koşulu bu değerlerden biriyle eşleşmezse istemciler, boşluk olmadan [Camelbüyük harfleri](https://en.wikipedia.org/wiki/Camel_case) kullanarak hata koşulunun kısa açıklamasını kullanabilir. *Telemetri* iletisi gönderebilme hizmeti için hizmet bağlantısı gerekir, bu nedenle *telemetri* iletisinde yalnızca geçici veya geçici hata koşulları bildirilebilir. İstemcinin hizmetle bağlantı *kurmasını engelleyen hata* koşulları, istemcinin *telemetri* iletileri de dahil olmak üzere hizmete ileti göndermesini engeller.

| Hata | Kullanım |
| ----- | ----- |
| DNSfailure | İstemci, ağ yığınındaki bir DNS hatası nedeniyle hizmete bağlanamadı. |
| Etiş olmayan | İstemci bir bağlantı denedi, ancak ağ yığını hiçbir fiziksel ağın kullanılamadığını bildirdi. |
| NoAuthorization | Bağlantı için bir yetkilendirme belirteci alınmaya çalışılırken istemci bağlantısı başarısız oldu. |
| NoResources | İstemci bir bağlantı kurmaya çalışırken bazı yerel kaynakları (örneğin, bellek) tükendi. |
| Yasak | Hizmet WebSocket yükseltme isteğinde bir http `403 Forbidden` durum kodu döndürdüğünden, istemci hizmete bağlanamadı. |
| Yetkilendirilmemiş | Hizmet WebSocket yükseltme isteğinde bir http `401 Unauthorized` durum kodu döndürdüğünden, istemci hizmete bağlanamadı. |
| BadRequest | Hizmet WebSocket yükseltme isteğinde bir http `400 Bad Request` durum kodu döndürdüğünden, istemci hizmete bağlanamadı. |
| Sunucu kullanılamıyor | Hizmet WebSocket yükseltme isteğinde bir http `503 Server Unavailable` durum kodu döndürdüğünden, istemci hizmete bağlanamadı. |
| ServerError | Hizmet WebSocket yükseltme isteğinde bir `HTTP 500` iç hata durum kodu döndürdüğünden, istemci hizmete bağlanamadı. |
| zaman aşımı | İstemcinin bağlantı isteği hizmetten yanıt vermeden zaman aşımına uğradı. *Bitiş* alanı, istemcinin zaman aşımına uğradığını ve bağlantı beklemeyi durdurduğu saati içerir. |
| 'Da clienterror | İstemci, bazı iç istemci hataları nedeniyle bağlantıyı sonlandırdı. |

### <a name="metric-microphone"></a>Ölçüt`Microphone`

Tüm konuşma dönüşler için ölçümgereklidir.`Microphone` Bu ölçüm, Ses girişinin bir konuşma isteği için etkin olarak kullanıldığı zaman, istemcideki zamanı ölçer.

İstemci uygulamanızda `Microphone` ölçüm için *Başlangıç* saati değerlerini kaydetme yönergeleri olarak aşağıdaki örnekleri kullanın:

* Bir istemci uygulaması, bir kullanıcının mikrofonu başlatmak için bir fiziksel düğmeye basması gerekir. Düğmeye bastıktan sonra istemci uygulaması, mikrofona ait girişi okur ve konuşma hizmetine gönderir. `Microphone` Ölçüm için *Başlangıç* değeri, mikrofon başlatıldığında ve giriş sağlamaya hazırlandıktan sonra düğme gönderilişinde geçen süreyi kaydeder. `Microphone` Ölçüm için *bitiş* değeri, istemci uygulamanın hizmetten `speech.endDetected` iletiyi aldıktan sonra hizmete akış sesi durdurduğu saati kaydeder.

* İstemci uygulaması, "her zaman" dinleme yapan bir anahtar sözcük spour kullanır. Yalnızca bir anahtar sözcük spour, konuşulan bir tetikleyici tümceciği algıladıktan sonra istemci uygulaması, girişi mikrofondan toplayıp konuşma hizmetine gönderiyor. `Microphone` Ölçüm için *Başlangıç* değeri, anahtar sözcüğünün, istemciden gelen girişi kullanmaya başlamasını Başlatan süreyi kaydeder. `Microphone` Ölçüm için *bitiş* değeri, istemci uygulamanın hizmetten `speech.endDetected` iletiyi aldıktan sonra hizmete akış sesi durdurduğu saati kaydeder.

* Bir istemci uygulamasının sabit bir ses akışına erişimi vardır ve bu ses akışında bir *konuşma algılama modülünde*sessizlik/konuşma algılaması gerçekleştirir. `Microphone` Ölçüm için *Başlangıç* değeri, *konuşma algılama modülünün* istemciye, ses akışından giriş kullanmaya başlamasını bildirme zamanını kaydeder. `Microphone` Ölçüm için *bitiş* değeri, istemci uygulamanın hizmetten `speech.endDetected` iletiyi aldıktan sonra hizmete akış sesi durdurduğu saati kaydeder.

* İstemci uygulaması, çok yönlü bir isteği ikinci bir şekilde işliyor ve ikinci çift için giriş toplamak üzere mikrofonu açmak üzere bir hizmet yanıtı iletisi tarafından bilgilendirilir. `Microphone` Ölçüm için *Başlangıç* değeri, istemci uygulamanın mikrofonu etkinleştirme süresini kaydeder ve bu ses kaynağından gelen girişi kullanmaya başlar. `Microphone` Ölçüm için *bitiş* değeri, istemci uygulamanın hizmetten `speech.endDetected` iletiyi aldıktan sonra hizmete akış sesi durdurduğu saati kaydeder.

`Microphone` Ölçüm için *bitiş* saati değeri, istemci uygulamanın akış ses girişini durdurduğu saati kaydeder. Çoğu durumda, istemci `speech.endDetected` hizmetten iletiyi aldıktan sonra bu olay kısa bir süre sonra oluşur. İstemci uygulamaları, `Microphone` ölçümün *bitiş* saati değerinin daha sonra `speech.endDetected` ileti için alış süresi değerinden daha sonra gerçekleşmesini sağlayarak protokole doğru bir şekilde uyduklarında emin olabilirler. Genellikle, tek bir ve başka bir sürenin bitişi arasında bir gecikme olduğu için istemciler, sonraki bir sıradaki her türlü doğru bir süre için `Microphone` ölçüm *Başlangıç* saatinin, istemci, hizmete ses girişi akışını sağlamak için mikrofonu kullanmaya *başladı* .

| Alan | Açıklama | Kullanım |
| ----- | ----------- | ----- |
| Name | L | Gerekli |
| Start | İstemcinin, mikrofondan veya başka bir ses akışından ses girişi kullanmaya başladığı veya anahtar sözcük spour 'ten bir tetikleyici aldığı zaman | Gerekli |
| End | İstemcinin mikrofon veya ses akışını kullanarak durduğu zaman | Gerekli |
| Hata | Oluşan hatanın açıklaması (varsa). Mikrofon işlemleri başarılı olduysa, istemciler bu alanı atmalıdır. Bu alanın uzunluk üst sınırı 50 karakterdir. | Hata durumları için gerekli, aksi belirtilmedikçe |

### <a name="metric-listeningtrigger"></a>Ölçüt`ListeningTrigger`
`ListeningTrigger` Ölçüm, kullanıcının konuşma girişini başlatan eylemi yürüttüğünde geçen süreyi ölçer. `ListeningTrigger` Ölçüm isteğe bağlıdır, ancak bu ölçümü sağlayabilecek istemciler bunu yapmak için önerilir.

İstemci uygulamanızda `ListeningTrigger` ölçüm için *Başlangıç* ve *bitiş* saati değerlerini kaydetme yönergeleri için aşağıdaki örnekleri kullanın.

* Bir istemci uygulaması, bir kullanıcının mikrofonu başlatmak için bir fiziksel düğmeye basması gerekir. Bu ölçümün *Başlangıç* değeri, düğme gönderme zamanını kaydeder. *Bitiş* değeri, düğme gönderimi tamamlandığında saati kaydeder.

* İstemci uygulaması, "her zaman" dinleme yapan bir anahtar sözcük spour kullanır. Anahtar sözcük spour, konuşulan bir tetikleyici tümceciği algıladıktan sonra istemci uygulaması, bu girişi mikrofondan okur ve konuşma hizmetine gönderir. Bu ölçüm için *Başlangıç* değeri, anahtar sözcüğünün, daha sonra tetikleyici ifadesi olarak algılanan ses aldığı süreyi kaydeder. *Bitiş* değeri, tetikleyici tümceciğinin son sözcüğünün Kullanıcı tarafından konuşulan saati kaydeder.

* Bir istemci uygulamasının sabit bir ses akışına erişimi vardır ve bu ses akışında bir *konuşma algılama modülünde*sessizlik/konuşma algılaması gerçekleştirir. Bu ölçümün *Başlangıç* değeri, *konuşma algılama modülünün* ses aldığı ve daha sonra konuşma olarak algıladığı süreyi kaydeder. *Bitiş* değeri, *konuşma algılama modülünün* konuşma algıladığında geçen süreyi kaydeder.

* İstemci uygulaması, çok yönlü bir isteği ikinci bir şekilde işliyor ve ikinci çift için giriş toplamak üzere mikrofonu açmak üzere bir hizmet yanıtı iletisi tarafından bilgilendirilir. İstemci uygulaması bu sırayla bir `ListeningTrigger` *ölçüm içermemelidir.*

| Alan | Açıklama | Kullanım |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | İsteğe Bağlı |
| Start | İstemci dinleme tetikleyicisinin başlatıldığı zaman | Gerekli |
| End | İstemci dinleme tetiklemenin bittiği zaman | Gerekli |
| Hata | Oluşan hatanın açıklaması (varsa). Tetikleyici işlemi başarılı olduysa, istemciler bu alanı atmalıdır. Bu alanın uzunluk üst sınırı 50 karakterdir. | Hata durumları için gerekli, aksi belirtilmedikçe |

#### <a name="sample-message"></a>Örnek ileti

Aşağıdaki örnekte, hem Received Iletileri hem de ölçüm parçalarını içeren bir telemetri iletisi gösterilmektedir:

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>Hata işleme

Bu bölümde, uygulamanızın işlemesi gereken hata iletilerinin ve koşulların türleri açıklanmaktadır.

### <a name="http-status-codes"></a>HTTP durum kodları

WebSocket yükseltme isteği sırasında, konuşma hizmeti `400 Bad Request`, vb. gibi standart http durum kodlarından herhangi birini döndürebilir. Uygulamanız bu hata koşullarını doğru şekilde işlemelidir.

#### <a name="authorization-errors"></a>Yetkilendirme hataları

WebSocket yükseltmesi sırasında yanlış yetkilendirme sağlanıyorsa, konuşma hizmeti bir http `403 Forbidden` durum kodu döndürür. Bu hata kodunu tetikleyesağlayan koşullar arasında aşağıdakiler bulunur:

* *Yetkilendirme* üstbilgisi eksik

* Geçersiz yetkilendirme belirteci

* Süre dolduğunda yetkilendirme belirteci

`403 Forbidden` Hata iletisi konuşma hizmetinde bir sorun göstermiyor. Bu hata iletisi, istemci uygulamasıyla ilgili bir sorun olduğunu gösterir.

### <a name="protocol-violation-errors"></a>Protokol ihlali hataları

Konuşma hizmeti bir istemciden herhangi bir protokol ihlali algılarsa, bir *durum kodu* ve sonlandırma *nedeni* döndükten sonra hizmet WebSocket bağlantısını sonlandırır. İstemci uygulamaları bu bilgileri, ihlalleri gidermek ve gidermek için kullanabilir.

#### <a name="incorrect-message-format"></a>Hatalı ileti biçimi

İstemci hizmete bu belirtimde verilen doğru biçimde kodlanmamış bir metin veya ikili ileti gönderirse, hizmet *1007 geçersiz yük verileri* durum kodu ile bağlantıyı kapatır.

Hizmet aşağıdaki örneklerde gösterildiği gibi çeşitli nedenlerle bu durum kodunu döndürür:

* "Yanlış ileti biçimi. İkili iletinin üst bilgi boyutu öneki geçersiz. " İstemci geçersiz bir üst bilgi boyutu ön ekine sahip bir ikili ileti gönderdi.

* "Yanlış ileti biçimi. İkili iletinin üst bilgi boyutu geçersiz. " İstemci geçersiz bir üst bilgi boyutu belirtilen bir ikili ileti gönderdi.

* "Yanlış ileti biçimi. UTF-8 ' e kod çözme ikili ileti üstbilgileri başarısız oldu. " İstemci UTF-8 ' de doğru şekilde kodlanmamış üstbilgiler içeren bir ikili ileti gönderdi.

* "Yanlış ileti biçimi. Kısa mesaj hiç veri içermiyor. " İstemci gövde verisi içermeyen bir kısa mesaj gönderdi.

* "Yanlış ileti biçimi. Metin iletisi kodu çözme, UTF-8 ile başarısız oldu. " İstemci, UTF-8 içinde doğru kodlanmamış bir SMS mesajı gönderdi.

* "Yanlış ileti biçimi. Kısa mesaj, üst bilgi ayırıcısı içermiyor. " İstemci, üst bilgi ayırıcısı içermeyen veya yanlış üstbilgi ayırıcısını kullanan bir kısa mesaj gönderdi.

#### <a name="missing-or-empty-headers"></a>Eksik veya boş üstbilgiler

İstemci, gerekli üst bilgiler *X-RequestId* veya *yol*olmayan bir ileti gönderirse, hizmet bir *1002 protokol hatası* durum kodu ile bağlantıyı kapatır. İleti "eksik/boş üst bilgi. {Header Name}. "

#### <a name="requestid-values"></a>RequestId değerleri

İstemci, yanlış biçimdeki bir *X-RequestId* üstbilgisini belirten bir ileti gönderirse, hizmet bağlantıyı kapatır ve *1002 protokol hata* durumunu döndürür. İleti "geçersiz istek. X-RequestId üst bilgi değeri, hiç kesik olmayan UUID biçiminde belirtilmedi. "

#### <a name="audio-encoding-errors"></a>Ses kodlama hataları

Bir istemci, bir açmayı Başlatan bir ses öbeği gönderirse ve ses biçimi veya kodlama gerekli belirtime uygun değilse, hizmet bağlantıyı kapatır ve *1007 geçersiz yük verileri* durum kodu döndürür. İleti, biçim kodlaması hata kaynağını gösterir.

#### <a name="requestid-reuse"></a>RequestId yeniden kullanım

Bir istemci tamamlandığında, istek tanımlayıcısını yeniden kullanan bir ileti gönderirse, hizmet bağlantıyı kapatır ve bir *1002 protokol hata* durum kodu döndürür. İleti "geçersiz istek. İstek tanımlayıcılarının yeniden kullanılmasına izin verilmiyor. "

## <a name="connection-failure-telemetry"></a>Bağlantı hatası telemetrisi

İstemcilerin olası en iyi kullanıcı deneyimini sağlamak için *telemetri* iletisini kullanarak bir bağlantı içindeki önemli denetim noktaları için zaman damgalarının konuşma hizmetini bilgilendirmeniz gerekir. İstemcilerin denendiği, ancak başarısız olan bağlantı hizmetini bilgilendirdiği aynı derecede önemlidir.

Başarısız olan her bağlantı girişimi için, istemciler benzersiz bir *X-RequestId* üstbilgi değeri olan bir *telemetri* iletisi oluşturur. İstemci bir bağlantı kuramadığından, JSON gövdesindeki alındı *iletileri* alanı atlanabilir. Yalnızca *ölçümler* alanındaki girişdahil`Connection` edilir. Bu giriş, başlangıç ve bitiş zamanı damgalarının yanı sıra karşılaşılan hata koşulunu da içerir.

### <a name="connection-retries-in-telemetry"></a>Telemetride bağlantı yeniden denemeleri

İstemciler, bağlantı girişimini tetikleyen olay tarafından *birden çok bağlantı denemesinden* *yeniden denemeleri* ayırt etmelidir. Kullanıcı girişi olmadan programlı olarak gerçekleştirilen bağlantı girişimleri yeniden denemeler değildir. Kullanıcı girişine yanıt olarak gerçekleştirilen birden çok bağlantı girişimi birden çok bağlantı girişimdir. İstemciler, Kullanıcı tarafından tetiklenen her bağlantı denemesini benzersiz bir *X-RequestId* ve *telemetri* iletisi olarak verir. İstemciler, programlı yeniden denemeler için *X-RequestId* ' i yeniden kullanır. Tek bir bağlantı girişimi için birden çok yeniden deneme yapılmışsa, her yeniden deneme denemesi `Connection` *telemetri* iletisine giriş olarak eklenir.

Örneğin, bir kullanıcının bir bağlantı başlatmak için anahtar sözcük tetikleyicisi konuşduğunu ve DNS hataları nedeniyle ilk bağlantı denemesinin başarısız olduğunu varsayalım. Ancak, istemci tarafından programlı olarak gerçekleştirilen ikinci bir deneme başarılı olur. İstemci, kullanıcıdan ek giriş gerektirmeden bağlantıyı yeniden denentiğinden, istemci bağlantıyı anlatmak için birden çok `Connection` girişi olan tek bir *telemetri* iletisi kullanır.

Başka bir örnek olarak, bir kullanıcının bir bağlantı başlatmak için anahtar sözcük tetikleyicisi konuşduğunu ve bu bağlantı denemesinin üç yeniden denemeden sonra başarısız olduğunu varsayalım. İstemci daha sonra verir, hizmete bağlanmaya çalışmayı durduruyor ve kullanıcıya bir sorun olduğunu bildirir. Kullanıcı anahtar sözcük tetikleyicisini yeniden konuşuyor. Bu kez, istemcinin hizmete bağlandığını varsayalım. Bağlandıktan sonra istemci, bağlantı başarısızlıklarını tanımlayan üç `Connection` giriş içeren hizmete hemen bir telemetri iletisi gönderir. `turn.end` İletiyi aldıktan sonra istemci, başarılı bağlantıyı açıklayan başka bir *telemetri* iletisi gönderir.

## <a name="error-message-reference"></a>Hata iletisi başvurusu

### <a name="http-status-codes"></a>HTTP durum kodları

| HTTP durum kodu | Açıklama | Sorun giderme |
| - | - | - |
| 400 Hatalı Istek | İstemci yanlış bir WebSocket bağlantı isteği gönderdi. | Tüm gerekli parametreleri ve HTTP üstbilgilerini sağlayıp değerlerin doğru olduğundan emin olun. |
| 401 Yetkisiz | İstemci gerekli yetkilendirme bilgilerini içermiyordu. | WebSocket bağlantısına *Yetkilendirme* üst bilgisini gönderiyor olup olmadığınızı denetleyin. |
| 403 Yasak | İstemci yetkilendirme bilgilerini gönderdi, ancak geçersiz. | *Yetkilendirme* üst bilgisinde, bir zaman aşımına uğradı veya geçersiz bir değer göndermediğinden emin olun. |
| 404 Bulunamadı | İstemci desteklenmeyen bir URL yoluna erişmeye çalıştı. | WebSocket bağlantısı için doğru URL 'YI kullandığınızdan emin olun. |
| 500 sunucu hatası | Hizmet bir iç hatayla karşılaştı ve isteği karşılayamadı. | Çoğu durumda bu hata geçicidir. İsteği yeniden deneyin. |
| 503 Hizmet Kullanılamıyor | Hizmet, isteği işlemek için kullanılamadı. | Çoğu durumda bu hata geçicidir. İsteği yeniden deneyin. |

### <a name="websocket-error-codes"></a>WebSocket hata kodları

| WebSocketsStatus kodu | Açıklama | Sorun giderme |
| - | - | - |
| 1000 normal kapatma | Hizmet bir hata olmadan WebSocket bağlantısını kapattı. | WebSocket kapanışı beklenmiyorsa, hizmetin WebSocket bağlantısını nasıl ve ne zaman sonlandırabileceğini anladığınızdan emin olmak için belgeleri yeniden okuyun. |
| 1002 protokol hatası | İstemci protokol gereksinimlerine uyamadı. | Protokol belgelerini anladığınızdan ve gereksinimler hakkında açık olduğundan emin olun. Protokol gereksinimlerini ihlal ediyor olup olmadığını görmek için hata nedenlerine ilişkin önceki belgeleri okuyun. |
| 1007 geçersiz yük verisi | İstemci bir protokol iletisinde geçersiz yük gönderdi. | Hatalar için hizmete gönderdiğiniz son iletiyi denetleyin. Yük hataları hakkında önceki belgeleri okuyun. |
| 1011 sunucu hatası | Hizmet bir iç hatayla karşılaştı ve isteği karşılayamadı. | Çoğu durumda bu hata geçicidir. İsteği yeniden deneyin. |

## <a name="related-topics"></a>İlgili konular

WebSocket tabanlı konuşma hizmeti protokolünün uygulanması olan bir [JavaScript SDK 'sına](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) bakın.
