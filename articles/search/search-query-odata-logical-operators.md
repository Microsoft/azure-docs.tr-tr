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
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113192"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Azure Bilişsel Arama OData mantıksal işleçleri-`and`, `or`, `not`

Azure Bilişsel Arama 'de [OData filtre ifadeleri](query-odata-filter-orderby-syntax.md) `true` veya `false`değerlendiren Boole ifadeleridir. Bir dizi [daha basit](search-query-odata-comparison-operators.md) filtre yazarak ve bunları [Boolean ALKU](https://en.wikipedia.org/wiki/Boolean_algebra)'dan mantıksal işleçler kullanarak oluşturarak karmaşık bir filtre yazabilirsiniz:

- `and`: hem sol hem de sağ alt ifadeleri `true`değerlendirdiğinde `true` değerlendirilen bir ikili işleç.
- `or`: sol veya sağ alt ifadelerinden biri `true`olarak değerlendirilirse `true` değerlendirilen bir ikili işleç.
- `not`: alt ifadesi `false`ve tam tersi olursa `true` değerlendirilen birli bir işleç.

Bunlar, [koleksiyon işleçleri `any` ve `all`](search-query-odata-collection-operators.md)ile birlikte çok karmaşık arama ölçütlerine hızlı bir şekilde ifade edebilen filtreler oluşturmanız için izin verir.

## <a name="syntax"></a>Sözdizimi

Aşağıdaki EBNF ([Genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)), mantıksal işleçleri kullanan bir OData ifadesinin dilbilgisini tanımlar.

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

İki mantıksal ifade biçimi vardır: ikili (`and`/`or`) ve burada iki alt ifade ve birli (`not`). Alt ifadeler herhangi bir türden Boolean ifadeler olabilir:

- `Edm.Boolean` türündeki alanlar veya Aralık değişkenleri
- `geo.intersects` veya `search.ismatch` gibi `Edm.Boolean`türünde değerler döndüren işlevler
- `rating gt 4` gibi [Karşılaştırma ifadeleri](search-query-odata-comparison-operators.md)
- `Rooms/any(room: room/Type eq 'Deluxe Room')` gibi [koleksiyon ifadeleri](search-query-odata-collection-operators.md)
- Boolean sabit değerleri `true` veya `false`.
- `and`, `or`ve `not`kullanılarak oluşturulan diğer mantıksal ifadeler.

> [!IMPORTANT]
> Tüm alt ifade türlerinin, özellikle lambda ifadeleri içinde `and`/`or`ile kullanılabileceğini bazı durumlar vardır. Ayrıntılar için bkz. [Azure 'Da OData koleksiyon işleçleri bilişsel arama](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Mantıksal işleçler ve `null`

İşlevler ve karşılaştırmalar gibi çoğu Boole ifadesi `null` değer üretemiyor ve mantıksal işleçler doğrudan `null` değişmez değerine uygulanamaz (örneğin, `x and null` izin verilmez). Ancak, Boolean alanları `null`olabilir, bu nedenle `and`, `or`ve `not` işleçlerinin null durumunda nasıl davrandığına dikkat etmeniz gerekir. Bu, `b` `Edm.Boolean`türünde bir alan olan aşağıdaki tabloda özetlenmiştir:

| İfadeler | `b` `null` olduğunda sonuç |
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

Bir Boole alanı `b` bir filtre ifadesinde kendisiyle göründüğünde, `b eq true`yazılmış gibi davranır, bu nedenle `b` `null`, ifade `false`olarak değerlendirilir. Benzer şekilde, `not b` `not (b eq true)`gibi davranır, bu nedenle `true`olarak değerlendirilir. Bu şekilde, `null` alanları `false`ile aynı şekilde davranır. Bu, yukarıdaki tabloda gösterildiği gibi `and` ve `or`kullanarak diğer ifadelerle birleştirildiğinde nasıl davrandıklarından tutarlıdır. Buna rağmen, `false` (`b eq false`) bir doğrudan karşılaştırması hala `false`olarak değerlendirilir. Diğer bir deyişle, Boolean ifadelerde gibi davransa bile, `null` `false`eşit değildir.

## <a name="examples"></a>Örnekler

`rating` alanın 3 ile 5 arasında (dahil) olduğu belgeleri eşleştirin:

    rating ge 3 and rating le 5

`ratings` alanının tüm öğelerinin 3 ' ten az veya 5 ' ten büyük olduğu belgeleri eşleştirin:

    ratings/all(r: r lt 3 or r gt 5)

`location` alanın verilen çokgen içinde olduğu belgelerde ve belge "public" terimini içermiyorsa eşleştirin.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

160 'den düşük bir temel fiyata sahip olan bir lüks, Kanada 'daki otel belgelerini eşleştirin:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Azure Bilişsel Arama için OData ifade diline genel bakış](query-odata-filter-orderby-syntax.md)
- [Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md)
- [Belgeleri &#40;Azure Bilişsel Arama ara REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
