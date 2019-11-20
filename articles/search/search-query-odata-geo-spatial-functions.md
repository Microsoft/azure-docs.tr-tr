---
title: OData coğrafi uzamsal işlev başvurusu
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama sorgularında, OData coğrafi uzamsal işlevleri, coğrafi. uzaklık ve coğrafi. kesişimleri kullanımı için sözdizimi ve başvuru belgeleri.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113169"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Azure Bilişsel Arama-`geo.distance` ve `geo.intersects` OData coğrafi uzamsal işlevleri

Azure Bilişsel Arama, `geo.distance` ve `geo.intersects` işlevleri aracılığıyla [OData filtre ifadelerinde](query-odata-filter-orderby-syntax.md) coğrafi uzamsal sorguları destekler. `geo.distance` işlevi iki noktaya, biri bir alan veya Aralık değişkeni, diğeri de filtrenin bir parçası olarak geçen bir sabit değer arasındaki mesafeyi kilometre olarak döndürür. `geo.intersects` işlevi, verilen bir bir çokgen içindeyse, noktanın bir alan veya Aralık değişkeni olduğu ve çokgenin, filtrenin parçası olarak geçirildiği bir sabit olarak belirtildiği şekilde `true` döndürür.

`geo.distance` işlevi, arama sonuçlarını belirli bir noktadan uzaklığına göre sıralamak için [ **$OrderBy** parametresinde](search-query-odata-orderby.md) de kullanılabilir. **$Orderby** `geo.distance` söz dizimi **$Filter**ile aynıdır. **$Orderby**`geo.distance` kullanırken, geçerli olduğu alan `Edm.GeographyPoint` türünde olmalıdır ve aynı zamanda **sıralanabilir**olmalıdır.

> [!NOTE]
> **$OrderBy** parametresinde `geo.distance` kullanırken, işleve geçirdiğiniz alan yalnızca tek bir coğrafi nokta içermelidir. Diğer bir deyişle, `Collection(Edm.GeographyPoint)`değil `Edm.GeographyPoint` türünde olması gerekir. Azure Bilişsel Arama 'de koleksiyon alanlarını sıralamak mümkün değildir.

## <a name="syntax"></a>Sözdizimi

Aşağıdaki EBNF ([Genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) `geo.distance` ve `geo.intersects` işlevlerinin dilbilgisini ve üzerinde çalıştıkları coğrafi uzamsal değerleri tanımlar:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Etkileşimli bir sözdizimi diyagramı da kullanılabilir:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Tüm EBNF için bkz. [Azure bilişsel arama Için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md) .

### <a name="geodistance"></a>coğrafi. mesafe

`geo.distance` işlevi `Edm.GeographyPoint` iki parametre alır ve bunlar arasındaki uzaklığı kilometre cinsinden olan `Edm.Double` bir değer döndürür. Bu, genellikle ölçümlerdeki uzaklıkları döndüren OData coğrafi uzamsal işlemlerini destekleyen diğer hizmetlerden farklıdır.

`geo.distance` parametrelerden biri bir Coğrafya noktası sabiti olmalıdır ve diğeri de bir alan yolu olmalıdır (veya bir filtre durumunda `Collection(Edm.GeographyPoint)`) bir alanda yineleme olması durumunda bir Aralık değişkeni olmalıdır. Bu parametrelerin sırası böyle değildir.

Coğrafya noktası sabiti, boylam ve enlem 'nin Sayısal sabitler olduğu `geography'POINT(<longitude> <latitude>)'`formundadır.

> [!NOTE]
> Bir filtrede `geo.distance` kullanırken, işlev tarafından döndürülen uzaklığı `lt`, `le`, `gt`veya `ge`kullanarak bir sabit ile karşılaştırmanız gerekir. Uzaklıklar karşılaştırılırken işleçler `eq` ve `ne` desteklenmez. Örneğin, bu `geo.distance`doğru bir kullanımdır: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>coğrafi. kesişiyor

`geo.intersects` işlevi `Edm.GeographyPoint` ve sabit `Edm.GeographyPolygon` türünde bir değişken alır ve nokta çokgenin sınırları içindeyse bir `Edm.Boolean` -- `true`, aksi takdirde `false`.

Çokgen, bir sınırlayıcı halkasını tanımlayan bir punto sırası olarak depolanan iki boyutlu bir yüzeydir (aşağıdaki [örneklere](#examples) bakın). Poligonun kapatılması gerekir, yani ilk ve son nokta kümeleri aynı olmalıdır. [Bir çokgen Içindeki noktaların saatin ters sırada olması gerekir](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>180. Meridyen 'i kapsayan coğrafi uzamsal sorgular ve çokgenler

Çok sayıda coğrafi uzamsal sorgu kitaplığı, 180. meriyen 'i (Dateline yakınında) içeren bir sorguyu formül dışında sınırlar veya bir geçici çözüm gerektirir (örneğin, her iki tarafında da çokgeni ikiye bölmek gibi).

Azure Bilişsel Arama 'de, sorgu şekli dikdörtgen ise ve koordinatlarınız boylam ve Enlem (örneğin, `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`) üzerinde bir ızgara düzenine hizalandıysanız, 180 derece boylam içeren coğrafi uzamsal sorgular beklendiği gibi çalışır. Aksi halde, dikdörtgen olmayan veya hizalanmamış şekiller için bölünmüş Çokgen yaklaşımını göz önünde bulundurun.  

### <a name="geo-spatial-functions-and-null"></a>Coğrafi uzamsal işlevler ve `null`

Azure Bilişsel Arama içindeki diğer tüm koleksiyon dışı alanlar gibi, `Edm.GeographyPoint` türündeki alanlar `null` değerler içerebilir. Azure Bilişsel Arama, `null`olan bir alan için `geo.intersects` değerlendirirken, sonuç her zaman `false`olur. Bu durumda `geo.distance` davranışı, bağlama göre değişir:

- Filtreler ' de bir `null` alanının `geo.distance` `null`sonuçlanır. Bu, null olmayan hiçbir değere kıyasla `null` `false`olarak değerlendirilmediği için belgenin eşleşmeyeceği anlamına gelir.
- Sonuçları **$OrderBy**kullanarak sıralarken, bir `null` alanının `geo.distance` olası en yüksek uzaklığa neden olur. Bu alan içeren belgeler, sıralama yönü `asc` kullanıldığında (varsayılan) ve diğer tüm diğer `desc`diğerlerinden daha yükseği varsa, diğer tüm diğerlerine göre sıralanır.

## <a name="examples"></a>Örnekler

### <a name="filter-examples"></a>Filtre örnekleri

Belirli bir başvuru noktasındaki 10 kiloters içindeki tüm oteller bulun (konum `Edm.GeographyPoint`türünde bir alandır):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Bir çokgen (konum `Edm.GeographyPoint`türünde bir alandır) olarak tanımlanan belirli bir görünüm penceresinin içindeki tüm oteller bulun. Çokgenin kapatıldığını unutmayın (ilk ve son nokta kümeleri aynı olmalıdır) ve [noktaların saatin tersi sırada listelenmesi gerekir](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Sıralama örnekleri

Oteller `rating`göre azalan şekilde sıralayın, sonra verilen koordinatlardan uzaklıktan sonra artan:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Oteller `search.score` ve `rating`göre azalan sırada sıralayın, sonra da aynı derecelendirmelere sahip iki otel arasında, en yakın bir tane olmak üzere verilen koordinatlardan uzaklıktan artan sırada sıralama yapın:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Azure Bilişsel Arama için OData ifade diline genel bakış](query-odata-filter-orderby-syntax.md)
- [Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md)
- [Belgeleri &#40;Azure Bilişsel Arama ara REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
