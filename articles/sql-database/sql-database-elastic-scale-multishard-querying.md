---
title: Parçalı veritabanlarını sorgula
description: Elastik veritabanı istemci kitaplığını kullanarak parçalar arasında sorguları çalıştırın.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 6458b52e707b7e4c11fe8b501f3393e1009a748c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823548"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Elastik veritabanı araçlarını kullanarak çok parçalı sorgulama

## <a name="overview"></a>Genel Bakış

[Elastik veritabanı araçlarıyla](sql-database-elastic-scale-introduction.md), parçalı veritabanı çözümleri oluşturabilirsiniz. **Çok parçalı sorgulama** , çeşitli parçalar arasında bir sorgu çalıştırmayı gerektiren veri toplama/raporlama gibi görevler için kullanılır. (Bunu, tek parça üzerinde tüm işleri gerçekleştiren [veriye bağımlı yönlendirmeye](sql-database-elastic-scale-data-dependent-routing.md)tersine getirir.)

1. **Trygetrangeshardmap** [(Java, .net](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap)), **Trygetlistshardmap** [](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap) [](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)[](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap)[](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap) [](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap) [](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) [(Java, .net) kullanarak bir rangeshardmap (Java, .net) veya listshardmap (Java, .net) alın. .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) veya **Getshardmap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) yöntemi. Bkz. [bir ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) oluşturma ve [bir rangeshardmap veya listshardmap 'i edinme](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. **Multishardconnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)) nesnesi oluşturun.
3. **Multishardor ve MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) oluşturun.
4. **CommandText özelliğini** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) bir T-SQL komutuna ayarlayın.
5. **Executequeryasync veya ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) yöntemini çağırarak komutunu yürütün.
6. **Multishardresultset veya MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)) sınıfını kullanarak sonuçları görüntüleyin.

## <a name="example"></a>Örnek

Aşağıdaki kod, *myshardmap*adlı belirli bir **shardmap** kullanılarak birden çok parçalı sorgulama kullanımını gösterir.

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

Önemli bir fark, çok parçalı bağlantıların yapıtından oluşur. **SqlConnection** tek bir veritabanı üzerinde çalışırken, **Multikıardconnection** , kendi girişi olarak bir ***parça koleksiyonu*** alır. Parça haritalarından parçaları koleksiyonunu doldurun. Daha sonra sorgu, tek bir genel sonucu birleştirmek için **UNıON All** semantiğinin kullanıldığı parçalar koleksiyonunda yürütülür. İsteğe bağlı olarak, satırın kaynaklandığı parçanın adı, komutu üzerindeki **Executionoptions** özelliği kullanılarak çıkışa eklenebilir.

**Myshardmap. GetShards ()** çağrısını göz önünde ayırın. Bu yöntem, parça eşlemesinden tüm parçaları alır ve tüm ilgili veritabanlarında Sorgu çalıştırmanın kolay bir yolunu sağlar. Çok parçalı bir sorgu için parçalar koleksiyonu, **Myshardmap. GetShards ()** çağrısından döndürülen koleksiyon ÜZERINDE bir LINQ sorgusu gerçekleştirerek daha fazla olabilir. Kısmi sonuçlar ilkesiyle birlikte, çok parçalı sorgulamada geçerli yetenek, yüzlerce parçaya kadar iyi çalışmak üzere tasarlanmıştır.

Çok bölgeli sorgulama sınırlaması Şu anda sorgulanan parçalar ve parçalar için doğrulama yetersizliğidir. Verilere bağımlı yönlendirme, belirli bir parçanın, sorgulama sırasında parça eşlemenin bir parçası olduğunu doğruladığında, çok parçalı sorgular bu denetimi gerçekleştirmez. Bu, parça eşlemesinden kaldırılmış veritabanlarında çalışan çok parçalı sorgulara yol açabilir.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Çok parçalı sorgular ve bölünmüş birleştirme işlemleri

Çok parçalı sorgular, sorgulanan veritabanının parçalanmasını devam eden bölünmüş birleştirme işlemlerine katılımını doğrulamaz. (Bkz. [elastik veritabanı bölünmüş birleştirme aracını kullanarak ölçeklendirme](sql-database-elastic-scale-overview-split-and-merge.md).) Bu, aynı parçadaki satırların aynı çok parçalı sorgudaki birden çok veritabanı için gösterileceği tutarsızlıklara yol açabilir. Bu sınırlamaların farkında olun ve çok parçalı sorgular gerçekleştirirken parça haritasında devam eden bölünmüş birleştirme işlemlerini ve değişiklikleri bırakmayı düşünün.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]