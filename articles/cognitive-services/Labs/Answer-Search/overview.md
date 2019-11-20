---
title: Proje Yanıt Arama nedir?
titlesuffix: Azure Cognitive Services
description: Proje Yanıt Arama'ya giriş.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 676b7f63b82a0e7c06264cb6dcbead8c1344a076
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707124"
---
# <a name="what-is-project-answer-search"></a>Proje Yanıt Arama nedir?
Proje Yanıt Arama API'si soru sorgularına yanıt bulmak için Bing v7 uç noktasını kullanır. "What is the circumference of the earth?" (Dünyanın çevresinin uzunluğu nedir?) gibi bir soruya olgu içeren bir yanıt döndürülür.  Kişi, yer veya nesne sorguları, sorguda tanımlanan varlıkla ilgili bilgileri döndürür. Bu senaryolar sohbet botları, mesajlaşma uygulamaları, okuyucular gibi uygulamalarda yararlı olabilir.  

Sorgular, sorgu senaryosuna göre yanıt döndürür: Web sayfaları her zaman döndürülür, [olgular](fact-queries.md) ve/veya [varlıklar](entity-queries.md) ise gerekli olmaları durumunda döndürülür.

## <a name="endpoint"></a>Uç Nokta
Bir soruya yanıt veya bir kişi, yer ya da nesne hakkında bilgi almak için Yanıt Arama API'si uç noktasına istek gönderebilirsiniz. Farklı belirtimler için üst bilgileri ve URL parametrelerini kullanın.  Geçerli bir belirteçle birlikte *Ocp-Apim-Subscription-Key* üst bilgisini ekleyin.  market parametresi gereklidir. Şu an için yalnızca `en-us` pazarı desteklenmektedir.

Aşağıdaki sorgu, sorunun yanıtlarını alır: "Dünya çevresi nedir?"

AL:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

```

Aranacak nesneyi belirtmek için `q=` URL parametresinin kullanılması gerekir.

## <a name="response-object"></a>Yanıt nesnesi

Yanıt HTTP üst bilgilerini, web sayfalarını, olguları ve/veya varlıkları içerir.

```
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "https://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "https://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "https://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "https://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "https://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

```

## <a name="terms-of-use"></a>Kullanım koşulları
Projee Yanıt Arama ve Proje Video Eğilimleri, [Bing Arama Kullanım ve Görüntüleme Gereksinimlerine](use-display-requirements.md) tabidir.

URL Önizleme API'sinden alınan veriler sizin tarafınızdan veya sizin adınıza hareket eden üçüncü şahıslar tarafından Microsoft harici bir hizmeti veya özelliği test etme, geliştirme, eğitme, dağıtma veya kullanıma sunma amacıyla kullanılamaz, saklanamaz, depolanamaz, önbelleğe alınamaz, paylaşılamaz veya dağıtılamaz. 

## <a name="throttling-requests"></a>İstekleri azaltma

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Veri atfı  

Proje Yanıt Arama yanıtları, üçüncü taraflara ait bilgiler içerir. Kullanıcı deneyiminizde kullanılan Creative Commons lisansına uygun hareket etme gibi kullanımınızın gerektirdiği durumlardan sorumlu olursunuz.  
  
Yanıt ya da sonuç `contractualRules`, `attributions` veya `provider` alanlarını içeriyorsa veriler için atıfta bulunmanız gerekir. Yanıtta bu alanlardan biri yoksa atıfta bulunmanıza gerek yoktur. Yanıtta `contractualRules` alanı ile `attributions` ve/veya `provider` alanı varsa verilere atıfta bulunmak için sözleşme kurallarını kullanmanız gerekir.  
  
Aşağıdaki örnek MediaAttribution sözleşme kuralına ve `provider` alanına sahip olan bir Image nesnesine sahip olan varlığı göstermektedir. MediaAttribution kuralı görüntüyü kuralın hedefi olarak tanımlamaktadır. Bu nedenle görüntünün `provider` alanını yoksayabilir ve atıfta bulunmak için MediaAttribution kuralını kullanabilirsiniz.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
Sözleşme kuralında `targetPropertyName` alanı varsa kural yalnızca hedeflenen alan için geçerli olur. Aksi takdirde, kural `contractualRules` alanını içeren üst nesneye uygulanır.  
  
  
Aşağıdaki örnekte `LinkAttribution` kuralı `targetPropertyName` alanını içerdiğinden kural `description` alanına uygulanır. Belirli alanlara uygulanan kurallar için hedeflenen verilerin hemen altına, sağlayıcının web sitesine bağlantı içeren bir satır eklemeniz gerekir. Örneğin açıklamaya atıfta bulunmak için açıklama metninin hemen altına, sağlayıcının web sitesindeki verilere bağlantı içeren bir satır ekleyin. Burada en.wikipedia.org sitesine bağlantı oluşturmanız gerekir.  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>Lisans Atfı  

Sözleşme kuralları listesinde [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) kuralı varsa bildirimi lisansın geçerli olduğu içeriğin hemen altındaki satırda görüntülemeniz gerekir. `LicenseAttribution` kuralı lisansın geçerli olduğu özelliği tanımlamak için `targetPropertyName` alanını kullanır.  
  
Aşağıda `LicenseAttribution` kuralını içeren bir örnek gösterilmektedir.  
  
![Lisans atfı](./media/licenseattribution.png)  
  
Görüntülediğiniz lisans bildirimi, lisans hakkında bilgi içeren web sitesine bağlantı içermelidir. Genellikle lisansın adı bir köprü haline getirilir. Örneğin bildirim **Metin CC-BY-SA lisansı kapsamındadır** şeklindeyse ve CC-BY-SA lisansın adıysa CC-BY-SA bölümünü köprü haline getirmeniz gerekir.  
  
### <a name="link-and-text-attribution"></a>Bağlantı ve Metin Atfı  

[LinkAttribution](reference.md#linkattribution) ve [TextAttribution](reference.md#textattribution) kuralları genellikle veri sağlayıcısını tanımlamak için kullanılır. `targetPropertyName` alanı kuralın uygulanacağı alanı tanımlar.  
  
Sağlayıcılara atıfta bulunmak için atıfların geçerli olduğu içeriğin (hedeflenen alan gibi) hemen altına bir satır ekleyin. Satırın, verilerin kaynağının sağlayıcılar olduğunu gösterecek şekilde düzenlenmesi gerekir. Örneğin "Veri sağlayıcısı: en.wikipedia.org". `LinkAttribution` kuralları için sağlayıcının web sitesine köprü oluşturmanız gerekir.  
  
Aşağıda `LinkAttribution` ve `TextAttribution` kurallarını içeren bir örnek gösterilmektedir.  
  
![Bağlantı metni atfı](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Medya Atfı  

Varlıkta görüntü varsa ve bunu görüntülerseniz, sağlayıcının web sitesine tıklanabilir bağlantı sağlamanız gerekir. Varlık bir [MediaAttribution](reference.md#mediaattribution) kuralı içeriyorsa tıklama bağlantısını oluşturmak için kural URL'sini kullanın. Aksi takdirde tıklama bağlantısını oluşturmak için görüntünün `provider` alanındaki URL'yi kullanın.  
  
Aşağıdaki örnekte bir görüntünün `provider` alanı ve sözleşme kuralları gösterilmektedir. Örnekte sözleşme kuralı olduğu için görüntünün `provider` alanını yoksayıp `MediaAttribution` kuralını uygulamanız gerekir.  
  
![Medya atfı](./media/mediaattribution.png)  

## <a name="next-steps"></a>Sonraki adımlar
- [C# hızlı başlangıcı](c-sharp-quickstart.md)
- [Java hızlı başlangıcı](java-quickstart.md)
- [Node hızlı başlangıcı](node-quickstart.md)
- [Python hızlı başlangıcı](python-quickstart.md)
