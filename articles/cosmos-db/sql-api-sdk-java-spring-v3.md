---
title: Spring Data Azure Cosmos DB v3 for SQL API sürüm notları ve kaynakları
description: Sürüm tarihleri, kullanımdan kaldırma tarihleri ve SQL zaman uyumsuz Java SDK 'sının her Azure Cosmos DB bir sürümü arasında yapılan değişiklikler dahil olmak üzere, SQL API için Azure Cosmos DB v3 verileri hakkında bilgi edinin.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 594d38425be0304a9f7737bdfba60b29187a2e2d
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363521"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Yay verileri Azure Cosmos DB v3 for Core (SQL) API: sürüm notları ve kaynakları
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

Çekirdek (SQL) için Spring Data Azure Cosmos DB sürüm 3, geliştiricilerin Spring uygulamalarında Azure Cosmos DB kullanmasına izin verir. Spring Data Azure Cosmos DB veritabanlarını ve koleksiyonları işlemek, belgelerle çalışmak ve sorgu vermek için Spring Data arabirimini kullanıma sunar. Aynı Maven yapıtı içinde hem eşitleme hem de Async (reaktif) API 'Leri desteklenir. 

> [!IMPORTANT]
> Spring Data Azure Cosmos DB yay veri çerçevesine bir bağımlılığı vardır.
> 
> Azure-Spring-Data-Cosmos sürümleri, 3.0.0 ' den 3.4.0 ' ye kadar destek Spring veri 2,2 ve 2,3 ' dir.
> 
> Azure-Spring-Data-Cosmos sürümleri 3.5.0 ve üzeri, 2.4.3 ve üzeri Spring Data sürümlerini destekler.
>

[Spring Framework](https://spring.io/projects/spring-framework) , Java uygulaması geliştirmeyi kolaylaştırın bir programlama ve yapılandırma modelidir. Yay, bağımlılık ekleme kullanarak uygulamaların "sıhhi tesisat" düzeyini kolaylaştırır. Uygulama oluşturmayı ve test etmeyi daha basit hale getiren yay gibi birçok geliştirici. [Spring Boot](https://spring.io/projects/spring-boot) , sıhhi tesisat 'nin bu işlemesini, Web uygulaması ve mikro hizmet geliştirmeye yönelik bir gözle genişletiyor. [Spring Data](https://spring.io/projects/spring-data) , Spring veya Spring Boot uygulaması bağlamından Azure Cosmos DB gibi veri depolarına erişmek için bir programlama modeli ve çerçevesidir. 

Spring Data Azure Cosmos DB, [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) uygulamalarınızda kullanabilirsiniz.

> [!IMPORTANT]  
> Bu sürüm notları, Spring Data Azure Cosmos DB sürüm 3 ' ü yöneliktir. [Sürüm 2 için sürüm notlarını buradan](sql-api-sdk-java-spring-v2.md)bulabilirsiniz. 
>
> Spring Data Azure Cosmos DB yalnızca SQL API 'sini destekler.
>
> Diğer Azure Cosmos DB API 'Lerinde yay verileri hakkında bilgi için şu makalelere bakın:
> * [Azure Cosmos DB ile Apache Cassandra için yay verileri](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB ile Spring Data MongoDB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Yay verileri Gremlin Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>Hızla kullanmaya başlayın

  [Spring boot başlangıç kılavuzumuzu](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)Izleyerek spring Data Azure Cosmos DB ile çalışmaya başlayın. Spring Boot Starter yaklaşımı, Spring Data Azure Cosmos DB bağlayıcısını kullanmaya başlamak için önerilen yoldur.

  Alternatif olarak, aşağıda gösterildiği gibi Spring Data Azure Cosmos DB bağımlılığını dosyanıza ekleyebilirsiniz `pom.xml` :

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>Yardımcı içerik

| Content | Bağlantı |
|---|---|
|**SDK indirmesi**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**API belgeleri** | [Java API başvuru belgeleri](/java/api/com.azure.spring.data.cosmos) |
|**SDK 'ya katkıda bulunma** | [GitHub 'da Java merkezi deposu için Azure SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Kullanmaya başlama** | [Hızlı başlangıç: Azure Cosmos DB SQL API verilerini yönetmek için bir yay verileri Azure Cosmos DB uygulaması oluşturma](./create-sql-api-spring-data.md) <br> [Hızlı başlangıç kodu ile GitHub deposu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Temel kod örnekleri** | [Azure Cosmos DB: Spring Data Azure Cosmos DB SQL API örnekleri](sql-api-spring-data-sdk-samples.md) <br> [Örnek kodla GitHub deposu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Performans ipuçları**| [Java SDK v4 (Spring verileri için geçerlidir) için performans ipuçları](performance-tips-java-sdk-v4-sql.md)| 
| **Sorun giderme** | [Java SDK v4 sorunlarını giderme (Spring verileri için geçerlidir)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB atölyeler ve Labs** |[Cosmos DB atölyeler giriş sayfası](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Ek notlar

* Spring Data Azure Cosmos DB Java JDK 8 ve Java JDK 11 ' i destekler.
* Spring Data 2,3 Şu anda destekleniyor, Spring Data 2,4 Şu anda desteklenmiyor.

## <a name="faq"></a>SSS

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Sonraki adımlar

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)hakkında daha fazla bilgi edinin.

[Spring Framework](https://spring.io/projects/spring-framework)hakkında daha fazla bilgi edinin.

[Spring Boot](https://spring.io/projects/spring-boot)hakkında daha fazla bilgi edinin.

[Yay verileri](https://spring.io/projects/spring-data)hakkında daha fazla bilgi edinin.