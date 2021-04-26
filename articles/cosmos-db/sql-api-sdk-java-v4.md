---
title: SQL API sürüm notları ve kaynakları için Azure Cosmos DB Java SDK v4
description: SQL API 'SI ve SDK için Azure Cosmos DB Java SDK v4, sürüm tarihleri, kullanımdan kaldırma tarihleri ve Azure Cosmos DB SQL zaman uyumsuz Java SDK 'sının her sürümü arasında yapılan değişiklikler dahil olmak üzere.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d31f3d1c510ffe6c3f0a739a4e41313c8c6e7cf0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364830"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API 'SI için Java SDK v4 Azure Cosmos DB: sürüm notları ve kaynakları
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

Core (SQL) için Java SDK 'Sı v4 Azure Cosmos DB, zaman uyumsuz bir API ve eşitleme API 'sini tek bir Maven yapıtı halinde birleştirir. V4 SDK 'Sı, proje reaktör ve [Netty kitaplığı](https://netty.io/)temelinde gelişmiş performans, yeni API özellikleri ve zaman uyumsuz destek sunar. Kullanıcılar, Azure Cosmos DB Java SDK v4 ile [Azure Cosmos DB zaman uyumsuz Java SDK v2](sql-api-sdk-async-java.md) ve [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md)ile daha iyi performans bekleyebilir.

> [!IMPORTANT]  
> Bu sürüm notları yalnızca Azure Cosmos DB Java SDK v4 içindir. Şu anda v4 'den daha eski bir sürüm kullanıyorsanız, v4 'ye yükseltme konusunda yardım için [Azure Cosmos DB Java SDK 'sı v4](migrate-java-v4-sdk.md) Kılavuzu ' na bakın.
>
> İşte hızlı bir şekilde çalışmaya yönelik üç adım!
> 1. SDK 'yı kullanabilmek için [desteklenen en düşük Java çalışma zamanı 'nı (JDK 8](/java/azure/jdk/) ) yükleyebilirsiniz.
> 2. Maven yapıtına erişmenizi sağlayan [Azure Cosmos DB Java SDK v4 Için hızlı başlangıç kılavuzu](./create-sql-api-java.md) aracılığıyla çalışın ve temel Azure Cosmos DB isteklerine kılavuzluk eder.
> 3. Uygulamanızın SDK 'sını iyileştirmek için Azure Cosmos DB Java SDK v4 [Performans ipuçları](performance-tips-java-sdk-v4-sql.md) ve [sorun giderme](troubleshoot-java-sdk-v4-sql.md) kılavuzlarını okuyun.
>
> [Azure Cosmos DB atölyeler ve Labs](https://aka.ms/cosmosworkshop) , Java SDK 'sı v4 Azure Cosmos DB kullanmayı öğrenmek için başka harika bir kaynaktır!
>

## <a name="helpful-content"></a>Yardımcı içerik

| Content | Bağlantı |
|---|---|
|**SDK indirmesi**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API belgeleri** | [Java API başvuru belgeleri](/java/api/overview/azure/cosmosdb/client) |
|**SDK 'ya katkıda bulunma** | [GitHub 'da Java merkezi deposu için Azure SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Kullanmaya başlama** | [Hızlı başlangıç: Azure Cosmos DB SQL API verilerini yönetmek için bir Java uygulaması oluşturma](./create-sql-api-java.md) <br> [Hızlı başlangıç kodu ile GitHub deposu](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Temel kod örnekleri** | [Azure Cosmos DB: SQL API'si için Java örnekleri](sql-api-java-sdk-samples.md) <br> [Örnek kodla GitHub deposu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Değişiklik akışı olan konsol uygulaması**| [Değişiklik akışı-Java SDK v4 örneği](create-sql-api-java-changefeed.md) <br> [Örnek kodla GitHub deposu](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Web uygulaması örneği**| [Java SDK v4 ile Web uygulaması oluşturma](sql-api-java-application.md) <br> [Örnek kodla GitHub deposu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Performans ipuçları**| [Java SDK v4 için performans ipuçları](performance-tips-java-sdk-v4-sql.md)| 
| **Sorun giderme** | [Java SDK v4 sorunlarını giderme](troubleshoot-java-sdk-v4-sql.md) |
| **Daha eski bir SDK 'dan v4 'ye geçiş** | [Java V4 SDK’ya geçirme](migrate-java-v4-sdk.md) |
| **Desteklenen en düşük çalışma zamanı**|[JDK 8](/java/azure/jdk/) | 
| **Azure Cosmos DB atölyeler ve Labs** |[Cosmos DB atölyeler giriş sayfası](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>SSS
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Sonraki adımlar
Cosmos DB hakkında daha fazla bilgi edinmek için [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hizmeti sayfasına bakın.