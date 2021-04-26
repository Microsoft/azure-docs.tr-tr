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
ms.date: 04/02/2021
ms.openlocfilehash: 0a9b1a746a5228ba958b9c1c0cca38b088ca1176
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222256"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Azure Bilişsel Arama karmaşık veri türlerini modelleme

Azure Bilişsel Arama dizinini doldurmak için kullanılan dış veri kümeleri birçok şekilde gelebilir. Bazen hiyerarşik veya iç içe geçmiş alt yapıları içerirler. Örnekler tek bir müşteri için birden çok adres, tek bir SKU için birden çok renk ve boyut, tek bir kitabın birden çok yazarı vb. içerebilir. Modelleme koşullarında, bu yapıları *karmaşık*, *bileşik*, *bileşik* veya *toplama* veri türleri olarak ifade edilen olarak görebilirsiniz. Bu kavram için kullanılan Azure Bilişsel Arama terimi karmaşık bir **türdür**. Azure Bilişsel Arama 'de karmaşık türler **karmaşık alanlar** kullanılarak modellenir. Karmaşık alan, diğer karmaşık türler de dahil olmak üzere herhangi bir veri türünde olabilecek alt öğeler (alt alanlar) içeren bir alandır. Bu, bir programlama dilinde yapılandırılmış veri türleri gibi benzer bir şekilde çalışmaktadır.

Karmaşık alanlar, veri türüne bağlı olarak belgedeki tek bir nesneyi ya da bir nesne dizisini temsil eder. Türündeki alanlar `Edm.ComplexType` tek nesneleri temsil ederken, tür alanları `Collection(Edm.ComplexType)` nesnelerin dizilerini temsil eder.

Azure Bilişsel Arama, karmaşık türleri ve koleksiyonları yerel olarak destekler. Bu türler, bir Azure Bilişsel Arama dizininde neredeyse tüm JSON yapılarını modelleyebilir. Azure Bilişsel Arama API 'lerinin önceki sürümlerinde yalnızca düzleştirilmiş satır kümeleri içeri aktarılabilir. En yeni sürümde, dizininiz artık kaynak verilere daha yakından karşılık olabilir. Diğer bir deyişle, kaynak verilerinizde karmaşık türler varsa, dizininiz de karmaşık türlere sahip olabilir.

Başlamak için, Azure portal **verileri Içeri aktarma** Sihirbazı ' nda yükleyebilmeniz için [oteller veri kümesini](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)öneririz. Sihirbaz, kaynakta karmaşık türleri algılar ve algılanan yapıları temel alan bir dizin şeması önerir.

> [!Note]
> Karmaşık türler için destek, ' den itibaren genel kullanıma sunulmuştur `api-version=2019-05-06` . 
>
> Arama çözümünüz bir koleksiyondaki düzleştirilmiş veri kümelerinde daha önceki geçici çözümler üzerine inşa ediyorsanız, dizininizi en yeni API sürümünde desteklenen karmaşık türleri içerecek şekilde değiştirmelisiniz. API sürümlerini yükseltme hakkında daha fazla bilgi için bkz. [en yeni REST API sürümüne yükseltme](search-api-migration.md) veya [en yeni .NET SDK sürümüne yükseltme](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Karmaşık bir yapı örneği

Aşağıdaki JSON belgesi basit alanlar ve karmaşık alanlardan oluşur. Ve gibi karmaşık alanların `Address` `Rooms` alt alanları vardır. `Address` belgedeki tek bir nesne olduğundan, bu alt alanlar için tek bir değer kümesi vardır. Buna karşılık, `Rooms` koleksiyon içindeki her nesne için bir tane olmak üzere alt alanları için birden çok değer kümesi vardır.


```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Tags": ["Free wifi", "on-site parking", "indoor pool", "continental breakfast"],
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "RoomNumber": 1105,
      "BaseRate": 96.99,
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99,
    }
    . . .
  ]
}
```

## <a name="indexing-complex-types"></a>Karmaşık türler dizinleniyor

Dizin oluşturma sırasında, tek bir belge içindeki tüm karmaşık koleksiyonlar üzerinde en fazla 3000 öğe olabilir. Karmaşık bir koleksiyonun bir öğesi bu koleksiyonun üyesidir, bu nedenle Odalar durumunda (otel örneğinde tek karmaşık koleksiyon), her oda bir öğedir. Yukarıdaki örnekte, "gizli nokta Motel" 500 Odalar varsa, otel belgesinde 500 oda öğesi olur. İç içe karmaşık koleksiyonlar için, dış (üst) öğesine ek olarak, iç içe geçmiş her öğe de sayılır.

Bu sınır yalnızca karmaşık koleksiyonlar için geçerlidir; karmaşık türler (örneğin, adres) veya dize koleksiyonları (Etiketler gibi) için geçerlidir.

## <a name="creating-complex-fields"></a>Karmaşık alanlar oluşturma

Herhangi bir dizin tanımında olduğu gibi, karmaşık türler içeren bir şema oluşturmak için Portal, [REST API](/rest/api/searchservice/create-index)veya [.NET SDK 'sını](/dotnet/api/azure.search.documents.indexes.models.searchindex) kullanabilirsiniz. 

Aşağıdaki örnek, basit alanlar, koleksiyonlar ve karmaşık türler içeren bir JSON Dizin şemasını gösterir. Karmaşık bir tür içinde her bir alt alanın bir tür olduğunu ve en üst düzey alanların olduğu gibi özniteliklere sahip olabileceğini unutmayın. Şema yukarıdaki örnek verilere karşılık gelir. `Address` , koleksiyon olmayan karmaşık bir alandır (otel bir adrese sahiptir). `Rooms` karmaşık bir koleksiyon alanıdır (otel birçok odaya sahiptir).

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

Genel içindeki alanlar için uygulanan [yeniden dizin oluşturma kuralları](search-howto-reindex.md) , hala karmaşık alanlar için geçerlidir. Buradaki ana kuralların birçoğuna restating, bir alanı karmaşık bir türe eklemek dizin yeniden oluşturma gerektirmez, ancak çoğu değişiklik yapılır.

### <a name="structural-updates-to-the-definition"></a>Tanım için yapısal güncelleştirmeler

Bir dizin yeniden oluşturma gereksinimi olmadan istediğiniz zaman bir karmaşık alana yeni alt alanlar ekleyebilirsiniz. Örneğin, `Address` `Rooms` bir dizine en üst düzey bir alan eklemekle benzer şekilde, "ZipCode" öğesine veya "değişiklik" olarak eklenmesine izin verilir. Mevcut belgelerde, verilerinizi güncelleştirerek bu alanları açıkça doldurana kadar yeni alanlar için null değer vardır.

Karmaşık bir tür içinde her bir alt alanın bir türe sahip olduğunu ve en üst düzey alanların yaptığı gibi özniteliklere sahip olabileceğini unutmayın.

### <a name="data-updates"></a>Veri güncelleştirmeleri

Bir dizinde bulunan mevcut belgelerin güncelleştirilmesi, `upload` karmaşık ve basit alanlar için aynı şekilde çalışmaktadır; tüm alanlar değişir. Ancak `merge` (veya `mergeOrUpload` varolan bir belgeye uygulandığında) tüm alanlarda aynı şekilde çalışmaz. Özellikle, `merge` bir koleksiyon içindeki öğelerin birleştirilmesini desteklemez. Bu sınırlama, temel türlerin ve karmaşık koleksiyonların koleksiyonları için vardır. Bir koleksiyonu güncelleştirmek için, tam koleksiyon değerini almanız, değişiklikler yapmanız ve sonra yeni koleksiyonu Dizin API 'SI isteğine eklemeniz gerekir.

## <a name="searching-complex-fields"></a>Karmaşık alanları arama

Serbest biçimli arama ifadeleri karmaşık türlerle beklenen şekilde çalışır. Belgedeki herhangi bir yerde aranabilir alan veya alt alan eşleşiyorsa, belgenin kendisi bir eşleşmedir.

Birden çok hüküm ve işleçle sahip olduğunuzda sorgular daha fazla bilgi edinmiş olur ve bazı şartlar belirtilen alan adlarına sahiptir ve bu da [Lucene sözdizimi](query-lucene-syntax.md)ile mümkün olur. Örneğin, bu sorgu iki terimi ("Portland" ve "veya") adres alanının iki alt alanına göre eşleştirmeye çalışır:

> `search=Address/City:Portland AND Address/State:OR`

Bu gibi sorgular, filtrelerin aksine tam metin arama için *bağıntılı* değildir. Filtrelerde, karmaşık bir koleksiyonun alt alanları üzerinde bulunan sorgular, [ `any` veya `all` ](search-query-odata-collection-operators.md)içindeki Aralık değişkenleri kullanılarak bağıntılı. Yukarıdaki Lucene sorgusu, "Istanbul, Maine" ve "Istanbul, Oregon" içeren belgeleri, Oregon 'daki diğer şehirlerle birlikte döndürür. Bu durum her yan tümce tüm belgedeki alanının tüm değerlerine uygulandığı için, "geçerli alt belge" kavramı yoktur. Bunun hakkında daha fazla bilgi için bkz. [Azure bilişsel arama OData koleksiyon filtrelerini anlama](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Karmaşık alanları seçme

`$select`Parametresi, arama sonuçlarında hangi alanların döndürüleceğini seçmek için kullanılır. Karmaşık bir alanın belirli alt alanlarını seçmek üzere bu parametreyi kullanmak için üst alanı ve alt alanı eğik çizgiyle () ayırarak dahil edin `/` .

> `$select=HotelName, Address/City, Rooms/BaseRate`

Arama sonuçlarında isterseniz, alanların dizinde alınabilir olarak işaretlenmesi gerekir. Yalnızca, alınabilir olarak işaretlenen alanlar bir `$select` bildirimde kullanılabilir.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtre, model ve karmaşık alanları sıralama

Filtreleme ve ara sıra aramaları için kullanılan aynı [OData yolu söz dizimi](query-odata-filter-orderby-syntax.md) , bir arama isteğindeki alanları otomatik olarak sıralama, sıralama ve seçme için de kullanılabilir. Karmaşık türler için, hangi alt alanların sıralanabilir veya çok yönlü tablo olarak işaretleneceğini belirleyen kurallar uygulanır. Bu kurallar hakkında daha fazla bilgi için [create INDEX API Reference](/rest/api/searchservice/create-index)bölümüne bakın.

### <a name="faceting-sub-fields"></a>Alt alanları renklendirme

Herhangi bir alt alan, veya türünde olmadığı sürece, çok yönlü tablo olarak işaretlenebilir `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)` .

Model sonuçlarında döndürülen belge sayıları, karmaşık bir koleksiyondaki (Odalar) alt belgelerde değil, üst belge (otel) için hesaplanır. Örneğin, bir otelin "Suite" türünde 20 oda olduğunu varsayalım. Bu model parametresi verildiğinde `facet=Rooms/Type` model sayısı odalar için 20 değil, otel için bir tane olacaktır.

### <a name="sorting-complex-fields"></a>Karmaşık alanları sıralama

Sıralama işlemleri, belgeler (otel) için geçerlidir ve alt belgelere (Odalar) uygulanmaz. Odalar gibi karmaşık bir tür koleksiyonunuz olduğunda, odaların tümünde sıralayamazsınız. Aslında, hiçbir koleksiyonu sıralayamazsınız.

Alanların belge başına tek bir değeri olduğunda, alanın basit bir alan olması veya karmaşık bir türdeki alt alan olması durumunda sıralama işlemleri çalışır. Örneğin, `Address/City` otel başına yalnızca bir adres bulunduğundan, `$orderby=Address/City` Bu nedenle oteller şehre göre sıralanacaktır.

### <a name="filtering-on-complex-fields"></a>Karmaşık alanlarda filtreleme

Bir filtre ifadesinde karmaşık bir alanın alt alanlarına başvurabilirsiniz. Yalnızca oluşturma, sıralama ve alanları seçme için kullanılan aynı [OData yolu sözdizimini](query-odata-filter-orderby-syntax.md) kullanın. Örneğin, aşağıdaki filtre Kanada 'daki tüm oteller döndürür:

> `$filter=Address/Country eq 'Canada'`

Karmaşık bir koleksiyon alanını filtrelemek için [ `any` ve `all` işleçleriyle](search-query-odata-collection-operators.md)bir **lambda ifadesi** kullanabilirsiniz. Bu durumda, lambda ifadesinin **Aralık değişkeni** , alt alanları olan bir nesnedir. Standart OData yolu söz dizimi ile bu alt alanlara başvurabilirsiniz. Örneğin, aşağıdaki filtre en az bir lüks ve tüm içgörü olmayan odalar ile tüm oteller döndürür:

> `$filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)`

En üst düzey basit alanlarda olduğu gibi, karmaşık alanların basit alt alanları, yalnızca **filtrelenebilir** özniteliği Dizin tanımında ayarlanmış olan filtrelere dahil edilebilir `true` . Daha fazla bilgi için [create INDEX API Reference](/rest/api/searchservice/create-index)bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

**Veri alma** Sihirbazı 'nda [oteller veri kümesini](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) deneyin. Verilere erişmek için Benioku dosyasında sağlanmış Cosmos DB bağlantı bilgilerine ihtiyacınız olacaktır.

Bu bilgilerle, sihirbazdaki ilk adım yeni bir Azure Cosmos DB veri kaynağı oluşturmaktır. Sihirbazda daha fazla bilgi, hedef dizin sayfasına geldiğinizde karmaşık türlerde bir dizin görürsünüz. Bu dizini oluşturup yükleyin ve ardından yeni yapıyı anlamak için sorguları yürütün.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: içeri aktarma, dizin oluşturma ve sorgular için Portal Sihirbazı](search-get-started-portal.md)
