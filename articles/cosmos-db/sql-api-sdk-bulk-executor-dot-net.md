---
title: "Azure Cosmos DB: toplu yürütücü .NET API 'SI, SDK & kaynakları"
description: Toplu yürütücü .NET API 'SI ve SDK ve sürüm tarihleri, emeklilik tarihleri ve Azure Cosmos DB toplu yürütücü .NET SDK 'sının her sürümü arasında yapılan değişiklikler dahil olmak üzere tüm bilgileri öğrenin.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/24/2019
ms.author: ramkris
ms.openlocfilehash: 0ff281e54df0273cc4c72d1780f4a99fe9719aec
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900182"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET toplu yürütücü kitaplığı: Indirme bilgileri 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET değişiklik akışı](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST Kaynak Sağlayıcısı](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Toplu yürütücü-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Toplu yürütücü-Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Açıklama**| .Net toplu yürütücü kitaplığı, istemci uygulamalarının Azure Cosmos DB hesaplarında toplu işlemler gerçekleştirmesine olanak sağlar. Bu kitaplık, BulkImport, BulkUpdate ve BulkDelete ad alanları sağlar. BulkImport modülü, bir koleksiyon için sağlanan aktarım hızı en büyük ölçüde tüketilebilmesi için belgeleri en iyi duruma getirilmiş bir şekilde toplu Içe alabilir. BulkUpdate modülü Azure Cosmos kapsayıcılarındaki mevcut verileri düzeltme eki olarak toplu güncelleştirebilir. BulkDelete modülü, bir koleksiyon için sağlanan aktarım hızı en yüksek ölçüde tüketilebilmesi için belgeleri iyileştirilmiş bir şekilde toplu olarak silebilir.|
|**SDK indirmesi**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub 'da toplu yürütücü kitaplığı**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API belgeleri**|[.NET API başvuru belgeleri](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Başlarken**|[Toplu yürütücü kitaplığı .NET SDK ile çalışmaya başlama](bulk-executor-dot-net.md)|
| **Desteklenen geçerli çerçeve**| Microsoft .NET Framework 4.5.2, 4.6.1 ve .NET Standard 2,0 |

## <a name="release-notes"></a>Sürüm notları

### <a name="a-name241-preview241-preview"></a><a name="2.4.1-preview"/>2.4.1-Preview

* Tüm yeniden denemeler dahil toplam süreyi doğru şekilde ölçmek için BulkDelete yanıtında fixed TotalElapsedTime.

### <a name="a-name240-preview240-preview"></a><a name="2.4.0-preview"/>2.4.0-Preview

* SDK bağımlılığı > = 2.5.1 olarak değiştirildi

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Köşe ve kenarlar için TTL 'yi kabul etmek üzere Graf toplu yürütücü desteği eklendi

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* Ağ Geçidi modunda çalışırken Azure Cosmos DB elastik ölçekleme sırasında özel durumlara neden olan bir sorun düzeltildi. Bu düzeltilme, 1.4.1 Release 'e denk gelir.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* Bölüm anahtarını kabul etmek için SQL API hesapları için BulkDelete desteği eklendi, silinecek belge kimliği başlıkları. Bu değişiklik, 1.4.0 Release ile işlevsel olarak denk olur.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* .NET Standard 2,0 ' i desteklemek için Mongobulkyürütücü dahil. Bu özellik, 1.3.0 Release 'e, hedef Framework olarak 2,0 .NET Standard destekleyici bir eklenmesiyle birlikte işlevsel olarak eşdeğer hale getirir.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* Toplu yürütücü kitaplığı 'nın .NET Core uygulamalarıyla çalışmasını sağlamak için desteklenen hedef çerçevelerinden biri olarak 2,0 .NET Standard eklendi.

### <a name="a-name185185"></a><a name="1.8.5"/>1.8.5

* SDK sürümünde kilit kaldırıldı. Paket artık SDK 'ya bağımlıdır > = 2.5.1.

### <a name="a-name184184"></a><a name="1.8.4"/>1.8.4

* Bir POCO nesneleri ile sayısal değerleri içeren bir liste ile BulkImport çağrılırken tanımlayıcıların sabit işlenmesi.

### <a name="a-name183183"></a><a name="1.8.3"/>1.8.3

* Tüm yeniden denemeler dahil toplam süreyi doğru şekilde ölçmek için BulkDelete yanıtında fixed TotalElapsedTime.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Belirli senaryolarda yüksek CPU tüketimi düzeltildi.
* İzleme artık TraceSource kullanıyor. Kullanıcılar `BulkExecutorTrace` kaynağı için dinleyicileri tanımlayabilir.
* 2 MB boyutunda bir belge gönderirken bir kilit oluşmasına neden olabilecek nadir bir senaryo düzeltildi.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Toplu yürütücü artık Azure Cosmos DB .NET SDK 'sının en son sürümünü kullanacak şekilde güncelleştirildi (2.4.0)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* Köşe ve kenarlar için TTL 'yi kabul etmek üzere Graf toplu yürütücü desteği eklendi

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* Ağ Geçidi modunda çalışırken Azure Cosmos DB elastik ölçekleme sırasında özel durumlara neden olan bir sorun düzeltildi.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* Bölüm anahtarını kabul etmek için SQL API hesapları için BulkDelete desteği eklendi, silinecek belge kimliği başlıkları.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Toplu yürütücü tarafından kullanılan Kullanıcı aracısında bir biçimlendirme sorununa neden olan bir sorun düzeltildi.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Toplu yürütücü içeri aktarma ve güncelleştirme API 'Lerinde, depolama, özel durum oluşturmadan geçerli kapasiteyi aştığında Cosmos kapsayıcısının esnek ölçeklendirilmesine şeffaf bir şekilde uyum sağlar.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* DocumentDB .NET SDK 'sının 2.1.3 sürümüne bağımlılığı.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Toplu yürütücü 'in sabit koleksiyonlara aktarılırken JSRT hatası oluşturması nedeniyle oluşan bir sorun düzeltildi.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Azure Cosmos DB SQL API hesapları için BulkDelete işlemi desteği eklendi.
* MongoDB için Azure Cosmos DB API 'sine sahip hesaplar için BulkImport işlemi desteği eklendi.
* DocumentDB .NET SDK 'sının 2.0.0 sürümüne bağımlılığı. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Azure Cosmos DB Gremlin API hesapları için BulkImport işlemi desteği eklendi.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Azure Cosmos DB SQL API hesapları için BulkImport işlemine ikincil hata düzeltildi.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Azure Cosmos DB SQL API hesapları için BulkImport ve BulkUpdate işlemlerine yönelik destek eklendi.

## <a name="next-steps"></a>Sonraki adımlar

Toplu yürütücü Java Kitaplığı hakkında bilgi edinmek için aşağıdaki makaleye bakın:

[Java toplu yürütücü kitaplığı SDK 'Sı ve sürüm bilgileri](sql-api-sdk-bulk-executor-java.md)
