---
title: Bing Yazım Denetimi API’sine istek gönderme
titleSuffix: Azure Cognitive Services
description: Bing Yazım Denetimi modları, ayarları ve API ile ilgili diğer bilgiler hakkında bilgi edinin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 893317b8f46415b1df540d67ebf28b65c5ba6d32
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883444"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Bing Yazım Denetimi API’sine istek gönderme

Bir metin dizesinde yazım ve dilbilgisi denetimi gerçekleştirmek için şu uç noktaya GET isteği göndermeniz gerekir:  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
İstek, HTTPS protokolünü kullanmalıdır.

Tüm isteklerin bir sunucudan gönderilmesini öneririz. Anahtarı bir istemci uygulamanın parçası olarak dağıtmak, kötü amaçlı bir üçüncü tarafa anahtara erişmek için daha fazla fırsat sunar. Sunucu Ayrıca API 'nin gelecekteki sürümleri için tek bir yükseltme noktası sağlar.

İstek denetlenecek metin dizesini içeren [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text) sorgu parametresini belirtmelidir. Tercihe bağlı olmakla birlikte, istek, sonuçların gelmesini istediğiniz pazarı tanımlayan [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt) sorgu parametresini de belirtmelidir. `mode` gibi isteğe bağlı sorgu parametrelerinin bir listesi için bkz. [Sorgu Parametreleri](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters). Tüm sorgu parametre değerleri URL olarak kodlanmış olmalıdır.  
  
İstek [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey) üstbilgisini belirtmelidir. İsteğe bağlı olmakla birlikte, aşağıdaki üst bilgileri de belirtmeniz önerilir. Bu üst bilgiler Bing Yazım Denetimi API'si daha doğru sonuçlar döndürmesini yardımcı olur:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

Tüm istek ve yanıt üstbilgilerinin bir listesi için bkz. [Üst Bilgiler](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers).

JavaScript kullanarak Bing Yazım Denetimi API'si çağrılırken, tarayıcınızın yerleşik güvenlik özellikleri bu üstbilgilerin değerlerine erişmenizi engelleyebilir.

Bu sorunu çözmek için, bir CORS proxy üzerinden Bing Yazım Denetimi API'si isteği yapabilirsiniz. Bu tür bir ara sunucudan yanıt, yanıt `Access-Control-Expose-Headers` üstbilgilerini beyaz listeleyen ve JavaScript için kullanılabilir hale getiren bir üstbilgiye sahiptir.

[Öğretici uygulamasının](../tutorials/spellcheck.md) isteğe bağlı istemci üst bilgilerine erişmesine izin vermek IÇIN bir CORS proxy yüklemek kolaydır. İlk olarak, henüz yüklemediyseniz [Node.js'yi yükleyin](https://nodejs.org/en/download/). Sonra bir komut isteminde aşağıdaki komutu girin.

    npm install -g cors-proxy-server

Sonra, HTML dosyasındaki Bing Yazım Denetimi API'si uç noktasını şu şekilde değiştirin:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Son olarak, aşağıdaki komutla CORS ara sunucusunu başlatın:

    cors-proxy-server

Öğretici uygulamasını kullanırken komut penceresini açık bırakın; pencere kapatılırsa ara sunucu durdurulur. Arama sonuçlarının altındaki Genişletilebilir http üstbilgileri bölümünde artık `X-MSEdge-ClientID` üstbilgiyi görebilir (diğerleri arasından) ve her istek için aynı olduğunu doğrulayabilirsiniz.

## <a name="example-api-request"></a>Örnek API isteği

Aşağıda önerilen tüm sorgu parametrelerini ve üstbilgilerini içeren bir istek gösterilmektedir. Bing API'lerinden birini ilk kez çağırıyorsanız istemci kimliği üst bilgisini eklemeyin. İstemci kimliğini yalnızca önceden bir Bing API'sini çağırdıysanız ve Bing, kullanıcı ve cihaz birleşimi için bir istemci kimliği döndürdüyse dahil edin. 
  
> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Aşağıda, bir önceki isteğin yanıtı gösterilmektedir. Örnek ayrıca Bing’e özgü yanıt üst bilgilerini de göstermektedir.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Bing Yazım Denetimi API'si nedir?](../overview.md)
- [Bing Yazım Denetimi API’si v7 Başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
