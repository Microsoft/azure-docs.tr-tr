---
title: Azure Cosmos DB için SQL API Python örnekleri
description: CRUD işlemleri de dahil olmak üzere Azure Cosmos DB ortak görevler için GitHub 'da Python örnekleri bulun.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 08/11/2020
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: 947a958b7cce6909332ebdb1832be168bfdd0bde
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801817"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python örnekleri
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET v2 SDK örnekleri](sql-api-dotnet-samples.md)
> * [.NET v3 SDK örnekleri](sql-api-dotnet-v3sdk-samples.md)
> * [Java v4 SDK örnekleri](sql-api-java-sdk-samples.md)
> * [Spring Data v3 SDK örnekleri](sql-api-spring-data-sdk-samples.md)
> * [Node.js Örnekleri](sql-api-nodejs-samples.md)
> * [Python Örnekleri](sql-api-python-samples.md)
> * [Azure Kod Örneği Galerisi](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Azure Cosmos DB kaynaklarında CRUD işlemlerini ve diğer yaygın işlemleri gerçekleştiren örnek çözümler, [Azure-DocumentDB-Python](https://github.com/Azure/azure-documentdb-python) GitHub deposuna dahildir. Bu makalede aşağıdakiler sunulmaktadır:

* Python örnek proje dosyalarının her birindeki görevlere bağlantılar.
* İlgili API başvurusu içeriğine bağlantılar.

## <a name="prerequisites"></a>Önkoşullar

- Cosmos DB hesabı. Seçenekleriniz şunlardır:
    * Bir Azure etkin aboneliği içinde:
        * [Azure Ücretsiz hesabı oluşturun](https://azure.microsoft.com/free) veya mevcut aboneliğinizi kullanın 
        * [Visual Studio aylık krediler](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB ücretsiz katman](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Azure etkin aboneliği olmadan:
        * 30 gün boyunca bir test ortamı olan [ücretsiz Azure Cosmos DB deneyin](https://azure.microsoft.com/try/cosmosdb/).
        * [Azure Cosmos DB Öykünücüsü](https://aka.ms/cosmosdb-emulator) 
- İçinde yürütülebilir dosya ile [Python 2,7 veya 3.6 +](https://www.python.org/downloads/) `python` `PATH` .
- [Visual Studio Code](https://code.visualstudio.com/).
- [Visual Studio Code Için Python uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 
- [Python için SQL API SDK Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="database-examples"></a>Veritabanı örnekleri

[Database_management. Kopyala](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) Python örneği aşağıdaki görevlerin nasıl yapılacağını gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos veritabanları hakkında bilgi edinmek için bkz. [veritabanları, kapsayıcılar ve öğelerle çalışma](account-databases-containers-items.md) kavramsal makalesi.

| Görev | API başvurusu |
| --- | --- |
| [Veritabanı oluşturma](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [KIMLIĞE göre bir veritabanı oku](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [Veritabanlarını sorgulama](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [Hesabın veritabanlarını listeleme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [Veritabanı silme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>Kapsayıcı örnekleri

[Container_management. Kopyala](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) Python örneği aşağıdaki görevlerin nasıl yapılacağını gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos koleksiyonları hakkında bilgi edinmek için bkz. [veritabanları, kapsayıcılar ve öğelerle çalışma](account-databases-containers-items.md) kavramsal makalesi.

| Görev | API başvurusu |
| --- | --- |
| [Bir kapsayıcı için sorgu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [Kapsayıcı oluşturma](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [Bir veritabanındaki tüm kapsayıcıları listeleme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [KIMLIĞI ile bir kapsayıcı al](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [Kapsayıcının sağlanan verimini yönetin](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer, container.replace_throughput|
| [Kapsayıcı silme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>Öğe örnekleri

[İtem_management. Kopyala](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) Python örneği aşağıdaki görevlerin nasıl yapılacağını gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos belgeleri hakkında bilgi edinmek için bkz. [veritabanları, kapsayıcılar ve öğelerle çalışma](account-databases-containers-items.md) kavramsal makalesi.

| Görev | API başvurusu |
| --- | --- |
| [Kapsayıcıda öğe oluşturma](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [Öğe KIMLIĞINE göre oku](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [Bir kapsayıcıdaki tüm öğeleri okuma](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [Öğeyi KIMLIĞINE göre sorgulama](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [Bir öğeyi değiştirme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [Bir öğeyi yukarı Ekle](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [Öğeyi silme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [Bir kapsayıcıdaki öğelerin değişiklik akışını al](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>Dizin örnekleri

[İndex_management. Kopyala](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) Python örneği aşağıdaki görevlerin nasıl yapılacağını gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos DB Dizin oluşturma hakkında bilgi edinmek için bkz. [Dizin oluşturma ilkeleri](index-policy.md), [Dizin oluşturma türleri](index-overview.md#index-types)ve [Dizin oluşturma yolları](index-policy.md#include-exclude-paths) kavramsal makaleler.

| Görev | API başvurusu |
| --- | --- |
| [Belirli bir öğeyi dizinlemenin dışında tut](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Dizini oluşturulmuş belirli öğelerle el ile dizin oluşturma kullanma](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | belgelerini. Indexingdirective. Include |
| [Dizin oluşturma işleminden dışlanan yollar](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Özellikte dışlanacak yolları tanımlayın `IndexingPolicy` |
| [Dizelerde aralık dizinlerini kullanma](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Dize veri türünde Aralık dizinleriyle dizin oluşturma ilkesi tanımlayın. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Dizin dönüştürme gerçekleştirme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container (güncelleştirilmiş dizin oluşturma ilkesini kullanın)|
| [Yolda yalnızca karma dizin bulunduğunda taramalar kullan](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | `enable_scan_in_query=True` `enable_cross_partition_query=True` öğeleri sorgularken ve öğesini ayarlama |
