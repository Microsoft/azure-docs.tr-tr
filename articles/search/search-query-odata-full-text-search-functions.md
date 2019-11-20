---
title: OData tam metin arama işlevi başvurusu
titleSuffix: Azure Cognitive Search
description: OData tam metin arama işlevleri, Azure Bilişsel Arama sorgularında Search. IsMatch ve Search. ısmatchpuanlama.
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
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793359"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Azure Bilişsel Arama 'de OData tam metin arama işlevleri-`search.ismatch` ve `search.ismatchscoring`

Azure Bilişsel Arama, `search.ismatch` ve `search.ismatchscoring` işlevleri aracılığıyla [OData filtre ifadeleri](query-odata-filter-orderby-syntax.md) bağlamında tam metin aramayı destekler. Bu işlevler, tam metin aramasını, yalnızca [Arama API](https://docs.microsoft.com/rest/api/searchservice/search-documents)'sinin en üst düzey `search` parametresi kullanılarak mümkün olmayan yollarla birleştirmenize olanak tanır.

> [!NOTE]
> `search.ismatch` ve `search.ismatchscoring` işlevleri yalnızca [Arama API 'sindeki](https://docs.microsoft.com/rest/api/searchservice/search-documents)filtrelerde desteklenir. Bunlar, [öneri](https://docs.microsoft.com/rest/api/searchservice/suggestions) veya [otomatik tamamlama](https://docs.microsoft.com/rest/api/searchservice/autocomplete) API 'lerinde desteklenmez.

## <a name="syntax"></a>Sözdizimi

Aşağıdaki EBNF ([Genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) `search.ismatch` ve `search.ismatchscoring` işlevlerinin dilbilgisini tanımlar:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Etkileşimli bir sözdizimi diyagramı da kullanılabilir:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Tüm EBNF için bkz. [Azure bilişsel arama Için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md) .

### <a name="searchismatch"></a>Search. IsMatch

`search.ismatch` işlevi bir tam metin arama sorgusunu bir filtre ifadesinin parçası olarak değerlendirir. Arama sorgusuyla eşleşen belgeler sonuç kümesinde döndürülür. Bu işlevin aşağıdaki aşırı yüklemeleri kullanılabilir:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametreler aşağıdaki tabloda tanımlanmıştır:

| Parametre adı | Tür | Açıklama |
| --- | --- | --- |
| `search` | `Edm.String` | Arama sorgusu ( [basit](query-simple-syntax.md) veya [tam](query-lucene-syntax.md) Lucene sorgu sözdiziminde). |
| `searchFields` | `Edm.String` | Arama yapılacak aranabilir alanların virgülle ayrılmış listesi; dizindeki tüm aranabilir alanları varsayılan olarak belirler. `search` parametresinde alanlar arasında [arama](query-lucene-syntax.md#bkmk_fields) kullanılırken, Lucene sorgusundaki alan belirticileri bu parametrede belirtilen tüm alanları geçersiz kılar. |
| `queryType` | `Edm.String` | `'simple'` veya `'full'`; Varsayılan olarak `'simple'`olur. `search` parametresinde hangi sorgu dilinin kullanıldığını belirtir. |
| `searchMode` | `Edm.String` | `'any'` veya `'all'`, varsayılan olarak `'any'`. Belgeyi eşleşme olarak saymak için `search` parametresindeki arama terimlerinin herhangi birinin veya tümünün eşleşmesi gerekip gerekmediğini gösterir. `search` parametresinde [Lucene Boolean işleçleri](query-lucene-syntax.md#bkmk_boolean) kullanıldığında, bu parametre üzerinden öncelikli olur. |

Yukarıdaki parametrelerin tümü, [Arama API 'sindeki karşılık gelen arama isteği parametrelerine](https://docs.microsoft.com/rest/api/searchservice/search-documents)eşdeğerdir.

`search.ismatch` işlevi `Edm.Boolean`türünde bir değer döndürür. Bu, Boolean [mantıksal işleçleri](search-query-odata-logical-operators.md)kullanarak diğer filtre alt ifadeleriyle bunu oluşturmanıza olanak sağlar.

> [!NOTE]
> Azure Bilişsel Arama, lambda ifadeleri içinde `search.ismatch` veya `search.ismatchscoring` kullanmayı desteklemez. Bu, tam metin arama eşleşmelerini aynı nesne üzerinde kesin filtre eşleşmeleri ile ilişkilendirebileceği nesne koleksiyonları üzerine filtre yazmak mümkün olmadığı anlamına gelir. Bu sınırlama hakkında daha fazla bilgi ve örnek için bkz. [Azure bilişsel arama 'da koleksiyon filtrelerinde sorun giderme](search-query-troubleshoot-collection-filters.md). Bu sınırlamanın neden varolduğu hakkında daha ayrıntılı bilgi için bkz. [Azure bilişsel arama 'da koleksiyon filtrelerini anlama](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>arama. ısmatchpuanlama

`search.ismatch` işlevi gibi `search.ismatchscoring` işlevi, parametre olarak geçirilmiş tam metin arama sorgusuyla eşleşen belgeler için `true` döndürür. Aralarındaki fark, `search.ismatchscoring` sorgusuyla eşleşen belgelerin ilgi puanının Genel belge puanına katkıda bulunabilmesine karşın, `search.ismatch`durumda belge puanı değiştirilmez. Bu işlevin aşağıdaki aşırı yüklemeleri, `search.ismatch`benzer parametrelerle kullanılabilir:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

`search.ismatch` ve `search.ismatchscoring` işlevleri aynı filtre ifadesinde kullanılabilir.

## <a name="examples"></a>Örnekler

"Su ön" kelimesiyle belge bulun. Bu filtre sorgusu, `search=waterfront`olan bir [arama isteğiyle](https://docs.microsoft.com/rest/api/searchservice/search-documents) özdeştir.

    search.ismatchscoring('waterfront')

"Hostel" sözcüğünü içeren belgeleri bulun ve 4 ' e eşit veya daha büyük derecelendirme veya "Motel" sözcüğünü ve derecesi 5 ' e eşit olan belgeleri bulun. Bu isteğin `search.ismatchscoring` işlevi olmadan ifade edilenemediğini not edin.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

"Merkezlerini" sözcüğü olmadan belge bul.

    not search.ismatch('luxury')

"Okyanus görünümü" veya derecelendirme 5 ' e eşit olan belgeleri bulun. `search.ismatchscoring` sorgusu yalnızca `HotelName` ve `Rooms/Description`alanlara karşı yürütülür.

Disbirleşimin yalnızca ikinci yan tümcesiyle eşleşen belgeler, 5 ' e eşit `Rating` için de çok--oteller döndürülür. Bu belgelerin, ifadenin puanlanmış parçalarından hiçbiriyle eşleşmediğinden emin olmak için, puanın sıfıra eşit olarak döndürülmeleri gerekir.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

"Otel" ve "Havaalanı" koşullarının, otel açıklamasına ait 5 sözcükten ve her odada en az bir kısmında izin verilmediği belgeleri bulun. Bu sorgu, [tam Lucene sorgu dilini](query-lucene-syntax.md)kullanır.

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Azure Bilişsel Arama için OData ifade diline genel bakış](query-odata-filter-orderby-syntax.md)
- [Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md)
- [Belgeleri &#40;Azure Bilişsel Arama ara REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
