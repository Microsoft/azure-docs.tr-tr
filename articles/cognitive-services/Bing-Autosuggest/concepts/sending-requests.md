---
title: Bing Otomatik Öneri API'si istek gönderme
titleSuffix: Azure Cognitive Services
description: Bing Otomatik Öneri API'si, arama kutusundaki kısmi sorgu dizesine göre önerilen sorguların bir listesini döndürür. İstek gönderme hakkında daha fazla bilgi edinin.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: dd845c0fb877afa76b84eb5c2d86392f763eccf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353400"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>İstekleri Bing Otomatik Öneri API'si gönderiliyor.

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Uygulamanız Bing Arama API'leri herhangi birine sorgu gönderiyorsa, kullanıcılarınızın arama deneyimini geliştirmek için Bing Otomatik Öneri API'si kullanabilirsiniz. Bing Otomatik Öneri API'si, arama kutusundaki kısmi sorgu dizesine göre önerilen sorguların bir listesini döndürür. Uygulamanızdaki bir arama kutusuna karakterler girildiğinde, önerileri bir açılan listede görüntüleyebilirsiniz. Bu API 'ye istek gönderme hakkında daha fazla bilgi edinmek için bu makaleyi kullanın. 

## <a name="bing-autosuggest-api-endpoint"></a>Bing Otomatik Öneri API'si uç noktası

**Bing otomatik öneri API'si** , kısmi bir arama teriminden önerilen sorguların listesini döndüren bir uç nokta içerir.

Bing API 'yi kullanarak önerilen sorguları almak için `GET` aşağıdaki uç noktaya bir istek gönderin. Daha fazla belirtim tanımlamak için üstbilgiler ve URL parametreleri kullanın.

**Uç nokta:** Arama önerilerini kullanıcının tarafından tanımlanan girişle ilgili JSON sonuçları olarak döndürür `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Üstbilgiler, parametreler, Pazar kodları, yanıt nesneleri, hatalar vb. hakkında ayrıntılar için [Bing otomatik öneri API'si v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) başvurusuna bakın.

**Bing** API 'leri türlerine göre sonuçlar döndüren arama eylemlerini destekler. Tüm arama uç noktaları sonuçları JSON yanıt nesneleri olarak döndürür.
Tüm uç noktalar, Boylam, enlem ve arama yarıçapı tarafından belirli bir dili ve/veya konumu döndüren sorguları destekler.

Her uç nokta tarafından desteklenen parametreler hakkında tüm bilgiler için, her tür için başvuru sayfalarına bakın.
Otomatik öneri API 'sini kullanan temel isteklerin örnekleri için bkz. [otomatik öneri hızlı başlangıç](/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Bing Otomatik Öneri API'si istekleri

> [!NOTE]
> * Bing Otomatik Öneri API'si isteklerinin HTTPS protokolünü kullanması gerekir.

Tüm isteklerin bir sunucudan gönderilmesini öneririz. Anahtarı bir istemci uygulamasının bir parçası olarak dağıtmak, daha fazla fırsat kötü amaçlı üçüncü taraf erişimi sağlar. Ayrıca, bir sunucudan yapılan çağrılar, gelecekteki güncelleştirmeler için tek bir yükseltme noktası sağlar.

İstek kullanıcının kısmi arama terimini içeren [q](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) sorgu parametresini belirtmelidir. Tercihe bağlı olmakla birlikte, istek, sonuçların gelmesini istediğiniz pazarı tanımlayan [mkt](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) sorgu parametresini de belirtmelidir. İsteğe bağlı sorgu parametrelerinin bir listesi için bkz. [Sorgu Parametreleri](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Tüm sorgu parametre değerleri URL olarak kodlanmış olmalıdır.

İstek [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) üstbilgisini belirtmelidir. İsteğe bağlı olmakla birlikte şu üstbilgileri de belirtmeniz önerilir:

- [Kullanıcı Aracısı](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-arama konumu](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

İstemci IP'si ve konum üstbilgileri konuma duyarlı içerik döndürmek için önemlidir.

Tüm istek ve yanıt üstbilgilerinin bir listesi için bkz. [Üstbilgiler](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> JavaScript 'ten Bing Otomatik Öneri API'si çağırdığınızda, tarayıcınızın yerleşik güvenlik özellikleri bu üstbilgilerin değerlerine erişmenizi engelleyebilir.

Bu sorunu çözmek için, bir CORS proxy üzerinden Bing Otomatik Öneri API'si isteği yapabilirsiniz. Bu tür bir ara sunucudan alınan yanıtın, `Access-Control-Expose-Headers` yanıt üstbilgilerini filtreleyecek ve JavaScript için kullanılabilir hale getiren bir üstbilgisi vardır.

[Öğretici uygulamamız](../tutorials/autosuggest.md) , isteğe bağlı istemci üst bilgilerine erişmesine izin vermek IÇIN bir CORS proxy yüklemek kolaydır. İlk olarak, henüz yüklemediyseniz [Node.js'yi yükleyin](https://nodejs.org/en/download/). Sonra bir komut isteminde aşağıdaki komutu girin.

```console
npm install -g cors-proxy-server
```

Sonra, HTML dosyasındaki Bing Otomatik Öneri API'si uç noktasını şu şekilde değiştirin:

```http
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
```

Son olarak, aşağıdaki komutla CORS ara sunucusunu başlatın:

```console
cors-proxy-server
```

Öğretici uygulamasını kullanırken komut penceresini açık bırakın; pencere kapatılırsa ara sunucu durdurulur. Arama sonuçlarının altındaki genişletilebilir HTTP Üst Bilgileri bölümünde artık `X-MSEdge-ClientID` üst bilgisini (diğerleriyle birlikte) görebilir ve bunun her istekte aynı olduğunu doğrulayabilirsiniz.

İstekler tüm önerilen sorgu parametrelerini ve üst bilgileri içermelidir. 

Aşağıdaki örnekte, *sail* için önerilen sorgu dizelerini döndüren bir istek gösterilmektedir.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Bing API'lerinden birini ilk kez çağırıyorsanız istemci kimliği üst bilgisini eklemeyin. İstemci kimliği üst bilgisini yalnızca önceden bir Bing API'sini çağırdıysanız ve Bing, kullanıcı ve cihaz birleşimi için bir istemci kimliği döndürdüyse dahil edin.

Aşağıdaki Web önerisi grubu, yukarıdaki isteğin bir yanıtı. Grup, her öneri `displayText` , ve alanı dahil olmak üzere arama sorgu önerilerinin bir listesini içerir `query` `url` .

`displayText` alanı, arama kutunuzun açılır listesini doldurmak için kullandığınız önerilen sorguyu içerir. Yanıtın içerdiği tüm önerileri, verilen sırada görüntülemeniz gerekir.  

Kullanıcı aşağı açılan listeden bir sorgu seçerse, [Bing Arama API'leri](../../bing-web-search/bing-api-comparison.md?bc=%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fen-us%2fazure%2fcognitive-services%2fbing-autosuggest%2ftoc.json) birini çağırmak ve sonuçları kendiniz göstermek veya döndürülen alanı kullanarak kullanıcıyı Bing sonuçları sayfasına göndermek için kullanabilirsiniz `url` .

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Bing Otomatik Öneri nedir?](../get-suggested-search-terms.md)
- [Bing Otomatik Öneri API’si v7 başvurusu](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Bing Otomatik Öneri API'si önerilen arama terimleri alınıyor](get-suggestions.md)