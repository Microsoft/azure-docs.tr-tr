---
title: Ortak arama Dizin Oluşturucu sorunlarını giderme
titleSuffix: Azure Cognitive Search
description: Veri kaynağı bağlantısı, güvenlik duvarı ve eksik belgeler dahil olmak üzere Azure Bilişsel Arama 'daki Dizin oluşturucularla ilgili hataları ve yaygın sorunları giderin.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c5a16d957f1e0414f92d0cc03442d88d438e4c92
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793622"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Azure Bilişsel Arama ortak Dizin Oluşturucu sorunlarını giderme

Dizin oluşturucular, verileri Azure Bilişsel Arama 'da dizin oluştururken çeşitli sorunlar halinde çalışabilir. Hatanın ana kategorileri şunlardır:

* [Bir veri kaynağına bağlanma](#data-source-connection-errors)
* [Belge işleme](#document-processing-errors)
* [Bir dizine belge alımı](#index-errors)

## <a name="data-source-connection-errors"></a>Veri kaynağı bağlantı hataları

### <a name="blob-storage"></a>Blob Depolama

#### <a name="storage-account-firewall"></a>Depolama hesabı güvenlik duvarı

Azure depolama, yapılandırılabilir bir güvenlik duvarı sağlar. Azure Bilişsel Arama depolama hesabınıza bağlanabilmesi için varsayılan olarak güvenlik duvarı devre dışıdır.

Güvenlik Duvarı etkinleştirildiğinde belirli bir hata iletisi yoktur. Genellikle, güvenlik duvarı hataları `The remote server returned an error: (403) Forbidden`benzer şekilde görünür.

[Portalda](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)güvenlik duvarının etkinleştirildiğini doğrulayabilirsiniz. Desteklenen tek geçici çözüm, [' tüm ağlar '](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)'tan erişime izin vermeyi seçerek güvenlik duvarını devre dışı bırakmamak.

Dizin oluşturucuda iliştirilmiş bir beceri yoksa, arama hizmetinizin IP adresleri için [bir özel durum eklemeyi](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) _deneyebilirsiniz._ Ancak, bu senaryo desteklenmez ve çalışması garanti edilmez.

FQDN 'sini (`<your-search-service-name>.search.windows.net`) ping yaparak, arama hizmetinizin IP adresini bulabilirsiniz.

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>Dizin oluşturma etkin değil

Azure Bilişsel Arama Cosmos DB Dizin oluşturma üzerinde örtük bir bağımlılığa sahiptir. Cosmos DB Otomatik Dizin oluşturmayı kapatırsanız Azure Bilişsel Arama başarılı bir durum döndürür, ancak kapsayıcı içerikleri dizinlemeye başarısız olur. Ayarları denetleme ve Dizin oluşturmayı açma hakkında yönergeler için, bkz. [Azure Cosmos DB Dizin oluşturmayı yönetme](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Belge Işleme hataları

### <a name="unprocessable-or-unsupported-documents"></a>Proceslabilen veya desteklenmeyen belgeler

Blob Dizin Oluşturucu, [hangi belge biçimlerinin açıkça desteklendiğini belgeler.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) Bazen bir BLOB depolama kapsayıcısı desteklenmeyen belgeler içeriyor. Diğer zamanlarda sorunlu belgeler olabilir. [Yapılandırma seçeneklerini değiştirerek](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)bu belgelerde Dizin oluşturucuyu durdurmayı önleyebilirsiniz:

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Belge içeriği eksik

Blob Indexer [bir kapsayıcıdaki bloblardan metin bulur ve ayıklar](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Metin ayıklama ile ilgili bazı sorunlar şunlardır:

* Belge yalnızca taranmış görüntüleri içerir. Taranmış görüntüler (JPGs) gibi metin olmayan içeriğe sahip PDF blob 'ları, standart bir blob dizin oluşturma işlem hattında sonuç üretmez. Metin öğeleriyle görüntü içeriğiniz varsa, bilişsel [arama](cognitive-search-concept-image-scenarios.md) 'yı kullanarak metni bulabilir ve ayıklayabilir.
* Blob Indexer yalnızca dizin meta verileri için yapılandırılmış. İçeriği ayıklamak için, blob dizin oluşturucunun [hem içeriği hem de meta verileri ayıklamak](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)üzere yapılandırılması gerekir:

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Dizin hataları

### <a name="missing-documents"></a>Eksik belgeler

Dizin oluşturucular bir [veri kaynağından](https://docs.microsoft.com/rest/api/searchservice/create-data-source)belge bulur. Bazen veri kaynağından dizinlenen bir belge, dizinde eksik gibi görünüyor. Bu hataların birçok yaygın nedeni olabilir:

* Belge Dizinlenmemiş. Başarılı bir Dizin Oluşturucu çalıştırması için portalı denetleyin.
* Dizin Oluşturucu çalıştırıldıktan sonra belge güncelleştirildi. Dizinleyicinin bir [zamanlamaya](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)göre olması, sonunda yeniden çalıştırılır ve belgeyi seçer.
* Veri kaynağında belirtilen [sorgu](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) , belgeyi dışlar. Dizin oluşturucular, veri kaynağının parçası olmayan belgeleri dizinlemez.
* [Alan eşlemeleri](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) veya [AI zenginleştirme](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) belgeyi değiştirdi ve beklediğinizden farklı görünüyor.
* Belgenizi bulmak için [arama BELGESI API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 'sini kullanın.
