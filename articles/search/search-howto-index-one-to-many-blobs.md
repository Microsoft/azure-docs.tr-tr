---
title: Birden çok belge içeren dizin blob 'ları
titleSuffix: Azure Cognitive Search
description: Her Blobun bir veya daha fazla arama dizini belgesi sağlayabildiği Azure Bilişsel Arama blob Dizin oluşturucuyu kullanarak Azure Blob 'larını metin içeriğine göre gezin.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430989"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Birden çok arama belgesi üretmek için Blobları dizinleme

Varsayılan olarak, bir blob Dizin Oluşturucu bir Blobun içeriğini tek bir arama belgesi olarak değerlendirir. Bir arama dizininde Blobun daha ayrıntılı bir gösterimini isterseniz, bir bloba birden çok arama belgesi oluşturmak için **Parsingmode** değerlerini ayarlayabilirsiniz. Birçok arama belgesi ile sonuçlanan **Parsingmode** değerleri `delimitedText` , ( [CSV](search-howto-index-csv-blobs.md)Için) ve `jsonArray` veya `jsonLines` ( [JSON](search-howto-index-json-blobs.md)için) içerir.

Bu ayrıştırma modlarından birini kullandığınızda, ortaya çıktı olan yeni arama belgelerinin benzersiz belge anahtarları olması ve bu değerin nereden geldiğini belirlemede bir sorun ortaya çıkar. Üst blob, biçiminde en az bir benzersiz değere sahiptir `metadata_storage_path property` , ancak bu değere birden fazla arama belgesine katkıda bulunursa, anahtar artık dizinde benzersiz değildir.

Bu sorunu gidermek için, blob Indexer `AzureSearch_DocumentKey` tek blob üst öğesinden oluşturulan her bir alt arama belgesini benzersiz bir şekilde tanımlayan bir oluşturur. Bu makalede, bu özelliğin nasıl çalıştığı açıklanır.

## <a name="one-to-many-document-key"></a>Bire çok belge anahtarı

Bir Azure Bilişsel Arama dizininde görüntülenen her belge, bir belge anahtarı tarafından benzersiz şekilde tanımlanır. 

Hiçbir ayrıştırma modu belirtilmediğinde ve arama belgesi anahtarı için Dizin Oluşturucu tanımında [Açık alan eşlemesi](search-indexer-field-mappings.md) yoksa, blob Indexer otomatik olarak `metadata_storage_path property` belge anahtarı olarak eşler. Bu eşleme, her Blobun ayrı bir arama belgesi olarak görünmesini sağlar ve bu alan eşlemesini kendiniz oluşturmak zorunda kalmanızı sağlar (normalde, yalnızca özdeş adlara ve türlerine sahip alanlar otomatik olarak eşleştirilir).

Yukarıda listelenen ayrıştırma modlarından herhangi birini kullanırken, bir blob "birçok" arama belgesiyle eşlenir ve bir belge anahtarı yalnızca blob meta verilerini uygun olmayan şekilde temel alır. Azure Bilişsel Arama, bu kısıtlamayı aşmak için bir bloba ayıklanan her bir varlık için "bire çok" belge anahtarı oluşturma yeteneğine sahiptir. Bu özellik AzureSearch_DocumentKey olarak adlandırılır ve bloba ayıklanan her bir varlığa eklenir. Bu özelliğin değeri, Bloblar genelinde her bir varlık için benzersiz olarak garanti edilir ve varlıklar ayrı arama belgeleri olarak görünür.

Varsayılan olarak, anahtar dizin alanı için hiçbir açık alan eşlemesi belirtilmediğinde, `AzureSearch_DocumentKey` `base64Encode` alan eşleme işlevi kullanılarak onunla eşleştirilir.

## <a name="example"></a>Örnek

Aşağıdaki alanlarla bir dizin tanımı olduğunu varsayalım:

+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Blob kabınızda aşağıdaki yapıyla blob 'lar vardır:

_ÜzerindeBlob1.js_

```json
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_ÜzerindeBlob2.js_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

Bir Dizin Oluşturucu oluşturup,  `jsonLines` anahtar alanı için herhangi bir açık alan eşlemesi belirtmeden parsingmode 'u olarak ayarlarsanız, aşağıdaki eşleme örtük olarak uygulanır.

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

Bu kurulum, aşağıdaki çizime benzer şekilde, önceden Kesinleştirme belge anahtarlarına neden olur (örneğin, Base64 kodlamalı KIMLIK breçekimi için kısaltıldı).

| ID | sıcaklık | basınç | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... Yıljeuannvbjsx | 100 | 100 | 2020-02-13T00:00:00Z |
| aHR0 ... Yıljeuannvbjsy | 33 | 30 | 2020-02-14T00:00:00Z |
| aHR0 ... Yıljluayla Annvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ... Yıljluayla Annvbjsy | 120 | 3 | 2017-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Dizin anahtarı alanı için özel alan eşleme

Önceki örnekle aynı dizin tanımının olduğunu varsayarsak, blob kabınızın blob 'ları Şu yapıya sahip olduğunu varsayalım:

_ÜzerindeBlob1.js_

```json
recordid, temperature, pressure, timestamp
1, 100, 100,"2019-02-13T00:00:00Z" 
2, 33, 30,"2019-02-14T00:00:00Z" 
```

_ÜzerindeBlob2.js_

```json
recordid, temperature, pressure, timestamp
1, 1, 1,"2018-01-12T00:00:00Z" 
2, 120, 3,"2013-05-11T00:00:00Z" 
```

`delimitedText` **Parsingmode** ile bir Dizin Oluşturucu oluşturduğunuzda, anahtar alanına aşağıdaki şekilde bir alan eşleme işlevi ayarlamak doğal olabilir:

```http
{
    "sourceFieldName" : "recordid",
    "targetFieldName": "id"
}
```

Ancak, bu eşleme,  Dizin `recordid` _Bloblar genelinde_ benzersiz olmadığından, dizinde gösterilen 4 belge ile sonuçlanmaz. Bu nedenle, `AzureSearch_DocumentKey` özellikten "bire çok" ayrıştırma modları için anahtar Dizin alanına uygulanan örtük alan eşlemesini kullanmanızı öneririz.

Açık alan eşlemesi ayarlamak istiyorsanız, _SourceField_ 'ın **Tüm Bloblar genelinde** her bir varlık için benzersiz olduğundan emin olun.

> [!NOTE]
> `AzureSearch_DocumentKey`Ayıklanan varlık başına benzersizlik sağlamak için kullanılan yaklaşım değişikliğe tabidir ve bu nedenle uygulamanızın gereksinimlerine göre bu değere dayanmamalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Blob dizin oluşturma işleminin temel yapısı ve iş akışı hakkında bilgi sahibi değilseniz, önce Azure Bilişsel Arama Azure [BLOB Storage 'ı kullanarak dizin oluşturmayı](search-howto-index-json-blobs.md) gözden geçirmeniz gerekir. Farklı blob içerik türleri için ayrıştırma modları hakkında daha fazla bilgi için aşağıdaki makaleleri gözden geçirin.

> [!div class="nextstepaction"]
> [CSV Bloblarını](search-howto-index-csv-blobs.md) 
>  dizine ekleme [JSON Bloblarını dizine alma](search-howto-index-json-blobs.md)
