---
title: REST API sürümlerini yükselt
titleSuffix: Azure Cognitive Search
description: API sürümlerindeki farklılıkları gözden geçirin ve mevcut kodu en yeni Azure Bilişsel Arama hizmeti REST API sürümüne geçirmek için hangi eylemlerin gerekli olduğunu öğrenin.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d7734fde529c24e8113ea3b019d343b7223f0122
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91929651"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Azure Bilişsel Arama en son REST API yükseltin

[**Arama REST API**](/rest/api/searchservice/)daha önceki bir sürümünü kullanıyorsanız, bu makale uygulamanızı en yenı **2020-06-30** API sürümüne yükseltmenize yardımcı olur.

Sürüm 2020-06-30 önemli bir yeni Özellik ([bilgi deposu](knowledge-store-concept-intro.md)) içerir ve birkaç küçük davranış değişikliği sunar. Bu nedenle, önceki sürümden yükseltiyorsanız (2019-05-06) kod değişikliklerinin en az olması için bu sürüm büyük ölçüde uyumludur.

> [!NOTE]
> Bir arama hizmeti, önceki sürümlere dahil olmak üzere bir dizi REST API sürümü destekler. Bu API sürümlerini kullanmaya devam edebilirsiniz, ancak yeni yeteneklere erişebilmek için kodunuzun en yeni sürüme geçirilmesini öneririz. Zaman içinde, REST API en eski sürümleri kullanımdan kaldırılmıştır ve [artık desteklenmeyecektir](search-api-versions.md#unsupported-versions).

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>Yükseltme

Yeni bir sürüme yükseltirken, büyük olasılıkla kodunuzda sürüm numarasını değiştirmek yerine birçok değişiklik yapmanız gerekmez. Kodu değiştirmeniz gerekebilecek tek durumlar şunlardır:

* API yanıtında tanınmayan özellikler döndürüldüğünde kodunuz başarısız olur. Varsayılan olarak uygulamanızın anlayamadığını özellikleri yoksaymalıdır.

* Kodunuzun API istekleri devam ettirir ve yeni API sürümüne yeniden göndermeye çalışır. Örneğin bu durum, uygulamanız arama API 'sinden döndürülen devamlılık belirteçlerini devam ettirir (daha fazla bilgi için `@search.nextPageParameters` [Arama API 'si başvurusunda](/rest/api/searchservice/Search-Documents)bölümüne bakın).

* Kodunuz 2019-05-06 ön tarihine sahip bir API sürümüne başvurur ve bu sürümdeki bir veya daha fazla son değişikliğe tabidir. [2019-05-06 sürümüne yükseltme](#upgrade-to-2019-05-06) , daha fazla ayrıntı sağlar. 

Bu durumlardan herhangi biri sizin için geçerlidir, kodunuzu uygun şekilde değiştirmeniz gerekebilir. Aksi takdirde, yeni sürüme eklenen özellikleri kullanmaya başlamak isteyebileceğiniz halde hiçbir değişiklik yapılması gerekmez.

## <a name="upgrade-to-2020-06-30"></a>2020-06-30 sürümüne yükselt

Sürüm 2020-06-30, REST API genel kullanıma açık olan sürümüdür. Tek bir değişiklik ve birkaç davranış farkı vardır. 

Özellikler artık bu API sürümünde genel kullanıma sunulmuştur:

* [Bilgi deposu](knowledge-store-concept-intro.md), becerileri aracılığıyla oluşturulan ve aşağı akış analizi için oluşturulan ve diğer uygulamalar aracılığıyla işlenen içerik kalıcı depolama. Bu özellik sayesinde, Dizin Oluşturucu temelli bir AI zenginleştirme işlem hattı, bir arama dizinine ek olarak bir bilgi deposunu doldurabilir. Bu özelliğin önizleme sürümünü kullandıysanız, genel kullanıma sunulan sürüme eşdeğerdir. Gerekli tek kod değişikliği api sürümünü değiştiriyor.

### <a name="breaking-change"></a>Son değişiklik

Önceki API sürümlerine karşı yazılan mevcut kod, API-Version = 2020-06-30 ' da ve daha sonra kod aşağıdaki işlevleri içeriyorsa, üzerine kesilir:

* Herhangi bir EDM. Date değişmez değeri (gibi `2020-12-12` ), filtre ifadelerinde, Edm. DateTimeOffset biçiminde bir tarih gelmelidir: `2020-12-12T00:00:00Z` . Bu değişiklik, saat dilimi farklılıkları nedeniyle hatalı veya beklenmeyen sorgu sonuçlarını işlemek için gerekli.

### <a name="behavior-changes"></a>Davranış değişiklikleri

* [BM25 derecelendirme algoritması](index-ranking-similarity.md) önceki derecelendirme algoritmasından daha yeni teknolojinin yerini alır. Yeni hizmetler, bu algoritmayı otomatik olarak kullanacaktır. Mevcut hizmetler için, parametreleri yeni algoritmayı kullanacak şekilde ayarlamanız gerekir.

* Null değerler için sıralanmış sonuçlar bu sürümde değiştirilmiştir, sıralama ve sıralama ise son ' a göre null değerleri ilk görünür `asc` `desc` . Null değerlerin nasıl sıralanacağını işlemek için kod yazdıysanız, bu değişikliği unutmayın.

## <a name="upgrade-to-2019-05-06"></a>2019-05-06 sürümüne yükselt

Sürüm 2019-05-06, REST API önceki genel kullanıma hazır sürümüdür. Bu API sürümünde genel kullanıma sunulan özellikler şunlardır:

* [AutoComplete](index-add-suggesters.md) , kısmen belirtilen bir terim girişini tamamlayan bir typeahead özelliğidir.
* [Karmaşık türler](search-howto-complex-data-types.md) , arama dizininde yapılandırılmış nesne verileri için yerel destek sağlar.
* Azure Blob dizinleme 'nin bir parçası olan [Jsonlines ayrıştırma modları](search-howto-index-json-blobs.md), bir yeni satır tarafından ayrılan JSON varlığı başına bir arama belgesi oluşturur.
* [AI zenginleştirme](cognitive-search-concept-intro.md) , bilişsel HIZMETLER 'in AI zenginleştirme altyapılarından yararlanan dizin oluşturma olanağı sunar.

### <a name="breaking-changes"></a>Yeni değişiklikler

Önceki API sürümlerine karşı yazılan mevcut kod, kod aşağıdaki işlevleri içeriyorsa API-Version = 2019-05-06 ve üzeri sürümlerde kesilir:

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB-DataSource için Dizin Oluşturucu artık "tür": "cosmosdb"

[Cosmos DB bir Dizin Oluşturucu](search-howto-index-cosmosdb.md )kullanıyorsanız, öğesini olarak değiştirmeniz gerekir `"type": "documentdb"` `"type": "cosmosdb"` .

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Dizin Oluşturucu yürütme sonucu hatalarının durumu artık yok

Dizin oluşturucunun yürütülmesi için hata yapısının daha önce bir `status` öğesi vardı. Bu öğe yararlı bilgiler sağlamadığı için kaldırıldı.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Dizin Oluşturucu veri kaynağı API 'SI artık bağlantı dizelerini döndürmüyor

API sürümlerinden 2019-05-06 ve 2019-05-06 ' den sonra, veri kaynağı API 'SI artık herhangi bir REST işleminin yanıtında bağlantı dizelerini döndürmez. Önceki API sürümlerinde, POST kullanılarak oluşturulan veri kaynakları için Azure Bilişsel Arama, bağlantı dizesinin düz metin olarak yer aldığı OData yanıtı ile **201** döndürdü.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Adlandırılmış varlık tanıma bilişsel yeteneği artık kullanımdan kaldırılmıştır

Kodunuzda [varlık tanıma özelliğini adı](cognitive-search-skill-named-entity-recognition.md) olarak adlandırdıysanız çağrı başarısız olur. Değiştirme işlevselliği, [varlık tanıma](cognitive-search-skill-entity-recognition.md)' dır. Yetenek başvurusunu başka hiçbir değişiklik olmadan değiştirebilirsiniz. API imzası her iki sürüm için de aynıdır. 

### <a name="upgrading-complex-types"></a>Karmaşık türler yükseltiliyor

API sürüm 2019-05-06 karmaşık türler için biçimsel destek ekledi. Kodunuz 2017-11-11-Preview veya 2016-09-01-Preview sürümünde karmaşık tür Denkliği için önceki önerilere uygulanmışsa, bilmeniz gereken sürüm 2019-05-06 ' den itibaren yeni ve değiştirilmiş sınırlar vardır:

+ Alt alanların derinliğine ve Dizin başına karmaşık koleksiyonların sayısına ilişkin sınırlar düşürüldü. Önizleme API-sürümlerini kullanarak bu sınırları aşan dizinler oluşturduysanız, API sürüm 2019-05-06 kullanarak bunları güncelleştirme veya yeniden oluşturma girişimleri başarısız olur. Bu sizin için geçerliyse, şemanızı yeni sınırlara uyacak şekilde yeniden tasarlamanız ve sonra dizininizi yeniden oluşturmanız gerekir.

+ Her belge için karmaşık koleksiyonların öğe sayısında API-sürüm 2019-05-06 ' den başlayan yeni bir sınır vardır. Önizleme API-sürümlerini kullanarak bu sınırları aşan belgelerle dizinler oluşturduysanız, API sürümü 2019-05-06 kullanarak bu verileri yeniden dizinle ilgili herhangi bir girişim başarısız olur. Bu sizin için geçerliyse, verilerinizin dizinini yeniden oluşturmadan önce belge başına karmaşık koleksiyon öğelerinin sayısını azaltmanız gerekir.

Daha fazla bilgi için bkz. [Azure bilişsel arama Için hizmet limitleri](search-limits-quotas-capacity.md).

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Eski bir karmaşık tür yapısını yükseltme

Kodunuz eski önizleme API sürümlerinden biriyle karmaşık türler kullanıyorsa şuna benzer bir dizin tanımı biçimi kullanıyor olabilirsiniz:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

API sürüm 2017-11-11-Önizleme sürümünde, dizin alanlarını tanımlamaya yönelik daha yeni bir ağaç benzeri biçim eklenmiştir. Yeni biçimde, her karmaşık alanın, alt alanlarının tanımlandığı bir alan koleksiyonu vardır. API sürüm 2019-05-06 ' de, bu yeni biçim özel olarak kullanılır ve eski biçimi kullanarak bir dizin oluşturma veya güncelleştirme girişimi başarısız olur. Eski biçimi kullanarak oluşturduğunuz dizinler varsa, API sürüm 2019-05-06 kullanılarak yönetilebilmeleri için önce bu dosyaları yeni biçimde güncelleştirmek üzere API sürümü 2017-11-11-Preview ' i kullanmanız gerekir.

API sürüm 2017-11-11-önizleme kullanarak aşağıdaki adımlarla "düz" dizinleri yeni biçime güncelleştirebilirsiniz:

1. Dizininizi almak için bir GET isteği gerçekleştirin. Zaten yeni biçimde kullanılıyorsa işiniz bitti demektir.

2. Dizini "düz" biçiminden yeni biçime çevirin. Bu yazma sırasında kullanılabilecek örnek bir kod olmadığından bunun için kod yazmanız gerekir.

3. Dizini yeni biçime güncelleştirmek için bir PUT isteği gerçekleştirin. Güncelleştirme dizini API 'SI tarafından izin verilmediğinden, bu alanın Searchability/filterbeceri alanı gibi diğer tüm ayrıntılarını değiştirmediğinden emin olun.

> [!NOTE]
> Azure portal eski "düz" biçimiyle oluşturulan dizinlerin yönetilmesi mümkün değildir. Lütfen dizinlerinizi "düz" gösterimden en kısa zamanda "ağaç" gösterimine yükseltin.

## <a name="next-steps"></a>Sonraki adımlar

Arama REST API başvuru belgelerini gözden geçirin. Sorunlarla karşılaşırsanız [Stack Overflow](https://stackoverflow.com/) yardım almak için bize danışın veya [desteğe başvurun](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Arama hizmeti REST API başvurusu](/rest/api/searchservice/)
