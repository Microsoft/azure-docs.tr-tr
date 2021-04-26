---
title: Java örnekleri
titleSuffix: Azure Cognitive Search
description: Java için Azure .NET SDK 'sını kullanan Azure Bilişsel Arama demo Java kod örneklerini bulun.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955046"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Azure Bilişsel Arama için Java kod örnekleri

Azure Bilişsel Arama çözümünün işlevselliğini ve iş akışını gösteren Java kod örnekleri hakkında bilgi edinin. Bu örnekler, aşağıdaki bağlantılardan keşfedebileceğiniz [**Java Için Azure SDK 'sı**](/azure/developer/java/sdk)için [**Azure bilişsel arama istemci kitaplığını**](/java/api/overview/azure/search-documents-readme) kullanır.

| Hedef | Bağlantı |
|--------|------|
| Paket indirme | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| API başvurusu | [com.azure.search.documlar](/java/api/com.azure.search.documents)  |
| API test çalışmaları | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Kaynak kod | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>SDK örnekleri

Azure SDK geliştirme ekibinin kod örnekleri API kullanımını gösterir. Bu örnekleri, GitHub 'da [**Azure/Azure-SDK-for-Java/Tree/Master/SDK/Search/Azure-Search-Documents/src/Samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) konumunda bulabilirsiniz.

| Örnekler | Description |
|---------|-------------|
| [Arama dizini oluşturma](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | [Arama dizinlerinin](search-what-is-an-index.md)nasıl oluşturulacağını gösterir. |
| [Eş anlamlı oluşturma](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | [Eş anlamlı eşlemelerin](search-synonyms.md)nasıl oluşturulacağını gösterir.  |
| [Dizin Oluşturucu oluşturmayı ara](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | [Dizin oluşturucular](search-indexer-overview.md)oluşturmayı gösterir. |
| [Dizin Oluşturucu veri kaynağı oluşturmayı ara](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | [Desteklenen Azure veri kaynaklarının](search-indexer-overview.md#supported-data-sources)Dizin Oluşturucu tabanlı dizin oluşturma için gerekli olan Dizin Oluşturucu veri kaynakları oluşturma işleminin nasıl yapılacağını gösterir. |
| [Beceri oluşturma](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Bağlı Dizin oluşturucular olan [becerileri](cognitive-search-working-with-skillsets.md) oluşturmayı ve dizin oluşturma sırasında AI tabanlı zenginleştirme yapmayı gösterir. |
| [Belge yükleme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Bir [veri içeri aktarma](search-what-is-data-import.md) işleminde bulunan bir dizine belge yükleme veya birleştirme işlemini gösterir. |
| [Sorgu söz dizimi](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | [Temel bir sorgunun](search-query-overview.md)nasıl ayarlanacağını gösterir. |

## <a name="doc-samples"></a>Belge örnekleri

Bilişsel Arama ekibinin kod örnekleri, özellikleri ve iş akışlarını gösterir. Bu örneklerin çoğuna öğreticiler, hızlı başlangıçler ve nasıl yapılır makalelerinde başvurulur. Bu örnekleri GitHub 'daki [**Azure-Samples/Azure-Search-Java-Samples**](https://github.com/Azure-Samples/azure-search-java-samples) ' da bulabilirsiniz.

| Örnekler | Makale | 
|---------|-------------|
| [hızlı başlangıç](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Hızlı başlangıç için kaynak kodu [: Java ve REST 'te bir arama dizini oluşturun](search-get-started-java.md). Bu örnek, Java SDK 'Sı için güncelleştirilmemiş. REST API 'Lerini çağırır. |

> [!Tip]
> GitHub 'da ürün, hizmet ve dile göre filtrelenmiş Microsoft kod örneklerini aramak için [örnekler tarayıcısını](/samples/browse/?languages=java&products=azure-cognitive-search) deneyin.

## <a name="other-samples"></a>Diğer örnekler

Aşağıdaki örnekler de Bilişsel Arama ekibi tarafından yayımlanır, Ancak belgelerde başvurulmaz. İlişkili Benioku dosyaları Kullanım yönergeleri sağlar.

| Örnekler | Description |
|---------|-------------|
| [Arama-Java-Başlarken](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | , Bir arama dizini oluşturmak, yüklemek ve sorgulamak için Java SDK istemci kitaplığını kullanır. Bu örnek şu anda tek başına. |
| [Search-Java-Indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Java 'da bir Azure Cosmos DB Dizin Oluşturucu gösterir. Bu örnek, Java SDK 'Sı için güncelleştirilmemiş. REST API 'Lerini çağırır.|