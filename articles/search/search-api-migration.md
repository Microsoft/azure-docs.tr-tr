---
title: REST API sürümlerini yükselt
titleSuffix: Azure Cognitive Search
description: API sürümlerindeki farklılıkları gözden geçirin ve mevcut kodu en yeni Azure Bilişsel Arama hizmeti REST API sürümüne geçirmek için hangi eylemlerin gerekli olduğunu öğrenin.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112158"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>En son Azure Bilişsel Arama hizmeti REST API sürümüne yükseltin

[Arama REST API](https://docs.microsoft.com/rest/api/searchservice/)önceki bir sürümünü kullanıyorsanız, bu makale, uygulamanızı en yeni genel kullanıma sahıp 2019-05-06 API sürümünü kullanacak şekilde yükseltmenize yardımcı olur.

REST API sürüm 2019-05-06, önceki sürümlerden bazı değişiklikler içeriyor. Bunlar çoğunlukla geriye dönük olarak uyumludur; Bu nedenle kodunuzun değiştirilmesi, daha önce kullandığınız sürüme bağlı olarak yalnızca en az çaba gerektirir. [Yükseltme adımları](#UpgradeSteps) , yeni özellikleri kullanmak için gereken kod değişikliklerini özetler.

> [!NOTE]
> Azure Bilişsel Arama hizmet örneği, daha önceki bir sürümü de dahil olmak üzere bir dizi REST API sürümünü destekler. Bu API sürümlerini kullanmaya devam edebilirsiniz, ancak yeni yeteneklere erişebilmek için kodunuzun en yeni sürüme geçirilmesini öneririz.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Sürüm 2019-05-06 ' deki yenilikler
Sürüm 2019-05-06 REST API en yeni genel kullanıma hazır sürümüdür. Bu API sürümünde genel olarak kullanılabilir duruma geçti olan özellikler şunlardır:

* [AutoComplete](index-add-suggesters.md) , kısmen belirtilen bir terim girişini tamamlayan bir typeahead özelliğidir.

* [Karmaşık türler](search-howto-complex-data-types.md) , arama dizininde yapılandırılmış nesne verileri için yerel destek sağlar.

* Azure Blob dizinleme 'nin bir parçası olan [Jsonlines ayrıştırma modları](search-howto-index-json-blobs.md), bir yeni satır tarafından ayrılan JSON varlığı başına bir arama belgesi oluşturur.

* [AI zenginleştirme](cognitive-search-concept-intro.md) , bilişsel HIZMETLER 'in AI zenginleştirme altyapılarından yararlanan dizin oluşturma olanağı sunar.

Birçok önizleme özelliği yayını, genel olarak kullanılabilen güncelleştirmeyle birlikte bulunur. Yeni Önizleme özellikleri listesini gözden geçirmek için bkz. [arama REST API-sürüm 2019-05-06-önizleme](search-api-preview.md).

## <a name="breaking-changes"></a>Yeni değişiklikler

Aşağıdaki işlevleri içeren mevcut kod, api-Version = 2019-05-06 ' de kesilir.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB-DataSource için Dizin Oluşturucu artık "tür": "cosmosdb"

[Cosmos DB Dizin Oluşturucu](search-howto-index-cosmosdb.md )kullanıyorsanız, `"type": "documentdb"` `"type": "cosmosdb"`olarak değiştirmeniz gerekir.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Dizin Oluşturucu yürütme sonucu hatalarının durumu artık yok

Dizin Oluşturucu yürütmesinin hata yapısının daha önce bir `status` öğesi vardı. Bu öğe yararlı bilgiler sağlamadığı için kaldırıldı.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Dizin Oluşturucu veri kaynağı API 'SI artık bağlantı dizelerini döndürmüyor

API sürümlerinden 2019-05-06 ve 2019-05-06 ' den sonra, veri kaynağı API 'SI artık herhangi bir REST işleminin yanıtında bağlantı dizelerini döndürmez. Önceki API sürümlerinde, POST kullanılarak oluşturulan veri kaynakları için Azure Bilişsel Arama, bağlantı dizesinin düz metin olarak yer aldığı OData yanıtı ile **201** döndürdü.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Adlandırılmış varlık tanıma bilişsel yeteneği artık kullanımdan kaldırılmıştır

Kodunuzda [ad varlık tanıma özelliğini](cognitive-search-skill-named-entity-recognition.md) çağırırsanız çağrı başarısız olur. Değiştirme işlevselliği, [varlık tanıma](cognitive-search-skill-entity-recognition.md)' dır. Yetenek başvurusunu başka hiçbir değişiklik olmadan değiştirebilirsiniz. API imzası her iki sürüm için de aynıdır. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Yükseltme adımları
Önceki bir GA sürümünden yükseltiyorsanız, 2017-11-11 veya 2016-09-01, büyük olasılıkla kodunuzun üzerinde herhangi bir değişiklik yapmanız gerekmez, aksi takdirde sürüm numarasını değiştirin. Kodu değiştirmeniz gerekebilecek tek durumlar şunlardır:

* API yanıtında tanınmayan özellikler döndürüldüğünde kodunuz başarısız olur. Varsayılan olarak uygulamanızın anlayamadığını özellikleri yoksaymalıdır.

* Kodunuzun API istekleri devam ettirir ve yeni API sürümüne yeniden göndermeye çalışır. Örneğin bu durum, uygulamanız arama API 'sinden döndürülen devamlılık belirteçlerini devam ettirir (daha fazla bilgi için [Arama API başvurusunda](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)`@search.nextPageParameters` arayın).

Bu durumlardan biri sizin için geçerlidir, kodunuzu uygun şekilde değiştirmeniz gerekebilir. Aksi takdirde, sürüm 2019-05-06 ' in [yeni özelliklerini](#WhatsNew) kullanmaya başlamak istemediğiniz takdirde hiçbir değişiklik yapılması gerekmez.

Bir önizleme API sürümünden yükseltiyorsanız, yukarıdaki de geçerli olur, ancak bazı Önizleme özelliklerinin 2019-05-06 sürümünde mevcut olmadığını da bilmelisiniz:

* ["Buna benzer" sorgular](search-more-like-this.md)
* [CSV blobu dizin oluşturma](search-howto-index-csv-blobs.md)
* [Cosmos DB Dizin oluşturucular için MongoDB API desteği](search-howto-index-cosmosdb.md)

Kodunuz bu özellikleri kullanıyorsa, bunların kullanımını kaldırmadan API sürüm 2019-05-06 ' e yükseltemezsiniz.

> [!IMPORTANT]
> Önizleme API 'Leri test ve değerlendirme amaçlıdır ve üretim ortamlarında kullanılmamalıdır.
> 

### <a name="upgrading-complex-types"></a>Karmaşık türler yükseltiliyor

Kodunuz eski önizleme API sürümleri 2017-11-11-Önizleme veya 2016-09-01-önizleme ile karmaşık türler kullanıyorsa, sürüm 2019-05-06 bölümünde bilmeniz gereken bazı yeni ve değiştirilmiş sınırlar vardır:

+ Alt alanların derinliğine ve Dizin başına karmaşık koleksiyonların sayısına ilişkin sınırlar düşürüldü. Önizleme API-sürümlerini kullanarak bu sınırları aşan dizinler oluşturduysanız, API sürüm 2019-05-06 kullanarak bunları güncelleştirme veya yeniden oluşturma girişimleri başarısız olur. Bu sizin için geçerliyse, şemanızı yeni sınırlara uyacak şekilde yeniden tasarlamanız ve sonra dizininizi yeniden oluşturmanız gerekir.

+ Her belge için karmaşık koleksiyonların öğe sayısında API-sürüm 2019-05-06 ' de yeni bir sınır vardır. Önizleme API-sürümlerini kullanarak bu sınırları aşan belgelerle dizinler oluşturduysanız, API sürümü 2019-05-06 kullanarak bu verileri yeniden dizinle ilgili herhangi bir girişim başarısız olur. Bu sizin için geçerliyse, verilerinizin dizinini yeniden oluşturmadan önce belge başına karmaşık koleksiyon öğelerinin sayısını azaltmanız gerekir.

Daha fazla bilgi için bkz. [Azure bilişsel arama Için hizmet limitleri](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Eski bir karmaşık tür yapısını yükseltme

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

Arama REST API başvuru belgelerini gözden geçirin. Sorunlarla karşılaşırsanız [StackOverflow](https://stackoverflow.com/) hakkında yardım isteyin veya [desteğe başvurun](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Arama hizmeti REST API başvurusu](https://docs.microsoft.com/rest/api/searchservice/)

