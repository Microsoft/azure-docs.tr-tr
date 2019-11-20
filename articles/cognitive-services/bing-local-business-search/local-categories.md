---
title: Bing yerel Iş Arama API 'SI için kategorileri ara
titleSuffix: Azure Cognitive Services
description: Bing yerel Iş Arama API uç noktası için arama kategorilerini belirtme hakkında bilgi edinmek için bu makaleyi kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 56b94d66eb0929d2fd0ca74a1a631d229330adfa
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906389"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Bing yerel Iş Arama API 'SI için kategorileri ara

Bing yerel Iş Arama API 'SI, bir kullanıcının konumunu kapatan sonuçlara göre çok sayıda kategoride yerel iş varlıkları aramanızı sağlar. Bu aramaları, `localCircularView` ve `localMapView` [parametreleriyle](specify-geographic-search.md)birlikte aramalara dahil edebilirsiniz.


## <a name="toplevel-categories"></a>TopLevel kategorileri 

Aşağıdaki türler, aramanın ana kategorilerini tanımlar.  `localCategories` Parametreye atanan virgülle ayrılmış bir liste kullanılarak birden fazla kategori belirtilebilir.  
- EatDrink 
- SeeDo 
- Atölye 
- HotelsAndMotels 
- Banksandalacaklı tunları 
- Park 
- Hastaneler 

## <a name="sub-categories"></a>Alt Kategoriler
Alt kategoriler ile `localCategories`aynı şekilde geçirilir. Alt Kategoriler daha belirgin kategorileridir. Bu, bir kategori C ve alt kategorilerinden birini, aynı virgülle ayrılmış listede belirtirseniz, yalnızca C 'yi belirttiklerinde aynı sonuçları elde edersiniz.

### <a name="eat-drink"></a>Kurutink yiyecek 
|  |  |  |  |
| - | - | - | - |
| BreweriesAndBrewPubs | Coctasillounges | AfricanRestaurants |
| AmericanRestaurants | Bagels | BarbecueRestaurants |
| Taverns | Sportsçubuklar | Bu |
| BarsGrillsAndPubs | BuffetRestaurants| BelgianRestaurants | 
| Britişlolar | Caferestaurlar | Caribbeanrestoranlar |
| Çineserestalar | CoffeeAndTea | Delicatessens | 
| DeliveryService | Diners | Discountmağazaları | 
| Donutlar | Fastyiyecek | Frenchrestoranlar | 
| FrozenYogurt | Geri Restoran | Süper pazarlar | 
| Greekrestoranlar | Grocers | Hawaii dili | 
| HungarianRestaurants | Iecreatofrozendesserts | Indianlotlar | 
| Ialianrestoranlar | JapaneseRestaurants | Juıces | 
| KoreanRestaurants | LiquorStores | Mexicanrestoranlar |
| MiddleEasternRestaurants | Pizza | PolishRestaurants | 
| PortugueseRestaurants | Pretzya | Restoranlar | 
| Russianandukraınrestoranlar | Sandwiches | Deniz ve restoran 'Lar | 
| Spanrestoranlar | Steakhouserestalarurlar | Sushilotlar | 
| Ders | Thairestoranlar | Türki Restoranlar | 
| Vegetarianandveganrestoranlar | VietnameseRestaurants|  |
 
### <a name="see-do"></a>Bkz. do 
|  |  |  |
| -- | -- | -- |
| Amusementpark | Atlamalar | Carniler |
| Casinos | LandmarksAndHistoricalSites | Mini Aturegolfkurslar |
| Moviemetreler | Museums | Lular |
| Sı, Seeingturları | Touristınformation | Buros |
 
### <a name="shop"></a>Atölye 
|  |  |  |
| -- | -- | -- |
| Antisorgtores | Kitap depoları | Cdandrecordmağazaları |
| Childrensclothingmağazaları | CigarAndTobaccoShops | Comicbookmağazaları |
| DepartmentStores | Discountmağazaları | FleaMarketsAndBazaars |
| Mobilyanitugeri Yüklemeler | HomeImprovementStores | JewelryAndWatchesStores |
| KitchenwareStores | LiquorStores | MallsAndShoppingCenters |
| Mensclothingmağazaları | Müzik depoları | Outletmağazaları |
| Petterlar | Petsupplymağazaların | SchoolAndOfficeSupplyStores |
| ShoeStores | SportingGoodsStores | ToyAndGameStores |
| Inserminandtakıma Tmağazaları | Womensclothingmağazaları |  |


## <a name="examples-of-local-categories-search"></a>Yerel Kategori arama örnekleri

Aşağıdaki örnekler, `localCategories` parametresine göre sonuçları alır:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

Aşağıdaki sorgu, Bing yerel Iş Arama API 'sinden ilk üç ' hospte ' sonuç sayısını kısıtlar:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

Aşağıdaki örnek JSON yanıtı, daha büyük Seattle alanında üç hastaneler içerir:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>Sonraki adımlar
- [Coğrafi arama sınırları](specify-geographic-search.md)
- [Sorgu ve yanıt](local-search-query-response.md)
- [İçindeki hızlı başlangıçC#](quickstarts/local-quickstart.md)
