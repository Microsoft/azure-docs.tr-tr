---
title: Görüntü öngörülerini edinin-Bing Resim Arama API'si
titleSuffix: Azure Cognitive Services
description: Bir görüntü hakkında daha fazla bilgi almak için Bing Resim Arama API'si nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: f84c6329c2a4dd0a9ad9e81f3700c9e31de95a2a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883424"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Bing Resim Arama API'si görüntü öngörülerini alın

> [!IMPORTANT]
> Görüntü öngörülerini almak için/images/details uç noktasını kullanmak yerine, daha kapsamlı öngörüler sağladığından [görsel arama](../bing-visual-search/overview.md) kullanmanız gerekir.


Her görüntü, görüntü hakkında bilgi almak için kullanabileceğiniz bir Öngörüler belirteci içerir. Örneğin, bir ilişkili görüntü koleksiyonu, görüntüyü içeren Web sayfaları veya görüntüde gösterilen ürünü satın alabileceğiniz bir tüccarların listesi alabilirsiniz.  

Bir görüntüyle ilgili Öngörüler elde etmek için, yanıtta görüntünün [ımageınsi, Stoken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) belirtecini yakalayın.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Sonra, görüntü ayrıntıları uç noktasını çağırın ve ınsi, [sToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) sorgu parametresini içindeki `imageInsightsToken`belirtece ayarlayın.  

Almak istediğiniz öngörüleri belirtmek için `modules` sorgu parametresini ayarlayın. Tüm öngörüleri almak için, `modules` olarak `All`ayarlayın. Yalnızca başlık ve koleksiyon öngörülerini almak için olarak `modules` `Caption%2CCollection`ayarlayın. Olası öngörülerin tüm listesi için bkz. [modüller](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested). Tüm Öngörüler tüm görüntüler için kullanılabilir değildir. Yanıt, varsa istediğiniz tüm öngörüleri içerir.

Aşağıdaki örnek, önceki görüntü için tüm kullanılabilir öngörüleri ister.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Bilinen bir görüntünün öngörülerini alma

Öngörülerini almak istediğiniz bir görüntünün URL 'SI varsa, görüntüyü belirtmek için [ınsiıstoken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) parametresi yerine [ımgurl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) sorgu parametresini kullanın. Ya da görüntü dosyanız varsa, bir POST isteğinin gövdesinde görüntünün ikilisini gönderebilirsiniz. Bir post isteği kullanıyorsanız, `Content-Type` üst bilgi olarak `multipart/data-form`ayarlanmalıdır. Her iki seçenek de resmin boyutu 1 MB 'ı aşamaz.  

Görüntüye bir URL varsa aşağıdaki örnek, bir görüntü öngörülerinin nasıl isteneceğini gösterir.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Tüm görüntü öngörülerini alma  

Bir görüntünün tüm öngörülerini istemek için, [modüller](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) sorgu parametresini olarak `All`ayarlayın. İlgili aramaları almak için isteğin Kullanıcı sorgu dizesini içermesi gerekir. Bu örnekte, görüntüyü belirtmek için [ınsi, Stoken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) kullanılması gösterilmektedir.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Üst düzey nesne, bir [resim](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) nesnesi yerine bir [ımageınsightsresponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) nesnesidir.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Görüntüdeki varlıkları tanıma  

Varlık tanıma özelliği bir görüntüdeki varlıkları tanımlar ve şu anda yalnızca kişiler. Bir görüntüdeki varlıkları tanımlamak için, [modüller](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) sorgu parametresini olarak `RecognizedEntities`ayarlayın.  

> [!NOTE]
> Bu modülü başka bir modülle belirtmeyebilirsiniz. Bu modülü başka modüllerle belirtirseniz, yanıt tanınan varlıkları içermez.  

Aşağıda, [Imgurl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) parametresini kullanarak görüntünün nasıl ayarlanacağı gösterilmektedir. Sorgu parametrelerini kodlayan URL 'yi unutmayın.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Aşağıda, bir önceki isteğin yanıtı gösterilmektedir. Görüntüde iki kişi bulunduğundan, yanıt her kişi için bir bölge tanımlar. Bu durumda, kişiler Ünlütyangösterimleri ve CelebRecognitionAnnotations gruplarında tanınmıştı. Bing, her bir gruptaki kişileri, orijinal görüntüdeki kişiyle eşleştikleri olasılığınızı temel alarak listeler. Liste azalan güvenle sırada. CelebRecognitionAnnotations grubu, eşleşmenin doğru olduğu en üst düzey güveni sağlar.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

`region` Alan, Bing 'in varlığı tanıdığı görüntünün alanını tanımlar. İnsanlar için bölge, kişinin yüzünü temsil eder.  

Dikdörtgenin değerleri, orijinal görüntünün genişliği ve yüksekliğine göredir ve 0,0 ile 1,0 arasında değişir. Örneğin, görüntü 300x200 ise ve bölgenin üst, sol köşe ise (10, 20) ve alt köşede sağ köşe nokta (290, 150) ise, normalleştirilmiş dikdörtgen şu şekilde olur:  

-   Tarafta 10/300 = 0,03333...  
-   Sayfanın Üstü:  20/200 = 0,1  
-   Right 290/300 = 0,9667...  
-   Aşağıya 150/200 = 0,75  

Bing 'in sonraki Öngörüler çağrılarında döndürdüğü bölgeyi kullanabilirsiniz. Örneğin, tanınan varlığın görsel açıdan benzer görüntülerini almak için. Daha fazla bilgi için bkz. görsel açıdan benzer ve varlık tanıma modülleriyle kullanılacak görüntüleri kırpma. Aşağıda, bölge alanları ve görüntüleri kırpmak için kullanacağınız sorgu parametreleri arasındaki eşleme gösterilmektedir.  

-   Sol ve [Cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal) eşlemeleri  
-   En üst- [kedi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat) haritaları  
-   [Oto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) için sağ haritalar  
-   Alt [CAB](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab) 'ye eşlemeler  

## <a name="finding-visually-similar-images"></a>Görsel açıdan benzer görüntüleri bulma  

Özgün görüntüye görsel olarak benzer görüntüleri bulmak için, [modüller](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) sorgu parametresini SimilarImages olarak ayarlayın.  

Aşağıdaki istek görsel açıdan benzer görüntülerin nasıl alınacağını gösterir. İstek, orijinal görüntüyü belirlemek için [ınsi, Stoken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) sorgu parametresini kullanır. İlgiyi artırmak için kullanıcının sorgu dizesini dahil etmelisiniz.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


Aşağıda, bir önceki isteğin yanıtı gösterilmektedir.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Görsel açıdan benzer ve varlık tanıma modülleriyle kullanılacak görüntüleri kırpma  

Resimlerin görsel olarak benzer olduğunu belirlemek veya varlık tanımayı gerçekleştirmek için kullandığı görüntünün bölgesini belirtmek için, [Cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal), [Cat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat), [CAB](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)ve [otomobil](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) sorgu parametrelerini kullanın. Varsayılan olarak, Bing görüntünün tamamını kullanır.  

Parametreler, Bing 'in karşılaştırma için kullandığı bölgenin üst, sol köşe ve alt köşesini belirler. Değerleri orijinal görüntünün genişlik ve yüksekliğinin kesirleri olarak belirtin. Kesirli değerler sol üst köşedeki (0,0, 0,0) ve sağ alt köşedeki (1,0, 1,0) ile başlar. Örneğin, üst köşedeki sol köşe, sol taraftaki bir çeyreğin en başından ve bir çeyrekten aşağı doğru bir şekilde başlar, 0,25 ve `cal` `cat` 0,25 olarak ayarlanır.  

Aşağıdaki çağrı dizisi, kırpma bölgesini belirtmenin etkisini gösterir. İlk çağrıda kırpma ve Bing, görüntünün ortasında yan yana duran iki kişiyi tanır.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Yanıtta iki tanınan varlık gösterilmektedir.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

İkinci çağrı, görüntüyü dikey olarak aşağı kırpar ve Bing görüntünün sağ tarafında tanınan tek bir kişiyi kabul.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Yanıt, tanınan bir varlığı gösterir.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Görsel açıdan benzer ürünler bulma  

Orijinal görüntüde bulunan ürünlere görsel olarak benzeyen ürünleri içeren görüntüleri bulmak için, [modüller](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) sorgu parametresini SimilarProducts olarak ayarlayın.  

Aşağıdaki istek, görsel açıdan benzer ürünlerin görüntülerinin nasıl alınacağını gösterir. İstek, önceki bir istekte döndürülen özgün görüntüyü belirlemek için [ınsiıstoken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) sorgu parametresini kullanır. İlgiyi artırmak için kullanıcının sorgu dizesini dahil etmelisiniz.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Aşağıda, bir önceki isteğin yanıtı gösterilmektedir. Yanıt, benzer bir ürünün görüntüsünü içerir ve ürünün kaç tüccarların olduğunu, ürün derecelendirmeleri olup olmadığını ve bulunan en düşük fiyatı ( `aggregateOffer` alana bakın) gösterir.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

Ürünü çevrimiçi olarak sunan tüccarların listesini almak için ( [offerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) alanına bakın), API 'yi yeniden çağırın ve shoppingsources olarak ayarlayın `modules` . Ardından, `insightsToken` sorgu parametresini Ürün Özeti görüntüsünde bulunan belirtece ayarlayın.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Önceki isteğin yanıtı aşağıda verilmiştir.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```
