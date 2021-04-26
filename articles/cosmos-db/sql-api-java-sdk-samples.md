---
title: 'Azure Cosmos DB SQL API: Java SDK v4 örnekleri'
description: CRUD işlemleri gibi Azure Cosmos DB SQL API'sinin kullanıldığı yaygın görevler için GitHub'da Java örnekleri bulabilirsiniz.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 0b0bdd204b2aa322adeb7373911b90e742c71999
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019017"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API: Java SDK v4 örnekleri
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET v2 SDK örnekleri](sql-api-dotnet-samples.md)
> * [.NET v3 SDK örnekleri](sql-api-dotnet-v3sdk-samples.md)
> * [Java v4 SDK örnekleri](sql-api-java-sdk-samples.md)
> * [Spring Data v3 SDK örnekleri](sql-api-spring-data-sdk-samples.md)
> * [Node.js Örnekleri](sql-api-nodejs-samples.md)
> * [Python Örnekleri](sql-api-python-samples.md)
> * [Azure Kod Örneği Galerisi](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Java SDK v4 hakkında daha fazla bilgi edinmek için lütfen Azure Cosmos DB Java SDK v4 [sürüm notları](sql-api-sdk-java-v4.md), [maven deposu](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4 [Performans ipuçları](performance-tips-java-sdk-v4-sql.md)ve Azure Cosmos DB Java SDK v4 [sorun giderme kılavuzu](troubleshoot-java-sdk-v4-sql.md) ' na bakın. Şu anda v4 'den daha eski bir sürüm kullanıyorsanız, v4 'ye yükseltme konusunda yardım için [Azure Cosmos DB Java SDK 'sı v4](migrate-java-v4-sdk.md) Kılavuzu ' na bakın.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- [Visual Studio abone avantajlarını etkinleştirebilirsiniz](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Visual Studio aboneliğiniz, her ay size ücretli Azure hizmetleri için kullanabileceğiniz krediler verir.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

[Azure-Cosmos-Java-SQL-api-Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) GitHub deposuna CRUD işlemlerini ve diğer yaygın işlemleri gerçekleştiren en son örnek uygulamalar, Azure Cosmos DB kaynaklarında bulunur. Bu makalede aşağıdakiler sunulmaktadır:

* Örnek Java proje dosyalarının her birindeki görevlere bağlantılar. 
* İlgili API başvurusu içeriğine bağlantılar.

**Önkoşullar**

Bu örnek uygulamayı çalıştırmanız için aşağıdakiler gereklidir:

* Java Development Kit 8
* Azure Cosmos DB Java SDK v4

Projenizde kullanım için en son Azure Cosmos DB Java SDK v4 ikililerini almak üzere Maven 'yi isteğe bağlı olarak kullanabilirsiniz. Maven tüm gerekli bağımlılıkları otomatik olarak ekler. Aksi takdirde, pom.xml dosyasında listelenen bağımlılıkları doğrudan indirebilir ve derleme yolunuza ekleyebilirsiniz.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Örnek uygulamaları çalıştırma**

Örnek depoyu kopyalama:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

Örnekleri bir IDE (tutulma, IntelliJ veya VSCODE) kullanarak veya Maven kullanarak komut satırından çalıştırabilirsiniz.

Bu ortam değişkenlerinin ayarlanması gerekir

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account primary key
```

örneklere, hesabınıza okuma/yazma erişimi vermek için.

Örnek çalıştırmak için, ana sınıfını belirtin 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

Burada *Sample. Synchronicity. MainClass* olabilir
* crudquickstart. Sync. SampleCRUDQuickstart
* crudquickstart. Async. SampleCRUDQuickstartAsync
* ındexmanagement. Sync. Sampleındexmanagement
* ındexmanagement. Async. Sampleındexmanagementasync
* StoredProcedure. Sync. SampleStoredProcedure
* StoredProcedure. Async. SampleStoredProcedureAsync
* değişiklik akışı. SampleChangeFeedProcessor *(changefeed yalnızca bir zaman uyumsuz örneğe sahip, eşitleme örneği yok.)* ... vb...

> [!NOTE]
> Örnekler birbirinden bağımsızdır; kendi kendine ayarlanır ve sonra kendini temizler. Örnekler, oluşturmak için birden çok çağrı oluşturur `CosmosContainer` . Bu her yapıldığında, oluşturulan koleksiyonun performans katmanına karşılık aboneliğiniz 1 saatlik kullanım için faturalandırılır. 
> 
> 

## <a name="database-examples"></a>Veritabanı örnekleri
[Veritabanı CRUD örnekleri](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) dosyası, aşağıdaki görevlerin nasıl gerçekleştirileceğini gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos veritabanları hakkında bilgi edinmek için bkz. [veritabanları, kapsayıcılar ve öğelerle çalışma](account-databases-containers-items.md) kavramsal makalesi. 

| Görev | API başvurusu |
| --- | --- |
| [Veritabanı oluşturma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L77-L85) | CosmosClient. createDatabaseIfNotExists |
| [KIMLIĞE göre bir veritabanı oku](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L88-L95) | CosmosClient. getDatabase |
| [Tüm veritabanlarını okuma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L98-L112) | CosmosClient. readAllDatabases |
| [Veritabanı silme](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L115-L123) | CosmosDatabase. Delete |

## <a name="collection-examples"></a>Koleksiyon örnekleri
[Koleksiyon CRUD örnekleri](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) dosyası, aşağıdaki görevlerin nasıl gerçekleştirileceğini gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos koleksiyonları hakkında bilgi edinmek için bkz. [veritabanları, kapsayıcılar ve öğelerle çalışma](account-databases-containers-items.md) kavramsal makalesi.

| Görev | API başvurusu |
| --- | --- |
| [Koleksiyon oluşturma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L97-L112) | CosmosDatabase. createContainerIfNotExists |
| [Bir koleksiyonun yapılandırılan performansını değiştirme](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L115-L123) | CosmosContainer. Replaceprovisionedüretilen Iş aktarımı |
| [KIMLIĞE göre koleksiyon al](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L126-L133) | CosmosDatabase. getContainer |
| [Veritabanındaki tüm koleksiyonları okuma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L136-L150) | CosmosDatabase. readAllContainers |
| [Koleksiyonu silme](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L153-L161) | CosmosContainer. Delete |

## <a name="autoscale-collection-examples"></a>Otomatik ölçeklendirme koleksiyonu örnekleri

Bu örnekleri çalıştırmadan önce otomatik ölçeklendirme hakkında daha fazla bilgi edinmek için, [hesabınızda](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) ve [veritabanlarınızı ve kapsayıcılarınızda](./provision-throughput-autoscale.md)otomatik ölçeklendirmeyi etkinleştirmeye yönelik bu yönergelere göz atın.

[Otomatik ölçeklendirme veritabanı CRUD örnekleri](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) dosyası, aşağıdaki görevlerin nasıl gerçekleştirileceğini gösterir.

| Görev | API başvurusu |
| --- | --- |
| [Belirtilen otomatik ölçeklendirme en fazla aktarım hızı ile veritabanı oluşturma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L78-L89) | CosmosClient. createDatabase<br>ThroughputProperties. Createoto Scaledüretilen Iş |

[Otomatik ölçeklendirme koleksiyonu CRUD örnekleri](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) dosyası, aşağıdaki görevlerin nasıl gerçekleştirileceğini gösterir. 

| Görev | API başvurusu |
| --- | --- |
| [Belirtilen otomatik ölçeklendirme üst işleme ile bir koleksiyon oluşturun](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L97-L110) | CosmosDatabase. createContainerIfNotExists |
| [Bir koleksiyonun en fazla yapılandırılan otomatik ölçeklendirme iş üretimini değiştirme](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L113-L120) | CosmosContainer. Replaceüretilen Iş |
| [Bir koleksiyonun otomatik ölçeklendirme üretilen iş yapılandırmasını oku](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L122-L133) | CosmosContainer. Readüretilen Iş |

## <a name="analytical-storage-collection-examples"></a>Analitik depolama koleksiyonu örnekleri

[Analitik depolama koleksiyonu CRUD örnekleri](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) dosyası, aşağıdaki görevlerin nasıl gerçekleştirileceğini gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos koleksiyonları hakkında bilgi edinmek için Azure Cosmos DB SYNAPSE ve analitik depo hakkında bilgi edinin.

| Görev | API başvurusu |
| --- | --- |
| [Koleksiyon oluşturma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L93-L108) | CosmosDatabase. createContainerIfNotExists |

## <a name="document-examples"></a>Belge örnekleri
[Belge CRUD örnekleri](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) dosyası, aşağıdaki görevlerin nasıl gerçekleştirileceğini gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos belgeleri hakkında bilgi edinmek için bkz. [veritabanları, kapsayıcılar ve öğelerle çalışma](account-databases-containers-items.md) kavramsal makalesi.

| Görev | API başvurusu |
| --- | --- |
| [Belge oluşturma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L133-L147) | CosmosContainer. CreateItem |
| [Bir belgeyi KIMLIĞE göre oku](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L179-L193) | CosmosContainer. ReadItem |
| [Belgeler için sorgu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176) | CosmosContainer. QueryItems |
| [Belgeyi değiştirme](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L195-L210) | CosmosContainer. ReplaceItem |
| [Belgeyi upsert etme](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L212-L2225) | CosmosContainer. Upsertıtem |
| [Bir belgeyi silme](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L303-L310) | CosmosContainer. DeleteItem |
| [Bir belgeyi koşullu ETag denetimi ile değiştirme](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L227-L264) | AccessCondition. setType<br>AccessCondition. setCondition |
| [Yalnızca belge değiştiğinde belgeyi okuma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L266-L300) | AccessCondition. setType<br>AccessCondition. setCondition |

## <a name="indexing-examples"></a>Dizin örnekleri
[Koleksiyon CRUD örnekleri](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) dosyası, aşağıdaki görevlerin nasıl gerçekleştirileceğini gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos DB Dizin oluşturma hakkında bilgi edinmek için bkz. [Dizin oluşturma ilkeleri](index-policy.md), [Dizin oluşturma türleri](index-overview.md#index-types)ve [Dizin oluşturma yolları](index-policy.md#include-exclude-paths) kavramsal makaleler. 

| Görev | API başvurusu |
| --- | --- |
| Belgeyi dizinin dışında tutma | Excludedındex<br>IndexingPolicy |
| Geç dizin oluşturma kullanma | Indexingpolicy. ındexingmode |
| [Belirtilen belge yollarını dizine ekle](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L145-L148) | IndexingPolicy.IncludedPaths |
| [Belirtilen belge yollarını dizinden Dışla](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L150-L153) | IndexingPolicy.ExcludedPaths |
| [Bileşik dizin oluşturma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L171-L186) | Indexingpolicy. Setcompositeındexes<br>CompositePath |
| Karma dizin yolunda bir aralık tarama işlemini zorlama | FeedOptions.EnableScanInQuery |
| Dizelerde Aralık dizinleri kullanma | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Dizin dönüştürme gerçekleştirme | - |
| [Jeo-uzamsal dizin oluşturma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L157-L166) | Indexingpolicy. Setspatialındexes<br>SpatialSpec<br>SpatialType |

Dizin oluşturma hakkında daha fazla bilgi için bkz. [Azure Cosmos DB dizin oluşturma ilkeleri](index-policy.md).

## <a name="query-examples"></a>Sorgu örnekleri
[Sorgu örnekleri](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) dosyası, SQL sorgu dilbilgisini kullanarak aşağıdaki görevlerin nasıl yapılacağını gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos DB SQL sorgu başvurusu hakkında bilgi edinmek için, bkz. [SQL sorgu örnekleri Azure Cosmos DB](./sql-query-getting-started.md). 

| Görev | API başvurusu |
| --- | --- |
| [Tüm belgeler için sorgu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L210-L214) | CosmosContainer. QueryItems |
| [== kullanan eşitlik için sorgu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L291-L295) | CosmosContainer. QueryItems |
| [!= ve NOT kullanan eşitsizlik için sorgu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L297-L305) | CosmosContainer. QueryItems |
| [>, <, >=, <= gibi aralık işleçlerini kullanarak sorgulama](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L307-L312) | CosmosContainer. QueryItems |
| [Dizelere karşı aralık işleçlerini kullanarak sorgulama](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L314-L319) | CosmosContainer. QueryItems |
| [SıRALAMA ölçütü olan sorgu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L321-L326) | CosmosContainer. QueryItems |
| [FARKLı sorgu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L328-L333) | CosmosContainer. QueryItems |
| [Toplama işlevleriyle sorgu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L335-L343) | CosmosContainer. QueryItems |
| [alt belgelerle çalışma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L345-L353) | CosmosContainer. QueryItems |
| [Belgeler arası Birleştirmeler ile sorgulama](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L355-L377) | CosmosContainer. QueryItems |
| [String, Math ve Array işleçleri ile sorgu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L379-L390) | CosmosContainer. QueryItems |
| [SqlQuerySpec kullanarak parametreli SQL ile sorgulama](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421) |CosmosContainer. QueryItems |
| [Açık disk belleği ile sorgu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216-L266) | CosmosContainer. QueryItems |
| [Bölümlenmiş koleksiyonları paralel olarak sorgulama](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L268-L289) | CosmosContainer. QueryItems |
| Bölümlenmiş koleksiyonlar için ORDER BY ile sorgu | CosmosContainer. QueryItems |

## <a name="change-feed-examples"></a>Akış örneklerini değiştirme 
[Değişiklik akışı Işlemcisi örnek](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) dosyası, aşağıdaki görevlerin nasıl yapılacağını gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos DB akışı değiştirme hakkında bilgi edinmek için bkz. [okuma Azure Cosmos DB değiştirme akışı](read-change-feed.md) ve [akış işlemcisini değiştirme](change-feed-processor.md).

| Görev | API başvurusu |
| --- | --- |
| [Temel değişiklik akışı işlevselliği](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) |ChangeFeedProcessor. changeFeedProcessorBuilder |
| Belirli bir zamandan değişiklik akışını okuma | ChangeFeedProcessor. changeFeedProcessorBuilder |
| [Baştan başlayarak değişiklik akışını okuma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) | - |

## <a name="server-side-programming-examples"></a>Sunucu tarafı programlama örnekleri

[Saklı yordam örnek](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) dosyası, aşağıdaki görevlerin nasıl yapılacağını gösterir. Aşağıdaki örnekleri çalıştırmadan önce Azure Cosmos DB sunucu tarafı programlama hakkında bilgi edinmek için, bkz. [saklı yordamlar, Tetikleyiciler ve Kullanıcı tanımlı işlevler](stored-procedures-triggers-udfs.md).

| Görev | API başvurusu |
| --- | --- |
| [Saklı yordam oluşturma](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L132-L151) | CosmosScripts. createStoredProcedure |
| [Saklı yordamı yürütme](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L167-L181) | CosmosStoredProcedure.exeşirin |
| [Saklı yordamı silme](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L183-L193) | CosmosStoredProcedure. Delete |

## <a name="user-management-examples"></a>Kullanıcı yönetim örnekleri
Kullanıcı Yönetimi örnek dosyasında aşağıdaki görevlerin nasıl yapılacağı gösterilmektedir:

| Görev | API başvurusu |
| --- | --- |
| Kullanıcı oluşturma | - |
| Koleksiyon veya belge üzerinde izinleri ayarlama | - |
| Bir kullanıcının izinlerinin listesini alma |- |
