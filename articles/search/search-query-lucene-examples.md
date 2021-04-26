---
title: Tam Lucene sorgu söz dizimini kullan
titleSuffix: Azure Cognitive Search
description: Benzer arama, yakınlık araması, terim artırma, normal ifade arama ve Azure Bilişsel Arama dizininde joker karakter aramaları için Lucene sorgu söz dizimini gösteren sorgu örnekleri.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693569"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>"Full" Lucene arama sözdizimini kullanın (Azure Bilişsel Arama Gelişmiş sorgular)

Azure Bilişsel Arama için sorgular oluştururken, özelleştirilmiş ve Gelişmiş sorgu ifadelerini formülleştirmek için varsayılan [basit sorgu ayrıştırıcısını](query-simple-syntax.md) daha güçlü bir [Lucene sorgu ayrıştırıcısıyla](query-lucene-syntax.md) değiştirebilirsiniz.

Lucene ayrıştırıcısı alan kapsamlı sorgular, belirsiz arama, indüzeltilme ve sonek joker karakter araması, yakınlık araması, terim artırma ve normal ifade arama gibi karmaşık sorgu biçimlerini destekler. Ek güç ek işlem gereksinimleriyle birlikte gelir, böylece biraz daha uzun bir yürütme süresi beklemelisiniz. Bu makalede, tam söz dizimi temelinde sorgu işlemlerini gösteren örneklerde ileredebilirsiniz.

> [!Note]
> Tam Lucene sorgu söz dizimi aracılığıyla etkinleştirilen özelleştirilmiş sorgu kurulumlarını birçoğu [metin çözümlenmez](search-lucene-query-architecture.md#stage-2-lexical-analysis)ve bu da, sözcük olarak ayırmayı veya kasaymayı düşünüyorsanız ortaya çıkmış olabilir. Sözcük temelli analiz yalnızca tüm koşullarda (bir terim sorgusu veya tümcecik sorgusu) gerçekleştirilir. Eksik koşullara sahip sorgu türleri (ön ek sorgusu, joker karakter sorgusu, Regex sorgusu, benzer sorgu) doğrudan sorgu ağacına eklenir, analiz aşaması atlanıyor. Kısmi sorgu koşullarında gerçekleştirilen tek dönüşüm küçük harfe göre yapılır. 
>

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
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "ara" değeri `*` belirtilmemiş bir sorgu, null veya boş aramaya eşdeğerdir. Özellikle faydalı değildir, ancak yapabileceğiniz en basit aramadır ve tüm değerleri olan dizindeki tüm alınabilir alanları gösterir.

+ "Full" olarak ayarlanan "queryType" tam Lucene sorgu ayrıştırıcısını çağırır ve bu söz dizimi için gereklidir.

+ "Select" seçeneği, arama sonucu oluşturma için, yalnızca arama sonuçları bağlamında yararlı olan alanlar da dahil olmak üzere bir virgülle ayrılmış alan listesi için kullanılır.

+ "say", arama ölçütleriyle eşleşen belge sayısını döndürür. Boş bir arama dizesinde, sayı dizindeki tüm belgeler olacaktır (oteller söz konusu olduğunda 50-örnek-dizin).

## <a name="example-1-fielded-search"></a>Örnek 1: parçalı arama

Belirli bir alana ayrılmış arama kapsamı tekil, katıştırılmış arama ifadeleri. Bu örnek, otel adlarını "otel" terimiyle arar, ancak "Motel" değil. VE kullanarak birden çok alan belirtebilirsiniz. 

Bu sorgu söz dizimini kullandığınızda, sorgulamak istediğiniz alanlar arama ifadesinin kendisinde olduğunda "searchFields" parametresini atlayabilirsiniz. Alan aramasına "searchFields" eklerseniz, `fieldName:searchExpression` her zaman "searchfields" üzerinden önceliklidir.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

Bu sorguya yönelik yanıt, "çare ve Spa" olarak filtrelenen ve adında "otel" veya "Motel" içeren oteller döndüren aşağıdaki örneğe benzer şekilde görünmelidir.

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

Arama ifadesi, isteğe bağlı olarak Boolean işleçleriyle tek bir terim veya bir tümcecik ya da parantez içinde daha karmaşık bir ifade olabilir. Bazı örnekler şunlardır:

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

Her iki dizenin de tek bir varlık olarak değerlendirilmesini istiyorsanız, bu örnekte adres/Stateeyalet alanında iki ayrı konum aramak için bir tümceciği tırnak işareti içine aldığınızdan emin olun. İstemciye bağlı olarak, tırnak işaretleri () arasında kaçış yapmanız gerekebilir `\` .

İçinde belirtilen alanın `fieldName:searchExpression` aranabilir bir alan olması gerekir. Alan tanımlarının nasıl ilişkilendirilecağına ilişkin ayrıntılar için bkz. [Dizin oluşturma (REST API)](/rest/api/searchservice/create-index) .

## <a name="example-2-fuzzy-search"></a>Örnek 2: benzer arama

Hatalı arama, yanlış yazılmış sözcükler de dahil olmak üzere benzer koşullarla eşleşir. Benzer bir arama yapmak için, `~` tek bir sözcüğün sonuna, düzenleme uzaklığını belirten, 0 ile 2 arasında bir değer olan, bir, isteğe bağlı bir parametre ile birlikte tilde sembolünü ekleyin. Örneğin, `blue~` veya `blue~1` mavi, maves ve tutkalla döndürür.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

Bu sorgu için yanıt, eşleşen belgelerde, breçekimi için kırpılmış "Concierge" olarak çözümlenir:

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

Tümcecikler doğrudan desteklenmez, ancak gibi çok parçalı bir tümceciğin her bir teriminde benzer bir eşleşme belirtebilirsiniz `search=Tags:landy~ AND sevic~` .  Bu sorgu ifadesi "Laundry Service" üzerinde 15 eşleşme bulur.

> [!Note]
> Benzer sorgular [çözümlenmez](search-lucene-query-architecture.md#stage-2-lexical-analysis). Eksik koşullara sahip sorgu türleri (ön ek sorgusu, joker karakter sorgusu, Regex sorgusu, benzer sorgu) doğrudan sorgu ağacına eklenir, analiz aşaması atlanıyor. Kısmi sorgu terimlerinde gerçekleştirilen tek dönüşüm küçük harfe eşit.
>

## <a name="example-3-proximity-search"></a>Örnek 3: yakınlık araması

Yakınlık araması, bir belgedeki birbirini yakın terimleri bulur. Bir ifadenin sonuna, ardından yakınlık sınırını oluşturan sözcüklerin sayısını içeren bir tilde "~" simgesi ekleyin.

Bu sorgu, bir belgede "otel" ve "Havaalanı" terimlerini bir belgedeki 5 sözcükten sonra arar. İfadeyi korumak için tırnak işaretleri () kaçışına sahiptir `\"` :

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

Bu sorgu için yanıt aşağıdaki örneğe benzer görünmelidir:

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>Örnek 4: terim artırma

Terim artırma, bir belgeyi, süresi içermeyen belgelere göre, daha yüksek bir dönem içeriyorsa bir belgenin derecelendirdiğini ifade eder. Bir dönemi artırmak için, `^` arama yaptığınız terimin sonundaki bir artırma faktörü (bir sayı) ile birlikte şapka simgesini kullanın. Varsayılan artırma faktörü 1 ' dir, ancak pozitif olması gerekir, 1 ' den küçük olabilir (örneğin, 0,2). Kullanım süresi, Puanlama profillerindeki Puanlama profillerinden farklıdır ve belirli koşullar yerine belirli alanları artırır.

Bu "önce" sorgusunda, "plaj erişimi" araması yapın ve bir veya iki terim ile eşleşen yedi belge olduğuna dikkat edin.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

Aslında, "Access" ile eşleşen yalnızca bir belge vardır ve tek eşleşme olduğundan, belgede "plaj" teriminin eksik olmasına rağmen yerleştirme yüksek (ikinci konum) olur.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

"Sonra" sorgusunda, bu kez "erişim" teriminin "plaj" terimiyle sonuçları elde eden aramayı tekrarlayın. Sorgunun okunabilir bir sürümü `search=Description:beach^2 access` . İstemcinizi bağlı olarak, olarak ifade etmeniz gerekebilir `^2` `%5E2` .

"Plaj" terimini yükselttikten sonra, eski Carsbelle otelde eşleşme aşağı doğru aşağı doğru gider.

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>Örnek 5: Regex

Normal ifade araması, [RegExp sınıfında](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)belgelendiği gibi eğik çizgi "/" arasındaki içeriğe dayalı bir eşleşme bulur.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

Bu sorgu için yanıt aşağıdaki örneğe benzer görünmelidir:

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> Regex sorguları [çözümlenmez](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Kısmi sorgu terimlerinde gerçekleştirilen tek dönüşüm küçük harfe eşit.
>

## <a name="example-6-wildcard-search"></a>Örnek 6: joker karakter arama

Birden çok ( `*` ) veya tek ( `?` ) karakterli joker karakter aramaları için genellikle tanınan sözdizimini kullanabilirsiniz. Lucene sorgu ayrıştırıcısının, bu sembollerin tek bir terim ve tümcecik değil, kullanımını desteklediği unutulmamalıdır.

Bu sorguda, ' SC ' önekini içeren otel adlarını arayın. Bir `*` veya `?` sembolünü bir aramanın ilk karakteri olarak kullanamazsınız.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

Bu sorgu için yanıt aşağıdaki örneğe benzer görünmelidir:

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> Joker karakter sorguları [çözümlenmez](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Kısmi sorgu terimlerinde gerçekleştirilen tek dönüşüm küçük harfe eşit.
>

## <a name="next-steps"></a>Sonraki adımlar

Koddaki sorguları belirtmeyi deneyin. Aşağıdaki bağlantılarda, Azure SDK 'Ları kullanılarak arama sorgularının nasıl ayarlanacağı açıklanmaktadır.

+ [.NET SDK kullanarak dizininizi sorgulama](search-get-started-dotnet.md)
+ [Python SDK 'sını kullanarak dizininizi sorgulama](search-get-started-python.md)
+ [JavaScript SDK 'sını kullanarak dizininizi sorgulama](search-get-started-javascript.md)

Ek sözdizimi başvurusu, sorgu mimarisi ve örnekler aşağıdaki bağlantılarda bulunabilir:

+ [Lucene sözdizimi sorgu gelişmiş sorgular oluşturmaya yönelik örnekler](search-query-lucene-examples.md)
+ [Azure Bilişsel Arama’da tam metin araması nasıl çalışır?](search-lucene-query-architecture.md)
+ [Basit sorgu söz dizimi](query-simple-syntax.md)
+ [Tam Lucene sorgu söz dizimi](query-lucene-syntax.md)
+ [Filtre sözdizimi](search-query-odata-filter.md)