---
title: "Azure Cosmos DB: toplu yürütücü .NET API 'SI, SDK & kaynakları"
description: Toplu yürütücü .NET API 'SI ve SDK ve sürüm tarihleri, emeklilik tarihleri ve Azure Cosmos DB toplu yürütücü .NET SDK 'sının her sürümü arasında yapılan değişiklikler dahil olmak üzere tüm bilgileri öğrenin.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.openlocfilehash: 99b6e06b817f98e64968615b5e652b707268fe78
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364507"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET toplu yürütücü kitaplığı: Indirme bilgileri 
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
| **Açıklama**| .NET toplu yürütücü kitaplığı, istemci uygulamalarının Azure Cosmos DB hesaplarında toplu işlemler gerçekleştirmesine olanak sağlar. Bu kitaplık, BulkImport, BulkUpdate ve BulkDelete ad alanları sağlar. BulkImport modülü, bir koleksiyon için sağlanan aktarım hızı en büyük ölçüde tüketilebilmesi için belgeleri en iyi duruma getirilmiş bir şekilde toplu Içe alabilir. BulkUpdate modülü Azure Cosmos kapsayıcılarındaki mevcut verileri düzeltme eki olarak toplu güncelleştirebilir. BulkDelete modülü, bir koleksiyon için sağlanan aktarım hızı en yüksek ölçüde tüketilebilmesi için belgeleri iyileştirilmiş bir şekilde toplu olarak silebilir.|
|**SDK indirmesi**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub 'da toplu yürütücü kitaplığı**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API belgeleri**|[.NET API başvuru belgeleri](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor)|
|**Kullanmaya başlama**|[Toplu yürütücü kitaplığı .NET SDK ile çalışmaya başlama](bulk-executor-dot-net.md)|
| **Şu anda desteklenen çerçeve**| Microsoft .NET Framework 4.5.2, 4.6.1 ve .NET Standard 2,0 |

> [!NOTE]
> Toplu yürütücü kullanıyorsanız, lütfen SDK 'da yerleşik toplu yürütücü bulunan [.NET SDK 'sının](tutorial-sql-api-dotnet-bulk-import.md)en son sürümü olan 3. x sürümünü inceleyin. 

## <a name="release-notes"></a>Sürüm notları

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-Önizleme

* Tüm yeniden denemeler dahil toplam süreyi doğru şekilde ölçmek için BulkDelete yanıtında fixed TotalElapsedTime.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-Önizleme

* SDK bağımlılığı >= 2.5.1 olarak değiştirildi

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* Köşe ve kenarlar için TTL 'yi kabul etmek üzere Graf toplu yürütücü desteği eklendi

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* Ağ Geçidi modunda çalışırken Azure Cosmos DB elastik ölçekleme sırasında özel durumlara neden olan bir sorun düzeltildi. Bu düzeltilme, 1.4.1 Release 'e denk gelir.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* Bölüm anahtarını kabul etmek için SQL API hesapları için BulkDelete desteği eklendi, silinecek belge KIMLIĞI başlıkları. Bu değişiklik, 1.4.0 Release ile işlevsel olarak denk olur.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* .NET Standard 2,0 ' i desteklemek için Mongobulkyürütücü dahil. Bu özellik, 1.3.0 Release 'e, hedef Framework olarak 2,0 .NET Standard destekleyici bir eklenmesiyle birlikte işlevsel olarak eşdeğer hale getirir.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-Önizleme

* Toplu yürütücü kitaplığı 'nın .NET Core uygulamalarıyla çalışmasını sağlamak için desteklenen hedef çerçevelerinden biri olarak 2,0 .NET Standard eklendi.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Kaçış tekliflerine sahip değerler giriş parametresi olarak geçirildiğinde, BulkDeleteAsync ile ilgili bir sorun düzeltildi.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Mongobulkyürütücü üzerinde, doldurma ekleyerek ve bazı durumlarda belge boyutu sınırının üzerine giderek belge boyutunu beklenmedik bir şekilde artıran bir sorun düzeltildi.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* Koleksiyonda iç içe bölüm anahtarı yolları olduğunda, BulkDeleteAsync ile ilgili bir sorun düzeltildi.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* Mongobulkyürütücü artık IDisposable 'ı uyguluyor ve kullanıldıktan sonra atılmalıdır.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* SDK sürümünde kilit kaldırıldı. Paket artık SDK 'ya bağımlıdır >= 2.5.1.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* Bir POCO nesneleri ile sayısal değerleri içeren bir liste ile BulkImport çağrılırken tanımlayıcıların sabit işlenmesi.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* Tüm yeniden denemeler dahil toplam süreyi doğru şekilde ölçmek için BulkDelete yanıtında fixed TotalElapsedTime.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* Belirli senaryolarda yüksek CPU tüketimi düzeltildi.
* İzleme artık TraceSource kullanıyor. Kullanıcılar, kaynak için dinleyicileri tanımlayabilir `BulkExecutorTrace` .
* 2 MB boyutunda bir belge gönderirken bir kilit oluşmasına neden olabilecek nadir bir senaryo düzeltildi.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* Toplu yürütücü artık Azure Cosmos DB .NET SDK 'sının en son sürümünü kullanacak şekilde güncelleştirildi (2.4.0)

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* Köşe ve kenarlar için TTL 'yi kabul etmek üzere Graf toplu yürütücü desteği eklendi

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Ağ Geçidi modunda çalışırken Azure Cosmos DB elastik ölçekleme sırasında özel durumlara neden olan bir sorun düzeltildi.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* Bölüm anahtarını kabul etmek için SQL API hesapları için BulkDelete desteği eklendi, silinecek belge KIMLIĞI başlıkları.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Toplu yürütücü tarafından kullanılan Kullanıcı aracısında bir biçimlendirme sorununa neden olan bir sorun düzeltildi.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Toplu yürütücü içeri aktarma ve güncelleştirme API 'Lerinde, depolama, özel durum oluşturmadan geçerli kapasiteyi aştığında Cosmos kapsayıcısının esnek ölçeklendirilmesine şeffaf bir şekilde uyum sağlar.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* DocumentDB .NET SDK 'sının 2.1.3 sürümüne bağımlılığı.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Toplu yürütücü 'in sabit koleksiyonlara aktarılırken JSRT hatası oluşturması nedeniyle oluşan bir sorun düzeltildi.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Azure Cosmos DB SQL API hesapları için BulkDelete işlemi desteği eklendi.
* MongoDB için Azure Cosmos DB API 'sine sahip hesaplar için BulkImport işlemi desteği eklendi.
* DocumentDB .NET SDK 'sının 2.0.0 sürümüne bağımlılığı. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* Azure Cosmos DB Gremlin API hesapları için BulkImport işlemi desteği eklendi.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Azure Cosmos DB SQL API hesapları için BulkImport işlemine ikincil hata düzeltildi.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Azure Cosmos DB SQL API hesapları için BulkImport ve BulkUpdate işlemlerine yönelik destek eklendi.

## <a name="next-steps"></a>Sonraki adımlar

Toplu yürütücü Java Kitaplığı hakkında bilgi edinmek için aşağıdaki makaleye bakın:

[Java toplu yürütücü kitaplığı SDK 'Sı ve sürüm bilgileri](sql-api-sdk-bulk-executor-java.md)