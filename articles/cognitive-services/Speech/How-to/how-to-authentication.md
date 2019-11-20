---
title: Bing Konuşma kimlik doğrulaması | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Konuşma API'si kullanmak için kimlik doğrulaması isteyin
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d1e708ff29293b87935d0d191ba44ad4a11917a0
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965667"
---
# <a name="authenticate-to-the-speech-api"></a>Konuşma API 'sine kimlik doğrulama

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Konuşma, kullanarak kimlik doğrulamasını destekler:

- Abonelik anahtarı.
- Yetkilendirme belirteci.

## <a name="use-a-subscription-key"></a>Abonelik anahtarı kullan

Konuşma hizmetini kullanmak için önce bilişsel hizmetler 'in (daha önce Project Oxford) bir parçası olan konuşma API 'sine abone olmanız gerekir. Bilişsel [Hizmetler aboneliği](https://azure.microsoft.com/try/cognitive-services/) sayfasından ücretsiz deneme aboneliği anahtarlarına sahip olabilirsiniz. Konuşma API 'sini seçtikten sonra anahtarı almak için **API anahtarı al** ' ı seçin. Birincil ve ikincil anahtar döndürür. Her iki anahtar de aynı kotaya bağlıdır, bu nedenle her iki anahtarı da kullanabilirsiniz.

Uzun süreli kullanım veya daha yüksek bir kota için [Azure hesabı](https://azure.microsoft.com/free/)için kaydolun.

Konuşma REST API kullanmak için, istek üstbilgisindeki `Ocp-Apim-Subscription-Key` alana abonelik anahtarını geçirmeniz gerekir.

Name| Biçimi| Açıklama
----|-------|------------
Ocp-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Aşağıda bir istek üst bilgisi örneği verilmiştir:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> Uygulamanızda [istemci kitaplıkları](../GetStarted/GetStartedClientLibraries.md) kullanıyorsanız, aşağıdaki bölümde açıklandığı gibi, abonelik anahtarınızla yetkilendirme belirtecini almak istediğinizi doğrulayın. İstemci kitaplıkları bir yetkilendirme belirteci almak için abonelik anahtarını kullanır ve ardından kimlik doğrulaması için belirteci kullanır.

## <a name="use-an-authorization-token"></a>Yetkilendirme belirteci kullanma

Alternatif olarak, kimlik doğrulama/yetkilendirme açısından kimlik doğrulaması için bir yetkilendirme belirteci kullanabilirsiniz. Bu belirteci almak için ilk olarak, [önceki bölümde](#use-a-subscription-key)açıklandığı gıbı konuşma API 'sinden bir abonelik anahtarı edinmeniz gerekir.

### <a name="get-an-authorization-token"></a>Yetkilendirme belirteci al

Geçerli bir abonelik anahtarınız olduktan sonra bilişsel hizmetler 'in belirteç hizmetine bir POST isteği gönderin. Yanıtta, yetkilendirme belirtecini JSON Web Token (JWT) olarak alırsınız.

> [!NOTE]
> Belirtecin süresi 10 dakikadır. Belirteci yenilemek için aşağıdaki bölüme bakın.

Belirteç hizmeti URI 'SI şurada bulunur:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Aşağıdaki kod örneği, bir erişim belirtecinin nasıl alınacağını gösterir. Kendi `YOUR_SUBSCRIPTION_KEY` abonelik anahtarınızla değiştirin:

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

Örnek, Bash ile Linux 'ta kıvrımlı kullanır. Platformda yoksa, kıvrımlı yüklemeniz gerekebilir. Örnek ayrıca Windows, git Bash, ZSH ve diğer kabukların Cygwin üzerinde de kullanılabilir.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

Aşağıda örnek bir POST isteği verilmiştir:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Belirteç hizmetinden bir yetkilendirme belirteci alamazsanız, abonelik anahtarınızın hala geçerli olup olmadığını denetleyin. Ücretsiz deneme anahtarı kullanıyorsanız, bilişsel [Hizmetler aboneliği](https://azure.microsoft.com/try/cognitive-services/) sayfasına gidin, ücretsiz deneme anahtarını uygulamak için kullandığınız hesabı kullanarak oturum açmak Için "oturum aç" a tıklayın ve abonelik anahtarının süresinin dolup dolmadığını veya kotayı aşıp aşmadığını denetleyin.

### <a name="use-an-authorization-token-in-a-request"></a>İstekte bir yetkilendirme belirteci kullanma

Konuşma API 'sini her çağırdığınızda, `Authorization` üstbilgideki yetkilendirme belirtecini geçirmeniz gerekir. `Authorization` Üst bilgi bir JWT erişim belirteci içermelidir.

Aşağıdaki örnek, konuşma REST API çağırdığınızda bir yetkilendirme belirtecinin nasıl kullanılacağını gösterir.

> [!NOTE]
> Değiştirin `YOUR_AUDIO_FILE` ile önceden kaydedilmiş ses dosyanızın yolu. Önceki `YOUR_ACCESS_TOKEN` adımda aldığınız yetkilendirme belirteciyle değiştirin ve [yetkilendirme belirteci alın](#get-an-authorization-token).

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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
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

---

### <a name="renew-an-authorization-token"></a>Yetkilendirme belirtecini yenileme

Yetkilendirme belirtecinin süresi belirli bir süre (Şu anda 10 dakika) sonra dolar. Yetkilendirme belirtecini süresi dolmadan önce yenilemeniz gerekir.

Aşağıdaki kod, yetkilendirme belirtecinin nasıl yenileneceği C# içindeki örnek bir uygulamasıdır:

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;
        private Timer accessTokenRenewer;

        //Access token expires every 10 minutes. Renew it every 9 minutes.
        private const int RefreshTokenDuration = 9;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

            // renew the token on set duration.
            accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                           this,
                                           TimeSpan.FromMinutes(RefreshTokenDuration),
                                           TimeSpan.FromMilliseconds(-1));
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private void RenewAccessToken()
        {
            this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
            Console.WriteLine("Renewed token.");
        }

        private void OnTokenExpiredCallback(object stateInfo)
        {
            try
            {
                RenewAccessToken();
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
            }
            finally
            {
                try
                {
                    accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
                }
                catch (Exception ex)
                {
                    Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
                }
            }
        }

        private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```
