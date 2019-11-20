---
title: CSV Blobları üzerinde arama
titleSuffix: Azure Cognitive Search
description: Şu anda genel önizleme aşamasında olan Delimitedmetin ayrıştırma modunu kullanarak Azure Blob depolamadan CSV 'yi ayıklayın ve içeri aktarın.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2166e100f03f21c218618d19dc37ee70c6ab29ef
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113035"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Azure Bilişsel Arama ile aynı Demitedmetin ayrıştırma modunu ve BLOB Dizin oluşturucularını kullanarak CSV bloblarını dizin oluşturma 

> [!IMPORTANT] 
> Delimitedmetin ayrıştırma modu şu anda genel önizlemededir. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API sürüm 2019-05-06-önizleme](search-api-preview.md) bu özelliği sağlar. Şu anda portal veya .NET SDK desteği yok.

Varsayılan olarak, [Azure bilişsel arama blob Indexer](search-howto-indexing-azure-blob-storage.md) , ayrılmış metin bloblarını tek bir metin öbeği olarak ayrıştırır. Ancak, CSV verileri içeren bloblarla, genellikle blobdaki her satırı ayrı bir belge olarak değerlendirmek istersiniz. Örneğin, aşağıdaki sınırlandırılmış metin verildiğinde, her biri "ID", "Dateyayınlanan" ve "Tags" alanlarını içeren iki belgeye ayrıştırın: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Bu makalede, `delimitedText` ayrıştırma modunu ayarlayarak CSV bloblarını bir Azure Bilişsel Arama blob Dizinleyicisi ile ayrıştırmayı öğreneceksiniz. 

> [!NOTE]
> Birden çok arama belgesini bir Azure blobundan çıkarmak için [bire çok dizin oluşturma](search-howto-index-one-to-many-blobs.md) içindeki Dizin Oluşturucu yapılandırma önerilerini izleyin.

## <a name="setting-up-csv-indexing"></a>CSV dizinlemeyi ayarlama
CSV bloblarını indekslemek için, bir Dizin Oluşturucu [oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-indexer) isteğinde `delimitedText` ayrıştırma moduyla bir Dizin Oluşturucu tanımı oluşturun veya güncelleştirin:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`, her Blobun ilk (boş olmayan) satırın üstbilgiler içerdiğini belirtir.
Blob 'lar bir başlangıç üst bilgisi satırı içermiyorsa, üst bilgiler Dizin Oluşturucu yapılandırmasında belirtilmelidir: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

`delimitedTextDelimiter` yapılandırma ayarını kullanarak sınırlayıcı karakteri özelleştirebilirsiniz. Örneğin:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Şu anda yalnızca UTF-8 kodlaması desteklenir. Diğer kodlamalar için desteğe ihtiyacınız varsa [UserVoice](https://feedback.azure.com/forums/263029-azure-search)üzerinde oy verin.

> [!IMPORTANT]
> Ayrılmış metin ayrıştırma modunu kullandığınızda Azure Bilişsel Arama, veri kaynağınızdaki tüm Blobların CSV olacağını varsayar. Aynı veri kaynağında CSV ve CSV olmayan Blobların bir karışımını desteketmeniz gerekiyorsa lütfen [UserVoice](https://feedback.azure.com/forums/263029-azure-search)üzerinde oy verin.
> 
> 

## <a name="request-examples"></a>İstek örnekleri
Tümünü bir araya getirmek, tüm yük örnekleri aşağıda verilmiştir. 

DataSource 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Dizinleyic

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Azure Bilişsel Arama daha iyi hale getirmemize yardımcı olun
Geliştirmeler için özellik istekleriniz veya fikirler varsa, bu girişi [UserVoice](https://feedback.azure.com/forums/263029-azure-search/)üzerinde belirtin.

