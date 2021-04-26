---
title: Python örnekleri
titleSuffix: Azure Cognitive Search
description: Python veya REST için Azure .NET SDK 'sını kullanan Azure Bilişsel Arama demo Python kod örneklerini bulun.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955131"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Azure Bilişsel Arama için Python kod örnekleri

Azure Bilişsel Arama çözümünün işlevlerini ve iş akışını gösteren Python kodu örnekleri hakkında bilgi edinin. Bu örnekler, aşağıdaki bağlantılardan keşfedebileceğiniz [**Python Için Azure SDK 'Sı**](/azure/developer/python/) [**Azure bilişsel arama istemci kitaplığını**](/python/api/overview/azure/search-documents-readme) kullanır.

| Hedef | Bağlantı |
|--------|------|
| Paket indirme | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API başvurusu | [Azure-Arama-belgeler](/python/api/azure-search-documents)  |
| API test çalışmaları | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| Kaynak kod | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK örnekleri

Azure SDK geliştirme ekibinin kod örnekleri API kullanımını gösterir. Bu örnekleri, GitHub 'daki [**Azure-SDK-for-Python/Tree/Master/SDK/arama/Azure-Arama-belgeler/örnekler**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) içinde bulabilirsiniz.

| Örnekler | Description |
|---------|-------------|
| [Kimlik doğrulaması](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Bir istemcinin nasıl yapılandırılacağını ve hizmette kimlik doğrulaması yapılacağını gösterir. | 
| [Dizin oluşturma-okuma-güncelleştirme-silme işlemleri](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | [Arama dizinlerini](search-what-is-an-index.md)oluşturma, güncelleştirme, alma, listeleme ve silme işlemlerinin nasıl yapılacağını gösterir. |
| [Dizin Oluşturucu oluşturma-okuma-güncelleştirme-silme işlemleri](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | [Dizin oluşturucular](search-indexer-overview.md)oluşturma, güncelleştirme, alma, listeleme, sıfırlama ve silme işlemlerinin nasıl yapılacağını gösterir. |
| [Dizin Oluşturucu veri kaynaklarını ara](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | [Desteklenen Azure veri kaynaklarının](search-indexer-overview.md#supported-data-sources)Dizin Oluşturucu tabanlı dizin oluşturma için gerekli olan Dizin Oluşturucu veri kaynaklarını oluşturma, güncelleştirme, alma, listeleme ve silme işlemlerinin nasıl yapılacağını gösterir. |
| [Eş Anlamlı Sözcükler](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | [Eş anlamlı haritalar](search-synonyms.md)oluşturma, güncelleştirme, alma, listeleme ve silme işlemlerinin nasıl yapılacağını gösterir.  |
| [Belge yükleme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Bir [veri içeri aktarma](search-what-is-data-import.md) işleminde bulunan bir dizine belge yükleme veya birleştirme işlemini gösterir. |
| [Basit sorgu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | [Temel bir sorgunun](search-query-overview.md)nasıl ayarlanacağını gösterir. |
| [Filtre sorgusu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | [Filtre ifadesi](search-filters.md)ayarlamayı gösterir. |
| [Model sorgusu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | [Modellerle](search-filters-facets.md)çalışmayı gösterir. |

## <a name="doc-samples"></a>Belge örnekleri

Bilişsel Arama ekibinin kod örnekleri, özellikleri ve iş akışlarını gösterir. Bu örneklerin çoğuna öğreticiler, hızlı başlangıçler ve nasıl yapılır makalelerinde başvurulur. Bu örnekleri GitHub 'daki [**Azure-Samples/Azure-Search-Python-Samples**](https://github.com/Azure-Samples/azure-search-python-samples) kısmında bulabilirsiniz.

| Örnekler | Makale |
|---------|---------|
| [hızlı başlangıç](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Hızlı başlangıç için kaynak kodu [: Python 'da bir arama dizini oluşturun](search-get-started-python.md). Bu makale, örnek verileri kullanarak bir arama dizini oluşturma, yükleme ve sorgulama için temel iş akışını ele almaktadır. |
| [öğretici-AI-zenginleştirme](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Öğretici için kaynak kodu [: Azure Bloblarından aranabilir içerik oluşturmak Için Python ve AI kullanın](cognitive-search-tutorial-blob-python.md). Bu makalede, Beceri 'ın, aranabilir veya tüketilebilir hale getirmek için ham içerik oluşturduğu ve dönüştürmesinin Bilişsel Beceri ile bir blob Indexer oluşturma gösterilmektedir. |
| [AzureML-özel yetenek](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Kaynak kodu [Örneğin: Python kullanarak özel bir yetenek oluşturma](cognitive-search-custom-skill-python.md). Bu makalede, Azure Machine Learning 'teki derin öğrenme modelleriyle Indexer ve beceri tümleştirmesi gösterilmektedir. |

> [!Tip]
> GitHub 'da ürün, hizmet ve dile göre filtrelenmiş Microsoft kod örneklerini aramak için [örnekler tarayıcısını](/samples/browse/?languages=python&products=azure-cognitive-search) deneyin.