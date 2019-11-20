---
title: OData search.in işlev başvurusu
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama sorgularında search.in işlevinin kullanılmasına yönelik sözdizimi ve başvuru belgeleri.
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113110"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Azure Bilişsel Arama OData `search.in` işlevi

[OData filtre ifadelerinde](query-odata-filter-orderby-syntax.md) yaygın bir senaryo, her bir belgedeki tek bir alanın olası birçok değerden birine eşit olup olmadığını denetlemenize olanak tanır. Örneğin, bu, bir veya daha fazla asıl kimliği içeren bir alanı, sorguyu veren kullanıcıyı temsil eden bir asıl kimlik listesine göre denetleyerek, bazı uygulamalar [güvenlik kırpması](search-security-trimming-for-azure-search.md) uygular. Bunun gibi bir sorgu yazmanın bir yolu, [`eq`](search-query-odata-comparison-operators.md) ve [`or`](search-query-odata-logical-operators.md) işleçlerini kullanmaktır:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Ancak, `search.in` işlevini kullanarak bunu yazmanın daha kısa bir yolu vardır:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> `search.in` kullanımı, daha kısa ve kolay bir şekilde, Ayrıca, [](#bkmk_performance) filtre içinde içerilecek yüzlerce veya hatta binlerce değer olduğunda [filtrelerin belirli boyut sınırlamalarını](search-query-odata-filter.md#bkmk_limits) önler. Bu nedenle, eşitlik ifadelerinin daha karmaşık bir birleşimi yerine `search.in` kullanmanızı kesinlikle öneririz.

> [!NOTE]
> OData Standard 'ın 4,01 sürümü yakın zamanda [`in` işlecini](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)kullanıma sunmuştur ve bu da Azure bilişsel arama `search.in` işlevi olarak benzer davranışa sahiptir. Ancak Azure Bilişsel Arama bu işleci desteklemez, bu nedenle bunun yerine `search.in` işlevini kullanmanız gerekir.

## <a name="syntax"></a>Sözdizimi

Aşağıdaki EBNF ([Genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) `search.in` işlevinin dilbilgisini tanımlar:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Etkileşimli bir sözdizimi diyagramı da kullanılabilir:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Tüm EBNF için bkz. [Azure bilişsel arama Için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md) .

`search.in` işlevi, belirli bir dize alanı veya Aralık değişkeninin belirli bir değer listesinden birine eşit olup olmadığını sınar. Değişken ile listedeki her bir değer arasındaki eşitlik, `eq` işleçle aynı şekilde, büyük/küçük harfe duyarlı bir biçimde belirlenir. Bu nedenle, `search.in(myfield, 'a, b, c')` gibi bir ifade `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`eşdeğerdir, ancak bu `search.in` çok daha iyi performans sağlar.

`search.in` işlevinin iki aşırı yüklemesi vardır:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametreler aşağıdaki tabloda tanımlanmıştır:

| Parametre adı | Tür | Açıklama |
| --- | --- | --- |
| `variable` | `Edm.String` | Bir dize alanı başvurusu (veya `search.in` bir `any` veya `all` ifadesi içinde kullanıldığı durumda bir dize koleksiyonu alanı üzerinde bir Aralık değişkeni). |
| `valueList` | `Edm.String` | `variable` parametresiyle eşleştirilecek bir değer listesi içeren bir dize. `delimiters` parametresi belirtilmemişse, varsayılan sınırlayıcılar boşluk ve virgüldür. |
| `delimiters` | `Edm.String` | `valueList` parametresi ayrıştırılırken her karakterin ayırıcı olarak değerlendirilme dizesi. Bu parametrenin varsayılan değeri `' ,'`, aralarında boşluk ve/veya virgüller olan tüm değerlerin ayrılacağı anlamına gelir. Değerleriniz bu karakterleri içerdiğinden boşluklar ve virgüller dışında ayırıcılar kullanmanız gerekiyorsa, bu parametrede `'|'` gibi alternatif sınırlayıcılar belirtebilirsiniz. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>`search.in` performansı

`search.in`kullanırsanız, ikinci parametre yüzlerce veya binlerce değerin listesini içerdiğinde alt saniyelik yanıt süresini de bekleyebilir. `search.in`için geçirebilmeniz gereken öğe sayısı üzerinde hiçbir açık sınır yoktur, ancak hala maksimum istek boyutuyla sınırlı olabilirsiniz. Ancak, değer sayısı arttıkça gecikme artar.

## <a name="examples"></a>Örnekler

Adı ' Sea View Motel ' veya ' bütçe otel ' değerine eşit olan tüm oteller bulun. Tümcecikler varsayılan bir sınırlayıcı olan boşluklar içerir. Üçüncü dize parametresi olarak tek tırnak içinde alternatif bir sınırlayıcı belirtebilirsiniz:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Adı ' | ' ile ayrılmış ' Sea View Motel ' veya ' bütçe otel ' değerine eşit olan tüm oteller bulun:

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

' WiFi ' veya ' Tub ' etiketine sahip olan odaları içeren tüm oteller bulun:

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Etiketlerde ' ısıtılan tocekliler ' veya ' ince kurutucu dahil ' gibi bir koleksiyonda eşleşme bulun.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

' Motel ' veya ' cabin' ' etiketi olmadan tüm otelleri bul:

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Azure Bilişsel Arama için OData ifade diline genel bakış](query-odata-filter-orderby-syntax.md)
- [Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md)
- [Belgeleri &#40;Azure Bilişsel Arama ara REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
