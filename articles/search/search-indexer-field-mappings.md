---
title: Dizin oluşturucularda alan eşlemeleri
titleSuffix: Azure Cognitive Search
description: Dizin oluşturucudaki alan eşlemelerini, alan adlarında ve veri gösterimlerinde farklılıklar için bir hesaba göre yapılandırın.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72623787cdb27c568fe2b4ec075010674a3996ef
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74124000"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Azure Bilişsel Arama Dizinleyicileri kullanarak alan eşlemeleri ve dönüştürmeler

Azure Bilişsel Arama Dizinleyicileri kullanırken, bazen giriş verilerinin hedef dizininizin şemasıyla tam olarak eşleşmediğini fark edersiniz. Bu durumlarda, dizin oluşturma işlemi sırasında verilerinizi yeniden şekillendirmek için **alan eşlemelerini** kullanabilirsiniz.

Alan eşlemelerinin yararlı olduğu bazı durumlar:

* Veri kaynağınız `_id`adlı bir alana sahiptir, ancak Azure Bilişsel Arama alt çizgiyle başlayan alan adlarına izin vermez. Alan eşleme, bir alanı etkili bir şekilde yeniden adlandırmanızı sağlar.
* Aynı veri kaynağı verilerinden dizindeki çeşitli alanları doldurmak istiyorsunuz. Örneğin, bu alanlara farklı çözümleyiciler uygulamak isteyebilirsiniz.
* Bir dizin alanını birden fazla veri kaynağından alınan verilerle doldurmak istiyorsunuz ve veri kaynakları her biri farklı alan adları kullanır.
* Verilerinizi Base64 olarak kodlamanız veya kodu çözmelisiniz. Alan eşlemeleri, Base64 kodlaması ve kod çözme işlevleri dahil olmak üzere çeşitli **eşleme işlevlerini**destekler.

> [!NOTE]
> Azure Bilişsel Arama dizin oluşturucularının alan eşleme özelliği, veri dönüştürme için birkaç seçenekten veri alanlarını dizin alanlarıyla eşlemek için basit bir yol sağlar. Daha karmaşık veriler, dizin oluşturmanın kolay bir biçimde yeniden şekillendirilmesi için önceden işleme gerektirebilir.
>
> Microsoft Azure Data Factory, verileri içeri ve dönüştürme için güçlü bir bulut tabanlı çözümdür. Ayrıca, dizin oluşturmadan önce kaynak verileri dönüştürmek için kod yazabilirsiniz. Kod örnekleri için bkz. [model ilişkisel verileri modelleme](search-example-adventureworks-modeling.md) ve [çok düzeyli modelleri modelleyin](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Alan eşlemelerini ayarlama

Bir alan eşlemesi üç bölümden oluşur:

1. Veri kaynağınızdaki bir alanı temsil eden `sourceFieldName`. Bu özellik gereklidir.
2. Arama dizininizdeki bir alanı temsil eden isteğe bağlı `targetFieldName`. Atlanırsa, veri kaynağıyla aynı ad kullanılır.
3. Önceden tanımlanmış birkaç işlevden birini kullanarak verilerinizi dönüştürebilen isteğe bağlı bir `mappingFunction`. İşlevlerin tam listesi [aşağıda](#mappingFunctions)verilmiştir.

Alan eşlemeleri, Dizin Oluşturucu tanımının `fieldMappings` dizisine eklenir.

## <a name="map-fields-using-the-rest-api"></a>REST API kullanarak alanları eşleme

[Create Indexer](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) API isteği kullanarak yeni bir Dizin Oluşturucu oluştururken alan eşlemeleri ekleyebilirsiniz. Var olan bir dizin oluşturucunun alan eşlemelerini [güncelleştirme Dizin Oluşturucu](https://docs.microsoft.com/rest/api/searchservice/update-indexer) API isteği kullanarak yönetebilirsiniz.

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
>

## <a name="map-fields-using-the-net-sdk"></a>.NET SDK kullanarak alanları eşleme

.NET SDK 'da, özellikleri `SourceFieldName` ve `TargetFieldName`ve isteğe bağlı bir `MappingFunction` başvurusu olan [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) sınıfını kullanarak alan eşlemelerini tanımlarsınız.

Dizin oluşturucuyu oluştururken veya daha sonra `Indexer.FieldMappings` özelliğini ayarlayarak alan eşlemelerini belirtebilirsiniz.

Aşağıdaki C# örnek, bir Dizin Oluşturucu oluştururken alan eşlemelerini ayarlar.

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

Azure Bilişsel Arama belge anahtarında yalnızca URL güvenli karakterler görünebilir (çünkü müşterilerin [Arama API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 'sini kullanarak belgeyi ele alabilmesi gerekir). Anahtarınızın kaynak alanı URL-güvenli olmayan karakterler içeriyorsa, `base64Encode` işlevini dizin oluşturma sırasında dönüştürmek için kullanabilirsiniz.

Arama zamanında kodlanmış anahtarı aldığınızda, özgün anahtar değerini almak için `base64Decode` işlevini kullanabilir ve bunu kaynak belgeyi almak için kullanabilirsiniz.

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

Eşleme işleviniz için bir Parameters özelliği eklemezseniz, bu değer varsayılan olarak `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Azure Bilişsel Arama iki farklı Base64 kodlaması destekler. Aynı alanı kodlarken ve kodunu çözerken aynı parametreleri kullanmanız gerekir. Daha fazla bilgi için bkz. hangi parametrelerin kullanılacağına karar vermek için [Base64 kodlama seçenekleri](#base64details) .

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode işlevi

Giriş dizesinin Base64 kodunu çözme işlemini gerçekleştirir. Girişin, *URL-güvenli* Base64 kodlamalı bir dize olduğu varsayılır.

#### <a name="example---decode-blob-metadata-or-urls"></a>Örnek-blob meta verileri veya URL kodunu çöz

Kaynak verileriniz, düz metin olarak aranabilir olmasını istediğiniz blob meta veri dizeleri veya Web URL 'Leri gibi Base64 kodlamalı dizeler içerebilir. Arama dizininizi doldururken, kodlanmış verileri normal dizelere geri döndürmek için `base64Decode` işlevini kullanabilirsiniz.

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

Bir Parameters özelliği eklemezseniz, `{"useHttpServerUtilityUrlTokenEncode" : true}`değeri varsayılan olarak belirlenmiştir.

Azure Bilişsel Arama iki farklı Base64 kodlaması destekler. Aynı alanı kodlarken ve kodunu çözerken aynı parametreleri kullanmanız gerekir. Daha ayrıntılı bilgi için bkz. hangi parametrelerin kullanılacağına karar vermek için [Base64 kodlama seçenekleri](#base64details) .

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64 kodlama seçenekleri

Azure Bilişsel Arama, URL güvenli Base64 kodlamasını ve normal Base64 kodlamasını destekler. Dizin oluşturma sırasında Base64 kodlamalı bir dize, daha sonra aynı kodlama seçenekleriyle yeniden oluşturulmalıdır, aksi takdirde sonuç orijinalle eşleşmez.

Sırasıyla kodlama ve kod çözme için `useHttpServerUtilityUrlTokenEncode` veya `useHttpServerUtilityUrlTokenDecode` parametreleri `true`olarak ayarlanırsa, `base64Encode` [HttpServerUtility. Urltokenencoding](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) gibi davranır ve `base64Decode` [HttpServerUtility. urltokençözmede](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx)gibi davranır.

> [!WARNING]
> Anahtar değerleri üretmek için `base64Encode` kullanılırsa, `useHttpServerUtilityUrlTokenEncode` true olarak ayarlanmalıdır. Anahtar değerleri için yalnızca URL-güvenli Base64 kodlaması kullanılabilir. Anahtar değerlerinde bulunan karakterlere ilişkin kısıtlamaların tam olarak ayarlanması için bkz. [adlandırma kuralları &#40;Azure bilişsel arama&#41; ](https://docs.microsoft.com/rest/api/searchservice/naming-rules) .

Azure Bilişsel Arama .NET kitaplıkları, yerleşik kodlama sağlayan tam .NET Framework kabul eder. `useHttpServerUtilityUrlTokenEncode` ve `useHttpServerUtilityUrlTokenDecode` seçenekleri bu yerleşik işlev özelliğinden faydalanır. .NET Core veya başka bir çerçeve kullanıyorsanız, bu seçenekleri `false` ve Framework kodlama ve kod çözme işlevlerinin doğrudan çağrılması için ayarlamayı öneririz.

Aşağıdaki tabloda `00>00?00`dize için farklı Base64 kodlamaları karşılaştırılmaktadır. Base64 işlevleriniz için gerekli ek işlemeyi (varsa) öğrenmek için, kitaplık kodlama işlevinizi dize `00>00?00` uygulayın ve çıktıyı beklenen çıkış `MDA-MDA_MDA`ile karşılaştırın.

| Encoding | Base64 kodlama çıkışı | Kitaplık kodlamasından sonra ek işleme | Kitaplık kod çözmede önce ek işleme |
| --- | --- | --- | --- |
| Doldurma ile Base64 | `MDA+MDA/MDA=` | URL-güvenli karakterler kullanın ve doldurmayı kaldırın | Standart Base64 karakterlerini kullanın ve doldurma ekleyin |
| Doldurma olmadan Base64 | `MDA+MDA/MDA` | URL kullanımı güvenli karakterler | Standart Base64 karakterlerini kullan |
| URL-doldurma ile güvenli Base64 | `MDA-MDA_MDA=` | Doldurmayı kaldır | Doldurma Ekle |
| URL-doldurma olmadan güvenli Base64 | `MDA-MDA_MDA` | None | None |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition işlevi

Belirtilen sınırlayıcıyı kullanarak bir dize alanını böler ve ortaya çıkan bölme içinde belirtilen konumda belirteci seçer.

Bu işlev şu parametreleri kullanır:

* `delimiter`: giriş dizesini bölerken ayırıcı olarak kullanılacak bir dize.
* `position`: giriş dizesi bölünmeden sonra seçmek üzere belirtecin tam sayı sıfır tabanlı konumu.

Örneğin, giriş `Jane Doe`, `delimiter` `" "`(boşluk) ve `position` 0 ' dır, sonuç `Jane`olur; `position` 1 ise sonuç `Doe`olur. Konum mevcut olmayan bir belirtece başvuruyorsa bir hata döndürülür.

#### <a name="example---extract-a-name"></a>Örnek-bir adı Ayıkla

Veri kaynağınız bir `PersonName` alanı içerir ve bunu iki ayrı `FirstName` ve `LastName` alanı olarak dizinlemek istiyorsunuz. Bu işlevi, boşluk karakteri ayırıcı olarak kullanarak girişi ayırmak için kullanabilirsiniz.

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

Dize dizisi olarak biçimlendirilen bir dizeyi, dizindeki bir `Collection(Edm.String)` alanı doldurmak için kullanılabilen bir dize dizisine dönüştürür.

Örneğin, giriş dizesi `["red", "white", "blue"]`ise, `Collection(Edm.String)` türü hedef alanı üç değer `red`, `white`ve `blue`ile doldurulur. JSON dize dizileri olarak ayrıştırılabilecek giriş değerleri için bir hata döndürülür.

#### <a name="example---populate-collection-from-relational-data"></a>Örnek-koleksiyonu ilişkisel verilerden doldur

Azure SQL veritabanı, Azure Bilişsel Arama `Collection(Edm.String)` alanları için doğal olarak eşleyen yerleşik bir veri türüne sahip değil. Dize koleksiyonu alanlarını doldurmak için, kaynak verilerinizi bir JSON dize dizisi olarak önceden işleyebilir ve sonra `jsonArrayToStringCollection` Mapping işlevini kullanabilirsiniz.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

İlişkisel verileri dizin toplama alanlarına dönüştüren ayrıntılı bir örnek için bkz. [model ilişkisel verileri](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode işlevi

Bu işlev, "URL Safe" olması için bir dizeyi kodlamak üzere kullanılabilir. URL 'de izin verilmeyen karakterler içeren bir dizeyle birlikte kullanıldığında, bu işlev "güvenli olmayan" karakterleri karakter varlığı eşdeğerlerine dönüştürür. Bu işlev UTF-8 kodlama biçimini kullanır.

#### <a name="example---document-key-lookup"></a>Örnek-belge anahtarı arama

`urlEncode` işlev, yalnızca URL güvenli olmayan karakterler dönüştürülebileceğinden, diğer karakterleri olduğu gibi tutarken `base64Encode` işlevine alternatif olarak kullanılabilir.

Giriş dizesi `<hello>`, `(Edm.String)` türündeki hedef alan, değer ile doldurulur `%3chello%3e`

Arama zamanında kodlanmış anahtarı aldığınızda, özgün anahtar değerini almak için `urlDecode` işlevini kullanabilir ve bunu kaynak belgeyi almak için kullanabilirsiniz.

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

 Bazı Azure Storage istemcileri ASCII olmayan karakterler içeriyorsa blob meta verilerini otomatik olarak URL 'yi kodlayın. Ancak, bu tür meta verileri aranabilir (düz metin olarak) yapmak istiyorsanız, arama dizininizi doldururken kodlanmış verileri normal dizelere geri döndürmek için `urlDecode` işlevini kullanabilirsiniz.

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