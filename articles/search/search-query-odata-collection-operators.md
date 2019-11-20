---
title: OData koleksiyon işleci başvurusu
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama sorgularında filtre ifadeleri oluştururken, filtre bir koleksiyon veya karmaşık koleksiyon alanı üzerinde olduğunda Lambda ifadelerinde "Any" ve "All" işleçlerini kullanın.
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113242"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Azure Bilişsel Arama OData koleksiyon işleçleri `any` ve `all`

Azure Bilişsel Arama ile kullanmak üzere bir [OData filtresi ifadesi](query-odata-filter-orderby-syntax.md) yazarken, genellikle koleksiyon alanlarını filtrelemek yararlı olur. `any` ve `all` işleçlerini kullanarak bunu elde edebilirsiniz.

## <a name="syntax"></a>Sözdizimi

Aşağıdaki EBNF ([Genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)), `any` veya `all`kullanan bir OData ifadesinin dilbilgisini tanımlar.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Etkileşimli bir sözdizimi diyagramı da kullanılabilir:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Tüm EBNF için bkz. [Azure bilişsel arama Için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md) .

Koleksiyonları filtreleyerek üç ifade biçimi vardır.

- Koleksiyon alanı üzerinde ilk iki yineleme, bir lambda ifadesi biçiminde verilen bir koşulu koleksiyonun her öğesine uygulayarak.
  - `all` kullanan bir ifade, koşulun koleksiyonun her öğesi için true olması durumunda `true` döndürür.
  - `any` kullanan bir ifade, koşulun en az bir öğesi için true olması durumunda `true` döndürür.
- Koleksiyon filtresinin üçüncü formu, bir koleksiyon alanının boş olup olmadığını test etmek için lambda ifadesi olmadan `any` kullanır. Koleksiyonda herhangi bir öğe varsa, `true`döndürür. Koleksiyon boşsa, `false`döndürür.

Bir koleksiyon filtresindeki **lambda ifadesi** , bir programlama dilindeki Döngünün gövdesi gibidir. Yineleme sırasında koleksiyonun geçerli öğesini tutan **Aralık değişkeni**olarak adlandırılan bir değişkeni tanımlar. Ayrıca, koleksiyondaki her öğe için Aralık değişkenine uygulanacak filtre ölçütü olan başka bir Boolean ifadesi tanımlar.

## <a name="examples"></a>Örnekler

`tags` alanı tam olarak "WiFi" dizesini içeren belgelerle eşleştir:

    tags/any(t: t eq 'wifi')

`ratings` alanındaki her öğenin 3 ile 5 arasında (dahil) olduğu belgeleri eşleştirin:

    ratings/all(r: r ge 3 and r le 5)

`locations` alanındaki coğrafi koordinatların herhangi birinin verilen çokgen içinde olduğu belgeleri eşleştirin:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

`rooms` alanının boş olduğu belgeleri eşleştirin:

    not rooms/any()

Tüm odaların bulunduğu belgeleri Eşleştir, `rooms/amenities` alanı "TV" ve `rooms/baseRate` 100 ' den küçük:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Sınırlamalar

Her filtre ifadesi özelliği bir lambda ifadesinin gövdesi içinde kullanılamaz. Sınırlamalar, filtrelemek istediğiniz koleksiyon alanının veri türüne bağlı olarak farklılık gösterir. Aşağıdaki tabloda sınırlamalar özetlenmektedir.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Bu sınırlamalar ve örnekler hakkında daha fazla bilgi için bkz. [Azure bilişsel arama 'da koleksiyon filtrelerinde sorun giderme](search-query-troubleshoot-collection-filters.md). Bu sınırlamaların neden olduğu hakkında daha ayrıntılı bilgi için bkz. [Azure bilişsel arama 'da koleksiyon filtrelerini anlama](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Azure Bilişsel Arama için OData ifade diline genel bakış](query-odata-filter-orderby-syntax.md)
- [Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md)
- [Belgeleri &#40;Azure Bilişsel Arama ara REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
