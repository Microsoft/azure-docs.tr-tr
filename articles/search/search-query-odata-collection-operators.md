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
ms.openlocfilehash: 4c1243d5d9122539466e94b6bbfdd5ced588e69a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88934914"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Azure Bilişsel Arama OData toplama işleçleri `any` ve `all`

Azure Bilişsel Arama ile kullanmak üzere bir [OData filtresi ifadesi](query-odata-filter-orderby-syntax.md) yazarken, genellikle koleksiyon alanlarını filtrelemek yararlı olur. Ve işleçlerini kullanarak bunu elde edebilirsiniz `any` `all` .

## <a name="syntax"></a>Syntax

Aşağıdaki EBNF ([genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)), veya kullanan bir OData ifadesinin dilbilgisini tanımlar `any` `all` .

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
  - ' İ kullanan bir ifade, `all` `true` koşulun her öğe için true olması halinde döndürür.
  - Deyimi, `any` `true` koleksiyonun en az bir öğesi için true ise, döndüren bir ifade döndürür.
- Koleksiyon filtresinin üçüncü biçimi, `any` bir koleksiyon alanının boş olup olmadığını test etmek için lambda ifadesi olmadan kullanılır. Koleksiyonda herhangi bir öğe varsa, döndürür `true` . Koleksiyon boşsa, döndürür `false` .

Bir koleksiyon filtresindeki **lambda ifadesi** , bir programlama dilindeki Döngünün gövdesi gibidir. Yineleme sırasında koleksiyonun geçerli öğesini tutan **Aralık değişkeni** olarak adlandırılan bir değişkeni tanımlar. Ayrıca, koleksiyondaki her öğe için Aralık değişkenine uygulanacak filtre ölçütü olan başka bir Boolean ifadesi tanımlar.

## <a name="examples"></a>Örnekler

`tags`Alanı tam olarak "WiFi" dizesini içeren belgeleri eşleştir:

```text
tags/any(t: t eq 'wifi')
```

Alandaki her öğenin `ratings` 3 ila 5 (dahil) arasında olduğu belgeleri eşleştirin:

```text
ratings/all(r: r ge 3 and r le 5)
```

Alandaki coğrafi koordinatların herhangi birinin `locations` verilen çokgen içinde olduğu belgeleri eşleştirin:

```text
locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))
```

Alanın boş olduğu belgeleri Eşleştir `rooms` :

```text
not rooms/any()
```

Tüm odaların bulunduğu belgelerde, `rooms/amenities` alan "TV" ve `rooms/baseRate` 100 ' den küçük olan belgeleri eşleştirin:

```text
rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)
```

## <a name="limitations"></a>Sınırlamalar

Her filtre ifadesi özelliği bir lambda ifadesinin gövdesi içinde kullanılamaz. Sınırlamalar, filtrelemek istediğiniz koleksiyon alanının veri türüne bağlı olarak farklılık gösterir. Aşağıdaki tabloda sınırlamalar özetlenmektedir.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Bu sınırlamalar ve örnekler hakkında daha fazla bilgi için bkz. [Azure bilişsel arama 'da koleksiyon filtrelerinde sorun giderme](search-query-troubleshoot-collection-filters.md). Bu sınırlamaların neden olduğu hakkında daha ayrıntılı bilgi için bkz. [Azure bilişsel arama 'da koleksiyon filtrelerini anlama](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Azure Bilişsel Arama için OData ifade diline genel bakış](query-odata-filter-orderby-syntax.md)
- [Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md)
- [Azure Bilişsel Arama REST API &#40;belgelerde arama yapın&#41;](/rest/api/searchservice/Search-Documents)