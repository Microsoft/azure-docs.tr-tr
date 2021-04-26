---
title: Azure Cosmos DB sayfalandırma
description: Sayfalama kavramları ve devamlılık belirteçleri hakkında bilgi edinin
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: d47a7b9e2229ffbd747b1ff7b9491ce99e9b190f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492243"
---
# <a name="pagination-in-azure-cosmos-db"></a>Azure Cosmos DB sayfalandırma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB, sorgularda birden fazla sonuç sayfası olabilir. Bu belgede sorgu sonuçlarının birden çok sayfaya bölünmeyeceğine karar vermek için Azure Cosmos DB sorgu altyapısının kullandığı ölçütler açıklanmaktadır. İsteğe bağlı olarak, birden çok sayfayı kapsayan sorgu sonuçlarını yönetmek için devamlılık belirteçlerini kullanabilirsiniz.

## <a name="understanding-query-executions"></a>Sorgu yürütmelerini anlama

Bazen sorgu sonuçları birden çok sayfaya bölünecektir. Her sayfanın sonuçları ayrı bir sorgu yürütmesi tarafından oluşturulur. Sorgu sonuçları tek bir yürütmede döndürülmediğinde, Azure Cosmos DB sonuçları otomatik olarak birden çok sayfaya bölecektir.

Bir sorgu tarafından döndürülen en fazla öğe sayısını ayarlayarak belirtebilirsiniz `MaxItemCount` . , `MaxItemCount` İstek başına belirtilir ve sorgu altyapısının bu öğe sayısını veya daha az sayıda dönemeyeceğini söyler. `MaxItemCount` `-1` Sorgu yürütme başına sonuç sayısı için bir sınır koymak istemiyorsanız, öğesini olarak ayarlayabilirsiniz.

Ayrıca, sorgu altyapısının sorgu sonuçlarını birden çok sayfaya bölünmesi gerekebilecek başka nedenler de vardır. Bu modüller şunlardır:

- Kapsayıcı daraltıldı ve daha fazla sorgu sonucu döndürecek RUs kullanılamıyor
- Sorgu yürütmesinin yanıtı çok büyüktü
- Sorgu yürütme zamanı çok uzun
- Sorgu altyapısının ek yürütmeler ile sonuçları döndürmesi daha verimlidir

Sorgu yürütmesi başına döndürülen öğe sayısı her zaman en az veya eşit olur `MaxItemCount` . Ancak, diğer ölçütlerde sorgunun döndürebilecek sonuç sayısı sınırlı olabilir. Aynı sorguyu birden çok kez çalıştırırsanız, sayfa sayısı sabit olmayabilir. Örneğin, bir sorgu kısıtlanmıyorsa, sayfa başına daha az kullanılabilir sonuç olabilir, bu da sorgunun ek sayfalara sahip olacağı anlamına gelir. Bazı durumlarda, sorgunuzun boş bir sonuç sayfası döndürebilmesini de mümkün olabilir.

## <a name="handling-multiple-pages-of-results"></a>Birden çok sayfa sonucunu işleme

Doğru sorgu sonuçları sağlamak için tüm sayfalarda ilerlemeniz gerekir. Ek sayfa kalmayana kadar sorguları yürütmeye devam etmelisiniz.

Birden çok sayfalı sorgulardan sonuçları işlemek için bazı örnekler şunlardır:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Node.js SDK’sı](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Python SDK'sı](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>Devamlılık belirteçleri

.NET SDK ve Java SDK 'sında, isteğe bağlı olarak, sorgu ilerleme durumu için devam belirteçlerini bir yer işareti olarak kullanabilirsiniz. Azure Cosmos DB sorgu yürütmeleri sunucu tarafında durum bilgisiz olur ve devamlılık belirteci kullanılarak herhangi bir zamanda devam edebilir. Devamlılık belirteçleri Node.js SDK 'da desteklenmez. Python SDK 'Sı için, tek bölüm sorguları için desteklenir ve sorgunun kendisi içinde olması yeterli olmadığından, Seçenekler nesnesinde PK belirtilmesi gerekir.

Devamlılık belirteçlerini kullanmak için bazı örnekler şunlardır:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)
- [Python SDK'sı](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/test/test_query.py#L533)

Sorgu bir devamlılık belirteci döndürürse ek sorgu sonuçları vardır.

Azure Cosmos DB REST API, üst bilgiyle devamlılık belirteçlerini yönetebilirsiniz `x-ms-continuation` . .NET veya Java SDK ile sorgulama yaparken, `x-ms-continuation` yanıt üst bilgisi boş değilse, sorgunun ek sonuçlara sahip olduğu anlamına gelir.

Aynı SDK sürümünü kullandığınız sürece, devamlılık belirteçleri hiçbir zaman sona ermez. [Bir devamlılık belirtecinin boyutunu](/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb)isteğe bağlı olarak kısıtlayabilirsiniz. Sorgularınızdaki veri miktarı veya fiziksel bölüm sayısı ne olursa olsun sorgular tek bir devamlılık belirteci döndürür.

Bu sorgular önemli miktarda durum depolamayı gerektirdiğinden [Group By](sql-query-group-by.md) veya [DISTINCT](sql-query-keywords.md#distinct) içeren sorgular için devamlılık belirteçlerini kullanamazsınız. İle sorgular için `DISTINCT` , sorguya eklerseniz devamlılık belirteçlerini kullanabilirsiniz `ORDER BY` .

İşte `DISTINCT` bir devamlılık belirteci kullanan bir sorgu örneği:

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB'ye giriş](introduction.md)
- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY yan tümcesi](sql-query-order-by.md)
