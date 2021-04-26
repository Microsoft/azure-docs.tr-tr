---
title: Bing Yazım Denetimi API’sini kullanma
titleSuffix: Azure Cognitive Services
description: Bing Yazım Denetimi modları, ayarları ve API ile ilgili diğer bilgiler hakkında bilgi edinin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 2031c31c6ea083452bbdbb95be74adf29be1f858
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349325"
---
# <a name="using-the-bing-spell-check-api"></a>Bing Yazım Denetimi API’sini kullanma

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bağlamsal dilbilgisi ve yazım denetimi gerçekleştirmek için Bing Yazım Denetimi API'si kullanma hakkında bilgi edinmek için bu makaleyi kullanın. Çoğu yazım denetimcisi sözlük tabanlı kural kümelerine güvenirken, Bing yazım denetleyicisi doğru ve bağlamsal düzeltmeler sağlamak için makine öğrenimi ve istatistiksel makine çevirisi kullanır. 

## <a name="spell-check-modes"></a>Yazım denetimi modları

API, `Proof` ve `Spell` olmak üzere iki denetleme modunu destekler.  Örnekleri [burada](https://azure.microsoft.com/services/cognitive-services/spell-check/) deneyebilirsiniz.

### <a name="proof---for-documents"></a>Prova-belgeler için 

`Proof`, varsayılan moddur. `Proof` yazım denetimi modu, belge oluşturmaya yardımcı olmak için büyük/küçük harf kullanımını düzeltme ve temel noktalama işaretleri ekleme gibi çeşitli özellikler sunarak en kapsamlı denetimleri sağlar. Ancak bu mod yalnızca en-US (İngilizce-Amerika Birleşik Devletleri), es-ES (İspanyolca) ve pt-BR (Portekizce) pazarlarında kullanılabilir. (Not: İspanyolca ve Portekizce için yalnızca beta sürümünde sunulur). Diğer tüm pazarlar için mode sorgu parametresini Spell olarak ayarlayın. 

> [!NOTE]
> Sorgu metninin uzunluğu 4096 ' i aşarsa, 4096 karaktere kısaltılacak ve sonra işlenir. 

### <a name="spell----for-web-searchesqueries"></a>Web aramaları/sorguları için yazım denetimi

`Spell`, daha iyi arama sonuçları döndürmek için daha agresif şekilde çalışır. `Spell` modu, çoğu yazım hatasını bulur ancak `Proof` modunda yakalanan, gibi bazı dil bilgisi hatalarını (örneğin, büyük/küçük harf kullanımı ve yinelenen sözcükler ile ilgili hatalar) saptayamaz.

> [!NOTE]
> * Desteklenen en fazla sorgu uzunluğu aşağıda verilmiştir. Sorgu uzunluk üst sınırını aşarsa sorgu ve sonuçları değiştirilmez.
>    * Şu dil kodları için 130 karakter: en, de, es, fr, pl, PT, ZF, ru, nl, NB, tr-tr, it, zh, Ko. 
>    * Tüm diğerleri için 65 karakter.
> * Yazım modu sorgularda köşeli ayraç karakterlerini ( `[` ve `]` ) desteklemez ve tutarsız sonuçlara neden olabilir. Yazım modunu kullanırken Sorgularınızdaki onları kaldırmanızı öneririz.

## <a name="market-setting"></a>Pazar ayarı

İsteğiniz içinde sorgu parametresiyle bir [Pazar kodu](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) belirtilmelidir `mkt` . Bu durumda, API, isteğin IP adresine göre varsayılan bir pazar kullanacaktır.


## <a name="http-post-and-get-support"></a>HTTP POST ve GET desteği

Hem HTTP POST hem de HTTP GET, API tarafından desteklenir. Bunlardan hangisini kullanacağınız, denetlemeyi planladığınız metnin uzunluğuna göre değişir. Dizelerin tümü 1.500 karakterden kısaysa GET kullanırsınız. Ancak, 10.000 karaktere kadar uzunluktaki dizeleri desteklemek istiyorsanız POST kullanırsınız. Metin dizesi tüm UTF-8 karakterlerini içerebilir.

Sıradaki örnekte, bir metin dizesinin yazımının ve dil bilgisinin denetlenmesine yönelik bir POST isteği gösterilmektedir. [Mode](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) sorgu parametresi bütünlük açısından örneğe dahil edilmiştir. (`mode` varsayılan olarak Denetleme olduğundan, bu parametre dışarıda bırakılabilirdi.) [Text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) sorgu parametresinde, denetlenecek dize bulunur.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

HTTP GET kullanmanız halinde, URL'nin sorgu dizesine `text` sorgu parametresini dahil etmeniz gerekir
  
Aşağıda, bir önceki isteğin yanıtı gösterilmektedir. Yanıt, [SpellCheck](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck) nesnesi içerir. 
  
```json
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
  
[flaggedTokens](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) alanında, API'nin [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) dizesinde bulduğu yazım ve dil bilgisi hataları listelenir. `token` alanı, değiştirilecek sözcüğü içerir. `text` dizesindeki belirteci bulmak için `offset` alanında sıfır temelli uzaklığı kullanırsınız. Ardından, söz konusu konumdaki sözcüğü `suggestion` alanındaki sözcükle değiştirirsiniz. 

`type` alanı RepeatedToken olsa da belirteci `suggestion` ile değiştirirsiniz, ancak muhtemelen sondaki boşluğu kaldırmanız gerekir.

## <a name="throttling-requests"></a>İstekleri azaltma

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Bing Yazım Denetimi API’si nedir?](../overview.md)
- [Bing Yazım Denetimi API’si v7 Başvurusu](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)