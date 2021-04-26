---
title: CSV Blobları üzerinde arama
titleSuffix: Azure Cognitive Search
description: DelimitedText ayrıştırma modunu kullanarak CSV 'yi Azure Blob depolamadan ayıklayın ve içeri aktarın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430488"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Azure Bilişsel Arama ile aynı Demitedmetin ayrıştırma modunu ve BLOB Dizin oluşturucularını kullanarak CSV bloblarını dizin oluşturma

Azure Bilişsel Arama [BLOB Indexer](search-howto-indexing-azure-blob-storage.md) , CSV `delimitedText` dosyaları için her satırı ayrı bir arama belgesi olarak ele alan bir ayrıştırma modu sağlar. Örneğin, virgülle ayrılmış aşağıdaki metin verildiğinde `delimitedText` arama dizininde iki belge oluşmasına neden olur: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

`delimitedText`Ayrıştırma modu olmadan, CSV dosyasının tüm içeriği bir arama belgesi olarak değerlendirilir.

Tek bir bloba birden çok arama belgesi oluştururken, belge anahtarı atamalarının nasıl çalıştığını anlamak için [birden çok arama belgesi oluşturmak üzere dizin oluşturma bloblarını](search-howto-index-one-to-many-blobs.md) gözden geçirdiğinizden emin olun. Blob Dizin Oluşturucu, her yeni belgeyi benzersiz bir şekilde tanımlayan değerleri bulma veya oluşturma özelliğine sahiptir. Özellikle, `AzureSearch_DocumentKey` bir blob daha küçük parçalara ayrıştırıldığında, daha sonra değerin dizinde arama belgesi anahtarı olarak kullanıldığı durumlarda oluşturulan bir geçişli kayıt oluşturabilir.

## <a name="setting-up-csv-indexing"></a>CSV dizinlemeyi ayarlama

CSV bloblarını indekslemek için, bir Dizin Oluşturucu `delimitedText` [oluşturma](/rest/api/searchservice/create-indexer) isteğinde ayrıştırma moduyla bir Dizin Oluşturucu tanımı oluşturun veya güncelleştirin:

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` Her Blobun ilk (boş olmayan) satırın üstbilgiler içerdiğini belirtir.
Blob 'lar bir başlangıç üst bilgisi satırı içermiyorsa, üst bilgiler Dizin Oluşturucu yapılandırmasında belirtilmelidir: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

Yapılandırma ayarını kullanarak sınırlayıcı karakteri özelleştirebilirsiniz `delimitedTextDelimiter` . Örnek:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> Şu anda yalnızca UTF-8 kodlaması desteklenir. Diğer kodlamalar için desteğe ihtiyacınız varsa [UserVoice](https://feedback.azure.com/forums/263029-azure-search)üzerinde oy verin.

> [!IMPORTANT]
> Ayrılmış metin ayrıştırma modunu kullandığınızda Azure Bilişsel Arama, veri kaynağınızdaki tüm Blobların CSV olacağını varsayar. Aynı veri kaynağında CSV ve CSV olmayan Blobların bir karışımını desteketmeniz gerekiyorsa lütfen [UserVoice](https://feedback.azure.com/forums/263029-azure-search)üzerinde oy verin.
>

## <a name="request-examples"></a>İstek örnekleri

Tümünü bir araya getirmek, tüm yük örnekleri aşağıda verilmiştir. 

DataSource 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

Dizinleyic

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


