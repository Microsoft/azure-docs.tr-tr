---
title: Bing Varlık Arama API'si varlıkları arayın
titleSuffix: Azure Cognitive Services
description: Arama sorgularından varlıkları ve yerleri ayıklamak ve aramak için Bing Varlık Arama API'si kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9dabceda17defb24f2a916cd641f625feb551c6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353298"
---
# <a name="searching-for-entities-with-the-bing-entity-api"></a>Bing varlık API 'SI ile varlıkları arama

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Bing Otomatik Öneri API'si ile arama terimleri önerin

Kullanıcıların arama terimlerini gireceği bir arama kutusu sağlıyorsanız deneyimi geliştirmek için [Bing Otomatik Öneri API'sini](../../bing-autosuggest/get-suggested-search-terms.md) kullanın. API, kullanıcı yazarken kısmi arama terimlerine dayalı önerilen sorgu dizelerini yönetin.

Kullanıcı arama terimini girdikten sonra [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) sorgu parametresini ayarlamadan önce terimi URL ile kodlayın. Örneğin kullanıcı *Marcus Appel* yazarsa `q` içinde *Marcus+Appel* veya *Marcus%20Appel* olarak kullanın.

Arama teriminde yazım hatası varsa arama yanıtında [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) nesnesi bulunur. Nesne, özgün terimi ve Bing'in arama için kullandığı düzeltilmiş halini döndürür.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="the-bing-entity-search-api-response"></a>Bing Varlık Arama API'si yanıtı

API yanıtı bir [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse) nesnesi içeriyor. Bing ilgili bir varlık veya yer bulduğunda nesne `entities` alanını, `places` alanını veya ikisini birden içerir. Aksi takdirde yanıt nesnesi iki alanı da içermez.
> [!NOTE]
> Varlık yanıtları birden fazla pazarı destekler ancak Places yanıtı yalnızca ABD'deki İşletme konumlarını destekler. 

`entities` alanı bir [EntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) nesnesidir ve [Entity](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity) nesnelerinin listesini içerir (`value` alanına bakın). Listede tek bir baskın varlık, birden fazla kesinleştirme varlığı veya ikisi birden bulunabilir. 

Bing bir varlık, isteği karşılayan tek varlık olduğunu düşündüğü zaman döndürülür (hangi varlığın isteği karşılayan bir belirsizlik yoktur). İsteği birden fazla varlık karşılıyorsa listede birden fazla kesinleştirme varlığı bulunur. Örneğin istekte bir film serisinin genel adı kullanılıyorsa listede muhtemelen kesinleştirme varlıkları bulunacaktır. Ancak istekte serideki filmlerden birinin adı belirtiliyorsa listede muhtemelen tek bir baskın varlık olacaktır.

Varlıklar şarkıcılar, oyuncular, atletler, modeller gibi tanınmış kişileri, Mount Rainier veya Lincoln Memorial gibi önemli yerleri ve muz, goldendoodle, kitap veya film adı gibi nesneleri içerir. [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) alanı, varlık türünü tanımlayan ipuçları içerir. Örneğin varlığın kişi, film, hayvan veya önemli nokta olduğunu gösterebilir. Olası türlerin listesi için bkz. [Varlık Türleri](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Aşağıda baskın varlık ve kesinleştirme varlığı içeren bir yanıt gösterilmiştir.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
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
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

Varlıkta `name`, `description` ve `image` alanı vardır. Bu alanları kullanıcı deneyiminizde görüntülediğinizde atıfta bulunmanız gerekir. `contractualRules` alanında uygulamanız gereken özniteliklerin listesi bulunur. Atıfta bulunulacak alanı sözleşme kuralı belirler. Atıfta bulunma hakkında daha fazla bilgi için bkz. [Atıfta bulunma](#data-attribution).

```json
"contractualRules": [{
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
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

Varlık bilgilerini (ad, açıklama ve görüntü) görüntülediğinizde `webSearchUrl` alanındaki URL'yi kullanarak varlığı içeren Bing arama sonuçları sayfasına da bağlantı vermeniz gerekir.

## <a name="find-places"></a>Yerleri bul

`places`Alan, [yer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#place) nesnelerinin bir listesini Içeren bir [Localentityanswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) nesnesidir (daha fazla bilgi için [varlık türlerine](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types) bakın). Listede isteği karşılayan bir veya daha fazla yerel varlık bulunur.

Yerler restoranlar, oteller veya yerel işletmeler olabilir. [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) alanı, yerel varlık türünü tanımlayan ipuçları içerir. Listede Place, LocalBusiness, Restaurant gibi ipuçları bulunur. Dizideki ardışık ipuçları varlık türünü daraltır. Olası türlerin listesi için bkz. [Varlık Türleri](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Varlık yanıtları birden fazla pazarı destekler ancak Places yanıtı yalnızca ABD'deki İşletme konumlarını destekler. 

*Yakınımdaki restoranlar* gibi konuma dayalı varlık sorguları doğru sonuçları sunmak için kullanıcının konumuna ihtiyaç duyar. İsteklerinizde her zaman kullanıcının konumunu belirtmek için X-Search-Location ve X-MSEdge-ClientIP üst bilgileri kullanılmalıdır. Bing, kullanıcının konumunun sorgu için faydalı olacağını düşündüğünde [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) öğesinin `askUserForLocation` alanını **true** olarak ayarlar. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Yer sonucunda yerin adı, adresi, telefon numarası ve varlık web sitesinin URL'si bulunur. Varlık bilgilerini görüntülediğinizde `webSearchUrl` alanındaki URL'yi kullanarak varlığı içeren Bing arama sonuçları sayfasına da bağlantı vermeniz gerekir.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> Varlıklar API'sinden alınan veriler sizin tarafınızdan veya sizin adınıza hareket eden üçüncü şahıslar tarafından Microsoft harici bir hizmeti veya özelliği test etme, geliştirme, eğitme, dağıtma veya kullanıma sunma amacıyla kullanılamaz, saklanamaz, depolanamaz, önbelleğe alınamaz, paylaşılamaz veya dağıtılamaz.  

## <a name="data-attribution"></a>Veri atfı

Bing Varlık API'si yanıtları, üçüncü taraflara ait bilgiler içerir. Kullanıcı deneyiminizde kullanılan Creative Commons lisansına uygun hareket etme gibi kullanımınızın gerektirdiği durumlardan sorumlu olursunuz.

Yanıt ya da sonuç `contractualRules`, `attributions` veya `provider` alanlarını içeriyorsa veriler için atıfta bulunmanız gerekir. Yanıtta bu alanlardan biri yoksa atıfta bulunmanıza gerek yoktur. Yanıtta `contractualRules` alanı ile `attributions` ve/veya `provider` alanı varsa verilere atıfta bulunmak için sözleşme kurallarını kullanmanız gerekir.

Aşağıdaki örnek MediaAttribution sözleşme kuralına ve `provider` alanına sahip olan bir Image nesnesine sahip olan varlığı göstermektedir. MediaAttribution kuralı görüntüyü kuralın hedefi olarak tanımlamaktadır. Bu nedenle görüntünün `provider` alanını yoksayabilir ve atıfta bulunmak için MediaAttribution kuralını kullanabilirsiniz.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Sözleşme kuralında `targetPropertyName` alanı varsa kural yalnızca hedeflenen alan için geçerli olur. Aksi takdirde, kural `contractualRules` alanını içeren üst nesneye uygulanır.

Aşağıdaki örnekte `LinkAttribution` kuralı `targetPropertyName` alanını içerdiğinden kural `description` alanına uygulanır. Belirli alanlara uygulanan kurallar için hedeflenen verilerin hemen altına, sağlayıcının web sitesine bağlantı içeren bir satır eklemeniz gerekir. Örneğin açıklamaya atıfta bulunmak için açıklama metninin hemen altına, sağlayıcının web sitesindeki verilere bağlantı içeren bir satır ekleyin. Burada contoso.com sitesine bağlantı oluşturmanız gerekir.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Lisans atfı

Sözleşme kuralları listesinde [LicenseAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#licenseattribution) kuralı varsa bildirimi lisansın geçerli olduğu içeriğin hemen altındaki satırda görüntülemeniz gerekir. `LicenseAttribution` kuralı lisansın geçerli olduğu özelliği tanımlamak için `targetPropertyName` alanını kullanır.

Aşağıda `LicenseAttribution` kuralını içeren bir örnek gösterilmektedir.

![Lisans atfı](../media/cognitive-services-bing-entities-api/licenseattribution.png)

Görüntülediğiniz lisans bildirimi, lisans hakkında bilgi içeren web sitesine bağlantı içermelidir. Genellikle lisansın adı bir köprü haline getirilir. Örneğin bildirim **Metin CC-BY-SA lisansı kapsamındadır** şeklindeyse ve CC-BY-SA lisansın adıysa CC-BY-SA bölümünü köprü haline getirmeniz gerekir.

### <a name="link-and-text-attribution"></a>Bağlantı ve metin atfı

[LinkAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#linkattribution) ve [TextAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#textattribution) kuralları genellikle veri sağlayıcısını tanımlamak için kullanılır. `targetPropertyName` alanı kuralın uygulanacağı alanı tanımlar.

Sağlayıcılara atıfta bulunmak için atıfların geçerli olduğu içeriğin (hedeflenen alan gibi) hemen altına bir satır ekleyin. Satırın, verilerin kaynağının sağlayıcılar olduğunu gösterecek şekilde düzenlenmesi gerekir. Örneğin "Veri sağlayıcısı: contoso.com". `LinkAttribution` kuralları için sağlayıcının web sitesine köprü oluşturmanız gerekir.

Aşağıda `LinkAttribution` ve `TextAttribution` kurallarını içeren bir örnek gösterilmektedir.

![Bağlantı metni atfı](../media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Medya atfı

Varlıkta görüntü varsa ve bunu görüntülerseniz, sağlayıcının web sitesine tıklanabilir bağlantı sağlamanız gerekir. Varlık bir [MediaAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mediaattribution) kuralı içeriyorsa tıklama bağlantısını oluşturmak için kural URL'sini kullanın. Aksi takdirde tıklama bağlantısını oluşturmak için görüntünün `provider` alanındaki URL'yi kullanın.

Aşağıdaki örnekte bir görüntünün `provider` alanı ve sözleşme kuralları gösterilmektedir. Örnekte sözleşme kuralı olduğu için görüntünün `provider` alanını yoksayıp `MediaAttribution` kuralını uygulamanız gerekir.

![Medya atfı](../media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Arama veya arama benzeri deneyim

Bing Web Araması API'sinde olduğu gibi Bing Varlık Arama API'si de yalnızca doğrudan kullanıcı sorgusu veya araması sonucunda ya da bir uygulama veya deneyimde bulunan ve kullanıcının arama isteği gibi yorumlanabilecek bir eylem sonucunda kullanılabilir. Örnek olması amacıyla aşağıda bazı kabul edilebilir arama veya arama benzeri deneyimler sunulmuştur.

- Kullanıcı bir uygulamadaki arama kutusuna doğrudan sorgu girer
- Kullanıcı belirli bir metni veya görüntüyü seçer ve "daha fazla bilgi" ya da "ek bilgi" ister
- Kullanıcı arama botuna belirli bir konuyla ilgili soru sorar
- Görsel arama türündeki bir senaryoda kullanıcı belirli bir nesne veya varlık üzerinde durur

Deneyiminizin arama benzeri deneyim sınıfına girip girmediğinden emin değilseniz Microsoft'a danışmanız önerilir.

## <a name="throttling-requests"></a>İstekleri azaltma

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Bing Varlık Arama API'si olan varlıkları aramaya başlamak için [hızlı başlangıç](../quickstarts/csharp.md) yapmayı deneyin.