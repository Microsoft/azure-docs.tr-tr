---
title: SQL API (Önizleme) için Azure Cosmos DB Apache Spark 3 OLTP Bağlayıcısı Sürüm notları ve kaynakları
description: Sürüm tarihleri, emeklilik tarihleri ve Azure Cosmos DB SQL Java SDK 'sının her sürümü arasında yapılan değişiklikler dahil olmak üzere, SQL API 'SI için Azure Cosmos DB Apache Spark 3 OLTP Bağlayıcısı (Önizleme) hakkında bilgi edinin.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e7d206b63d6e1bf741a5dc298dd5bbc2d48ab11b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368704"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-preview-release-notes-and-resources"></a>Çekirdek (SQL) API (Önizleme) için Azure Cosmos DB Apache Spark 3 OLTP Bağlayıcısı: sürüm notları ve kaynakları
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

**Azure Cosmos DB Spark 3 OLTP Bağlayıcısı (Önizleme)** , SQL apı kullanarak Azure Cosmos DB için Apache Spark v3 desteği sağlar.
[Azure Cosmos DB](introduction.md) , geliştiricilerin SQL, MongoDB, Cassandra, Graph ve tablo gibi çeşitli standart API 'ler kullanılarak verilerle çalışmasını sağlayan, küresel olarak dağıtılmış bir veritabanı hizmetidir.

> [!Note]
> Azure Cosmos DB Spark 3 OLTP bağlayıcısının bu sürümü bir önizleme derlemesi.
> Bu derleme yüklenmedi veya performans test edilmedi.
> Bu derleme üretim senaryolarında kullanım için önerilmez.
>

## <a name="documentation"></a>Belgeler

- [Başlarken](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [Katalog API’si](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [Yapılandırma parametresi başvurusu](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>Sürüm uyumluluğu

| Bağlayıcı     | Spark         | En düşük Java sürümü | Desteklenen Scala sürümleri |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0-Beta. 1  | 3.1.1         |        8             | 2,12                     |

## <a name="download"></a>İndir

Spark bağlayıcısını Maven 'ten Databricks Runtime 8 ' e otomatik olarak yüklemek için jar Maven koordinatını kullanabilirsiniz: `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.0.0-beta.1`

Ayrıca, SBT projenizde Cosmos DB Spark Bağlayıcısı ile tümleştirebilirsiniz:
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.0.0-beta.1"
```

Cosmos DB Spark Bağlayıcısı, [Maven merkezi](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/4.0.0-beta.1/jar)depositesinde kullanılabilir.

### <a name="general"></a>Genel

Herhangi bir hatayla karşılaşırsanız lütfen [buraya](https://github.com/Azure/azure-sdk-for-java/issues/new)bir sorun bildirin.

Yapılan yeni bir özellik veya değişiklik önermek için bir sorunu hata ile aynı şekilde dosyalayamazsınız.

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>Sonraki adımlar

[Azure Cosmos DB Spark 3 OLTP Bağlayıcısı ile çalışmaya yönelik hızlı başlangıç kılavuzumuzu](create-sql-api-spark.md)gözden geçirin.
