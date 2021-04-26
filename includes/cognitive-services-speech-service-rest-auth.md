---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 01/08/2021
ms.author: erhopf
ms.openlocfilehash: 22127f81d871fe333750020196540db17e7544f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98033472"
---
## <a name="authentication"></a>Kimlik Doğrulaması

Her istek bir yetkilendirme üst bilgisi gerektirir. Bu tabloda, her bir hizmet için hangi üstbilgilerin desteklendiği gösterilmektedir:

| Desteklenen yetkilendirme üstbilgileri | Konuşmayı metne dönüştürme | Metin okuma |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Yes | Yes |
| Yetkilendirme: taşıyıcı | Yes | Yes |

`Ocp-Apim-Subscription-Key`Üstbilgiyi kullanırken yalnızca abonelik anahtarınızı sağlamanız gerekir. Örnek:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

`Authorization: Bearer`Üst bilgisini kullanırken uç noktaya istek yapmanız gerekir `issueToken` . Bu istekte, abonelik anahtarınızı 10 dakikalık geçerli bir erişim belirteci için değiş tokuş edersiniz. Sonraki birkaç bölümde, bir belirteci nasıl alabileceğinizi ve bir belirteç kullanacağınızı öğreneceksiniz.

### <a name="how-to-get-an-access-token"></a>Erişim belirteci alma

Erişim belirteci almak için, `issueToken` ve abonelik anahtarınızı kullanarak uç noktaya bir istek yapmanız gerekir `Ocp-Apim-Subscription-Key` .

`issueToken`Uç nokta şu biçimdedir:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

`<REGION_IDENTIFIER>`Bu tablodaki aboneliğinizin bölgesiyle eşleşen tanımlayıcıyla değiştirin:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Erişim belirteci isteğinizi oluşturmak için bu örnekleri kullanın.

#### <a name="http-sample"></a>HTTP örneği

Bu örnek, belirteç almak için basit bir HTTP isteğidir. `YOUR_SUBSCRIPTION_KEY`Konuşma hizmeti abonelik anahtarınızla değiştirin. Aboneliğiniz Batı ABD bölgede değilse, `Host` üstbilgiyi bölgenizin ana bilgisayar adıyla değiştirin.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Yanıtın gövdesi JSON Web Token (JWT) biçimindeki erişim belirtecini içerir.

#### <a name="powershell-sample"></a>PowerShell örneği

Bu örnek, bir erişim belirteci almak için basit bir PowerShell betiğtir. `YOUR_SUBSCRIPTION_KEY`Konuşma hizmeti abonelik anahtarınızla değiştirin. Aboneliğinizle eşleşen bölge için doğru uç noktayı kullandığınızdan emin olun. Bu örnek şu anda Batı ABD olarak ayarlanmıştır.

```powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>Kıvrımlı örnek

Kıvrımlı, Linux 'ta (ve Linux için Windows alt sisteminde) kullanılabilen bir komut satırı aracıdır. Bu kıvrımlı komut, erişim belirtecinin nasıl alınacağını gösterir. `YOUR_SUBSCRIPTION_KEY`Konuşma hizmeti abonelik anahtarınızla değiştirin. Aboneliğinizle eşleşen bölge için doğru uç noktayı kullandığınızdan emin olun. Bu örnek şu anda Batı ABD olarak ayarlanmıştır.

```console
curl -v -X POST \
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C# örneği

Bu C# sınıfı, erişim belirtecinin nasıl alınacağını gösterir. Sınıfı örneklediğinizde konuşma hizmeti abonelik anahtarınızı geçirin. Aboneliğiniz Batı ABD bölgede değilse, değerini `FetchTokenUri` aboneliğinizin bölgesiyle eşleşecek şekilde değiştirin.

```csharp
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
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

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Python örneği

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Erişim belirteci kullanma

Erişim belirtecinin, üst bilgi olarak hizmete gönderilmesi gerekir `Authorization: Bearer <TOKEN>` . Her erişim belirteci 10 dakika için geçerlidir. Her zaman yeni bir belirteç alabilirsiniz, ancak ağ trafiğini ve gecikmesini en aza indirmek için dokuz dakika boyunca aynı belirteci kullanmanızı öneririz.

İşte kısa ses için konuşmayı metne dönüştürme REST API örnek bir HTTP isteği:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
