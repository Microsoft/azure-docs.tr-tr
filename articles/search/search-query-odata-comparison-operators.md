---
title: OData karşılaştırma işleci başvurusu
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama sorgularında OData karşılaştırma işleçleri (EQ, ne, gt, lt, GE ve Le) kullanılmasına yönelik sözdizimi ve başvuru belgeleri.
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
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113213"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Azure Bilişsel Arama OData karşılaştırma işleçleri `eq`, `ne`, `gt`, `lt`, `ge`ve `le`

Azure Bilişsel Arama 'deki bir [OData filtre ifadesinde](query-odata-filter-orderby-syntax.md) en temel işlem, bir alanı verilen değerle karşılaştırmaktır. İki tür karşılaştırma yapılabilir--eşitlik karşılaştırması ve Aralık karşılaştırması. Bir alanı sabit değerle karşılaştırmak için aşağıdaki işleçleri kullanabilirsiniz:

Eşitlik işleçleri:

- `eq`: bir alanın sabit değere **eşit** olup olmadığını test edin
- `ne`: bir alanın sabit değere **eşit** olup olmadığını test edin

Aralık işleçleri:

- `gt`: bir alanın sabit değerden **büyük** olup olmadığını test edin
- `lt`: bir alanın sabit değerden **küçük** olup olmadığını test edin
- `ge`: bir alanın sabit değere **eşit veya ondan büyük** olup olmadığını test edin
- `le`: bir alanın sabit değere **eşit veya ondan küçük** olup olmadığını test edin

Bir alanın belirli bir değer aralığı içinde olup olmadığını test etmek için, Aralık işleçlerini [mantıksal işleçlerle](search-query-odata-logical-operators.md) birlikte kullanabilirsiniz. Bu makalenin devamındaki [örneklere](#examples) bakın.

> [!NOTE]
> İsterseniz, sabit değeri işlecin sol tarafına ve alan adını sağ tarafta koyabilirsiniz. Aralık işleçleri için karşılaştırma anlamı tersine çevrilir. Örneğin, sabit değer Solsa, `gt` sabit değerin alandan büyük olup olmadığını test edecektir. Karşılaştırma işleçlerini, `geo.distance`gibi bir işlevin sonucunu karşılaştırmak için de kullanabilirsiniz. `search.ismatch`gibi Boole işlevleri için, sonucu `true` veya `false` olarak karşılaştırmak isteğe bağlıdır.

## <a name="syntax"></a>Sözdizimi

Aşağıdaki EBNF ([Genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)), karşılaştırma işleçlerini kullanan bir OData ifadesinin dilbilgisini tanımlar.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Etkileşimli bir sözdizimi diyagramı da kullanılabilir:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Tüm EBNF için bkz. [Azure bilişsel arama Için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md) .

İki karşılaştırma ifadesi biçimi vardır. Bunlar arasındaki tek fark, işlecin, işlecin sol veya sağ tarafında görünüp görüntülenmemesidir. İşlecin diğer tarafındaki ifade bir **değişken** veya işlev çağrısı olmalıdır. Bir değişken, bir alan adı ya da bir [lambda ifadesi](search-query-odata-collection-operators.md)söz konusu olduğunda bir Aralık değişkeni olabilir.

## <a name="data-types-for-comparisons"></a>Karşılaştırmalar için veri türleri

Karşılaştırma işlecinin her iki tarafındaki veri türleri uyumlu olmalıdır. Örneğin, sol kenar `Edm.DateTimeOffset`türünde bir alan ise, sağ kenar bir tarih-saat sabiti olmalıdır. Sayısal veri türleri daha esnektir. Aşağıdaki tabloda açıklandığı gibi, herhangi bir sayısal türdeki değişkenleri ve işlevleri diğer herhangi bir sayısal tür sabitleri ile karşılaştırabilirsiniz.

| Değişken veya işlev türü | Sabit değer türü | Sınırlamalar |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Karşılaştırma, [`NaN`için özel kurallara](#special-case-nan) tabidir |
| `Edm.Double` | `Edm.Int64` | Sabit, `Edm.Double`'e dönüştürülüp büyük boyutlu değerler için duyarlık kaybı ile sonuçlanır |
| `Edm.Double` | `Edm.Int32` | yok |
| `Edm.Int64` | `Edm.Double` | `NaN`, `-INF`veya `INF` karşılaştırmalara izin verilmez |
| `Edm.Int64` | `Edm.Int64` | yok |
| `Edm.Int64` | `Edm.Int32` | Sabit, karşılaştırmadan önce `Edm.Int64` dönüştürülür |
| `Edm.Int32` | `Edm.Double` | `NaN`, `-INF`veya `INF` karşılaştırmalara izin verilmez |
| `Edm.Int32` | `Edm.Int64` | yok |
| `Edm.Int32` | `Edm.Int32` | yok |

`Edm.Int64` bir alanı `NaN`olarak karşılaştıran izin verilmeyen karşılaştırmalar için, Azure Bilişsel Arama REST API bir "HTTP 400: Hatalı Istek" hatası döndürür.

> [!IMPORTANT]
> Sayısal tür karşılaştırmaları esnek olsa da, sabit değerin karşılaştırıldığı değişken veya işlevle aynı veri türü olması için filtrelerdeki karşılaştırmalar yazmanız önemle önerilir. Bu, özellikle de duyarlık kaybı olan örtük dönüştürmeler mümkün olduğunda kayan nokta ve tamsayı değerlerini karıştırdığınızda önemlidir.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>`null` ve `NaN` için özel durumlar

Karşılaştırma işleçlerini kullanırken, Azure Bilişsel Arama tüm koleksiyon olmayan alanların `null`olabileceğini unutmamak önemlidir. Aşağıdaki tabloda her iki tarafın de `null`bir karşılaştırma ifadesi için olası tüm sonuçlar gösterilmektedir:

| İşleç | Yalnızca alan veya değişken `null` olduğunda sonuç | Yalnızca sabit `null` olduğunda sonuç | Hem alan hem de değişken ve sabit `null` olduğunda sonuç |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Hatalı Istek hatası | HTTP 400: Hatalı Istek hatası |
| `lt` | `false` | HTTP 400: Hatalı Istek hatası | HTTP 400: Hatalı Istek hatası |
| `ge` | `false` | HTTP 400: Hatalı Istek hatası | HTTP 400: Hatalı Istek hatası |
| `le` | `false` | HTTP 400: Hatalı Istek hatası | HTTP 400: Hatalı Istek hatası |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Özet ' te, `null` yalnızca kendine eşittir ve diğer herhangi bir değerden daha az veya daha büyük değildir.

Dizininizdeki `Edm.Double` türünde alanlar varsa ve `NaN` değerlerini bu alanlara yüklerseniz, filtre yazarken bu hesaba hesap eklemeniz gerekir. Azure Bilişsel Arama, `NaN` değerlerini işlemek için IEEE 754 standardını uygular ve bu tür değerlerle karşılaştırmalar, aşağıdaki tabloda gösterildiği gibi belirgin olmayan sonuçlar üretir.

| İşleç | En az bir işlenen `NaN` sonuç |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Özet içinde `NaN`, kendisi dahil olmak üzere herhangi bir değere eşit değildir.

### <a name="comparing-geo-spatial-data"></a>Coğrafi uzamsal verileri karşılaştırma

`Edm.GeographyPoint` türünde bir alanı doğrudan sabit bir değerle karşılaştıramazsınız, ancak `geo.distance` işlevini kullanabilirsiniz. Bu işlev `Edm.Double`türünde bir değer döndürür, böylece sabit coğrafi uzamsal koordinatlardan uzaklıktan yola çıkarak filtrelemek için sayısal bir sabitle karşılaştırabilirsiniz. Aşağıdaki [örneklere](#examples) bakın.

### <a name="comparing-string-data"></a>Dize verilerini karşılaştırma

Dizeler, `eq` ve `ne` işleçlerini kullanarak tam eşleşmelerin filtrelerinde karşılaştırılabilir. Bu karşılaştırmalar büyük/küçük harfe duyarlıdır.

## <a name="examples"></a>Örnekler

`Rating` alanın 3 ile 5 arasında (dahil) olduğu belgeleri eşleştirin:

    Rating ge 3 and Rating le 5

`Location` alanın, verilen Enlem ve Boylam 'den 2 kilometreden az olduğu belgeleri eşleştirin:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

`LastRenovationDate` alanın 1 Ocak 2015, gece yarısı UTC 'den büyük veya bu değere eşit olduğu belgeleri eşleştirin:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

`Details/Sku` alanın `null`olmadığı belge eşleştir:

    Details/Sku ne null

En az bir odada "Deluxe Oda" türünde olduğu ve `Rooms/Type` alanının dizesinin filtreye tam olarak eşleştiği, otel için belge eşleştir:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Azure Bilişsel Arama için OData ifade diline genel bakış](query-odata-filter-orderby-syntax.md)
- [Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md)
- [Belgeleri &#40;Azure Bilişsel Arama ara REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
