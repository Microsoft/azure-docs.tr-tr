---
title: Düz metin Blobları üzerinde arama
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de tam metin araması için Azure Bloblarından düz metin ayıklamak üzere bir arama Dizin Oluşturucu yapılandırın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509461"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Azure Bilişsel Arama düz metin bloblarını dizin oluşturma

Tam metin arama için aranabilir blob metnini ayıklamak üzere bir [BLOB Dizin Oluşturucu](search-howto-indexing-azure-blob-storage.md) kullanırken, daha iyi dizin oluşturma sonuçları elde etmek için bir ayrıştırma modu atayabilirsiniz. Varsayılan olarak, Dizin Oluşturucu blob içeriğini tek bir metin öbeği olarak ayrıştırır. Ancak, tüm Bloblar aynı kodlamada düz metin içeriyorsa, ayrıştırma modunu kullanarak dizin oluşturma performansını önemli ölçüde geliştirebilirsiniz `text` .

Kullanım ayrıştırması için öneriler `text` şunları içerir:

+ Dosya türü. txt
+ Dosyalar herhangi bir türdür, ancak içerik metindir (örneğin, program kaynak kodu, HTML, XML vs.). Bir işaretleme dilindeki dosyalar için, herhangi bir sözdizimi karakteri statik metin olarak gönderilir.

Tüm dizin oluşturucularının JSON 'a serileştirilme olduğunu hatırlayın. Varsayılan olarak, tüm metin dosyasının içeriği bir büyük alan içinde olarak dizinlenir `"content": "<file-contents>"` . Yeni satır ve dönüş yönergeleri içerik alanına katıştırılır ve olarak ifade edilir `\r\n\` .

Daha ayrıntılı bir sonuç istiyorsanız ve dosya türü uyumluysa, aşağıdaki çözümleri göz önünde bulundurun:

+ [`delimitedText`](search-howto-index-csv-blobs.md) Kaynak CSV ise ayrıştırma modu
+ [ `jsonArray` ya `jsonLines` ](search-howto-index-json-blobs.md)da kaynak JSON ise

İçeriği birden çok parçaya bölmek için üçüncü bir seçenek de [AI zenginleştirme](cognitive-search-concept-intro.md)formunda gelişmiş özellikler gerektirir. Dosya öbeklerini tanımlayan ve farklı arama alanlarına atayan analizler ekler. [Yerleşik yetenekler](cognitive-search-predefined-skills.md)aracılığıyla tam veya kısmi bir çözüm bulabilirsiniz, ancak daha olası bir çözüm, içeriğinizi anlayan ve özel bir [beceriye](cognitive-search-custom-skill-interface.md)kaydırılmış olan özel öğrenme modelinde, içeriğinizi anlayan bir model öğreniyor olabilir.

## <a name="set-up-plain-text-indexing"></a>Düz metin dizinlemeyi ayarlama

Düz metin bloblarını dizine eklemek için, bir dizin `parsingMode` oluşturma isteğinde yapılandırma özelliği ile bir Dizin Oluşturucu tanımı oluşturun veya güncelleştirin `text` : [](/rest/api/searchservice/create-indexer)

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

Varsayılan olarak, `UTF-8` kodlama varsayılır. Farklı bir kodlama belirtmek için `encoding` yapılandırma özelliğini kullanın: 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>İstek örneği

Ayrıştırma modları, Dizin Oluşturucu tanımında belirtilmiştir.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>Sonraki adımlar

+ [Azure Bilişsel Arama'daki Dizin Oluşturucular](search-indexer-overview.md)
+ [Blob Dizin oluşturucuyu yapılandırma](search-howto-indexing-azure-blob-storage.md)
+ [Blob dizinlemeye genel bakış](search-blob-storage-integration.md)