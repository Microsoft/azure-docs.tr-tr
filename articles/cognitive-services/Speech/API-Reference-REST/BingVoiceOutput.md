---
title: Microsoft konuşma hizmeti 'nin Metin Okuma API'si | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Çeşitli seslerle ve dillerde gerçek zamanlı metin okuma dönüştürmesi sağlamak için metin okuma API 'sini kullanın
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ee9b0b47fb88cba948bc06db6eb83fe9c076fe40
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966866"
---
# <a name="bing-text-to-speech-api"></a>Bing metin okuma API 'SI

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Giriş

Bing metin okuma API 'SI sayesinde, uygulamanız bir bulut sunucusuna HTTP istekleri gönderebilir, burada metin, insan sesi taşıyan konuşmaya anında birleştirilir ve bir ses dosyası olarak döndürülür. Bu API, çeşitli farklı seslerde ve dillerde gerçek zamanlı metinden konuşmaya dönüştürme sağlamak için birçok farklı bağlamda kullanılabilir.

## <a name="VoiceSynReq"></a>Ses sensimi isteği

### <a name="Subscription"></a>Yetkilendirme belirteci

Her ses sensimi isteği bir JSON Web Token (JWT) erişim belirteci gerektirir. JWT erişim belirteci, konuşma isteği üst bilgisinde geçirilir. Belirtecin süre sonu 10 dakikadır. Geçerli JWT erişim belirteçlerini almak için kullanılan API anahtarlarını abone etme ve alma hakkında daha fazla bilgi için bkz. bilişsel [Hizmetler aboneliği](https://azure.microsoft.com/try/cognitive-services/).

API anahtarı, belirteç hizmetine geçirilir. Örneğin:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Belirteç erişimi için gerekli üst bilgi bilgileri aşağıdaki gibidir.

Name| Biçimi | Açıklama
----|----|----
Ocp-Apim-Subscription-Key | ASCII | Abonelik anahtarınız

Belirteç hizmeti, JWT erişim belirtecini olarak `text/plain`döndürür. Ardından JWT, konuşma uç noktasına, `Base64 access_token` dize `Bearer`önekli bir yetkilendirme üstbilgisi olarak geçirilir. Örneğin:

`Authorization: Bearer [Base64 access_token]`

İstemcilerin, metinden konuşmaya hizmetine erişmek için aşağıdaki uç noktayı kullanması gerekir:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Daha önce açıklandığı gibi abonelik anahtarınızı içeren bir erişim belirteci edinene kadar bu bağlantı bir `403 Forbidden` yanıt hatası oluşturur.

### <a name="Http"></a>HTTP üstbilgileri

Aşağıdaki tabloda, ses birleştirme istekleri için kullanılan HTTP üstbilgileri gösterilmektedir.

Üstbilgi |Value |Açıklamalar
----|----|----
Content-Type | Application/SSML + XML | Giriş içerik türü.
X-Microsoft-OutputFormat | **1.** SSML-16khz-16bit-mono-TTS <br> **2.** RAW-16khz-16bit-mono-PCM <br>**3.** ses-16khz-16kbps-mono-SIREN <br> **4.** Riff-16khz-16kbps-mono-SIREN <br> **5.** Riff-16khz-16bit-mono-PCM <br> **6.** ses-16khz-128kbit hızı-mono-MP3 <br> **7.** ses-16khz-64kbit hızı-mono-MP3 <br> **8.** ses-16khz-32K bit hızı-mono-MP3 | Çıkış ses biçimi.
X-Search-AppID | Bir GUID (yalnızca onaltılı, tire yok) | İstemci uygulamasını benzersiz bir şekilde tanımlayan bir KIMLIK. Bu, uygulamalar için mağaza KIMLIĞI olabilir. Bir tane yoksa, KIMLIK, bir uygulama için Kullanıcı tarafından oluşturulmuş olabilir.
X-Search-ClientID | Bir GUID (yalnızca onaltılı, tire yok) | Her yükleme için bir uygulama örneğini benzersiz bir şekilde tanımlayan bir KIMLIK.
User-Agent | Uygulama adı | Uygulama adı gereklidir ve 255 karakterden kısa olmalıdır.
Authorization | Yetkilendirme belirteci |  <a href="#Subscription">Yetkilendirme belirteci</a> bölümüne bakın.

### <a name="InputParam"></a>Giriş parametreleri

Bing metin okuma API 'sine yapılan istekler HTTP POST çağrıları kullanılarak yapılır. Üst bilgiler önceki bölümde belirtilmiştir. Gövde, sentezlenen metni temsil eden konuşma birleştirme biçimlendirme dili (SSML) girişi içerir. Konuşmacının dili ve cinsiyeti gibi konuşma yönlerini denetlemek için kullanılan biçimlendirmenin açıklaması için bkz. [SSML W3C belirtimi](https://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Desteklenen SSML girişinin en büyük boyutu, tüm Etiketler dahil olmak üzere 1.024 karakterdir.

###  <a name="SampleVoiceOR"></a>Örnek: sesli çıktı isteği

Sesli çıkış isteğine bir örnek aşağıdaki gibidir:

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Sesli çıkış yanıtı

Bing metin okuma API 'SI, istemciye geri ses göndermek için HTTP POST kullanır. API yanıtı, ses akışını ve codec 'i içerir ve istenen çıkış biçimiyle eşleşir. Belirli bir istek için döndürülen ses 15 saniyeyi aşmamalıdır.

### <a name="SuccessfulRecResponse"></a>Örnek: başarılı sensıs yanıtı

Aşağıdaki kod, başarılı bir Voice sensıs isteğine yönelik JSON yanıtının bir örneğidir. Kodun açıklamaları ve biçimlendirmesi yalnızca bu örneğin amaçlıdır ve gerçek yanıttan çıkarılır.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Örnek: sensıs hatası

Aşağıdaki örnek kodda bir Voice-sensıs sorgu hatasına yönelik JSON yanıtı gösterilmektedir:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Hata yanıtları

Hata | Açıklama
----|----
HTTP/400 Hatalı Istek | Gerekli bir parametre eksik, boş veya null ya da gerekli veya isteğe bağlı bir parametreye geçirilen değer geçersiz. "Geçersiz" yanıtını almak için bir neden, izin verilen uzunluktan daha uzun bir dize değeri geçirmektir. Sorunlu parametrenin kısa bir açıklaması dahildir.
HTTP/401 yetkilendirilmemiş | İstek yetkili değil.
HTTP/413 RequestEntityTooLarge  | SSML girişi desteklenenden daha büyük.
HTTP/502 BadGateway | Ağla ilgili bir sorun veya sunucu tarafı sorun vardır.

Bir hata yanıtına örnek olarak aşağıdaki gibidir:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>SSML aracılığıyla ses çıkışını değiştirme

Microsoft metin okuma API 'SI [, W3C konuşma birleştirme biçimlendirme dili (SSML) sürüm 1,0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/)' de tanımlandığı şekilde ssml 1,0 ' i destekler. Bu bölümde, SSML etiketlerini kullanarak, üretilen sesli çıkışın belirli özelliklerini, konuşma oranı, telaffuz gibi çeşitli özellikleri değiştirme örnekleri gösterilmektedir.

1. Kesme ekleme

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
   ```

2. Konuşma hızını değiştirme

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

3. Söylenişi

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
   ```

4. Birimi Değiştir

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

5. Aralık değiştirme

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

6. Prosody dağılımını değiştirme

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> Ses verilerinin aşağıdaki biçimde dosyalanmış 8k veya 16k WAV olması gerektiğini aklınızda olun: **CRC kodu** (CRC-32): geçerli aralıkta 4 bayt (DWORD) 0x00000000 ~ 0xFFFFFFFF; **Ses biçimi bayrağı**: geçerli aralıkta 4 bayt (DWORD) 0x00000000 ~ 0xFFFFFFFF; **Örnek sayısı**: Geçerli Aralık 4 bayt (DWORD) 0x00000000 ~ 0x7; **İkili gövdenin boyutu**: Geçerli Aralık 4 bayt (DWORD) 0x00000000 ~ 0x7; **İkili gövde**: n bayt.

## <a name="SampleApp"></a>Örnek uygulama

Uygulama ayrıntıları için bkz. [Visual C#.net metin okuma örnek uygulaması](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Desteklenen yerel ayarlar ve ses yazı tipleri

Aşağıdaki tabloda, desteklenen bazı yerel ayarlar ve ilgili ses yazı tiplerinin bazıları tanımlanmaktadır.

Yerel Ayar | Cinsiyet | Hizmet adı eşleme
---------|--------|------------
AR-örn. | Kadın | "Microsoft sunucu konuşma Sesli konuşmayı metne (ar-Örneğin, Hoda)"
ar-SA | Erkek | "Microsoft Server Konuşma metin konuşma ses (ar-SA, Naayf)"
BG-BG | Erkek | "Microsoft Server Konuşma metin okuma ses (bg-BG, çalışan Ivan)"
CA-ES | Kadın | "Microsoft Server Konuşma metin okuma ses (ca-ES, HerenaRUS)"
cs-CZ | Erkek | "Microsoft sunucu konuşma Sesli konuşmayı metne (cs-CZ, Jakub)"
v-DK | Kadın | "Microsoft Server Konuşma metin konuşma ses (v-DK, HelleRUS)"
de-AT | Erkek | "Microsoft Server Konuşma metin konuşma ses (de-AT, Michael)"
de-CH | Erkek | "Microsoft Server Konuşma metin konuşma ses (de-CH, Karsten)"
de-DE | Kadın | "Microsoft Server konuşma Metin Okuma sesi (de-DE, Hedda)"
de-DE | Kadın | "Microsoft Server Konuşma metin konuşma ses (de-DE, HeddaRUS)"
de-DE | Erkek | "Microsoft Server konuşma Metin Okuma sesi (de-DE, Stefan, Apollo)"
el-GR | Erkek | "Microsoft Server Konuşma metin konuşma ses (el-GR, Stefanos)"
tr-AU | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-AU, Catherine)"
tr-AU | Kadın | "Microsoft Server Konuşma metin konuşma ses (en-AU, HayleyRUS)"
CA tr | Kadın | "Microsoft Server Konuşma metin konuşma ses (tr-CA, Gamze)"
CA tr | Kadın | "Microsoft Server Konuşma metin konuşma ses (tr-CA, HeatherRUS)"
en-GB | Kadın | "Microsoft Server Konuşma metin konuşma ses (en-GB, Susan, Apollo)"
en-GB | Kadın | "Microsoft Server Konuşma metin konuşma ses (en-GB, HazelRUS)"
en-GB | Erkek | "Microsoft Server Konuşma metin konuşma ses (en-GB, George, Apollo)"
IE tr | Erkek | "Microsoft Server Konuşma metin konuşma ses (tr-IE, Sean)"
tr-giriş | Kadın | "Microsoft Server Konuşma metin konuşma ses (en-IN Heera, Apollo)"
tr-giriş | Kadın | "Microsoft Server Konuşma metin konuşma ses (en-IN, PriyaRUS)"
tr-giriş | Erkek | "Microsoft Server Konuşma metin konuşma ses (en-IN Ravi, Apollo)"
en-US | Kadın | "Microsoft Server Konuşma metin konuşma ses (en-US, ZiraRUS)"
en-US | Kadın | "Microsoft Server Konuşma metin konuşma ses (en-US, JessaRUS)"
en-US | Erkek | "Microsoft Server Konuşma metin konuşma ses (en-US, BenjaminRUS)"
es-ES | Kadın | "Microsoft Server Konuşma metin okuma ses (es-ES, Gamze, Apollo)"
es-ES | Kadın | "Microsoft Server Konuşma metin okuma ses (es-ES, HelenaRUS)"
es-ES | Erkek | "Microsoft Server Konuşma metin okuma ses (es-ES, Pablo, Apollo)"
es-MX | Kadın | "Microsoft Server Konuşma metin okuma ses (es-MX, HildaRUS)"
es-MX | Erkek | "Microsoft Server Konuşma metin okuma ses (es-MX, Raul Apollo)"
FI-FI | Kadın | "Microsoft Server Konuşma metin konuşma ses (fi-FI, HeidiRUS)"
fr-CA | Kadın | "Microsoft Server Konuşma metin okuma ses (fr-CA, Caroline)"
fr-CA | Kadın | "Microsoft Server Konuşma metin okuma ses (fr-CA, HarmonieRUS)"
FR-CH | Erkek | "Microsoft Server Konuşma metin okuma ses (fr-CH, Guillaume)"
fr-FR | Kadın | "Microsoft Server Konuşma metin okuma ses (fr-FR, Julie, Apollo)"
fr-FR | Kadın | "Microsoft Server Konuşma metin okuma ses (fr-FR, HortenseRUS)"
fr-FR | Erkek | "Microsoft Server Konuşma metin okuma ses (fr-FR, Paul, Apollo)"
He IL| Erkek| "Microsoft Server Konuşma metin konuşma ses (he-IL, Asaf)"
yüksek giriş | Kadın | "Microsoft Server Konuşma metin konuşma ses (Merhaba açma, Kalpana, Apollo)"
yüksek giriş | Kadın | "Microsoft Server Konuşma metin konuşma ses (Merhaba-IN, Kalpana)"
yüksek giriş | Erkek | "Microsoft Server Konuşma metin konuşma ses (Merhaba-IN, Hemant)"
hr-HR | Erkek | "Microsoft Server Konuşma metin okuma ses (hr-HR, Matej)"
hu-HU | Erkek | "Microsoft Server Konuşma metin konuşma ses (hu-HU, Szabolcs)"
ID | Erkek | "Microsoft Server Konuşma metin konuşma ses (-ID, Andika)"
İt-IT | Erkek | "Microsoft Server Konuşma metin konuşma ses (Cosimo, it-IT, Apollo)"
İt-IT | Kadın | "Microsoft Server Konuşma metin konuşma ses (it-IT, LuciaRUS)"
ja-JP | Kadın | "Microsoft Server Konuşma metin okuma ses (ja-JP, Ayumi, Apollo)"
ja-JP | Erkek | "Microsoft Server Konuşma metin okuma ses (ja-JP, Ichiro, Apollo)"
ja-JP | Kadın | "Microsoft Server Konuşma metin okuma ses (ja-JP, HarukaRUS)"
ko-KR | Kadın | "Microsoft Server Konuşma metin konuşma ses (ko-KR, HeamiRUS)"
ms-MY | Erkek | "Microsoft Server Konuşma metin okuma ses (ms MY, Rizwan)"
NB-yok | Kadın | "Microsoft Server Konuşma metin okuma ses (nb-yok, HuldaRUS)"
NL-NL | Kadın | "Microsoft Server Konuşma metin okuma ses (nl-NL, HannaRUS)"
pl-PL | Kadın | "Microsoft Server Konuşma metin okuma ses (pl-PL, PaulinaRUS)"
pt-BR | Kadın | "Microsoft Server Konuşma metin okuma ses (pt-BR, HeloisaRUS)"
pt-BR | Erkek | "Microsoft Server Konuşma metin okuma ses (pt-BR, Daniel, Apollo)"
pt-PT | Kadın | "Microsoft Server Konuşma metin okuma ses (pt-PT, HeliaRUS)"
Ro-RO | Erkek | "Microsoft Server Konuşma metin konuşma ses (ro-RO, Andrei)"
ru-RU | Kadın | "Microsoft Server Konuşma metin okuma ses (ru-RU, Irina, Apollo)"
ru-RU | Erkek | "Microsoft Server Konuşma metin okuma ses (ru-RU, Pavel, Apollo)"
ru-RU | Kadın | "Microsoft Server Konuşma metin okuma ses (ru-RU, EkaterinaRUS)"
SK-SK | Erkek | "Microsoft Server Konuşma metin okuma ses (sk-SK, Filip)"
SL SI | Erkek | "Microsoft Server Konuşma metin okuma ses (sl-sı, Lado)"
sv-SE | Kadın | "Microsoft Server Konuşma metin konuşma ses (sv-SE, HedvigRUS)"
Veri-ın | Erkek | "Microsoft Server Konuşma metin konuşma ses (veri-ın, Valluvar)"
TH TH | Erkek | "Microsoft Server Konuşma metin okuma ses (th-TH, Pattara)"
tr-TR | Kadın | "Microsoft Server Konuşma metin okuma ses (tr-TR, SedaRUS)"
VI VN | Erkek | "Microsoft Server Konuşma metin okuma ses (vi-VN bir)"
zh-CN | Kadın | "Microsoft Server Konuşma metin konuşma ses (zh-CN, HuihuiRUS)"
zh-CN | Kadın | "Microsoft Server Konuşma metin konuşma ses (zh-CN, Yaoyao, Apollo)"
zh-CN | Erkek | "Microsoft Server Konuşma metin konuşma ses (zh-CN, Kangkang, Apollo)"
zh-HK | Kadın | "Microsoft Server Konuşma metin konuşma ses (zh-HK Tracy, Apollo)"
zh-HK | Kadın | "Microsoft Server Konuşma metin konuşma ses (zh-HK TracyRUS)"
zh-HK | Erkek | "Microsoft Server Konuşma metin konuşma ses (zh-HK Danny, Apollo)"
zh-TW | Kadın | "Microsoft Server Konuşma metin konuşma ses (zh-TW Yating, Apollo)"
zh-TW | Kadın | "Microsoft Server Konuşma metin konuşma ses (zh-TW, HanHanRUS)"
zh-TW | Erkek | "Microsoft Server Konuşma metin konuşma ses (zh-TW Zhiwei, Apollo)"

 \* AR-ÖRNEĞIN modern Standart Arapça (MSA) desteği.

> [!NOTE]
> Bing Konuşma API'si 'un en iyi hale getirilmesini sağlamak için, **Microsoft Server konuşma metin okuma seslerinin (CS-CZ, Vit)** ve **microsoft Server konuşma metin okuma Voice (en-IE, Shaun)** 3/31/2018 sonrasında kullanım dışı olacağını unutmayın. Yetenek. Lütfen kodunuzu güncelleştirilmiş adlarla güncelleştirin.

## <a name="TrouNSupport"></a>Sorun giderme ve destek

Tüm soruları ve sorunları [Bing Konuşma Service](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN forumuna gönderin. Tüm ayrıntıları ekleyin, örneğin:

* Tam istek dizesine bir örnek.
* Uygulanabiliyorsa, günlük kimliklerini içeren bir başarısız isteğin tam çıktısı.
* Başarısız olan isteklerin yüzdesi.
