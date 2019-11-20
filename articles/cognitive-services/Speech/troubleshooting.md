---
title: Sorun giderme Bing Konuşma | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Konuşma kullanırken sorunları çözme.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1213045867f84efd6d77aebff12b8c8725d82d08
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965561"
---
# <a name="troubleshooting-bing-speech"></a>Sorun giderme Bing Konuşma

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Hatayla`HTTP 403 Forbidden`

Konuşma tanıma API 'si kullanılırken bir `HTTP 403 Forbidden` hata döndürür.

### <a name="cause"></a>Nedeni

Bu hata genellikle kimlik doğrulama sorunlarından kaynaklanır. Geçerli `Ocp-Apim-Subscription-Key` `HTTP 403 Forbidden` veya `Authorization` üst bilgi içermeyen bağlantı istekleri, hizmet tarafından yanıt vererek reddedilir.

Kimlik doğrulaması için abonelik anahtarı kullanıyorsanız, nedeni

- Abonelik anahtarı eksik veya geçersiz
- Abonelik anahtarının kullanım kotası aşıldı
- REST API `Ocp-Apim-Subscription-Key` çağrıldığında alan istek üst bilgisinde ayarlanmadı

Kimlik doğrulaması için yetkilendirme belirteci kullanıyorsanız, aşağıdaki nedenlerden dolayı hata ortaya çıkabilir.

- Rest kullanılırken, istekte üstbilgieksik`Authorization`
- Yetkilendirme üstbilgisinde belirtilen yetkilendirme belirteci geçersiz
- Yetkilendirme belirtecinin zaman aşımına uğradı. Erişim belirtecinin süresi 10 dakikadır.

Kimlik doğrulaması hakkında daha fazla bilgi için bkz. [kimlik doğrulama](How-to/how-to-authentication.md) sayfası.

### <a name="troubleshooting-steps"></a>Sorun giderme adımları

#### <a name="verify-that-your-subscription-key-is-valid"></a>Abonelik anahtarınızın geçerli olduğunu doğrulayın

Doğrulama için aşağıdaki komutu çalıştırabilirsiniz. *YOUR_SUBSCRIPTION_KEY* değerini kendi abonelik anahtarınızla değiştirin. Abonelik anahtarınız geçerliyse, yetkilendirme belirtecini JSON Web Token (JWT) olarak Yanıtla ' yı alırsınız. Aksi takdirde, yanıtta bir hata alırsınız.

> [!NOTE]
> Kendi `YOUR_SUBSCRIPTION_KEY` abonelik anahtarınızla değiştirin.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[kıvr](#tab/curl)

Örnek, Bash ile Linux 'ta kıvrımlı kullanır. Platformda yoksa, kıvrımlı yüklemeniz gerekebilir. Örnek, Windows, git Bash, ZSH ve diğer kabukların de Cygwin üzerinde çalışmalıdır.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Uygulamanızda veya REST isteğinde, yukarıda kullanılan abonelik anahtarını kullandığınızdan emin olun.

#### <a name="verify-the-authorization-token"></a>Yetkilendirme belirtecini doğrulama

Bu adım yalnızca kimlik doğrulaması için yetkilendirme belirtecini kullanıyorsanız gereklidir. Yetkilendirme belirtecinin hala geçerli olduğunu doğrulamak için aşağıdaki komutu çalıştırın. Komutu hizmete bir POST isteği yapar ve hizmetten bir yanıt iletisi bekler. Hala http `403 Forbidden` hatası alıyorsanız, erişim belirtecinin doğru şekilde ayarlandığından ve dolmadığından emin olun.

> [!IMPORTANT]
> Belirtecin süresi 10 dakikadır.
> [!NOTE]
> Önceden `YOUR_AUDIO_FILE` kaydedilmiş ses dosyanızın yoluyla ve `YOUR_ACCESS_TOKEN` önceki adımda döndürülen yetkilendirme belirteciyle değiştirin.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[kıvr](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Hatayla`HTTP 400 Bad Request`

Bu nedenle, genellikle istek gövdesi geçersiz ses verileri içerir. Şu anda yalnızca WAV dosyasını destekliyoruz.

## <a name="error-http-408-request-timeout"></a>Hatayla`HTTP 408 Request Timeout`

Hata, büyük olasılıkla hizmete hiçbir ses verisi gönderilmediğinden ve hizmetin zaman aşımından sonra bu hatayı döndürdüğünden oluşur. REST API için, ses verileri istek gövdesine yerleştirilmelidir.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` Yanıttaki`InitialSilenceTimeout`

Ses verileri genellikle soruna neden olan nedenidir. Örneğin,

- Sesin başlangıcında uzun bir sessizlik süresi vardır. Hizmet, birkaç saniye sonra tanımayı durdurur ve döndürür `InitialSilenceTimeout`.
- Ses, ses verilerinin sessizlik olarak değerlendirilmesini sağlayan desteklenmeyen codec biçimi kullanır.
