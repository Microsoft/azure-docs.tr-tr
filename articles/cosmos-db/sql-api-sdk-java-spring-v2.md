---
title: SQL API sürüm notları ve kaynakları için Spring Data Azure Cosmos DB v2
description: Sürüm tarihleri, kullanımdan kaldırma tarihleri ve Azure Cosmos DB SQL Async Java SDK 'sının her sürümü arasında yapılan değişiklikler dahil olmak üzere SQL API için Spring Data Azure Cosmos DB v2 hakkında bilgi edinin.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 4cd92efb7b6596288e4b6ef8e81f82f775a24e01
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363453"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v2 for Core (SQL) API: sürüm notları ve kaynakları
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

 Temel (SQL) için Spring Data Azure Cosmos DB sürüm 2, geliştiricilerin Spring uygulamalarında Azure Cosmos DB kullanmasına izin verir. Spring Data Azure Cosmos DB veritabanlarını ve koleksiyonları işlemek, belgelerle çalışmak ve sorgu vermek için Spring Data arabirimini kullanıma sunar. Aynı Maven yapıtı içinde hem eşitleme hem de Async (reaktif) API 'Leri desteklenir. 

[Spring Framework](https://spring.io/projects/spring-framework) , Java uygulaması geliştirmeyi kolaylaştırın bir programlama ve yapılandırma modelidir. Yay, bağımlılık ekleme kullanarak uygulamaların "sıhhi tesisat" düzeyini kolaylaştırır. Uygulama oluşturmayı ve test etmeyi daha basit hale getiren yay gibi birçok geliştirici. [Spring Boot](https://spring.io/projects/spring-boot) , sıhhi tesisat 'nin bu işlemesini, Web uygulaması ve mikro hizmet geliştirmeye yönelik bir gözle genişletiyor. [Spring Data](https://spring.io/projects/spring-data) , Spring veya Spring Boot uygulaması bağlamından Azure Cosmos DB gibi veri depolarına erişmek için bir programlama modelidir. 

Spring Data Azure Cosmos DB, [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) uygulamalarınızda kullanabilirsiniz.

> [!IMPORTANT]  
> Bu sürüm notları, Spring Data Azure Cosmos DB sürüm 2 ' dir. [Sürüm 3 için sürüm notlarını burada](sql-api-sdk-java-spring-v3.md)bulabilirsiniz. 
>
> Spring Data Azure Cosmos DB yalnızca SQL API 'sini destekler.
>
> Diğer Azure Cosmos DB API 'Lerinde yay verileri hakkında bilgi için aşağıdaki makalelere bakın:
> * [Azure Cosmos DB ile Apache Cassandra için yay verileri](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB ile Spring Data MongoDB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Yay verileri Gremlin Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Hızlı bir şekilde geçmek ister misiniz?
> 1. SDK 'yı kullanabilmeniz için [desteklenen minimum Java Runtime olan JDK 8](/java/azure/jdk/)' i yükler.
> 2. [Starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)kullanarak bir Spring Data Azure Cosmos db uygulaması oluşturun. İşte bu kadar!
> 3. [Yay verileri Azure Cosmos DB Geliştirici Kılavuzu](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)üzerinden çalışarak temel Azure Cosmos DB istekleriyle adım adım ilerleyin.
>
> Spring [ınitialsps](https://start.spring.io/)kullanarak Spring Boot Starter uygulamalarını hızlı bir şekilde çalıştırabilirsiniz!
>

## <a name="resources"></a>Kaynaklar

| Kaynak | Bağlantı |
|---|---|
| **SDK indirmesi** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API belgeleri** | [Yay verileri Azure Cosmos DB başvuru belgeleri]() |
|**SDK 'ya katkıda bulunma** | [Spring Data Azure Cosmos DB GitHub 'da depo](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [Java için Azure Cosmos DB Spring Boot Starter istemci kitaplığı](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Spring TODO uygulama örneği Azure Cosmos DB**| [App Service Linux 'ta uçtan uca Java deneyimi (Bölüm 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Geliştirici Kılavuzu** | [Spring Data Azure Cosmos DB geliştirici kılavuzu](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Starter 'ı kullanma** | [Azure Cosmos DB SQL API 'SI ile Spring Boot Starter 'ı kullanma](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Azure Cosmos DB Spring Boot Starter için GitHub deposu](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Azure App Service ile örnek** | [Linux üzerinde App Service ile Spring ve Cosmos DB’yi kullanma](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO uygulaması örneği](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Yayın geçmişi

### <a name="230-may-21-2020"></a>2.3.0 (Mayıs 21 Mayıs 2020)
#### <a name="new-features"></a>Yeni özellikler
* Yay önyükleme sürümünü 2.3.0 olarak güncelleştirir.


### <a name="225-may-19-2020"></a>2.2.5 (19 Mayıs 2020)
#### <a name="new-features"></a>Yeni özellikler
* Azure Cosmos DB sürümünü 3.7.3 sürümüne güncelleştirir.
#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri
* Azure Cosmos DB SDK 3.7.3 'ten bellek sızıntısı düzeltmeleri ve Netty sürüm yükseltmeleri içerir.

### <a name="224-april-6-2020"></a>2.2.4 (6 Nisan 2020)
#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri
* ' `allowTelemetry` Den hesaba gitmek için düzeltmeler bayrağı `CosmosDbConfig` .
* `TTL`Kapsayıcıda özelliği düzeltir.

### <a name="223-february-25-2020"></a>2.2.3 (25 Şubat 2020)
#### <a name="new-features"></a>Yeni özellikler
* `findAll`Bölüm anahtarı API 'sine göre yeni ekler.
* Azure Cosmos DB sürümünü 3.7.0 sürümüne güncelleştirir.
#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri
* Düzeltmeler `collectionName`  ->  `containerName` .
* `entityClass`Ve düzeltmeleri `domainClass`  ->  `domainType` .
* "Giriş varlığı yerine depo tarafından kaydedilen varlık koleksiyonunu Döndür" düzeltmelerini düzeltir.

### <a name="2110-february-25-2020"></a>2.1.10 (25 Şubat 2020)
#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri
* "Giriş varlığı yerine depo tarafından kaydedilen dönüş varlık koleksiyonu" için geri bağlantı noktaları. "

### <a name="222-january-15-2020"></a>2.2.2 (15 Ocak 2020)
#### <a name="new-features"></a>Yeni özellikler
* Azure Cosmos DB sürümünü 3.6.0 sürümüne güncelleştirir.
#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri

### <a name="221-december-31-2019"></a>2.2.1 (31 Aralık 2019)
#### <a name="new-features"></a>Yeni özellikler
* SDK sürümü Azure Cosmos DB güncelleştirme 3.5.0.
* Otomatik koleksiyon oluşturmayı etkinleştirmek veya devre dışı bırakmak için ek açıklama alanı ekler.
* Özel durum işlemeyi geliştirir. Üzerinden kullanıma sunar `CosmosClientException` `CosmosDBAccessException` .
* `requestCharge`Ve `activityId` aracılığıyla gösterir `ResponseDiagnostics` .
#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri
* SDK 3.5.0 güncelleştirme düzeltmeleri "Cosmos DB HTTP yanıt üst bilgisi 8192 bayttan büyük olduğunda özel durum," "Başsıfıellik Ilkesi. Defaultsıfıen düzeyi", sınırlı eskime ve tutarlı ön ek üzerinde başarısız olur. "
* `findById`Metodun davranışını düzeltir. Daha önce, bu yöntem bir özel durum oluşturmak yerine varlık bulunmazsa boş olarak döndürülür.
* Kullanıldığında sonraki sayfada sıralamanın uygulanmadığı bir hatayı düzeltir `CosmosPageRequest` .

### <a name="219-december-26-2019"></a>2.1.9 (26 Aralık 2019)
#### <a name="new-features"></a>Yeni özellikler
* Otomatik koleksiyon oluşturmayı etkinleştirmek veya devre dışı bırakmak için ek açıklama alanı ekler.
#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri
*  `findById`Metodun davranışını düzeltir. Daha önce, bu yöntem bir özel durum oluşturmak yerine varlık bulunmazsa boş olarak döndürülür.

### <a name="220-october-21-2019"></a>2.2.0 (21 Ekim 2019)
#### <a name="new-features"></a>Yeni özellikler
* Reaksel Cosmos depo desteğini tamamen doldurun.
* Azure Cosmos DB tanılama dizesi ve sorgu ölçümleri desteği.
* SDK 3.3.1 sürümüne güncelleştirme Azure Cosmos DB.
* Spring Framework sürümü 5.2.0. RELEASE sürümüne yükseltilir.
* Spring Data Commons sürümü 2.2.0. RELEASE sürümüne yükseltilir.
* `findByIdAndPartitionKey`Ve `deleteByIdAndPartitionKey` API 'leri ekler.
* Azure-DocumentDB ' den bağımlılığı kaldırır.
* DocumentDB Azure Cosmos DB için yeniden markalar.
#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri
* Düzeltmeler "sıralama, pageSize, depodaki toplam öğeden daha az olduğunda özel durum oluşturur."

### <a name="218-october-18-2019"></a>2.1.8 (18 Ekim 2019)
#### <a name="new-features"></a>Yeni özellikler
* DocumentDB API 'Lerini kullanımdan kaldırma.
* `findByIdAndPartitionKey`Ve `deleteByIdAndPartitionKey` API 'leri ekler.
* Temelinde iyimser kilitleme ekler `_etag` .
* Belge koleksiyonu adı için SpEL ifadesini izin vermez.
* `ObjectMapper`İyileştirmeler ekler.

### <a name="217-october-18-2019"></a>2.1.7 (18 Ekim 2019)
#### <a name="new-features"></a>Yeni özellikler
* Azure Cosmos DB SDK sürüm 3 bağımlılığı ekler.
* Reaktif Cosmos deposu ekler.
* `DocumentDbTemplate`Azure Cosmos DB SDK sürüm 3 ' ü kullanmak için uygulamasını güncelleştirir.
* Reaksel Cosmos depo desteği için başka yapılandırma değişiklikleri ekler.

### <a name="212-march-19-2019"></a>2.1.2 'yi (19 Mart 2019)
#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri
* `applicationInsights`İçin bağımlılığı kaldırır:
    * Bağımlılık kirletinin potansiyel riski.
    * Java 11 uyumsuzluğu.
    * CPU ve/veya bellek için olası performans etkisini önleme.

### <a name="207-march-20-2019"></a>2.0.7 (20 Mart 2019)
#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri
* Geri bağlantı noktası bağımlılığı şu şekilde kaldırır `applicationInsights` :
    * Bağımlılık kirletinin potansiyel riski.
    * Java 11 uyumsuzluğu.
    * CPU ve/veya bellek için olası performans etkisini önleme.

### <a name="211-march-7-2019"></a>2.1.1 (7 Mart 2019)
#### <a name="new-features"></a>Yeni özellikler
* Ana sürümü 2.1.1 olarak güncelleştirir.

### <a name="206-march-7-2019"></a>2.0.6 (7 Mart 2019)
#### <a name="new-features"></a>Yeni özellikler
* Telemetrideki tüm özel durumları yoksayın.

### <a name="210-december-17-2018"></a>2.1.0 (17 Aralık 2018)
#### <a name="new-features"></a>Yeni özellikler
* Sorunu gidermek için 2.1.0 sürümünü güncelleştirir.

### <a name="205-september-13-2018"></a>2.0.5 (13 Eylül 2018)
#### <a name="new-features"></a>Yeni özellikler
* Anahtar sözcükleri `exists` ve ekler `startsWith` .
* Güncelleştirme Benioku dosyası.
#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri
* Düzeltmeler "doğrudan varlık için kendi kendine href çağrılamaz."
* "Koleksiyon oluşturulmadıysa findAll başarısız olur." düzeltmeleri

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (ön sürüm) (23 Ağustos 2018)
#### <a name="new-features"></a>Yeni özellikler
* DocumentDB 'den cosmosdb 'ye olan paketi yeniden adlandırır.
* Sorgu yöntemi anahtar sözcüğünün yeni özelliği ekler. SQL API 'den 16 anahtar sözcük artık desteklenmektedir.
* Sayfalama ve sıralamaya sahip yeni bir sorgu özelliği ekler.
* Spring-Data-cosmosdb yapılandırmasını basitleştirir.
* `deleteCollection`Ve `deleteAll` API 'leri ekler.

#### <a name="key-bug-fixes"></a>Anahtar hata düzeltmeleri
* Hata düzelme ve hata azaltma.

## <a name="faq"></a>SSS
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Sonraki adımlar
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)hakkında daha fazla bilgi edinin.

[Spring Framework](https://spring.io/projects/spring-framework)hakkında daha fazla bilgi edinin.

[Spring Boot](https://spring.io/projects/spring-boot)hakkında daha fazla bilgi edinin.

[Yay verileri](https://spring.io/projects/spring-data)hakkında daha fazla bilgi edinin.