---
title: Parçalı veritabanlarını sorgula
description: Elastik veritabanı istemci kitaplığını kullanarak parçalar arasında sorguları çalıştırın.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5a0dd12efb9d94bda264b3bd04b05cdc3df917e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92786641"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Elastik veritabanı araçlarını kullanarak çok parçalı sorgulama
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="overview"></a>Genel Bakış

[Elastik veritabanı araçlarıyla](elastic-scale-introduction.md), parçalı veritabanı çözümleri oluşturabilirsiniz. **Çok parçalı sorgulama** , çeşitli parçalar arasında bir sorgu çalıştırmayı gerektiren veri toplama/raporlama gibi görevler için kullanılır. (Bunu, tek parça üzerinde tüm işleri gerçekleştiren [veriye bağımlı yönlendirmeye](elastic-scale-data-dependent-routing.md)tersine getirir.)

1. **Trygetrangeshardmap** [(Java, .net](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap) [),](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap) **Trygetlistshardmap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) veya **Getshardmap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap) [, .net) metodunu](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)kullanarak bir **rangeshardmap** [(Java, .net](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap)) veya **listshardmap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1) [) alın](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1). Bkz. [bir ShardMapManager](elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) oluşturma ve [bir rangeshardmap veya listshardmap 'i edinme](elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. **Multishardconnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)) nesnesi oluşturun.
3. **Multishardor ve MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) oluşturun.
4. **CommandText özelliğini** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) bir T-SQL komutuna ayarlayın.
5. **Executequeryasync veya ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) yöntemini çağırarak komutunu yürütün.
6. **Multishardresultset veya MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)) sınıfını kullanarak sonuçları görüntüleyin.

## <a name="example"></a>Örnek

Aşağıdaki kod, *myshardmap* adlı belirli bir **shardmap** kullanılarak birden çok parçalı sorgulama kullanımını gösterir.

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

Önemli bir fark, çok parçalı bağlantıların yapıtından oluşur. **SqlConnection** tek bir veritabanı üzerinde çalıştığı yerde, **Multishardconnection** , **kendi girişi olarak bir _parça koleksiyonu olan bir koleksiyon_ alır *. Parça haritalarından parçaları koleksiyonunu doldurun. Sorgu daha sonra,*** tek bir genel sonucu birleştirmek için _ UNION ALL semantiğinin kullanıldığı parçalar koleksiyonunda yürütülür. İsteğe bağlı olarak, satırın kaynaklandığı parçanın adı, komutu üzerindeki **Executionoptions** özelliği kullanılarak çıkışa eklenebilir.

**Myshardmap. GetShards ()** çağrısını göz önünde ayırın. Bu yöntem, parça eşlemesinden tüm parçaları alır ve tüm ilgili veritabanlarında Sorgu çalıştırmanın kolay bir yolunu sağlar. Çok parçalı bir sorgu için parçalar koleksiyonu, **Myshardmap. GetShards ()** çağrısından döndürülen koleksiyon ÜZERINDE bir LINQ sorgusu gerçekleştirerek daha fazla olabilir. Kısmi sonuçlar ilkesiyle birlikte, çok parçalı sorgulamada geçerli yetenek, yüzlerce parçaya kadar iyi çalışmak üzere tasarlanmıştır.

Çok bölgeli sorgulama sınırlaması Şu anda sorgulanan parçalar ve parçalar için doğrulama yetersizliğidir. Verilere bağımlı yönlendirme, belirli bir parçanın, sorgulama sırasında parça eşlemenin bir parçası olduğunu doğruladığında, çok parçalı sorgular bu denetimi gerçekleştirmez. Bu, parça eşlemesinden kaldırılmış veritabanlarında çalışan çok parçalı sorgulara yol açabilir.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Çok parçalı sorgular ve bölünmüş birleştirme işlemleri

Çok parçalı sorgular, sorgulanan veritabanının parçalanmasını devam eden bölünmüş birleştirme işlemlerine katılımını doğrulamaz. (Bkz. [elastik veritabanı bölünmüş birleştirme aracını kullanarak ölçeklendirme](elastic-scale-overview-split-and-merge.md).) Bu, aynı parçadaki satırların aynı çok parçalı sorgudaki birden çok veritabanı için gösterileceği tutarsızlıklara yol açabilir. Bu sınırlamaların farkında olun ve çok parçalı sorgular gerçekleştirirken parça haritasında devam eden bölünmüş birleştirme işlemlerini ve değişiklikleri bırakmayı düşünün.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]