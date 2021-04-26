---
title: Basit Lucene sorgu söz dizimini kullan
titleSuffix: Azure Cognitive Search
description: Tam metin araması, filtre arama ve Azure Bilişsel Arama dizinine karşı coğrafi arama için basit sözdizimi gösteren sorgu örnekleri.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694045"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Azure 'da "basit" arama sözdizimini kullanın Bilişsel Arama

Azure Bilişsel Arama 'de, [basit sorgu söz dizimi](query-simple-syntax.md) tam metin araması için varsayılan sorgu ayrıştırıcısını çağırır. Ayrıştırıcı hızlıdır ve tam metin araması, filtrelenmiş ve çok yönlü arama ve ön ek arama gibi yaygın senaryoları işler. Bu makalede, bir [arama belgeleri (REST API)](/rest/api/searchservice/search-documents) isteğindeki basit sözdizimi kullanımını göstermek için örnekler kullanılmaktadır.

> [!NOTE]
> Alternatif bir sorgu söz dizimi, benzer ve joker karakter arama gibi daha karmaşık sorgu yapılarını destekleyen [tam bir Lucene](query-lucene-syntax.md). Daha fazla bilgi ve örnek için bkz. [tam Lucene sözdizimini kullanma](search-query-lucene-examples.md).

## <a name="hotels-sample-index"></a>Oteller örnek dizini

Aşağıdaki sorgular, bu [hızlı](search-get-started-portal.md)başlangıçtaki yönergeleri izleyerek oluşturabileceğiniz oteller-Sample-Index ' i temel alır.

Örnek sorgular REST API ve POST istekleri kullanılarak oluşturulur. Bunları [Postman](search-get-started-rest.md) içine veya [bilişsel arama uzantısıyla Visual Studio Code](search-get-started-vs-code.md)yapıştırabilirsiniz.

İstek üst bilgileri aşağıdaki değerlere sahip olmalıdır:

| Anahtar | Değer |
|-----|-------|
| İçerik Türü | uygulama/json|
| api anahtarı  | `<your-search-service-api-key>`, ya sorgu ya da yönetici anahtarı |

URI parametreleri, aşağıdaki örneğe benzer şekilde, dizin adı, belge koleksiyonları, arama komutu ve API sürümü ile birlikte arama hizmeti uç noktanızı içermelidir:

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

İstek gövdesi geçerli bir JSON olarak oluşturulmalıdır:

```json
{
    "search": "*",
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "ara" değeri `*` belirtilmemiş bir sorgu, null veya boş aramaya eşdeğerdir. Özellikle faydalı değildir, ancak yapabileceğiniz en basit aramadır ve tüm değerleri olan dizindeki tüm alınabilir alanları gösterir.

+ "basit" olarak ayarlanan "queryType" varsayılandır ve yoksayılabilir, ancak bu makaledeki sorgu örneklerinin basit sözdiziminde ifade edilmesi daha da kolay hale zorlanır.

+ "Select" seçeneği, arama sonucu oluşturma için, yalnızca arama sonuçları bağlamında yararlı olan alanlar da dahil olmak üzere bir virgülle ayrılmış alan listesi için kullanılır.

+ "say", arama ölçütleriyle eşleşen belge sayısını döndürür. Boş bir arama dizesinde, sayı dizindeki tüm belgeler olacaktır (oteller söz konusu olduğunda 50-örnek-dizin).

## <a name="example-1-full-text-search"></a>Örnek 1: tam metin araması

Tam metin arama, Boolean işleçleri içeren veya içermeyen herhangi bir sayıda tek başına terim veya tırnak içine alınmış tümceler olabilir. 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

Önemli terimlerin veya ifadelerden oluşan bir anahtar sözcük araması en iyi çalışma eğilimindedir. Dize alanları, dizin oluşturma ve sorgulama sırasında metin analizini ve "The", "ve", "it" gibi önemli olmayan sözcükleri bırakarak. Sorgu dizesinin dizin içinde nasıl simgeleştirilmiş olduğunu görmek için, dizeyi bir [Çözümleme metin](/rest/api/searchservice/test-analyzer) çağrısında dizine geçirin.

"SearchMode" parametresi hassasiyeti denetler ve çağırır. Daha fazla geri çekme istiyorsanız, sorgu dizesinin herhangi bir bölümü eşleştiğinde bir sonuç döndüren varsayılan "Any" değerini kullanın. Dizenin tüm parçalarının eşleşmesi gerektiği Duyarlığın tercih ediyorsanız, searchMode öğesini "All" olarak değiştirin. Yukarıdaki sorguyu, searchMode nasıl değişiklik yapıldığını görmek için iki şekilde deneyin.

"Havuz Spa + Havaalanı" sorgusunun yanıtı, breçekimi için kırpılmış aşağıdaki örneğe benzer olmalıdır.

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

Yanıtta arama puanına dikkat edin. Bu, eşleşmenin ilgi puandır. Varsayılan olarak, bir arama hizmeti bu puanı temel alarak en çok 50 eşleşme döndürür.

"1,0" öğesinin Tekdüzen puanı, bir derecelendirme olmadığında, arama tam metin araması olmadığı veya hiçbir ölçüt sağlanmadığından oluşur. Örneğin, boş bir aramada (Search = `*` ), satırlar rastgele sırayla geri gelir. Gerçek ölçütleri dahil ettiğinizde, arama puanları anlamlı değerlere geliştikçe görüntülenir.

## <a name="example-2-look-up-by-id"></a>Örnek 2: KIMLIĞE göre ara

Arama sonuçlarını bir sorguda döndürdüğünüzde, mantıksal bir sonraki adım belgeden daha fazla alan içeren bir ayrıntı sayfası sağlamaktır. Bu örnek, belge KIMLIĞINI geçirerek [Arama belgesi](/rest/api/searchservice/lookup-document) kullanarak tek bir belgeyi döndürmeyi gösterir.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

Tüm belgeler benzersiz bir tanımlayıcıya sahiptir. Portalı kullanıyorsanız, **dizinler** sekmesinden Dizin ' i seçin ve sonra hangi alanın anahtarın olduğunu belirlemek için alan tanımlarına bakın. REST kullanarak, [Get Index](/rest/api/searchservice/get-index) çağrısı yanıt gövdesinde Dizin tanımını döndürür.

Yukarıdaki sorguya yönelik yanıt, anahtarı 41 olan belgeden oluşur. Dizin tanımında "alınabilir" olarak işaretlenen tüm alanlar, arama sonuçlarında döndürülür ve uygulamanızda işlenir.

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>Örnek 3: metin filtresi

[Filtre sözdizimi](search-query-odata-filter.md) , kendisi tarafından veya "ara" ile kullanabileceğiniz bir OData deyimidir. Birlikte kullanıldığında, önce tüm dizine "filtre" uygulanır ve sonra filtre sonuçlarında arama yapılır. Filtreler arama sorgusunun işlemesi gereken belge kümesini azalttığından, sorgu performansını iyileştirmeye yönelik kullanışlı bir teknik olabilir.

Filtreler, Dizin tanımında "filtrelenebilir" olarak işaretlenmiş tüm alanlar için tanımlanabilir. Oteller için-örnek-dizin, filtrelenebilir alanlar kategori, Etiketler, Parkingiçerilen, derecelendirme ve birçok adres alanını içerir.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Yukarıdaki sorguya yönelik yanıt, "sanat" veya "Tur" terimlerini içeren yalnızca "rapor ve Spa" olarak kategorilere ayrılmış olan oteller kapsamlandırılır. Bu durumda, yalnızca bir eşleşme vardır.

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>Örnek 4: filtre işlevleri

Filtre ifadelerinde ["Search. IsMatch" ve "Search. ısmatchpuanlama" işlevleri](search-query-odata-full-text-search-functions.md)bulunabilir ve bu, filtre içinde bir arama sorgusu oluşturmanıza olanak sağlar. Bu filtre ifadesi ücretsiz WiFi, ücretsiz Park, vb. gibi değişiklik belirlemek için *ücretsiz* olarak bir joker karakter kullanır.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

Yukarıdaki sorgu için yanıt, ücretsiz düzeltme sunan 19 oteller ile eşleşir. Arama puanın sonuçların tamamında tek biçimli bir "1,0" olduğuna dikkat edin. Bunun nedeni, arama ifadesinin null ya da boş olmasından kaynaklanır ve tam metin araması yoktur. İlgi puanları yalnızca tam metin aramasında döndürülür. "Arama" olmadan filtreler kullanıyorsanız, arama derecesini denetleyebilmeniz için yeterli sıralanabilir alanlara sahip olduğunuzdan emin olun.

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>Örnek 5: Aralık filtreleri

Aralık filtreleme, herhangi bir veri türü için filtre ifadeleri aracılığıyla desteklenir. Aşağıdaki örneklerde sayısal ve dize aralıkları gösterilmektedir. Veri türleri, Aralık filtrelerinde önemlidir ve sayısal veriler sayısal alanlarda ve dize alanlarında dize verilerinde en iyi şekilde çalışır. Dize alanlarında sayısal veriler, sayısal dizeler karşılaştırılabilir olmadığından aralıklar için uygun değildir.

Aşağıdaki sorgu sayısal bir aralıktır. Oteller-örnek-dizin ' de tek filtrelenebilir sayısal alan derecelendirmesidir.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

Bu sorguya yönelik yanıt, kısaltma için kırpılmış aşağıdaki örneğe benzer görünmelidir.

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

Sonraki sorgu, bir dize alanı (adres/durum bölgesi) üzerinde bir Aralık filtrelemesine sahiptir:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

Bu sorgu için yanıt, örnekçekimi için kırpılmış aşağıdaki örneğe benzer görünmelidir. Bu örnekte, alan, Dizin tanımında "sıralanabilir" olarak belirlenmediğinden Statestate 'e göre sıralama yapmak mümkün değildir.

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>Örnek 6: coğrafi arama

Oteller-örnek dizin, enlem ve Boylam koordinatlarıyla bir geo_location alanı içerir. Bu örnek, bir başlangıç noktasının çevresi içindeki belgelerde, sağladığınız rastgele bir uzaklığa (kilometre cinsinden) göre filtreleyen [coğrafi. Distance işlevini](search-query-odata-geo-spatial-functions.md#examples) kullanır. Sorgunun yüzey alanını azaltmak veya büyütmek için sorgudaki son değeri ayarlayabilirsiniz (10).

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

Bu sorguya yönelik yanıt, belirtilen koordinatların 10 kilometremesafesi içindeki tüm oteller döndürür:

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>Örnek 7: searchMode ile Boolean

Basit sözdizimi, bir `+, -, |` sorgu mantığını desteklemek ve, ya da değil,, veya, ya da değil, karakter biçimindeki Boole işleçlerini destekler. Boolean arama, birkaç önemli özel durum sayesinde bekleolabileceğiniz gibi davranır. 

Önceki örneklerde, "searchMode" parametresi duyarlık ve geri çağırma için bir mekanizma olarak sunulmuştur. "searchMode = Any" favoring geri çağırma (ölçütlere herhangi birini karşılayan bir belge eşleşme olarak kabul edilir) ve "searchMode = tümü" favoring duyarlık (tüm kriterlerin bir belge ile eşleşmesi gerekir). 

Boole araması bağlamında, bir sorguyu birden çok işleçle yığınlama ve daha da dar sonuçlar yerine daha geniş bir değer elde ediyorsanız "searchMode = Any" varsayılan değeri kafa karıştırıcı olabilir. Bu durum özellikle doğru değildir; burada sonuçlar, belirli bir terim veya tümceciği içermez.

Aşağıdaki örnek, bir gösterim sağlar. Aşağıdaki sorguyu searchMode (any) ile çalıştırmak, 42 belge döndürülür: "Restoran" terimini ve "uçak" ifadesi olmayan tüm belgeleri içerir. 

Boolean işleci ( `-` ) ve "Hava koşullandırma" ifadesi arasında boşluk olmadığına dikkat edin.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

"SearchMode = All" olarak değiştirmeniz, ölçütlerde bir toplu etki uygular ve "Restoran" terimini içeren belgelerden ("uçak" ifadesini içeren) daha küçük bir sonuç kümesi (7 eşleşme) döndürür.

Bu sorguya yönelik yanıt, artık breçekimi için kırpılmış aşağıdaki örneğe benzer şekilde görünür.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>Örnek 8: sayfalama sonuçları

Önceki örneklerde, arama sonuçları birleşimini etkileyen parametreler hakkında bilgi edindiniz. Bu, bir sonuç içinde hangi alanların olduğunu, sıralama düzenlerini ve tüm eşleşmelerin sayısını nasıl dahil edileceğini belirleyen "Select" de dahil olmak üzere. Bu örnek, belirli bir sayfada görünen sonuçların sayısını toplu olarak kullanmanıza olanak tanıyan sayfalama parametreleri biçiminde arama sonucu kompozisyonunun devamlıdır. 

Varsayılan olarak, bir arama hizmeti ilk 50 eşleşme döndürür. Her sayfadaki eşleşmelerin sayısını denetlemek için, toplu işlemin boyutunu tanımlamak üzere "top" seçeneğini kullanın ve ardından sonraki toplu işleri seçmek için "atla" yı kullanın.

Aşağıdaki örnek, sıralı sonuçlarda sayfalama etkilerini görmek daha kolay olduğundan, derecelendirme alanında bir filtre ve sıralama düzeni kullanır (derecelendirme hem filtrelenebilir hem de sıralanabilir). Düzenli bir tam arama sorgusunda, en üst Eşleşmeler "" ile sıralanır ve disk belleğine alınır @search.score .

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

Sorgu 21 eşleşen belge bulur, ancak "top" belirtildiğinde, yanıt 4,9 ' dan başlayan derecelendirmelerle birlikte yalnızca ilk beş eşleşme döndürür ve "Gölü t & B" ile 4,7 ' de sona eriyor. 

Sonraki 5 ' i almak için ilk toplu işi atlayın:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

İkinci toplu işe yönelik yanıt, sonraki beş eşleşme atlayarak "Pull'r Inn Motel" ile başlayan ilk beş eşleşme atlar. Ek toplu işlerle devam etmek için, 5 ' te "top" değerini tutar ve sonra her yeni istek (Skip = 5, Skip = 10, Skip = 15 vb.) için 5 ' i atlayarak "atlayın".

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

Temel sorgu söz dizimi ile bir alıştırma olduğuna göre, koddaki sorguları belirtmeyi deneyin. Aşağıdaki bağlantılarda, Azure SDK 'Ları kullanılarak arama sorgularının nasıl ayarlanacağı açıklanmaktadır.

+ [.NET SDK kullanarak dizininizi sorgulama](search-get-started-dotnet.md)
+ [Python SDK 'sını kullanarak dizininizi sorgulama](search-get-started-python.md)
+ [JavaScript SDK 'sını kullanarak dizininizi sorgulama](search-get-started-javascript.md)

Ek sözdizimi başvurusu, sorgu mimarisi ve örnekler aşağıdaki bağlantılarda bulunabilir:

+ [Lucene sözdizimi sorgu gelişmiş sorgular oluşturmaya yönelik örnekler](search-query-lucene-examples.md)
+ [Azure Bilişsel Arama’da tam metin araması nasıl çalışır?](search-lucene-query-architecture.md)
+ [Basit sorgu söz dizimi](query-simple-syntax.md)
+ [Tam Lucene sorgu söz dizimi](query-lucene-syntax.md)
+ [Filtre sözdizimi](search-query-odata-filter.md)