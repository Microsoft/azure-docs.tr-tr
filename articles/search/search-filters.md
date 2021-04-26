---
title: Arama sonuçlarına filtre uygula
titleSuffix: Azure Cognitive Search
description: Microsoft Azure barındırılan bir bulut arama hizmeti olan Azure Bilişsel Arama 'deki sorgularda arama sonuçlarını azaltmak için Kullanıcı güvenlik kimliğine, dile, coğrafi konuma veya sayısal değerlere göre filtreleyin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ba538f4753c2365406bd88286b6d54cff1a9e9ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800831"
---
# <a name="filters-in-azure-cognitive-search"></a>Azure Bilişsel Arama filtreler 

*Filtre* , sorguda kullanılan belgeleri seçmeye yönelik değer tabanlı ölçütler sağlar. Filtre tek bir değer veya bir OData [filtre ifadesi](search-query-odata-filter.md)olabilir. Tam metin aramasının aksine, bir filtre değeri veya ifadesi yalnızca katı eşleşme döndürür.

Çok [yönlü gezinme](search-filters-facets.md)gibi bazı arama deneyimleri, uygulamanın bir parçası olarak filtrelere bağlıdır, ancak belirli değerlere bir sorgu kapsamını atamak istediğiniz her zaman filtreler kullanabilirsiniz. Amacınız bunun yerine belirli alanlara bir sorgu kapsamlanmaktır, aşağıda açıklanan alternatif yöntemler vardır.

## <a name="when-to-use-a-filter"></a>Bir filtrenin ne zaman kullanılacağı

Filtreler, "yakın beni bul", çok yönlü gezinme ve yalnızca bir kullanıcının görebileceği belgeleri gösteren güvenlik filtreleri gibi çeşitli arama deneyimlerine göre belirlenir. Bu deneyimlerden herhangi birini uygularsanız, bir filtre gereklidir. Bu, coğrafi konum koordinatlarını, Kullanıcı tarafından seçilen model kategorisini veya istek sahibinin Güvenlik KIMLIĞINI sağlayan arama sorgusuna iliştirilen filtredir.

Yaygın senaryolar şunlardır:

+ Arama sonuçlarını dizindeki içeriğe göre dilimleyin. Otel konumu, kategorileri ve değişiklik içeren bir şema verildiğinde, ölçütlere göre açıkça eşleşecek bir filtre oluşturabilirsiniz (Seattle 'da, su üzerinde, bir görünümle). 

+ Arama deneyimini uygulama bir filtre gereksinimiyle birlikte gelir:

  + Çok [yönlü gezinme](search-faceted-navigation.md) , Kullanıcı tarafından seçilen model kategorisini geri geçirmek için bir filtre kullanır.
  + Coğrafi arama "yakın beni bul" uygulamalarında geçerli konumun koordinatlarını geçirmek için bir filtre kullanır. 
  + [Güvenlik filtreleri](search-security-trimming-for-azure-search.md) güvenlik tanımlayıcılarını bir filtre ölçütü olarak iletir, burada dizindeki bir eşleşme, belgeye erişim hakları için bir proxy görevi görür.

+ "Numara arama" yapın. Sayısal alanlar alınabilir ve arama sonuçlarında görünebilir, ancak bunlar aranabilir değildir (tam metin aramasına tabi değildir). Sayısal verilere dayalı seçim ölçütlerine ihtiyacınız varsa bir filtre kullanın.

### <a name="alternative-methods-for-reducing-scope"></a>Kapsam azaltma için alternatif Yöntemler

Arama sonuçlarınızda bir daraltma efekti istiyorsanız, filtreler tek seçiminiz değildir. Bu alternatifler amacınıza bağlı olarak daha iyi bir uyum sağlayabilir:

+ `searchFields` sorgu parametresi aramayı belirli alanlarla kısıtlar. Örneğin, dizininiz Ingilizce ve Ispanyolca açıklamaları için ayrı alanlar sağlıyorsa, tam metin aramasında kullanılacak alanları hedeflemek için searchFields ' i kullanabilirsiniz. 

+ `$select` parametresi, bir sonuç kümesinde hangi alanların ekleneceğini belirlemek için kullanılır, yanıtı çağıran uygulamaya göndermeden önce etkin bir şekilde kırpmaz. Bu parametre sorguyu iyileştirmez veya belge koleksiyonunu azaltır, ancak daha küçük bir yanıt hedefiniz ise, bu parametre dikkate alınması gereken bir seçenektir. 

Her iki parametre hakkında daha fazla bilgi için bkz. [arama belgeleri > istek > sorgu parametreleri](/rest/api/searchservice/search-documents#query-parameters).

## <a name="how-filters-are-executed"></a>Filtrelerin nasıl yürütüldüğü

Sorgu zamanında, bir filtre ayrıştırıcısı ölçütü girdi olarak kabul eder, ifadeyi bir ağaç olarak temsil edilen atomik Boole ifadelerine dönüştürür ve sonra filtre ağacını bir dizin içindeki filtrelenebilir alanlar üzerinde değerlendirir.

Filtreleme, arama ile birlikte meydana gelir ve belge alımı ve ilgi Puanlama için aşağı akış işleme dahil edilecek belgeleri niteleyen şekilde kapsar. Bir arama dizesiyle eşlendiğinde, filtre, sonraki arama işleminin geri çağırma kümesini etkin bir şekilde azaltır. Tek başına kullanıldığında (örneğin, sorgu dizesi nerede boşsa `search=*` ), filtre ölçütü tek giriştir. 

## <a name="defining-filters"></a>Filtreleri tanımlama

Filtreler, Bilişsel Arama tarafından desteklenen [filtre sözdiziminde](search-query-odata-filter.md) Ifade edilen OData ifadeleridir.

Her **arama** işlemi için bir filtre belirtebilirsiniz, ancak filtrenin kendisi birden çok alan, birden çok ölçüt içerebilir ve bir **IMatch** işlevi kullanırsanız birden çok tam metin arama ifadesi olabilir. Çok parçalı bir filtre ifadesinde, koşulları herhangi bir sırada belirtebilirsiniz (işleç önceliği kurallarına tabidir). Belirli bir dizideki koşulları yeniden düzenlemenize çalışırsanız, performansın hiçbir şekilde ele geçirmesine izin verilmez.

Filtre ifadesindeki sınırlardan biri, isteğin en büyük boyut sınırıdır. Filtrenin tamamında tüm istek, POST için en fazla 16 MB veya GET için 8 KB olabilir. Ayrıca, filtre ifadenizde yan tümce sayısında bir sınır vardır. Güzel bir Thumb kuralı, yüzlerce yan tümce varsa, bu sınıra çalışma riski altında olursunuz. Uygulamanızı, sınırsız boyut filtresi oluşturmamak üzere tasarlaması önerilir.

Aşağıdaki örneklerde, birkaç API 'de Prototipik filtre tanımları temsil etmektedir.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Kullanım düzenlerini filtrele

Aşağıdaki örneklerde, filtre senaryoları için çeşitli kullanım desenleri gösterilmektedir. Daha fazla fikir için bkz. [OData Expression sözdizimi > örnekleri](./search-query-odata-filter.md#examples).

+ Bir sorgu dizesi olmadan tek başına **$Filter**, filtre ifadesi ilgilendiğiniz belgeleri tamamen niteleyebiliyorlarsa yararlı olur. Sorgu dizesi olmadan, sözcük temelli veya dil analizi, Puanlama yok ve derecelendirme yoktur. Arama dizesinin yalnızca bir yıldız işareti olduğunu, yani "tüm belgeleri Eşleştir" anlamına geldiğini unutmayın.

  ```http
  {
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu"
  }
  ```

+ Filtrenin alt kümesini oluşturduğu sorgu dizesi ve **$Filter** birleşimi ve sorgu dizesi filtrelenmiş alt küme üzerinde tam metin araması için terim girişi sağlar. Koşulların eklenmesi (yürüyen mesafe), sonuçlarda en iyi eşleşen belgelerin daha yüksek bir şekilde derecelendirilir sonuçlarında arama puanları tanıtır. Sorgu dizesiyle bir filtre kullanılması en yaygın kullanım modelidir.

  ```http
  {
    "search": "walking distance theaters",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'"
  }

+ Compound queries, separated by "or", each with its own filter criteria (for example, 'beagles' in 'dog' or 'siamese' in 'cat'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # <a name="match-on-hostels-rated-higher-than-4-or-5-star-motels"></a>4 veya 5 yıldızlı Motels fazla dereceli olarak derecelendirilen Hostels ile Eşleştir.
   $filter = Search. ısmatchpuanlama (' Hostel ') ve derecelendirme GE 4 veya Search. ısmatchpuanlama (' Motel ') ve derecelendirme EQ 5

   # <a name="match-on-luxury-or-high-end-in-the-description-field-or-on-category-exactly-equal-to-luxury"></a>Açıklama alanında veya kategori üzerindeki ' merkezlerini ' veya ' High-End ' ile tam olarak ' merkezlerini ' değerine eşittir.
   $filter = Search. ısmatchpuanlama (' merkezlerini | High-End ', ' Description ') veya Category EQ ' merkezlerini ' &$count = true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter = Search. ısmatchpuanlama (' Pool ') ve derecelendirme GE 4

  Arama = havuz&$filter = derecelendirme GE 4
  ```

Follow up with these articles for comprehensive guidance on specific use cases:

+ [Facet filters](search-filters-facets.md)
+ [Security trimming](search-security-trimming-for-azure-search.md) 

## Field requirements for filtering

In the REST API, filterable is *on* by default for simple fields. Filterable fields increase index size; be sure to set `"filterable": false` for fields that you don't plan to actually use in a filter. For more information about settings for field definitions, see [Create Index](/rest/api/searchservice/create-index).

In the .NET SDK, the filterable is *off* by default. You can make a field filterable by setting the [IsFilterable property](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) of the corresponding [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) object to `true`. In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Varolan bir alanı filtrelenebilir hale getirme

Mevcut alanları filtrelenebilir hale getirmek için değiştiremezsiniz. Bunun yerine, yeni bir alan eklemeniz veya dizini yeniden oluşturmanız gerekir. Bir dizini yeniden oluşturma veya alanları yeniden doldurma hakkında daha fazla bilgi için bkz. [Azure bilişsel arama dizinini yeniden oluşturma](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Metin filtresi temelleri

Metin filtreleri, filtre içinde sağladığınız sabit dizeler ile dize alanlarıyla eşleşir: `$filter=Category eq 'Resort and Spa'`

Tam metin aramasının aksine, metin filtreleri için sözcük temelli analiz veya sözcük bölme yoktur, bu nedenle karşılaştırmalar yalnızca tam eşleşmeler için geçerlidir. Örneğin, *f* alanının "Sunny günü" içerdiğini varsayalım, `$filter=f eq 'Sunny'` eşleşmez, ancak `$filter=f eq 'sunny day'` işlem yapar. 

Metin dizeleri büyük/küçük harfe duyarlıdır. Büyük küçük harf olmayan sözcüklerin olmaması: `$filter=f eq 'Sunny day'` "Sunny günü" bulmayacak.

### <a name="approaches-for-filtering-on-text"></a>Metinde filtreleme yaklaşımları

| Yaklaşım | Description | Kullanılması gereken durumlar |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Ayrılmış bir dize listesine karşı bir alanla eşleşen bir işlev. | Birçok ham metin değerinin bir dize alanı ile eşleştirilmesi gereken, [Güvenlik filtreleri](search-security-trimming-for-azure-search.md) ve tüm filtreler için önerilir. **Search.in** işlevi hız için tasarlanmıştır ve ve kullanarak alanı her bir dizeye göre açıkça karşılaştırmadan çok daha hızlıdır `eq` `or` . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Aynı filtre ifadesinde tam metin arama işlemlerini kesin olarak Boolean filtre işlemleriyle karıştırabilmeniz için bir işlev. | Tek bir istekte birden çok arama filtresi kombinasyonu istediğinizde **Search. IsMatch** (veya Puanlama eşdeğerini, **arama. ısmatchpuanlama**) kullanın. Ayrıca, daha büyük bir dizedeki kısmi bir dizeyi filtrelemek için bir *Contains* filtresi için de kullanabilirsiniz. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Alanlar, işleçler ve değerlerden oluşan Kullanıcı tanımlı bir ifade. | Bir dize alanı ve bir dize değeri arasındaki tam eşleşmeleri bulmak istediğinizde bunu kullanın. |

## <a name="numeric-filter-fundamentals"></a>Sayısal filtre temelleri

Sayısal alanlar `searchable` tam metin araması bağlamında değil. Yalnızca dizeler tam metin aramasına tabidir. Örneğin, bir arama terimi olarak 99,99 girerseniz, öğelerin fiyatı $99,99 ' de alınır. Bunun yerine, belgenin dize alanlarında 99 numaralı öğeyi görürsünüz. Bu nedenle, sayısal verileriniz varsa, Bu varsayımını aralıklar, modeller, gruplar vb. dahil olmak üzere filtreler için kullanmanız gerekir. 

Sayısal alanlar (Fiyat, boyut, SKU, KIMLIK) içeren belgeler, alan işaretlenmişse arama sonuçlarında bu değerleri sağlar `retrievable` . Burada nokta, tam metin aramasının kendisinin sayısal alan türleri için geçerli olmadığı bir alandır.

## <a name="next-steps"></a>Sonraki adımlar

İlk olarak, **$Filter** parametrelerle sorgu göndermek Için portalda **Arama Gezgini** ' ni deneyin. [Gerçek zamanlı örnek dizin](search-get-started-portal.md) , arama çubuğuna yapıştırdığınızda aşağıdaki filtrelenmiş sorgular için ilginç sonuçlar sağlar:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Daha fazla örnekle çalışmak için bkz. [OData filtre Ifadesi söz dizimi > örnekleri](./search-query-odata-filter.md#examples).

## <a name="see-also"></a>Ayrıca bkz.

+ [Azure Bilişsel Arama’da tam metin araması nasıl çalışır?](search-lucene-query-architecture.md)
+ [Belgelerde Arama REST API'si](/rest/api/searchservice/search-documents)
+ [Basit sorgu söz dizimi](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene sorgu söz dizimi](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Desteklenen veri türleri](/rest/api/searchservice/supported-data-types)