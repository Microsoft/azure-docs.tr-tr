---
title: OData koleksiyon filtrelerini anlama
titleSuffix: Azure Cognitive Search
description: OData koleksiyon filtrelerinin, koleksiyonlara özgü sınırlamalar ve davranışlar dahil olmak üzere Azure Bilişsel Arama sorgularında nasıl çalıştığı mekanizması öğrenin.
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
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113060"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Azure Bilişsel Arama OData koleksiyon filtrelerini anlama

Azure Bilişsel Arama 'de koleksiyon alanlarını [filtrelemek](query-odata-filter-orderby-syntax.md) için [`any` ve `all` işleçlerini](search-query-odata-collection-operators.md) **lambda ifadeleriyle**birlikte kullanabilirsiniz. Lambda ifadeleri, bir **Aralık değişkenine**başvuran Boole ifadeleridir. `any` ve `all` işleçleri çoğu programlama dilinde bir `for` döngüsüne benzer, döngü değişkeninin rolünü alan Aralık değişkeni ve döngünün gövdesi olarak lambda ifadesi. Aralık değişkeni, döngünün yinelemesi sırasında koleksiyonun "Current" değerini alır.

En azından bu, kavramsal olarak nasıl çalıştığı. Azure Bilişsel Arama, `for` döngülerinin nasıl çalıştığı konusunda filtreleri çok farklı bir şekilde uygular. İdeal olarak, bu fark sizin için görünmez, ancak bazı durumlarda değildir. Nihai sonuç, lambda ifadeleri yazarken izlemeniz gereken kurallar vardır.

Bu makalede, Azure Bilişsel Arama 'in bu filtreleri nasıl yürüttüğünü inceleyerek koleksiyon filtreleri için kuralların neden olduğu açıklanmaktadır. Karmaşık lambda ifadeleriyle gelişmiş filtreler yazıyorsanız, filtrelerdeki nelerin ve neden olduğunu anlamak için bu makaleyi yararlı bulabilirsiniz.

Koleksiyon filtreleri kurallarının ne olduğu hakkında bilgi için, örnekler de dahil olmak üzere bkz. [Azure bilişsel arama 'Da OData koleksiyon filtrelerinde sorun giderme](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Koleksiyon filtrelerinin neden sınırlandırıldı

Tüm filtre özelliklerinin tüm koleksiyon türleri için desteklenmemesinin üç temel nedeni vardır:

1. Belirli veri türleri için yalnızca belirli işleçler desteklenir. Örneğin, `lt`, `gt`vb. kullanarak `true` ve `false` Boole değerlerini karşılaştırmak mantıklı değildir.
1. Azure Bilişsel Arama, `Collection(Edm.ComplexType)`türündeki alanlarda **bağıntılı aramayı** desteklemez.
1. Azure Bilişsel Arama, koleksiyonlar da dahil olmak üzere tüm veri türlerinde filtre yürütmek için ters dizinler kullanır.

İlk neden, OData dilinin ve EDM türü sisteminin nasıl tanımlandığınıza ilişkin bir sonucudur. Son ikisi bu makalenin geri kalanında daha ayrıntılı olarak açıklanmıştır.

## <a name="correlated-versus-uncorrelated-search"></a>Bağıntılı ve bağıntılı olmayan arama

Karmaşık nesneler koleksiyonuna birden çok filtre ölçütü uygulanırken, *koleksiyonda her bir nesne*için uygulandıklarından, ölçütler **bağıntılı** bir şekilde yapılır. Örneğin, aşağıdaki filtre 100 ' dan düşük bir fiyata sahip en az bir Deluxe Oda olan oteller döndürür:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Filtreleme *bağıntısız*ise yukarıdaki filtre, bir odanın Deluxe olduğu ve farklı bir odanın 100 ' den düşük bir taban oranına sahip olan oteller döndürebilir. Bu anlamlı değildir, çünkü lambda ifadesinin her iki yan tümcesi de aynı Aralık değişkenine uygulanır, yani `room`. Bu, bu tür filtrelerin bağıntılı olmasının nedenleridir.

Ancak, tam metin arama için belirli bir Aralık değişkenine başvurmanız mümkün değildir. Bu şekilde, [tam bir Lucene sorgusu](query-lucene-syntax.md) vermek için kullanılabilir arama kullanıyorsanız:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

oteller bir odanın bulunduğu yerde ve farklı bir odada açıklama içinde "şehir görünümü" bahsetmesini sağlayabilirsiniz. Örneğin, `1` `Id` aşağıdaki belge sorguyla eşleşir:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

Bunun nedeni, `Rooms/Type` tüm belgedeki `Rooms/Type` alanın analiz edilen tüm koşullarına ve benzer şekilde `Rooms/Description`için de aşağıdaki tablolarda gösterildiği gibi anlamına gelir.

Tam metin arama için `Rooms/Type` nasıl depolanır:

| `Rooms/Type` dönemi | Belge kimlikleri |
| --- | --- |
| lü | 1, 2 |
| standart | 1 |

Tam metin arama için `Rooms/Description` nasıl depolanır:

| `Rooms/Description` dönemi | Belge kimlikleri |
| --- | --- |
| Kurs | 2 |
| city | 1 |
| Bahçe | 1 |
| miktarda | 1 |
| Motel | 2 |
| yı | 1, 2 |
| standart | 1 |
| kurallarý | 1 |
| görünüm | 1 |

Bu nedenle, yukarıdaki filtreden farklı olarak, "bir odanın ' lüks ' 'e eşit `Type` sahip olduğu ve **aynı odanın** 100 ' den küçük `BaseRate` sahip olduğu belgeleri Eşleştir" ifadesi "" Deluxe "teriminin bulunduğu ve `Rooms/Description`" şehir görünümü "ifadesinin bulunduğu" arama sorgusu `Rooms/Type` "diyor. Alanları, ikinci durumda bağıntılı olabilecek ayrı odalar kavramı yoktur.

> [!NOTE]
> Azure Bilişsel Arama 'a eklenen bağıntılı arama desteğini görmek isterseniz, lütfen [Bu Kullanıcı ses öğesini](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)oylayın.

## <a name="inverted-indexes-and-collections"></a>Ters dizinler ve koleksiyonlar

Karmaşık koleksiyonlar üzerinde `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`vb. gibi basit koleksiyonlar için çok daha az kısıtlama olduğunu fark etmiş olabilirsiniz. Bunun nedeni, Azure Bilişsel Arama karmaşık koleksiyonları gerçek bir alt belge koleksiyonları olarak depolarken basit koleksiyonlar tüm koleksiyonlar olarak depolanmaz.

Örneğin, çevrimiçi bir satıcı için Dizin `seasons` gibi filtrelenebilir bir dize toplama alanı düşünün. Bu dizine yüklenmiş bazı belgeler şöyle görünebilir:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

`seasons` alanının değerleri **ters bir dizin**adlı bir yapıda saklanır ve şuna benzer bir şekilde görünür:

| Sözleşme Dönemi | Belge kimlikleri |
| --- | --- |
| yay | 1, 2 |
| ini | 1 |
| düştü | 1, 2 |
| Kış | 2, 3 |

Bu veri yapısı, bir soruyu harika bir hızla yanıtlamak üzere tasarlandı: hangi belgeler belirli bir terime ait görünüyor? Bu sorunun yanıtlanması, bir koleksiyon üzerinde bir döngüden daha basit bir eşitlik denetimi gibi daha fazla işe yarar. Aslında, bu neden dize koleksiyonları için, Azure Bilişsel Arama yalnızca `any`lambda ifadesinde bir karşılaştırma işleci olarak `eq` izin veriyor.

Daha sonra, eşitlik `or`ile aynı Aralık değişkeninde birden çok eşitlik denetimini nasıl birleştirebileceğinizi inceleyeceğiz. Sonuç olarak [, nicelik belirteçleri ve dağıtılabilir özelliği](https://en.wikipedia.org/wiki/Existential_quantification#Negation)için teşekkürler. Bu ifade:

    seasons/any(s: s eq 'winter' or s eq 'fall')

eşdeğerdir:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

ve iki `any` alt ifadesinin her biri ters bir dizin kullanılarak etkili bir şekilde çalıştırılabilir. Ayrıca, [nicelik sayısının Olumsuzlaştırma yasaları](https://en.wikipedia.org/wiki/Existential_quantification#Negation)sayesinde bu ifade:

    seasons/all(s: s ne 'winter' and s ne 'fall')

eşdeğerdir:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

`all` `ne` ve `and`ile kullanmak mümkün değildir.

> [!NOTE]
> Ayrıntılar bu belgenin kapsamının ötesinde olsa da, aynı ilkeler [coğrafi uzamsal noktaların koleksiyonları için uzaklık ve kesişim testlerine](search-query-odata-geo-spatial-functions.md) de genişletilir. `any`:
>
> - `geo.intersects`, iç içe geçirilemez
> - `geo.distance` `lt` veya `le` kullanılarak karşılaştırılmalıdır
> - ifadeler `or`değil, `and` ile birleştirilmelidir
>
> `all`için convero kuralları geçerlidir.

Örneğin `Collection(Edm.Int32)` gibi `lt`, `gt`, `le`ve `ge` işleçlerini destekleyen veri türleri koleksiyonlarında filtrelendiğinde daha geniş bir dizi ifadeye izin verilir. Özellikle, `or`kullanarak daha fazla birleştirilmiş olan `and`kullanan **Aralık karşılaştırmaları** içine birleştirileceği sürece, `any``or` ve `and` de kullanabilirsiniz. Bu Boole ifadesi yapısına, "and Iler" olarak bilinen, ayırt edici [normal form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form)adı verilir. Buna karşılık, bu veri türleri için `all` lambda ifadeleri, "and of ORs" olarak da bilinen, [ayırt edici normal biçimde (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)olmalıdır. Azure Bilişsel Arama, dize için hızlı bir arama yapmak gibi, bunları tersine çevrilmiş dizinler kullanarak yürütebildiğinden, bu tür Aralık karşılaştırmaları yapılmasına izin verir.

Özet bölümünde, bir lambda ifadesinde izin verilen nesnelerin kaydırma kuralları aşağıda verilmiştir:

- `any`içinde, *pozitif denetimler* her zaman eşitlik, Aralık karşılaştırmaları, `geo.intersects`veya `geo.distance` `lt` veya `le` ile karşılaştırıldığında izin verilir (uzaklığı denetlemek için "closeness" gibi bir eşitlik gibi düşünün).
- `any`içinde `or` her zaman izin verilir. Yalnızca, yalnızca and (DNF) ' i kullanıyorsanız, yalnızca, Aralık denetimlerini ifade eden veri türleri için `and` kullanabilirsiniz.
- `all`içinde kurallar ters çevrilir; yalnızca *negatif denetimlerine* izin verilir, `and` her zaman kullanabilir ve `or` yalnızca and of ORS (CNF) olarak ifade edilen Aralık denetimleri için kullanabilirsiniz.

Uygulamada, bunlar en büyük olasılıkla kullanabileceğiniz filtrelerin türleridir. Mümkün olan nesnelerin sınırlarını anlamak yine de yararlıdır.

Hangi tür filtrelerin izin verileceğini ve hangilerinin geçerli olduğunu gösteren belirli örnekler için bkz. [geçerli koleksiyon filtreleri yazma](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure Bilişsel Arama OData koleksiyon filtreleri sorunlarını giderme](search-query-troubleshoot-collection-filters.md)
- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Azure Bilişsel Arama için OData ifade diline genel bakış](query-odata-filter-orderby-syntax.md)
- [Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu](search-query-odata-syntax-reference.md)
- [Belgeleri &#40;Azure Bilişsel Arama ara REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
