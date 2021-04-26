---
title: Dizin oluşturucularda alan eşlemeleri
titleSuffix: Azure Cognitive Search
description: Dizin oluşturucudaki alan eşlemelerini, alan adlarında ve veri gösterimlerinde farklılıklar için bir hesaba göre yapılandırın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: fb3a77291d8b24d5774094533f8c214f1527d771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430454"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Azure Bilişsel Arama Dizinleyicileri kullanarak alan eşlemeleri ve dönüştürmeler

![Dizin Oluşturucu aşamaları](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "Dizin Oluşturucu aşamaları")

Azure Bilişsel Arama Dizinleyicileri kullanırken, bazen giriş verilerinin hedef dizininizin şemasıyla tam olarak eşleşmediğini fark edersiniz. Bu durumlarda, dizin oluşturma işlemi sırasında verilerinizi yeniden şekillendirmek için **alan eşlemelerini** kullanabilirsiniz.

Alan eşlemelerinin yararlı olduğu bazı durumlar:

* Veri kaynağınız adlı bir alana sahip `_id` , ancak Azure bilişsel arama alt çizgiyle başlayan alan adlarına izin vermiyor. Alan eşleme, bir alanı etkili bir şekilde yeniden adlandırmanızı sağlar.
* Aynı veri kaynağı verilerinden dizindeki çeşitli alanları doldurmak istiyorsunuz. Örneğin, bu alanlara farklı çözümleyiciler uygulamak isteyebilirsiniz.
* Bir dizin alanını birden fazla veri kaynağından alınan verilerle doldurmak istiyorsunuz ve veri kaynakları her biri farklı alan adları kullanır.
* Verilerinizi Base64 olarak kodlamanız veya kodu çözmelisiniz. Alan eşlemeleri, Base64 kodlaması ve kod çözme işlevleri dahil olmak üzere çeşitli **eşleme işlevlerini** destekler.

> [!NOTE]
> Dizin oluşturucularda alan eşlemeleri, veri alanlarını dizin alanlarıyla eşlemenin basit bir yoludur. bu sayede, hafif veri dönüştürme özelliği de vardır. Daha karmaşık veriler, dizini oluşturmak için kullanılan bir biçime yeniden şekillendirmek üzere ön işleme gerektirebilir. Göz önünde bulundurmanız gerekebilecek bir seçenek [Azure Data Factory](../data-factory/index.yml).

## <a name="set-up-field-mappings"></a>Alan eşlemelerini ayarlama

Bir alan eşlemesi üç bölümden oluşur:

1. `sourceFieldName`Veri kaynağınızdaki bir alanı temsil eden bir. Bu özellik gereklidir.
2. `targetFieldName`Arama dizininizdeki bir alanı temsil eden isteğe bağlı. Atlanırsa, veri kaynağıyla aynı ad kullanılır.
3. `mappingFunction`Önceden tanımlanmış birkaç işlevden birini kullanarak verilerinizi dönüştürebilen isteğe bağlı bir. Bu, hem giriş hem de çıkış alanı eşlemelerinde uygulanabilir. İşlevlerin tam listesi [aşağıda](#mappingFunctions)verilmiştir.

Alan eşlemeleri `fieldMappings` , Dizin Oluşturucu tanımının dizisine eklenir.

> [!NOTE]
> Hiçbir alan eşlemesi eklendiyse, Dizin oluşturucular veri kaynağı alanlarının aynı ada sahip dizin alanlarıyla eşlenmesi gerektiğini varsayar. Alan eşlemesi eklemek, kaynak ve hedef alan için bu varsayılan alan eşlemelerini kaldırır. [BLOB depolama Dizin Oluşturucu](search-howto-indexing-azure-blob-storage.md)gibi bazı Dizin oluşturucular, dizin anahtarı alanı için varsayılan alan eşlemelerini ekler.

## <a name="map-fields-using-rest"></a>REST kullanarak alanları eşleme

[Create Indexer](/rest/api/searchservice/create-Indexer) API isteği kullanarak yeni bir Dizin Oluşturucu oluştururken alan eşlemeleri ekleyebilirsiniz. Var olan bir dizin oluşturucunun alan eşlemelerini [güncelleştirme Dizin Oluşturucu](/rest/api/searchservice/update-indexer) API isteği kullanarak yönetebilirsiniz.

Örneğin, kaynak alanı farklı bir ada sahip bir hedef alanla eşleme aşağıda verilmiştir:

```JSON
PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Kaynak alana, birden çok alan eşlemesinde başvurulabilir. Aşağıdaki örnek, bir alanın nasıl "çatallı" olduğunu gösterir ve aynı kaynak alanı iki farklı Dizin alanına kopyalar:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Bilişsel Arama, alan eşlemelerinde alan ve işlev adlarını çözümlemek için büyük/küçük harfe duyarsız karşılaştırma kullanır. Bu kullanışlı bir durumdur (tüm büyük küçük harfe sahip olmanız gerekmez), ancak veri kaynağınız veya dizininizin yalnızca büyük/küçük harfe göre farklı alanlar olamayacağı anlamına gelir.  
>

## <a name="map-fields-using-net"></a>.NET kullanarak alanları eşleme

.NET SDK 'da, özelliklere [](/dotnet/api/azure.search.documents.indexes.models.fieldmapping) `SourceFieldName` ve `TargetFieldName` isteğe bağlı bir başvuruya sahip FieldMapping sınıfını kullanarak alan eşlemelerini tanımlarsınız `MappingFunction` .

Özelliği doğrudan ayarlayarak Dizin Oluşturucuyu veya daha yenisini oluştururken alan eşlemelerini belirtebilirsiniz `Indexer.FieldMappings` .

Aşağıdaki C# örneği, bir Dizin Oluşturucu oluştururken alan eşlemelerini ayarlar.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Alan eşleme işlevleri

Alan eşleme işlevi, bir alanın içeriğini dizinde depolanmadan önce dönüştürür. Şu eşleme işlevleri şu anda destekleniyor:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [URL kod çözme](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode işlevi

Giriş dizesinin *URL güvenli* Base64 kodlamasını gerçekleştirir. Girişin UTF-8 kodlamalı olduğunu varsayar.

#### <a name="example---document-key-lookup"></a>Örnek-belge anahtarı arama

Azure Bilişsel Arama belge anahtarında yalnızca URL güvenli karakterler görünebilir (çünkü müşterilerin [Arama API](/rest/api/searchservice/lookup-document) 'sini kullanarak belgeyi ele alabilmesi gerekir). Anahtarınızın kaynak alanı URL güvenli olmayan karakterler içeriyorsa, `base64Encode` Bu işlevi dizin oluşturma sırasında dönüştürmek için kullanabilirsiniz. Ancak, bir belge anahtarı (dönüştürme öncesinde ve sonrasında) 1.024 karakterden daha uzun olamaz.

Arama zamanında kodlanmış anahtarı aldığınızda, `base64Decode` Bu işlevi kullanarak özgün anahtar değerini alabilir ve kaynak belgeyi almak için kullanabilirsiniz.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

#### <a name="example---preserve-original-values"></a>Örnek-özgün değerleri koru

[BLOB Storage Indexer](search-howto-indexing-azure-blob-storage.md) , bir `metadata_storage_path` alan eşlemesi belirtilmemişse, blob 'un URI 'sinden dizin anahtar alanına bir alan eşlemesi otomatik olarak ekler. Bu değer, Azure Bilişsel Arama belge anahtarı olarak kullanılması güvenli olması için Base64 kodlandı. Aşağıdaki örnek, ' ın bir *URL-güvenli* Base64 kodlamalı sürümünü bir alanına nasıl eşleneceğini gösterir `metadata_storage_path` `index_key` ve bir alanda özgün değeri korur `metadata_storage_path` :

```JSON

"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

Eşleme işleviniz için bir Parameters özelliği eklemezseniz, varsayılan değer olarak belirlenmiştir `{"useHttpServerUtilityUrlTokenEncode" : true}` .

Azure Bilişsel Arama iki farklı Base64 kodlaması destekler. Aynı alanı kodlarken ve kodunu çözerken aynı parametreleri kullanmanız gerekir. Daha fazla bilgi için bkz. hangi parametrelerin kullanılacağına karar vermek için [Base64 kodlama seçenekleri](#base64details) .

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode işlevi

Giriş dizesinin Base64 kodunu çözme işlemini gerçekleştirir. Girişin, *URL-güvenli* Base64 kodlamalı bir dize olduğu varsayılır.

#### <a name="example---decode-blob-metadata-or-urls"></a>Örnek-blob meta verileri veya URL kodunu çöz

Kaynak verileriniz, düz metin olarak aranabilir olmasını istediğiniz blob meta veri dizeleri veya Web URL 'Leri gibi Base64 kodlamalı dizeler içerebilir. `base64Decode`Arama dizininizi doldururken, kodlanmış verileri normal dizelere geri döndürmek için işlevini kullanabilirsiniz.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Bir Parameters özelliği eklemezseniz, varsayılan değer olarak belirlenmiştir `{"useHttpServerUtilityUrlTokenEncode" : true}` .

Azure Bilişsel Arama iki farklı Base64 kodlaması destekler. Aynı alanı kodlarken ve kodunu çözerken aynı parametreleri kullanmanız gerekir. Daha ayrıntılı bilgi için bkz. hangi parametrelerin kullanılacağına karar vermek için [Base64 kodlama seçenekleri](#base64details) .

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64 kodlama seçenekleri

Azure Bilişsel Arama, URL güvenli Base64 kodlamasını ve normal Base64 kodlamasını destekler. Dizin oluşturma sırasında Base64 kodlamalı bir dize, daha sonra aynı kodlama seçenekleriyle yeniden oluşturulmalıdır, aksi takdirde sonuç orijinalle eşleşmez.

`useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` Sırasıyla kodlama ve kod çözme parametreleri olarak ayarlanmışsa `true` `base64Encode` [HttpServerUtility. urltokenencoding](/dotnet/api/system.web.httpserverutility.urltokenencode) gibi davranır ve `base64Decode` [HttpServerUtility. urltokenşifre çözme](/dotnet/api/system.web.httpserverutility.urltokendecode)gibi davranır.

> [!WARNING]
> `base64Encode`Anahtar değerleri üretmek için kullanılırsa, `useHttpServerUtilityUrlTokenEncode` true olarak ayarlanmalıdır. Anahtar değerleri için yalnızca URL-güvenli Base64 kodlaması kullanılabilir. Anahtar değerlerindeki karakterlerle ilgili kısıtlamaların tamamına yönelik [&#40;Azure Bilişsel Arama&#41;adlandırma kuralları ](/rest/api/searchservice/naming-rules) ' na bakın.

Azure Bilişsel Arama .NET kitaplıkları, yerleşik kodlama sağlayan tam .NET Framework kabul eder. `useHttpServerUtilityUrlTokenEncode`Ve `useHttpServerUtilityUrlTokenDecode` seçenekleri bu yerleşik işlevlerden yararlanır. .NET Core veya başka bir çerçeve kullanıyorsanız, bu seçenekleri, `false` çerçevesinin kodlama ve kod çözme işlevlerini doğrudan olarak ayarlamayı ve çağırmayı öneririz.

Aşağıdaki tabloda, dizenin farklı Base64 kodlamaları karşılaştırılmaktadır `00>00?00` . Base64 işlevleriniz için gerekli ek işlemeyi (varsa) öğrenmek için, kitaplık kodlama işlevinizi dizeye uygulayın `00>00?00` ve çıktıyı beklenen çıktıyla karşılaştırın `MDA-MDA_MDA` .

| Encoding | Base64 kodlama çıkışı | Kitaplık kodlamasından sonra ek işleme | Kitaplık kod çözmede önce ek işleme |
| --- | --- | --- | --- |
| Doldurma ile Base64 | `MDA+MDA/MDA=` | URL-güvenli karakterler kullanın ve doldurmayı kaldırın | Standart Base64 karakterlerini kullanın ve doldurma ekleyin |
| Doldurma olmadan Base64 | `MDA+MDA/MDA` | URL kullanımı güvenli karakterler | Standart Base64 karakterlerini kullan |
| URL-doldurma ile güvenli Base64 | `MDA-MDA_MDA=` | Doldurmayı kaldır | Doldurma Ekle |
| URL-doldurma olmadan güvenli Base64 | `MDA-MDA_MDA` | Yok | Yok |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition işlevi

Belirtilen sınırlayıcıyı kullanarak bir dize alanını böler ve ortaya çıkan bölme içinde belirtilen konumda belirteci seçer.

Bu işlev şu parametreleri kullanır:

* `delimiter`: giriş dizesini bölerken ayırıcı olarak kullanılacak bir dize.
* `position`: giriş dizesi bölünmeden sonra seçmek üzere belirtecin tam sayı sıfır tabanlı konumu.

Örneğin, giriş ise `Jane Doe` `delimiter` `" "` (boşluk) ve `position` 0 ise sonuç olur `Jane` ; Eğer `position` 1 ise sonuç olur `Doe` . Konum mevcut olmayan bir belirtece başvuruyorsa bir hata döndürülür.

#### <a name="example---extract-a-name"></a>Örnek-bir adı Ayıkla

Veri kaynağınız bir alan içerir `PersonName` ve bunu iki ayrı ve alan olarak dizinlemek istiyorsunuz `FirstName` `LastName` . Bu işlevi, boşluk karakteri ayırıcı olarak kullanarak girişi ayırmak için kullanabilirsiniz.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection işlevi

Dize dizisi olarak biçimlendirilen bir dizeyi, dizindeki bir alanı doldurmak için kullanılabilen bir dize dizisine dönüştürür `Collection(Edm.String)` .

Örneğin, giriş dizesi ise, `["red", "white", "blue"]` türü hedef alanı, `Collection(Edm.String)` ve olmak üzere üç değer ile doldurulur `red` `white` `blue` . JSON dize dizileri olarak ayrıştırılabilecek giriş değerleri için bir hata döndürülür.

#### <a name="example---populate-collection-from-relational-data"></a>Örnek-koleksiyonu ilişkisel verilerden doldur

Azure SQL veritabanı, Azure Bilişsel Arama ile doğal olarak eşleşen yerleşik bir veri türüne sahip değil `Collection(Edm.String)` . Dize koleksiyonu alanlarını doldurmak için, kaynak verilerinizi bir JSON dize dizisi olarak önceden işleyebilir ve sonra `jsonArrayToStringCollection` Mapping işlevini kullanabilirsiniz.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode işlevi

Bu işlev, "URL Safe" olması için bir dizeyi kodlamak üzere kullanılabilir. URL 'de izin verilmeyen karakterler içeren bir dizeyle birlikte kullanıldığında, bu işlev "güvenli olmayan" karakterleri karakter varlığı eşdeğerlerine dönüştürür. Bu işlev UTF-8 kodlama biçimini kullanır.

#### <a name="example---document-key-lookup"></a>Örnek-belge anahtarı arama

`urlEncode` işlev, `base64Encode` yalnızca URL güvensiz karakter dönüştürülürse, diğer karakterlerin olduğu gibi tutulması işlevine alternatif olarak kullanılabilir.

Örneğin, giriş dizesi `<hello>` -sonra tür hedef alanı, `(Edm.String)` değer ile doldurulur `%3chello%3e`

Arama zamanında kodlanmış anahtarı aldığınızda, `urlDecode` Bu işlevi kullanarak özgün anahtar değerini alabilir ve kaynak belgeyi almak için kullanabilirsiniz.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>Urlşifre kodu işlevi

 Bu işlev, UTF-8 kodlama biçimini kullanarak bir URL kodlamalı dizeyi kodu çözülen dizeye dönüştürür.

 ### <a name="example---decode-blob-metadata"></a>Örnek-blob meta verilerinin kodunu çöz

 Bazı Azure Storage istemcileri ASCII olmayan karakterler içeriyorsa blob meta verilerini otomatik olarak URL 'yi kodlayın. Ancak, bu tür meta verileri aranabilir (düz metin olarak) yapmak istiyorsanız, `urlDecode` arama dizininizi doldururken kodlanmış verileri normal dizelere geri döndürmek için işlevini kullanabilirsiniz.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>fixedLengthEncode işlevi
 
 Bu işlev, herhangi bir uzunluktaki dizeyi sabit uzunlukta bir dizeye dönüştürür.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Örnek-çok uzun olan belge anahtarlarını eşleme
 
Belge anahtarı hakkında 1024 karakterden daha uzun bir süredir hata olduğunda bu işlev, belge anahtarının uzunluğunu azaltmak için uygulanabilir.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```