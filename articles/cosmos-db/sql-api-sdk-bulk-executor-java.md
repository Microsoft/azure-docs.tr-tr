---
title: "Azure Cosmos DB: toplu yürütücü Java API 'SI, SDK & kaynakları"
description: Toplu yürütücü Java API 'SI ve SDK ve sürüm tarihleri, emeklilik tarihleri ve Azure Cosmos DB toplu yürütücü Java SDK 'sının her sürümü arasında yapılan değişiklikler dahil olmak üzere tüm bilgileri öğrenin.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 2d3c7026fd221b1a17b8efe56b03b2a26358c7ab
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364437"
---
# <a name="java-bulk-executor-library-download-information"></a>Java toplu yürütücü kitaplığı: Indirme bilgileri
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Değişiklik Akışı SDK'sı v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK’sı v4](sql-api-sdk-java-v4.md)
> * [Zaman uyumsuz Java SDK v2](sql-api-sdk-async-java.md)
> * [Zaman uyumlu Java SDK v2](sql-api-sdk-java.md)
> * [Spring Verileri v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Verileri v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP Bağlayıcısı](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP Bağlayıcısı](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST kaynak sağlayıcısı](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Toplu yürütücü - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Toplu yürütücü - Java](sql-api-sdk-bulk-executor-java.md)

| | Bağlantı/notlar |
|---|---|
|**Açıklama**|Toplu yürütücü kitaplığı, istemci uygulamalarının Azure Cosmos DB hesaplarında toplu işlemler gerçekleştirmesine olanak sağlar. toplu yürütücü kitaplığı, BulkImport ve BulkUpdate ad alanları sağlar. BulkImport modülü, bir koleksiyon için sağlanan aktarım hızı en büyük ölçüde tüketilebilmesi için belgeleri en iyi duruma getirilmiş bir şekilde toplu Içe alabilir. BulkUpdate modülü Azure Cosmos kapsayıcılarındaki mevcut verileri düzeltme eki olarak toplu güncelleştirebilir.|
|**SDK indirmesi**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub 'da toplu yürütücü kitaplığı**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API belgeleri**| [Java API başvuru belgeleri](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Kullanmaya başlama**|[Toplu yürütücü kitaplığı Java SDK 'sını kullanmaya başlama](bulk-executor-java.md)|
|**Desteklenen en düşük çalışma zamanı**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/)|

## <a name="release-notes"></a>Sürüm notları

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* JSON 'dan iç içe bölüm anahtar değerlerini doğru bir şekilde ayıklamak için DocumentAnalyzer. Java için çözüm.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Belirli hatalarda yeniden denemek için BulkDelete işlemlerine işlevsellik ekleyin ve ayrıca yeniden denenebilecek kullanıcıya hataların bir listesini geri döndürün.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Cosmos SDK sürümü 2.4.7 için güncelleştirme.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* ' İd ' ve bölüm anahtarı değerinden sonra gelen düzeltme eki uygulanmış belge özellikleri güncelleştirilmiş öğe listesine eklendikten sonra, ' mergeAll ' için ' id ' ve bölüm anahtarı değeri ile devam etmek için düzeltme.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Eşzamanlılık başlangıç derecesini 1 ' e güncelleştirin ve mini Batch için hata ayıklama günlüklerini eklendi.