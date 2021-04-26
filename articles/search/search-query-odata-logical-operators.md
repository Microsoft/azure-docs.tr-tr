---
title: OData mantıksal işleç başvurusu
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama sorgularında OData mantıksal işleçleri ve, veya, ve değil, için sözdizimi ve başvuru belgeleri.
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
ms.openlocfilehash: 27d5427d34de591f9cfeab2310d79a2fde217624
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88917882"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Azure Bilişsel Arama OData mantıksal işleçleri- `and` , `or` , `not`

Azure Bilişsel Arama 'de [OData filtre ifadeleri](query-odata-filter-orderby-syntax.md) , veya sonucunu veren Boole ifadeleridir `true` `false` . Bir dizi [daha basit](search-query-odata-comparison-operators.md) filtre yazarak ve bunları [Boolean ALKU](https://en.wikipedia.org/wiki/Boolean_algebra)'dan mantıksal işleçler kullanarak oluşturarak karmaşık bir filtre yazabilirsiniz:

- `and`: `true` Hem sol hem de sağ alt ifadelerinin olarak değerlendirilmesi için değerlendirilen bir ikili işleç `true` .
- `or`: `true` Sol veya sağ alt ifadelerden birinin olarak değerlendirilip değerlendirilmeyeceğini değerlendiren bir ikili işleç `true` .
- `not`: `true` Alt ifadesi olarak değerlendirilen `false` ve tam tersi olarak değerlendirilen birli bir işleç.

Bunlar, [koleksiyon işleçleri `any` ve `all` ](search-query-odata-collection-operators.md)ile birlikte çok karmaşık arama ölçütlerine hızlı bir şekilde ifade edebilen filtreler oluşturmanız için izin verir.

## <a name="syntax"></a>Syntax

Aşağıdaki EBNF ([genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) mantıksal işleçleri kullanan bir OData ifadesinin dilbilgisini tanımlar.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Etkileşimli bir sözdizimi diyagramı da kullanılabilir:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Tüm EBNF için bkz. [Azure bilişsel arama Için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md) .

İki mantıksal ifade biçimi vardır: ikili (), `and` / `or` burada iki alt ifade ve birli () vardır; `not` burada yalnızca bir tane vardır. Alt ifadeler herhangi bir türden Boolean ifadeler olabilir:

- Türündeki alanlar veya Aralık değişkenleri `Edm.Boolean`
- Türü değer döndüren işlevler, örneğin `Edm.Boolean` `geo.intersects` veya `search.ismatch`
- [Karşılaştırma ifadeleri](search-query-odata-comparison-operators.md), örneğin `rating gt 4`
- Gibi [koleksiyon ifadeleri](search-query-odata-collection-operators.md)`Rooms/any(room: room/Type eq 'Deluxe Room')`
- Boolean sabit değerleri `true` veya `false` .
- , Ve kullanılarak oluşturulan diğer mantıksal ifadeler `and` `or` `not` .

> [!IMPORTANT]
> `and` / `or` Özellikle lambda ifadeleri içinde, tüm alt ifade türlerinin birlikte kullanılabileceği bazı durumlar vardır. Ayrıntılar için bkz. [Azure 'Da OData koleksiyon işleçleri bilişsel arama](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Mantıksal işleçler ve `null`

İşlevler ve karşılaştırmalar gibi çoğu Boole ifadesi değer üretemiyor `null` ve mantıksal işleçler `null` doğrudan sabit değere uygulanamaz (örneğin, `x and null` buna izin verilmez). Ancak, Boolean alanları olabilir `null` , bu nedenle, `and` `or` ve işleçlerinin, `not` null durumunda nasıl davrandığına dikkat etmeniz gerekir. Bu, aşağıdaki tabloda özetlenmiştir, burada, `b` türünde bir alandır `Edm.Boolean` :

| Expression | `b`Ne zaman sonuç`null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Bir Boole alanı `b` bir filtre ifadesinde kendisiyle göründüğünde, yazılmış gibi davranır `b eq true` , `b` yani ise `null` ifadesi olarak değerlendirilir `false` . Benzer şekilde, `not b` gibi davranır `not (b eq true)` , olarak değerlendirilir `true` . Bu şekilde, `null` alanlar aynı şekilde davranır `false` . Bu, `and` `or` Yukarıdaki tabloda gösterildiği gibi ve kullanan diğer ifadelerle birleştirildiğinde, nasıl davrandıklarından tutarlıdır. Buna rağmen, () öğesine doğrudan `false` karşılaştırma `b eq false` olmaya devam edecektir `false` . Diğer bir deyişle, `null` `false` Boolean ifadelerde gibi davransa bile, öğesine eşit değildir.

## <a name="examples"></a>Örnekler

`rating`Alanın 3 ile 5 arasında (dahil) olduğu belgeleri eşleştirin:

```odata-filter-expr
    rating ge 3 and rating le 5
```

Alanın tüm öğelerinin `ratings` 3 ' ten küçük veya 5 ' ten büyük olduğu belgeleri eşleştirin:

```odata-filter-expr
    ratings/all(r: r lt 3 or r gt 5)
```

`location`Alanın verilen çokgen içinde bulunduğu belgeleri eşleştirin ve belge "genel" terimini içermez.

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')
```

160 'den düşük bir temel fiyata sahip olan bir lüks, Kanada 'daki otel belgelerini eşleştirin:

```odata-filter-expr
    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)
```

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Azure Bilişsel Arama için OData ifade diline genel bakış](query-odata-filter-orderby-syntax.md)
- [Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md)
- [Azure Bilişsel Arama REST API &#40;belgelerde arama yapın&#41;](/rest/api/searchservice/Search-Documents)