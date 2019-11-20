---
title: OData diline genel bakış
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama sorguları için filtreler, seçme ve sıralama ölçütü için OData diline genel bakış.
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
ms.openlocfilehash: e0db41098287ff011416932a0d44a1cb9f76127d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786152"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Azure 'da `$filter`, `$orderby`ve `$select` için OData diline genel bakış Bilişsel Arama

Azure Bilişsel Arama, **$Filter**, **$OrderBy**ve **$Select** ifadelerine yönelik OData ifade sözdiziminin bir alt kümesini destekler. Filtre ifadeleri sorgu ayrıştırma sırasında değerlendirilir, aramayı belirli alanlara kısıtlama veya dizin taramaları sırasında kullanılan eşleşme ölçütlerini ekleme. Sıra ifadeleri, döndürülen belgeleri sıralamak için bir sonuç kümesi üzerinde bir işleme sonrası adımı olarak uygulanır. Sonuç kümesinde hangi belge alanlarının ekleneceğini belirleyen ifadeleri seçin. Bu ifadelerin sözdizimi, **arama** parametresinde kullanılan [basit](query-simple-syntax.md) veya [tam](query-lucene-syntax.md) sorgu sözdiziminden farklıdır, ancak başvuru alanlarına yönelik sözdiziminde bir çakışma olabilir.

Bu makalede, filtrelerde, sıralamada ve Select ifadelerinde kullanılan OData ifade diline ilişkin bir genel bakış sunulmaktadır. Dil, en temel öğelerle başlayarak ve üzerinde oluşturma için "aşağıdan yukarıya" sunulmuştur. Her parametre için en üst düzey sözdizimi ayrı bir makalede açıklanmıştır:

- [$filter sözdizimi](search-query-odata-filter.md)
- [$orderby sözdizimi](search-query-odata-orderby.md)
- [$select sözdizimi](search-query-odata-select.md)

OData ifadeleri basit ve çok karmaşık olarak değişir, ancak bunların hepsi ortak öğeleri paylaşır. Azure Bilişsel Arama 'de bir OData ifadesinin en temel kısımları şunlardır:

- Dizininizdeki belirli alanlara başvuran **alan yolları**.
- Belirli bir veri türünün değişmez değeri olan **sabitler**.

> [!NOTE]
> Azure Bilişsel Arama terminoloji, [OData standbundan](https://www.odata.org/documentation/) birkaç yolla farklıdır. Azure Bilişsel Arama bir **alanı** çağırdığımız OData içinde **özellik** olarak adlandırılır ve benzer şekilde **alan yolu** ve **özellik yolu**için. Azure Bilişsel Arama 'de **belge** içeren bir **Dizin** , OData içinde **varlıkları**içeren bir **varlık kümesi** olarak daha genel olarak adlandırılır. Azure Bilişsel Arama terimleri bu başvuru boyunca kullanılır.

## <a name="field-paths"></a>Alan yolları

Aşağıdaki EBNF ([Genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) alan yollarının dilbilgisini tanımlar.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Etkileşimli bir sözdizimi diyagramı da kullanılabilir:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Tüm EBNF için bkz. [Azure bilişsel arama Için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md) .

Alan yolu, eğik çizgi ile ayrılmış bir veya daha fazla **tanımlayıcıdan** oluşur. Her tanımlayıcı, ASCII harf veya alt çizgi ile başlaması gereken bir karakter dizisidir ve yalnızca ASCII harfleri, rakamları veya alt çizgileri içermelidir. Harfler büyük veya küçük harf olabilir.

Bir tanımlayıcı, bir alanın adına veya bir filtre içindeki bir [koleksiyon ifadesinin](search-query-odata-collection-operators.md) (`any` ya da `all`) bağlamındaki bir **Aralık değişkenine** başvurabilir. Aralık değişkeni, koleksiyonun geçerli öğesini temsil eden bir döngü değişkeni gibidir. Karmaşık koleksiyonlar için, bu değişken, değişkenin alt alanlarına başvurmak için alan yollarını kullanmanın neden olduğu bir nesneyi temsil eder. Bu, birçok programlama dilinde nokta gösterimine benzerdir.

Alan yollarının örnekleri aşağıdaki tabloda gösterilmiştir:

| Alan yolu | Açıklama |
| --- | --- |
| `HotelName` | Dizinin en üst düzey alanını ifade eder |
| `Address/City` | Dizindeki bir karmaşık alanın `City` alt alanına başvurur; Bu örnekte `Edm.ComplexType` türü `Address` |
| `Rooms/Type` | Dizindeki bir karmaşık koleksiyon alanının `Type` alt alanına başvurur; Bu örnekte `Collection(Edm.ComplexType)` türü `Rooms` |
| `Stores/Address/Country` | Dizindeki bir karmaşık koleksiyon alanının `Address` alt alanının `Country` alt alanına başvurur; `Stores` türü `Collection(Edm.ComplexType)` ve `Address` bu örnekteki `Edm.ComplexType` türündedir |
| `room/Type` | `room` Range değişkeninin `Type` alt alanına, örneğin, filtre ifadesinde başvurur `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | `store` Range değişkeninin `Address` alt alanının `Country` alt alanına başvurur; Örneğin, filtre ifadesi `Stores/any(store: store/Address/Country eq 'Canada')` |

Bir alan yolunun anlamı, bağlama göre farklılık gösterir. Filtrelerdeki bir alan yolu, geçerli belgedeki bir alanın tek bir *örneğinin* değerini ifade eder. **$OrderBy**, **$Select**gibi diğer bağlamlarda veya [tam Lucene sözdiziminde ara](query-lucene-syntax.md#bkmk_fields)alanları, alan yolu alanın kendisini ifade eder. Bu fark, filtrelerdeki alan yollarını nasıl kullandığınız hakkında bazı sonuçlar içerir.

Alan yolunu `Address/City`göz önünde bulundurun. Filtre içinde, bu, "San Francisco" gibi geçerli belge için tek bir şehir anlamına gelir. Buna karşılık `Rooms/Type`, birçok Oda için `Type` alt alanı anlamına gelir (ilk odadaki "standart", ikinci Oda için "Deluxe" gibi). `Rooms/Type` alt alan `Type`tek bir *örneğine* başvurmadığından, bu, doğrudan bir filtrede kullanılamaz. Bunun yerine, Oda türünü filtrelemek için, bir Aralık değişkeni ile bir [lambda ifadesi](search-query-odata-collection-operators.md) kullanırsınız, örneğin:

    Rooms/any(room: room/Type eq 'deluxe')

Bu örnekte, Aralık değişkeni `room` `room/Type` alan yolunda görüntülenir. Bu şekilde, `room/Type` geçerli belgedeki geçerli odanın türü anlamına gelir. Bu, `Type` alt alanının tek bir örneğidir, bu nedenle doğrudan filtrede kullanılabilir.

### <a name="using-field-paths"></a>Alan yollarını kullanma

Alan yolları [Azure BILIŞSEL arama REST API](https://docs.microsoft.com/rest/api/searchservice/)'lerinin birçok parametresi için kullanılır. Aşağıdaki tabloda, kullanılabilecekleri tüm konumlar ve kullanımları üzerinde kısıtlamalar listelenmiştir:

| eklentisi | Parametre adı | Kısıtlamalar |
| --- | --- | --- |
| Dizin [Oluştur](https://docs.microsoft.com/rest/api/searchservice/create-index) veya [Güncelleştir](https://docs.microsoft.com/rest/api/searchservice/update-index) | `suggesters/sourceFields` | Hiçbiri |
| Dizin [Oluştur](https://docs.microsoft.com/rest/api/searchservice/create-index) veya [Güncelleştir](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/text/weights` | Yalnızca **aranabilir** alanlara başvurabilir |
| Dizin [Oluştur](https://docs.microsoft.com/rest/api/searchservice/create-index) veya [Güncelleştir](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/functions/fieldName` | Yalnızca **filtrelenebilir** alanlara başvurabilir |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `queryType` ne zaman `search` `full` | Yalnızca **aranabilir** alanlara başvurabilir |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Yalnızca çok **yönlü tablo** alanlarına başvurabilir |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Yalnızca **aranabilir** alanlara başvurabilir |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Yalnızca **aranabilir** alanlara başvurabilir |
| [Öner](https://docs.microsoft.com/rest/api/searchservice/suggestions) ve [AutoComplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Yalnızca bir [öneri aracı](index-add-suggesters.md) parçası olan alanlara başvurabilir |
| [Arama](https://docs.microsoft.com/rest/api/searchservice/search-documents), [önerme](https://docs.microsoft.com/rest/api/searchservice/suggestions)ve [otomatik tamamlama](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Yalnızca **filtrelenebilir** alanlara başvurabilir |
| [Arama](https://docs.microsoft.com/rest/api/searchservice/search-documents) ve [önerme](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Yalnızca **sıralanabilir** alanlara başvurabilir |
| [Arama](https://docs.microsoft.com/rest/api/searchservice/search-documents), [önerme](https://docs.microsoft.com/rest/api/searchservice/suggestions)ve [arama](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Yalnızca **alınabilir** alanlara başvurabilir |

## <a name="constants"></a>Lerde

OData içindeki sabitler, belirli bir [varlık veri modeli](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) türünün sabit değerlerdir. Azure Bilişsel Arama desteklenen türlerin listesi için bkz. [desteklenen veri türleri](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) . Koleksiyon türü sabitleri desteklenmez.

Aşağıdaki tabloda, Azure Bilişsel Arama tarafından desteklenen her bir veri türü için sabitler örnekleri gösterilmektedir:

| Veri türü | Örnek sabitler |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

Aşağıdaki EBNF ([Genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)), yukarıdaki tabloda gösterilen sabitler için dilbilgisini tanımlar. Coğrafi uzamsal türler için dilbilgisi, [Azure bilişsel arama 'Da OData coğrafi uzamsal işlevlerde](search-query-odata-geo-spatial-functions.md)bulunabilir.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Etkileşimli bir sözdizimi diyagramı da kullanılabilir:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Tüm EBNF için bkz. [Azure bilişsel arama Için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md) .

## <a name="building-expressions-from-field-paths-and-constants"></a>Alan yollarından ve sabitlerden ifadeler oluşturma

Alan yolları ve sabitler, bir OData ifadesinin en temel kısmıdır, ancak bunlar zaten tam ifadelerdir. Aslında, Azure Bilişsel Arama **$Select** parametresi Nothing, ancak alan yollarının virgülle ayrılmış bir listesidir ve **$OrderBy** **$Select**kıyasla daha karmaşık değildir. Dizininizdeki `Edm.Boolean` türünde bir alanınız varsa, bu alanın yolu ancak hiçbir şey olmayan bir filtre de yazabilirsiniz. `true` ve `false` sabitleri aynı şekilde geçerli filtrelerdir.

Ancak, çoğu zaman birden fazla alana ve sabitine başvuran daha karmaşık deyimler gerekecektir. Bu ifadeler parametreye bağlı olarak farklı şekillerde oluşturulmuştur.

Aşağıdaki EBNF ([Genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) **$Filter**, **$OrderBy**ve **$Select** parametreleri için dilbilgisini tanımlar. Bunlar alan yollarına ve sabitlere başvuran daha basit ifadelerden oluşturulmuştur:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Etkileşimli bir sözdizimi diyagramı da kullanılabilir:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Tüm EBNF için bkz. [Azure bilişsel arama Için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md) .

**$OrderBy** ve **$Select** parametreleri, daha basit ifadelerin virgülle ayrılmış listeleridir. **$Filter** parametresi, daha basit alt ifadelerden oluşan bir Boolean ifadedir. Bu alt ifadeler [`and`, `or`ve `not`](search-query-odata-logical-operators.md)gibi mantıksal işleçler, [`eq`, `lt`, `gt`, vb](search-query-odata-comparison-operators.md). gibi karşılaştırma işleçleri ve [`any` ve @no__t_10 gibi koleksiyon işleçleri kullanılarak birleştirilir _ ](search-query-odata-collection-operators.md).

**$Filter**, **$OrderBy**ve **$Select** parametreleri aşağıdaki makalelerde daha ayrıntılı bir şekilde araştırılabilir:

- [Azure Bilişsel Arama 'de OData $filter söz dizimi](search-query-odata-filter.md)
- [Azure Bilişsel Arama 'de OData $orderby söz dizimi](search-query-odata-orderby.md)
- [Azure Bilişsel Arama 'de OData $select söz dizimi](search-query-odata-select.md)

## <a name="see-also"></a>Ayrıca bkz.  

- [Azure Bilişsel Arama çok yönlü gezinme](search-faceted-navigation.md)
- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Belgeleri &#40;Azure Bilişsel Arama ara REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene sorgu söz dizimi](query-lucene-syntax.md)
- [Azure Bilişsel Arama basit sorgu söz dizimi](query-simple-syntax.md)
