---
title: OData Select başvurusu
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama sorgularının arama sonuçlarına döndürülecek alanların açık seçimi için sözdizimi ve dil başvurusu.
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
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113109"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Azure Bilişsel Arama 'de OData $select söz dizimi

 Azure Bilişsel Arama arama sonuçlarına dahil edilecek alanları seçmek için [OData **$Select** parametresini](query-odata-filter-orderby-syntax.md) kullanabilirsiniz. Bu makalede **$Select** sözdizimi ayrıntılı olarak açıklanmaktadır. Arama sonuçlarını sunarken **$Select** kullanma hakkında daha fazla genel bilgi için bkz. [Azure bilişsel arama arama sonuçlarıyla çalışma](search-pagination-page-layout.md).

## <a name="syntax"></a>Sözdizimi

**$Select** parametresi, her belge için hangi alanların sorgu sonuç kümesinde döndürüleceğini belirler. Aşağıdaki EBNF ([Genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) **$Select** parametresi için dilbilgisini tanımlar:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Etkileşimli bir sözdizimi diyagramı da kullanılabilir:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Tüm EBNF için bkz. [Azure bilişsel arama Için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md) .

**$Select** parametresi iki şekilde gelir:

1. Tüm alınabilir alanların döndürülmesi gerektiğini belirten tek bir yıldız (`*`) veya
1. Alan yollarının virgülle ayrılmış bir listesi, hangi alanların döndürüleceğini tanımlar.

İkinci formu kullanırken, yalnızca listede alınabilir alanları belirtebilirsiniz.

Kendi alt alanlarını açıkça belirtmeden bir karmaşık alanı listelemiyorsanız, tüm alınabilir alt alanlar sorgu sonuç kümesine dahil edilir. Örneğin, dizininizin `Street`, `City`ve `Country` tüm alınabilir alt alanlarla `Address` alana sahip olduğunu varsayalım. **$Select**`Address` belirtirseniz, sorgu sonuçları üç alt alanın tümünü de içerir.

## <a name="examples"></a>Örnekler

`HotelId`, `HotelName`ve `Rating` üst düzey alanlarını ve `Address``City` alt alanını dahil edin:

    $select=HotelId, HotelName, Rating, Address/City

Örnek bir sonuç şöyle görünebilir:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

`HotelName` en üst düzey alanını ve tüm alt alanları `Address`ve `Rooms` koleksiyonundaki her bir nesnenin `Type` ve `BaseRate` alt alanlarını dahil edin:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Örnek bir sonuç şöyle görünebilir:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure Bilişsel Arama arama sonuçlarıyla çalışma](search-pagination-page-layout.md)
- [Azure Bilişsel Arama için OData ifade diline genel bakış](query-odata-filter-orderby-syntax.md)
- [Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md)
- [Belgeleri &#40;Azure Bilişsel Arama ara REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
