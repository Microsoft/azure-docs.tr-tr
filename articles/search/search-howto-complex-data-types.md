---
title: Karmaşık veri türlerini modelleme
titleSuffix: Azure Cognitive Search
description: İç içe geçmiş veya hiyerarşik veri yapıları, ComplexType ve Collections veri türleri kullanılarak Azure Bilişsel Arama dizininde modellenebilir.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c7578b84fe1d23d2b4d97aa263cac576305db240
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889925"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Azure Bilişsel Arama karmaşık veri türlerini modelleme

Azure Bilişsel Arama dizinini doldurmak için kullanılan dış veri kümeleri birçok şekilde gelebilir. Bazen hiyerarşik veya iç içe geçmiş alt yapıları içerirler. Örnekler tek bir müşteri için birden çok adres, tek bir SKU için birden çok renk ve boyut, tek bir kitabın birden çok yazarı vb. içerebilir. Modelleme koşullarında, bu yapıları *karmaşık*, *bileşik*, *bileşik*veya *toplama* veri türleri olarak ifade edilen olarak görebilirsiniz. Bu kavram için kullanılan Azure Bilişsel Arama terimi karmaşık bir **türdür**. Azure Bilişsel Arama 'de karmaşık türler **karmaşık alanlar**kullanılarak modellenir. Karmaşık alan, diğer karmaşık türler de dahil olmak üzere herhangi bir veri türünde olabilecek alt öğeler (alt alanlar) içeren bir alandır. Bu, bir programlama dilinde yapılandırılmış veri türleri gibi benzer bir şekilde çalışmaktadır.

Karmaşık alanlar, veri türüne bağlı olarak belgedeki tek bir nesneyi ya da bir nesne dizisini temsil eder. `Edm.ComplexType` alanlar tek nesneleri temsil ederken, `Collection(Edm.ComplexType)` türündeki alanlar nesne dizilerini temsil eder.

Azure Bilişsel Arama, karmaşık türleri ve koleksiyonları yerel olarak destekler. Bu türler, bir Azure Bilişsel Arama dizininde neredeyse tüm JSON yapılarını modelleyebilir. Azure Bilişsel Arama API 'lerinin önceki sürümlerinde yalnızca düzleştirilmiş satır kümeleri içeri aktarılabilir. En yeni sürümde, dizininiz artık kaynak verilere daha yakından karşılık olabilir. Diğer bir deyişle, kaynak verilerinizde karmaşık türler varsa, dizininiz de karmaşık türlere sahip olabilir.

Başlamak için, Azure portal **verileri Içeri aktarma** Sihirbazı ' nda yükleyebilmeniz için [oteller veri kümesini](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)öneririz. Sihirbaz, kaynakta karmaşık türleri algılar ve algılanan yapıları temel alan bir dizin şeması önerir.

> [!Note]
> Karmaşık türler için destek, `api-version=2019-05-06`' de genel kullanıma sunulmuştur. 
>
> Arama çözümünüz bir koleksiyondaki düzleştirilmiş veri kümelerinde daha önceki geçici çözümler üzerine inşa ediyorsanız, dizininizi en yeni API sürümünde desteklenen karmaşık türleri içerecek şekilde değiştirmelisiniz. API sürümlerini yükseltme hakkında daha fazla bilgi için bkz. [en yeni REST API sürümüne yükseltme](search-api-migration.md) veya [en yeni .NET SDK sürümüne yükseltme](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Karmaşık bir yapı örneği

Aşağıdaki JSON belgesi basit alanlar ve karmaşık alanlardan oluşur. `Address` ve `Rooms`gibi karmaşık alanların alt alanları vardır. `Address` belgede tek bir nesne olduğundan, bu alt alanlar için tek bir değer kümesi vardır. Buna karşılık `Rooms`, alt alanları için bir, koleksiyondaki her nesne için birden çok değer kümesine sahiptir.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Karmaşık alanlar oluşturma

Herhangi bir dizin tanımında olduğu gibi, karmaşık türler içeren bir şema oluşturmak için Portal, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)veya [.NET SDK 'sını](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) kullanabilirsiniz. 

Aşağıdaki örnek, basit alanlar, koleksiyonlar ve karmaşık türler içeren bir JSON Dizin şemasını gösterir. Karmaşık bir tür içinde her bir alt alanın bir tür olduğunu ve en üst düzey alanların olduğu gibi özniteliklere sahip olabileceğini unutmayın. Şema yukarıdaki örnek verilere karşılık gelir. `Address`, koleksiyon olmayan karmaşık bir alandır (otel bir adrese sahiptir). `Rooms` karmaşık bir koleksiyon alanıdır (otel birçok odaya sahiptir).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Cognitive Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Karmaşık alanları güncelleştirme

Genel içindeki alanlar için uygulanan [yeniden dizin oluşturma kuralları](search-howto-reindex.md) , hala karmaşık alanlar için geçerlidir. Burada ana kuralların birçoğuna restating bir alan eklemek için dizin yeniden oluşturma gerekmez, ancak çoğu değişiklik yapılır.

### <a name="structural-updates-to-the-definition"></a>Tanım için yapısal güncelleştirmeler

Bir dizin yeniden oluşturma gereksinimi olmadan istediğiniz zaman bir karmaşık alana yeni alt alanlar ekleyebilirsiniz. Örneğin, bir dizine en üst düzey bir alan eklemekle benzer şekilde, `Rooms` "ZipCode" `Address` veya "değişiklik" olarak eklenmesine izin verilir. Mevcut belgelerde, verilerinizi güncelleştirerek bu alanları açıkça doldurana kadar yeni alanlar için null değer vardır.

Karmaşık bir tür içinde her bir alt alanın bir türe sahip olduğunu ve en üst düzey alanların yaptığı gibi özniteliklere sahip olabileceğini unutmayın.

### <a name="data-updates"></a>Veri güncelleştirmeleri

`upload` eylemi ile bir dizinde bulunan mevcut belgelerin güncelleştirilmesi, karmaşık ve basit alanlar için aynı şekilde çalışmaktadır--tüm alanlar değişir. Ancak, `merge` (veya var olan bir belgeye uygulandığında `mergeOrUpload`) tüm alanlarda aynı şekilde çalışmaz. Özellikle `merge`, bir koleksiyon içindeki öğelerin birleştirilmesini desteklemez. Bu sınırlama, temel türlerin ve karmaşık koleksiyonların koleksiyonları için vardır. Bir koleksiyonu güncelleştirmek için, tam koleksiyon değerini almanız, değişiklikler yapmanız ve sonra yeni koleksiyonu Dizin API 'SI isteğine eklemeniz gerekir.

## <a name="searching-complex-fields"></a>Karmaşık alanları arama

Serbest biçimli arama ifadeleri karmaşık türlerle beklenen şekilde çalışır. Belgedeki herhangi bir yerde aranabilir alan veya alt alan eşleşiyorsa, belgenin kendisi bir eşleşmedir.

Birden çok hüküm ve işleçle sahip olduğunuzda sorgular daha fazla bilgi edinmiş olur ve bazı şartlar belirtilen alan adlarına sahiptir ve bu da [Lucene sözdizimi](query-lucene-syntax.md)ile mümkün olur. Örneğin, bu sorgu iki terimi ("Portland" ve "veya") adres alanının iki alt alanına göre eşleştirmeye çalışır:

    search=Address/City:Portland AND Address/State:OR

Bu gibi sorgular, filtrelerin aksine tam metin arama için *bağıntılı* değildir. Filtrelerde, karmaşık bir koleksiyonun alt alanları üzerindeki sorgular, [`any` veya `all`](search-query-odata-collection-operators.md)Aralık değişkenleri kullanılarak bağıntılı. Yukarıdaki Lucene sorgusu, "Istanbul, Maine" ve "Istanbul, Oregon" içeren belgeleri, Oregon 'daki diğer şehirlerle birlikte döndürür. Bu durum her yan tümce tüm belgedeki alanının tüm değerlerine uygulandığı için, "geçerli alt belge" kavramı yoktur. Bunun hakkında daha fazla bilgi için bkz. [Azure bilişsel arama OData koleksiyon filtrelerini anlama](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Karmaşık alanları seçme

`$select` parametresi, arama sonuçlarında hangi alanların döndürüleceğini seçmek için kullanılır. Karmaşık bir alanın belirli alt alanlarını seçmek üzere bu parametreyi kullanmak için üst alanı ve alt alanı eğik çizgiyle (`/`) ayırarak dahil edin.

    $select=HotelName, Address/City, Rooms/BaseRate

Arama sonuçlarında isterseniz, alanların dizinde alınabilir olarak işaretlenmesi gerekir. `$select` bildiriminde yalnızca alınabilir olarak işaretlenen alanlar kullanılabilir.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtre, model ve karmaşık alanları sıralama

Filtreleme ve ara sıra aramaları için kullanılan aynı [OData yolu söz dizimi](query-odata-filter-orderby-syntax.md) , bir arama isteğindeki alanları otomatik olarak sıralama, sıralama ve seçme için de kullanılabilir. Karmaşık türler için, hangi alt alanların sıralanabilir veya çok yönlü tablo olarak işaretleneceğini belirleyen kurallar uygulanır. Bu kurallar hakkında daha fazla bilgi için [create INDEX API Reference](https://docs.microsoft.com/rest/api/searchservice/create-index#request)bölümüne bakın.

### <a name="faceting-sub-fields"></a>Alt alanları renklendirme

`Edm.GeographyPoint` veya `Collection(Edm.GeographyPoint)`türünde olmadığı sürece herhangi bir alt alan, çok yönlü tablo olarak işaretlenebilir.

Model sonuçlarında döndürülen belge sayıları, karmaşık bir koleksiyondaki (Odalar) alt belgelerde değil, üst belge (otel) için hesaplanır. Örneğin, bir otelin "Suite" türünde 20 oda olduğunu varsayalım. Bu model parametresi `facet=Rooms/Type`verildiğinde model sayısı odalar için 20 değil, otel için bir tane olacaktır.

### <a name="sorting-complex-fields"></a>Karmaşık alanları sıralama

Sıralama işlemleri, belgeler (otel) için geçerlidir ve alt belgelere (Odalar) uygulanmaz. Odalar gibi karmaşık bir tür koleksiyonunuz olduğunda, odaların tümünde sıralayamazsınız. Aslında, hiçbir koleksiyonu sıralayamazsınız.

Alanların belge başına tek bir değeri olduğunda, alanın basit bir alan olması veya karmaşık bir türdeki alt alan olması durumunda sıralama işlemleri çalışır. Örneğin, otel başına yalnızca bir adres olduğundan `Address/City` sıralanabilir olmasına izin verilir. bu nedenle, `$orderby=Address/City` oteller şehre göre sıralanır.

### <a name="filtering-on-complex-fields"></a>Karmaşık alanlarda filtreleme

Bir filtre ifadesinde karmaşık bir alanın alt alanlarına başvurabilirsiniz. Yalnızca oluşturma, sıralama ve alanları seçme için kullanılan aynı [OData yolu sözdizimini](query-odata-filter-orderby-syntax.md) kullanın. Örneğin, aşağıdaki filtre Kanada 'daki tüm oteller döndürür:

    $filter=Address/Country eq 'Canada'

Karmaşık bir koleksiyon alanını filtrelemek için, [`any` ve `all` işleçleriyle](search-query-odata-collection-operators.md)bir **lambda ifadesi** kullanabilirsiniz. Bu durumda, lambda ifadesinin **Aralık değişkeni** , alt alanları olan bir nesnedir. Standart OData yolu söz dizimi ile bu alt alanlara başvurabilirsiniz. Örneğin, aşağıdaki filtre en az bir lüks ve tüm içgörü olmayan odalar ile tüm oteller döndürür:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Üst düzey basit alanlarda olduğu gibi, karmaşık alanların basit alt alanları, yalnızca **filtrelenebilir** özniteliği Dizin tanımında `true` olarak ayarlandıysa filtrelere dahil edilebilir. Daha fazla bilgi için [create INDEX API Reference](https://docs.microsoft.com/rest/api/searchservice/create-index#request)bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

**Veri alma** Sihirbazı 'nda [oteller veri kümesini](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) deneyin. Verilere erişmek için Benioku dosyasında sağlanmış Cosmos DB bağlantı bilgilerine ihtiyacınız olacaktır.

Bu bilgilerle, sihirbazdaki ilk adım yeni bir Azure Cosmos DB veri kaynağı oluşturmaktır. Sihirbazda daha fazla bilgi, hedef dizin sayfasına geldiğinizde karmaşık türlerde bir dizin görürsünüz. Bu dizini oluşturup yükleyin ve ardından yeni yapıyı anlamak için sorguları yürütün.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: içeri aktarma, dizin oluşturma ve sorgular için Portal Sihirbazı](search-get-started-portal.md)
