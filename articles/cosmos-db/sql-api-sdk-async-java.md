---
title: "Azure Cosmos DB: SQL zaman uyumsuz Java API 'SI, SDK & kaynakları"
description: Sürüm tarihleri, kullanımdan kaldırma tarihleri ve SQL zaman uyumsuz Java SDK Azure Cosmos DB her sürümü arasında yapılan değişiklikler dahil olmak üzere SQL zaman uyumsuz Java API 'SI ve SDK hakkında bilgi edinin.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: a63194ae3ca9e5661b82113c450ebb929cc67e76
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364541"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API için zaman uyumsuz Java SDK Azure Cosmos DB: sürüm notları ve kaynakları
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
> * [REST](/rest/api
> * [REST kaynak sağlayıcısı](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [Toplu yürütücü - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Toplu yürütücü - Java](sql-api-sdk-bulk-executor-java.md)

SQL API zaman uyumsuz Java SDK 'Sı, [Netty kitaplığı](https://netty.io/)desteğiyle zaman uyumsuz Işlemler sağlayarak SQL API Java SDK 'sinden farklıdır. Önceden var olan [SQL API Java SDK 'sı](sql-api-sdk-java.md) , zaman uyumsuz işlemleri desteklemez. 

> [!IMPORTANT]  
> Bu, Azure Cosmos DB için en son Java SDK 'Sı *değildir* ! Projeniz için [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) kullanmayı düşünün. Yükseltmek için [Azure Cosmos DB Java SDK 'sı v4](migrate-java-v4-sdk.md) Kılavuzu ve [reaktör vs rxjava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) Kılavuzu 'ndaki yönergeleri izleyin. 
>

| | Bağlantılar |
|---|---|
| **SDK Indirmesi** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API belgeleri** |[Java API başvuru belgeleri](/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) | 
|**SDK 'ya katkıda bulunma** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Kullanmaya başlama** | [Zaman uyumsuz Java SDK 'sını kullanmaya başlama](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Kod örneği** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Performans ipuçları**| [GitHub Benioku dosyası](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Desteklenen en düşük çalışma zamanı**|[JDK 8](/java/azure/jdk/) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>SSS
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Ayrıca bkz.
Cosmos DB hakkında daha fazla bilgi edinmek için [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hizmeti sayfasına bakın.