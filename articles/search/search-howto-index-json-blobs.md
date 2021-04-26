---
title: JSON Blobları üzerinde arama
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama blob Indexer 'ı kullanarak metin içeriği için Azure JSON bloblarını gezin. Dizin oluşturucular, Azure Blob depolama gibi seçili veri kaynakları için veri alımını otomatik hale getirir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259059"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Azure Bilişsel Arama blob Dizin Oluşturucu kullanarak JSON bloblarını dizin oluşturma

Bu makalede, JSON belgelerinden oluşan Bloblar için [bir blob dizin oluşturucunun nasıl yapılandırılacağı](search-howto-indexing-azure-blob-storage.md) gösterilir. Azure Blob depolama 'daki JSON blob 'ları genellikle şu biçimlerden herhangi birini varsayar:

+ Tek bir JSON belgesi
+ Düzgün biçimlendirilmiş JSON öğelerinden oluşan bir dizi içeren JSON belgesi
+ Bir yeni satır ile ayrılmış birden çok varlık içeren bir JSON belgesi

Blob Indexer, **`parsingMode`** Yapı ayrıştırma modlarına göre arama belgesinin çıkışını iyileştirmek için bir parametre sağlar ve aşağıdaki seçeneklerden oluşur:

| parsingMode | JSON belgesi | Description |
|--------------|-------------|--------------|
| **`json`** | Blob başına bir tane | varsayılanını JSON bloblarını tek bir metin öbeği olarak ayrıştırır. Her JSON BLOBU tek bir arama belgesi haline gelir. |
| **`jsonArray`** | Blob başına birden çok | Blob 'ta her bir dizi öğesinin ayrı bir arama belgesi haline geldiği bir JSON dizisini ayrıştırır.  |
| **`jsonLines`** | Blob başına birden çok | Birden çok JSON varlığı (aynı zamanda bir dizi) içeren bir blobu ayrıştırır ve tek tek öğeleri bir yeni satır ile ayrılmış olarak. Dizin Oluşturucu her yeni satırdan sonra yeni bir arama belgesi başlatır. |

Hem hem de için **`jsonArray`** **`jsonLines`** , blob Indexer 'ın aynı bloba oluşturulan birden çok arama belgesinde Belge anahtarının belirsizlenmesini nasıl işlediğini anlamak için [bir Blobun Dizin oluşturmayı](search-howto-index-one-to-many-blobs.md) gözden geçirmeniz gerekir.

Dizin Oluşturucu tanımı içinde, isteğe bağlı olarak, hedef arama dizininizi doldurmak için kaynak JSON belgesinin hangi özelliklerinin kullanıldığını seçmek üzere [alan eşlemelerini](search-indexer-field-mappings.md) ayarlayabilirsiniz. Örneğin, **`jsonArray`** ayrıştırma modunu kullanırken, dizi alt düzey bir özellik olarak varsa, **`document root`** dizinin blob içine yerleştirildiğini gösteren bir özellik ayarlayabilirsiniz.

Aşağıdaki bölümlerde her mod daha ayrıntılı olarak açıklanır. Dizin Oluşturucu istemcileri ve kavramları hakkında bilginiz yoksa bkz. [Arama Dizin Oluşturucu oluşturma](search-howto-create-indexers.md). Ayrıca, burada yinelenmemiş [temel blob Indexer yapılandırmasının](search-howto-indexing-azure-blob-storage.md)ayrıntıları hakkında bilgi sahibi olmanız gerekir.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Tek JSON belgelerinin dizinini oluştur (blob başına bir tane)

Varsayılan olarak, blob Dizin oluşturucular JSON bloblarını bir kapsayıcıdaki her blob için bir arama belgesi olan tek bir metin öbeği olarak ayrıştırır. JSON yapılandırılmış ise, arama belgesi bu yapıyı, tek tek alanlar olarak temsil edilen ayrı öğeler ile yansıtabilir. Örneğin, Azure Blob depolamada aşağıdaki JSON belgesine sahip olduğunu varsayalım:

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Blob Indexer, JSON belgesini tek bir arama belgesinde ayrıştırır, "Text", "Dateyayınlanan" ve "Tags" ile eşleşen bir dizini aynı ada ve aynı hedef dizin alanlarına göre karşıya yüklüyor. "Text", "Dateyayınlanan ve" Tags "alanları ile bir dizin verildiğinde, blob Indexer istekte yer alan eşleme olmadan doğru eşlemeyi çıkarabilir.

Varsayılan davranış, JSON blobu başına bir arama belgesi olsa da, ' JSON ' ayrıştırma modunun ayarlanması içerik için iç alan eşlemelerini değiştirir ve alanları, `content` arama dizinindeki gerçek alanlar içinde yükseltmekte. Ayrıştırma modunun örnek bir Dizin Oluşturucu tanımı şöyle **`json`** görünebilir:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Tüm Dizin oluşturucularda olduğu gibi, alanlar açıkça eşleşmezse, [temel blob Indexer yapılandırması](search-howto-indexing-azure-blob-storage.md)bölümünde açıklandığı gibi BLOB içeriği ve meta veriler için kullanılabilen örtük alan eşlemelerini kullanmadığınız sürece tek tek [alan eşlemelerini](search-indexer-field-mappings.md) açıkça belirtmeniz beklenir.

### <a name="json-example-single-hotel-json-files"></a>JSON örneği (tek otel JSON dosyaları)

GitHub 'daki [otel JSON belgesi veri kümesi](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) , her blob YAPıLANDıRıLMıŞ bir JSON dosyasını temsıl ettiğinden JSON ayrıştırmayı test etmek için yararlıdır. Veri dosyalarını blob depolamaya yükleyebilir ve **verileri Içeri aktarma** Sihirbazı 'nı kullanarak bu içeriğin tek tek arama belgelerine nasıl ayrıştırılabileceğini hızla değerlendirebilirsiniz. 

Veri kümesi beş blobdan oluşur ve her biri adres koleksiyonu ve odalar koleksiyonu olan bir otel belgesi içerir. Blob Indexer her iki koleksiyonu algılar ve dizin şemasında giriş belgelerinin yapısını yansıtır.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON dizilerini ayrıştırma

Alternatif olarak, JSON dizi seçeneğini de kullanabilirsiniz. Blob 'lar düzgün biçimlendirilmiş bir JSON nesneleri dizisi içerdiğinde ve her öğenin ayrı bir arama belgesi olmasını istiyorsanız bu seçenek faydalıdır. Kullanarak **`jsonArrays`** , AŞAĞıDAKI JSON blobu her biri ve alanları içeren üç ayrı belge `"id"` üretir `"text"` .  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

**`parameters`** Dizin oluşturucudaki özelliği ayrıştırma modu değerlerini içerir. JSON dizisi için Dizin Oluşturucu tanımı aşağıdaki örneğe benzer görünmelidir.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>jsonArrays örneği (klinik deneme sürümleri örnek verileri)

GitHub 'da [belirlenen klinik deneme sürümleri JSON veri kümesi](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) , JSON dizisi ayrıştırmayı test etmek için yararlıdır. Veri dosyalarını blob depolamaya yükleyebilir ve **verileri Içeri aktarma** Sihirbazı 'nı kullanarak bu içeriğin tek tek arama belgelerine nasıl ayrıştırılabileceğini hızla değerlendirebilirsiniz. 

Veri kümesi, toplam 100 varlık için her biri bir JSON dizisi içeren sekiz blobdan oluşur. Varlıklar, hangi alanların doldurulduğuna göre farklılık gösterir, ancak nihai sonuç tüm bloblarda her bir varlık için tek bir arama belgesidir.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>İç içe JSON dizilerini ayrıştırma

İç içe yerleştirilmiş öğeleri olan JSON dizileri için, **`documentRoot`** birden çok düzeyli bir yapıyı göstermek için bir belirtebilirsiniz. Örneğin, bloblarınız şunun gibi görünür:

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

Bu yapılandırmayı, özelliğinde yer alan diziyi dizinlemek için kullanın `level2` :

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>Newlines ile ayrılmış JSON varlıklarını ayrıştırma

Blobun, bir yeni satır tarafından ayrılmış birden çok JSON varlığı içeriyorsa ve her bir öğenin ayrı bir arama belgesi olmasını istiyorsanız kullanın **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

JSON satırları için Dizin Oluşturucu tanımı aşağıdaki örneğe benzer görünmelidir.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>jsonLines örneği (Caselaw örnek verileri)

GitHub 'da [ayarlanan Caselaw JSON VERILERI](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) JSON yeni satır ayrıştırmayı test etmek için yararlıdır. Diğer örneklerde olduğu gibi, bu verileri blob depolamaya yükleyebilir ve **verileri Içeri aktarma** Sihirbazı 'nı kullanarak ayrı bloblarda ayrıştırma modunun etkisini hızlıca değerlendirebilirsiniz.

Veri kümesi, her varlığın tek bir yasal durumu açıkladığı, yeni bir satırla ayrı 10 JSON varlığı içeren bir Blobun oluşur. Nihai sonuç, varlık başına bir arama belgesidir.

## <a name="map-json-fields-to-search-fields"></a>JSON alanlarını arama alanlarına eşleyin

Alan eşlemeleri, alan adlarının ve türlerinin aynı olmadığı durumlarda kaynak alanı bir hedef alanla ilişkilendirmek için kullanılır. Ancak, alan eşlemeleri bir JSON belgesinin parçalarını eşleştirmek için de kullanılabilir ve bunları arama belgesinin en üst düzey alanlarına "kaldırın".

Aşağıdaki örnekte bu senaryo gösterilmektedir. Genel olarak alan eşlemeleri hakkında daha fazla bilgi için bkz. [alan eşlemeleri](search-indexer-field-mappings.md).

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Şu alanlara sahip bir arama dizini varsayın: türü, türü `text` `Edm.String` `date` `Edm.DateTimeOffset` ve `tags` türü `Collection(Edm.String)` . Dizindeki kaynak ve alanda yer alan "Dateyayımlandı" arasındaki tutarsızlığı fark edin `date` . JSON 'nizi istenen şekle eşlemek için aşağıdaki alan eşlemelerini kullanın:

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

Kaynak alanları [JSON işaretçisi](https://tools.ietf.org/html/rfc6901) gösterimi kullanılarak belirtilir. JSON belgenizin köküne başvuracak bir eğik çizgiyle başlayıp, eğik çizgi ile ayrılmış yolu kullanarak istediğiniz özelliği (rastgele iç içe geçme düzeyinde) seçin.

Ayrıca, sıfır tabanlı bir dizin kullanarak ayrı dizi öğelerine de başvurabilirsiniz. Örneğin, yukarıdaki örnekteki "Etiketler" dizisinin ilk öğesini seçmek için, şöyle bir alan eşlemesi kullanın:

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> **`sourceFieldName`** JSON blobu içinde mevcut olmayan bir özelliğe başvuruyorsa, bu eşleme bir hata olmadan atlanır. Bu davranış, dizin oluşturmanın, farklı bir şemaya (yaygın kullanım örneği olan) sahip JSON Blobları için devam etmesine olanak tanır. Doğrulama denetimi olmadığından, yanlış bir nedenle belge kaybetmemeniz için, yazım hatalarını dikkatlice inceleyin.
>

## <a name="next-steps"></a>Sonraki adımlar

+ [Blob Dizin oluşturucuyu yapılandırma](search-howto-indexing-azure-blob-storage.md)
+ [Alan eşlemelerini tanımlama](search-indexer-field-mappings.md)
+ [Dizin oluşturuculara genel bakış](search-indexer-overview.md)
+ [CSV bloblarını blob Indexer ile dizin oluşturma](search-howto-index-csv-blobs.md)
+ [Öğretici: Azure Blob depolamadan yarı yapılandırılmış verileri arama](search-semi-structured-data.md)