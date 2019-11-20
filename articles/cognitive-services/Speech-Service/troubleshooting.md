---
title: Konuşma SDK-konuşma hizmeti sorunlarını giderme
titleSuffix: Azure Cognitive Services
description: Bu makalede, konuşma SDK 'sını kullandığınızda karşılaşabileceğiniz sorunları çözmenize yardımcı olacak bilgiler sağlanmaktadır.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 3beda852b3e711ee969b7f80f8f326285cc2cad6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558891"
---
# <a name="troubleshoot-the-speech-sdk"></a>Konuşma SDK’sı sorunlarını giderme

Bu makalede, konuşma SDK 'sını kullandığınızda karşılaşabileceğiniz sorunları çözmenize yardımcı olacak bilgiler sağlanmaktadır.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Hata: WebSocket yükseltmesi bir kimlik doğrulama hatasıyla başarısız oldu (403)

Bölge veya hizmetiniz için yanlış uç nokta olabilir. URI'nin doğru olduğundan emin olmak için kontrol edin.

Ayrıca, bir sorun olabilir abonelik anahtarı veya yetkilendirme belirteci. Daha fazla bilgi için sonraki bölüme bakın.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Hata: HTTP 403 yasaklanmış veya HTTP 401 Yetkisiz

Bu hata genellikle tarafından kimlik doğrulama sorunları nedeniyle oluşur. Geçerli bir olmadan bağlantı isteklerini `Ocp-Apim-Subscription-Key` veya `Authorization` üst bilgi, 403 veya 401 durumuyla reddedilir.

* Kimlik doğrulaması için bir abonelik anahtarı kullanıyorsanız, aşağıdaki hata nedeniyle görebilirsiniz:

    - Abonelik anahtarı eksik veya geçersiz
    - Aboneliğinizin kullanımı kotayı aştınız

* Bir yetkilendirme belirteci için kimlik doğrulaması kullanıyorsanız, aşağıdaki hata nedeniyle görebilirsiniz:

    - Yetkilendirme belirteci geçersiz.
    - Yetkilendirme belirtecinin süresi doldu

### <a name="validate-your-subscription-key"></a>Abonelik anahtarınızı doğrulayın

Aşağıdaki komutlardan birini çalıştırarak bir geçerli abonelik anahtarı olduğunu doğrulayabilirsiniz.

> [!NOTE]
> Değiştirin `YOUR_SUBSCRIPTION_KEY` ve `YOUR_REGION` abonelik anahtarı ve ilgili bölge.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Geçerli bir abonelik anahtarı girdiyseniz, komut bir yetkilendirme belirteci döndürür, aksi takdirde bir hata döndürülür.

### <a name="validate-an-authorization-token"></a>Bir yetkilendirme belirtecini doğrula

Bir yetkilendirme belirteci kimlik doğrulaması için kullanıyorsanız, yetkilendirme belirtecini hala geçerli olduğunu doğrulamak için aşağıdaki komutlardan birini çalıştırın. Belirteçleri 10 dakika için geçerlidir.

> [!NOTE]
> Değiştirin `YOUR_AUDIO_FILE` ile önceden kaydedilmiş ses dosyanızın yolu. Değiştirin `YOUR_ACCESS_TOKEN` yetkilendirme belirteciyle önceki adımda döndürdü. Değiştirin `YOUR_REGION` doğru bölgeyle.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Geçerli bir yetkilendirme belirteci girdiyseniz, komut ses dosyanız için dökümü döndürür, aksi takdirde bir hata döndürülür.

---

## <a name="error-http-400-bad-request"></a>Hata: HTTP 400 Hatalı Istek

İstek gövdesi geçersiz ses verilerini içerdiğinde, bu hata genellikle oluşur. Yalnızca WAV biçimi desteklenmiyor. Ayrıca, isteğin üstbilgileri için uygun değerleri belirtmenize emin olmak için kontrol `Content-Type` ve `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Hata: HTTP 408 Istek zaman aşımı

Ses veri hizmetine gönderilen en olası hata oluşur. Bu hata, ağ sorunları da kaynaklanıyor olabilir.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>Yanıt "RecognitionStatus" olan "InitialSilenceTimeout"

Bu sorun genellikle tarafından ses veri neden olur. Çünkü bu hatayı görebilirsiniz:

* Uzun bir esnetme ses başına sessizlik yoktur. Bu durumda, hizmet tanıma birkaç saniye sonra durur ve döndürür `InitialSilenceTimeout`.

* Ses sessizlik kabul edilmesi ses verilerini neden olan desteklenmeyen codec biçimi kullanır.

## <a name="next-steps"></a>Sonraki adımlar

* [Sürüm notlarını gözden geçirin](releasenotes.md)
