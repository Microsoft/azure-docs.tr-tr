---
title: REST kullanarak Bing Konuşma tanıma API 'SI ile çalışmaya başlama | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Konuşulan sesi metne dönüştürmek için Microsoft bilişsel hizmetler 'deki konuşma tanıma API 'sine erişmek için REST 'i kullanın.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e962a12c6c27737f95e78e80036e51bac41147d5
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965779"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Hızlı Başlangıç: Bing Konuşma tanımayı kullanın REST API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bulut tabanlı Bing Konuşma hizmetiyle, konuşma sesini metne dönüştürmek için REST API kullanarak uygulamalar geliştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Konuşma API 'sine abone olun ve ücretsiz deneme aboneliği anahtarı alın

Konuşma API 'SI bilişsel hizmetler 'in (daha önce Project Oxford) bir parçasıdır. Bilişsel [Hizmetler aboneliği](https://azure.microsoft.com/try/cognitive-services/) sayfasından ücretsiz deneme aboneliği anahtarlarına sahip olabilirsiniz. Konuşma API 'sini seçtikten sonra anahtarı almak için **API anahtarı al** ' ı seçin. Birincil ve ikincil anahtar döndürür. Her iki anahtar de aynı kotaya bağlıdır, bu nedenle her iki anahtarı da kullanabilirsiniz.

> [!IMPORTANT]
>* Abonelik anahtarı alın. REST API erişebilmeniz için önce bir [abonelik anahtarınız](https://azure.microsoft.com/try/cognitive-services/)olmalıdır.
>
>* Abonelik anahtarınızı kullanın. Aşağıdaki REST örneklerinde, YOUR_SUBSCRIPTION_KEY değerini kendi abonelik anahtarınızla değiştirin.
>
>* Abonelik anahtarı alma hakkında daha fazla [kimlik doğrulama](../how-to/how-to-authentication.md) sayfasına bakın.

### <a name="prerecorded-audio-file"></a>Önceden kaydedilmiş ses dosyası

Bu örnekte, REST API nasıl kullanacağınızı göstermek için kayıtlı bir ses dosyası kullanıyoruz. Kısa bir ifade söyleyen bir ses dosyasını kendiniz kaydedin. Örneğin, "bugün gibi hava durumu nedir?" deyin ya da "İzlenecek komik filmlerinizi bulun." Konuşma tanıma API 'SI, dış mikrofon girişini de destekler.

> [!NOTE]
> Örnek, sesin **PCM tek kanallı (mono), 16 kHz**Ile bir wav dosyası olarak kaydedilmesini gerektirir.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Bir tanıma isteği oluşturun ve konuşma tanıma hizmetine gönderin

Konuşma tanıma için bir sonraki adım, uygun istek üst bilgisi ve gövdesi ile konuşma HTTP uç noktalarına bir POST isteği göndermektir.

### <a name="service-uri"></a>Hizmet URI'si

Konuşma tanıma hizmeti URI 'SI, [Tanıma modları](../concepts.md#recognition-modes) ve [tanıma dilleri](../concepts.md#recognition-languages)temel alınarak tanımlanmıştır:

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>`tanıma modunu belirtir ve aşağıdaki değerlerden biri olmalıdır: `interactive`, `conversation`, veya `dictation`. Bu, URI 'de gerekli bir kaynak yoludur. Daha fazla bilgi için bkz. [Tanıma modları](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>`Sorgu dizesinde gerekli bir parametredir. Ses dönüştürme için hedef dili tanımlar: Örneğin, `en-US` İngilizce (Birleşik Devletler). Daha fazla bilgi için bkz. [tanıma dilleri](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>`Sorgu dizesinde isteğe bağlı bir parametredir. İzin verilen değerler ve `simple` ' `detailed`dir. Varsayılan olarak, hizmet sonuçları `simple` biçiminde döndürür. Daha fazla bilgi için bkz. [çıkış biçimi](../concepts.md#output-format).

Hizmet URI 'lerinin bazı örnekleri aşağıdaki tabloda listelenmiştir.

| Tanıma modu  | Dil | Çıkış biçimi | Hizmet URI'si |
|---|---|---|---|
| `interactive` | pt-BR | Varsayılan | https:\//Speech.platform.Bing.com/Speech/Recognition/interactive/cognitiveservices/v1?Language=pt-br |
| `conversation` | en-US | Ayrıntılı | https:\//Speech.platform.Bing.com/Speech/Recognition/Conversation/cognitiveservices/v1?Language=en-US&Format=Detailed |
| `dictation` | fr-FR | Basit | https:\//Speech.platform.Bing.com/Speech/Recognition/Dictation/cognitiveservices/v1?Language=fr-fr&Format=Simple |

> [!NOTE]
> Hizmet URI 'SI yalnızca uygulamanız, konuşma tanıma hizmetini çağırmak için REST API 'Leri kullandığında gereklidir. [İstemci kitaplıklarından](GetStartedClientLibraries.md)birini kullanırsanız, genellıkle hangi URI 'nin kullanıldığını bilmeniz gerekmez. İstemci kitaplıkları yalnızca belirli bir istemci kitaplığı için geçerli olan farklı hizmet URI 'Leri kullanabilir. Daha fazla bilgi için, seçtiğiniz istemci kitaplığına bakın.

### <a name="request-headers"></a>İstek üst bilgileri

İstek üstbilgisinde aşağıdaki alanlar ayarlanmalıdır:

- `Ocp-Apim-Subscription-Key`: Hizmeti her çağırdığınızda, abonelik anahtarınızı `Ocp-Apim-Subscription-Key` üstbilgiye geçirmeniz gerekir. Konuşma hizmeti, abonelik anahtarları yerine yetkilendirme belirteçlerinin geçirilmesini de destekler. Daha fazla bilgi için bkz. [Kimlik doğrulaması](../How-to/how-to-authentication.md).
- `Content-type`: `Content-type` Alan, ses akışının biçimini ve codec 'ini tanımlar. Şu anda yalnızca WAV dosyası ve PCM mono 16000 kodlaması desteklenir. Bu biçim `audio/wav; codec=audio/pcm; samplerate=16000`için Content-Type değeri.

`Transfer-Encoding` alanı isteğe bağlıdır. Bu alanı olarak `chunked`ayarlarsanız, sesi küçük parçalara göre seçebilirsiniz. Daha fazla bilgi için bkz. [öbekli aktarım](../How-to/how-to-chunked-transfer.md).

Aşağıda bir örnek istek üst bilgisi verilmiştir:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Hizmete bir istek gönder

Aşağıdaki örnek, konuşma REST uç noktalarına bir konuşma tanıma isteğinin nasıl gönderileceğini gösterir. `interactive` Tanıma modunu kullanır.

> [!NOTE]
> Değiştirin `YOUR_AUDIO_FILE` ile önceden kaydedilmiş ses dosyanızın yolu. Kendi `YOUR_SUBSCRIPTION_KEY` abonelik anahtarınızla değiştirin.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[kıvr](#tab/curl)

Örnek, Bash ile Linux 'ta kıvrımlı kullanır. Platformda yoksa, kıvrımlı yüklemeniz gerekebilir. Örnek ayrıca Windows, git Bash, ZSH ve diğer kabukların Cygwin üzerinde de kullanılabilir.

> [!NOTE]
> Değerin veri yerine bir dosya adı olduğunu `YOUR_AUDIO_FILE` `@` `--data-binary` gösterdiği için, önceden kaydedilmiş ses dosyasının yolunu ile değiştirirken ses dosyasının adından önce bırakın.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
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

---

## <a name="process-the-speech-recognition-response"></a>Konuşma tanıma yanıtını işleme

İstek işlendikten sonra, konuşma hizmeti yanıtları JSON biçiminde bir yanıt olarak döndürür.

> [!NOTE]
> Önceki kod bir hata döndürürse, olası nedeni bulmak için bkz. [sorun giderme](../troubleshooting.md) .

Aşağıdaki kod parçacığı, akıştan yanıtı nasıl okuyakullanabileceğinizi gösteren bir örnek gösterir.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[kıvr](#tab/curl)

Bu örnekte, doğrudan kıvrımlı yanıt iletisini bir dizedeki geri döndürür. Bunu JSON biçiminde göstermek istiyorsanız, ek araçlar (örneğin, JQ) kullanabilirsiniz.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

Aşağıdaki örnek bir JSON yanıtdır:

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Sınırlamalar

REST API bazı sınırlamalar vardır:

- Yalnızca 15 saniyeye kadar ses akışını destekler.
- Tanıma sırasında ara sonuçları desteklemez. Kullanıcılar yalnızca son tanıma sonucunu alır.

Bu sınırlamaları kaldırmak için konuşma [istemci kitaplıklarını](GetStartedClientLibraries.md)kullanın. Ya da [konuşma WebSocket protokolü](../API-Reference-REST/websocketprotocol.md)ile doğrudan çalışabilirsiniz.

## <a name="whats-next"></a>Sırada ne var?

- Java, vb. ' de C#REST API kullanmayı öğrenmek için, bu [örnek uygulamalara](../samples.md)bakın.
- Hataları bulmak ve gidermek için bkz. [sorun giderme](../troubleshooting.md).
- Daha gelişmiş özellikler kullanmak için bkz. konuşma [istemci kitaplıklarını](GetStartedClientLibraries.md)kullanarak çalışmaya başlama.

### <a name="license"></a>Lisans

Tüm bilişsel hizmetler SDK 'Ları ve örnekleri, MıT lisansıyla lisanslanır. Daha fazla bilgi için bkz. [Lisans](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
