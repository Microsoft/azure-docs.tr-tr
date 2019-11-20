---
title: OData filtre başvurusu
titleSuffix: Azure Cognitive Search
description: OData dil başvurusu ve Azure Bilişsel Arama sorgularında filtre ifadeleri oluşturmak için kullanılan tam sözdizimi.
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113206"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Azure Bilişsel Arama 'de OData $filter söz dizimi

Azure Bilişsel Arama, tam metin arama terimlerinin yanı sıra bir arama sorgusuna ek ölçütler uygulamak için [OData Filtre ifadelerini](query-odata-filter-orderby-syntax.md) kullanır. Bu makalede, filtrelerin ayrıntılı sözdizimi ayrıntıları açıklanmıştır. Filtrelerin ne olduğu ve belirli sorgu senaryolarına yönelik olarak nasıl kullanılacağı hakkında daha fazla genel bilgi için bkz. [Azure bilişsel arama filtreleri](search-filters.md).

## <a name="syntax"></a>Sözdizimi

OData dilinde bir filtre, aşağıdaki EBNF ([Genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) tarafından gösterildiği gibi, bir Boolean ifadedir ve bu da birçok ifade türünden biri olabilir:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Etkileşimli bir sözdizimi diyagramı da kullanılabilir:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Tüm EBNF için bkz. [Azure bilişsel arama Için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md) .

Boolean ifadelerinin türleri şunlardır:

- `any` veya `all`kullanarak koleksiyon filtre ifadeleri. Bunlar koleksiyon alanlarına filtre ölçütlerini uygular. Daha fazla bilgi için bkz. [Azure bilişsel arama OData koleksiyon işleçleri](search-query-odata-collection-operators.md).
- İşleçler `and`, `or`ve `not`kullanarak diğer Boolean ifadeleri birleştiren mantıksal ifadeler. Daha fazla bilgi için bkz. [Azure 'Da OData mantıksal işleçleri bilişsel arama](search-query-odata-logical-operators.md).
- İşleç `eq`, `ne`, `gt`, `lt`, `ge`ve `le`kullanarak alanları veya Aralık değişkenlerini sabit değerlerle karşılaştıran Karşılaştırma ifadeleri. Daha fazla bilgi için bkz. [Azure 'Da OData karşılaştırma işleçleri bilişsel arama](search-query-odata-comparison-operators.md). Karşılaştırma ifadeleri, `geo.distance` işlevi kullanılarak coğrafi uzamsal koordinatlar arasındaki uzaklıkları karşılaştırmak için de kullanılır. Daha fazla bilgi için bkz. [Azure bilişsel arama 'Da OData coğrafi uzamsal işlevleri](search-query-odata-geo-spatial-functions.md).
- Boolean sabit değerleri `true` ve `false`. Program aracılığıyla filtre oluştururken bu sabitler yararlı olabilir, ancak Aksi takdirde uygulamada kullanılmayın.
- Aşağıdakiler dahil olmak üzere, Boole işlevlerine yapılan çağrılar:
  - `geo.intersects`, belirli bir noktanın belirli bir çokgen içinde olup olmadığını test eder. Daha fazla bilgi için bkz. [Azure bilişsel arama 'Da OData coğrafi uzamsal işlevleri](search-query-odata-geo-spatial-functions.md).
  - bir alan veya Aralık değişkenini bir değer listesindeki her bir değerle karşılaştıran `search.in`. Daha fazla bilgi için bkz. [Azure bilişsel arama 'de OData `search.in` işlevi](search-query-odata-search-in-function.md).
  - `search.ismatch` ve `search.ismatchscoring`bir filtre bağlamında tam metin arama işlemlerini yürütür. Daha fazla bilgi için bkz. [Azure bilişsel arama 'Da OData tam metin arama işlevleri](search-query-odata-full-text-search-functions.md).
- `Edm.Boolean`türündeki alan yolları veya Aralık değişkenleri. Örneğin, dizininizin `IsEnabled` adlı bir Boole alanı varsa ve bu alanın `true`olduğu tüm belgeleri döndürmek istiyorsanız, filtre ifadeniz yalnızca ad `IsEnabled`olabilir.
- Parantez içinde Boole ifadeleri. Parantez kullanımı, bir filtredeki işlem sırasını açıkça belirlemede yardımcı olabilir. OData işleçlerinin varsayılan önceliği hakkında daha fazla bilgi için sonraki bölüme bakın.

### <a name="operator-precedence-in-filters"></a>Filtrelerdeki operatör önceliği

Alt ifadelerinin etrafında parantez olmadan bir filtre ifadesi yazarsanız Azure Bilişsel Arama, bunu bir işleç öncelik kuralları kümesine göre değerlendirir. Bu kurallar, alt ifadeleri birleştirmek için kullanılan işleçleri temel alır. Aşağıdaki tablo, işleç gruplarını en yüksekten en düşük önceliğe göre listeler:

| Grup | İşleç (ler) |
| --- | --- |
| Mantıksal işleçler | `not` |
| Karşılaştırma işleçleri | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Mantıksal işleçler | `and` |
| Mantıksal işleçler | `or` |

Yukarıdaki tabloda daha yüksek bir operatör, işlenenlerin diğer işleçlerden farklı olarak "daha sıkı bir şekilde bağlanması" olacaktır. Örneğin, `and` `or`kıyasla daha yüksek önceliğe sahiptir ve karşılaştırma işleçleri bunlardan birinden daha yüksek önceliğe sahiptir, bu nedenle aşağıdaki iki ifade eşdeğerdir:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not` işleci, karşılaştırma işleçlerinden çok daha yüksek önceliğe sahiptir. Bunun nedeni şöyle bir filtre yazmayı denerseniz:

    not Rating gt 5

Şu hata iletisini alırsınız:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Bu hata, işleç yalnızca `Rating` alanla ilişkili olduğu için (`Edm.Int32`türü tüm karşılaştırma ifadesiyle değil). Bu düzeltilme `not` işleneni parantez içine koyulamıyor:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Filtre boyutu sınırlamaları

Azure Bilişsel Arama 'e gönderebilmeniz için filtre ifadelerinin boyut ve karmaşıklık sınırı vardır. Sınırlar, filtre deyiminizdeki yan tümce sayısına göre kabaca hesaplanır. Yüzlerce yan tümce varsa, sınırı aşmaya risk altında olursunuz. Uygulamanızı, sınırsız boyut filtresi üretmeyen bir şekilde tasarlamayı öneririz.

> [!TIP]
> Bir işlev çağrısı tek bir yan tümce olarak sayıldığından [`search.in` işlevi](search-query-odata-search-in-function.md) , eşitlik karşılaştırmalarının uzun ayırt edici olması yerine, filtre yan tümcesi sınırından kaçınmaya yardımcı olabilir.

## <a name="examples"></a>Örnekler

4 ' ten veya daha fazla derecelendirilen $200 ' dan düşük bir temel fiyatı olan en az bir odaya sahip tüm oteller bulun:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

2010 tarihinden itibaren giderilmiş olan "Sea View Motel" dışındaki tüm oteller bulun:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

2010 veya üzeri bir sürümde bulunan tüm oteller bulun. DateTime değişmez değeri, Pasifik standart saati için saat dilimi bilgilerini içerir:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Park eden tüm oteller ve tüm odaların sigpasız olduğunu bulun:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- veya-  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Merkezlerini veya park alanı dahil olmak üzere tüm oteller bulun ve 5 derecelendirmesine sahiptir:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

En az bir odada "WiFi" etiketiyle tüm oteller bulun (her odada bir `Collection(Edm.String)` alanında depolanan Etiketler bulunur):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Tüm odaları olan tüm otelleri bul:  

    $filter=Rooms/any()

Oda olmayan tüm oteller bulun:

    $filter=not Rooms/any()

Belirli bir başvuru noktasındaki 10 kiloters içindeki tüm oteller bulun (`Location` `Edm.GeographyPoint`türünde bir alandır):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Bir çokgen (`Location` Edm. Geographyıpoint türünde bir alandır) olarak tanımlanan belirli bir görünüm penceresinin içindeki tüm oteller bulun. Çokgen kapatılmalıdır, yani birinci ve son nokta kümelerinin aynı olması gerekir. Ayrıca, [noktaların saatin tersi sırada listelenmesi gerekir](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

"Description" alanının null olduğu tüm oteller bulun. Alan, hiç ayarlanmamışsa veya açıkça null olarak ayarlandıysa null olur:  

    $filter=Description eq null

Adı ' Sea View Motel ' veya ' bütçe otel ' değerine eşit olan tüm oteller bulun. Bu ifadeler boşluk içerir ve boşluk varsayılan sınırlayıcıdır. Üçüncü dize parametresi olarak tek tırnak içinde alternatif bir sınırlayıcı belirtebilirsiniz:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Adı ' | ' ile ayrılmış ' Sea View Motel ' veya ' bütçe otel ' değerine eşit olan tüm oteller bulun:  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Tüm odalar ' WiFi ' veya ' Tub ' etiketine sahip tüm oteller bulun:

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Etiketlerde ' ısıtılan tocekliler ' veya ' ince kurutucu dahil ' gibi bir koleksiyonda eşleşme bulun.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

"Su ön" kelimesiyle belge bulun. Bu filtre sorgusu, `search=waterfront`olan bir [arama isteğiyle](https://docs.microsoft.com/rest/api/searchservice/search-documents) özdeştir.

    $filter=search.ismatchscoring('waterfront')

"Hostel" sözcüğünü içeren belgeleri bulun ve 4 ' e eşit veya daha büyük derecelendirme veya "Motel" sözcüğünü ve derecesi 5 ' e eşit olan belgeleri bulun. Bu istek, `or`kullanarak filtre işlemleriyle tam metin aramasını birleştirilemediğinden, `search.ismatchscoring` işlevi olmadan ifade edilemedi.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

"Merkezlerini" sözcüğü olmadan belge bul.

    $filter=not search.ismatch('luxury')

"Okyanus görünümü" veya derecelendirme 5 ' e eşit olan belgeleri bulun. `search.ismatchscoring` sorgusu yalnızca `HotelName` ve `Description`alanlara karşı yürütülür. Disbirleşimin yalnızca ikinci yan tümcesiyle eşleşen belgeler, 5 ' e eşit `Rating` için de çok--oteller döndürülür. Bu belgeler, ifadenin puanlanmış parçalarından hiçbiriyle eşleşmediğinizden emin olmak için puanı sıfıra eşit olacak şekilde döndürülür.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

"Otel" ve "Havaalanı" koşullarının, açıklamada beş sözcükten fazla olmadığı ve tüm odaların sigpasız olduğu oteller bulun. Bu sorgu, [tam Lucene sorgu dilini](query-lucene-syntax.md)kullanır.

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Azure Bilişsel Arama için OData ifade diline genel bakış](query-odata-filter-orderby-syntax.md)
- [Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md)
- [Belgeleri &#40;Azure Bilişsel Arama ara REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
