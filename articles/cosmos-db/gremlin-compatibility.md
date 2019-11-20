---
title: Inkerpop özellikleriyle Gremlin uyumluluğu Azure Cosmos DB
description: Başvuru belgeleri grafik Altyapısı uyumluluk sorunları
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 0ed5824859b8463919a809861993f9f98a4f9251
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327026"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin uyumluluğu
Azure Cosmos DB Graph Engine, [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) geçiş adımları belirtimini yakından izler, ancak farklılıklar vardır.

## <a name="behavior-differences"></a>Davranış farklılıkları

* Azure Cosmos DB Graph Engine, ınkerpop Gremlin 'ın derinliği ilk kez olacak şekilde, ***ilk*** çapraz geçişi çalıştırır. Bu davranış, Cosmos DB benzer şekilde yatay ölçeklenebilir sistemde daha iyi performans elde eder. 

## <a name="unsupported-features"></a>Desteklenmeyen özellikler

* ***[Gremlin Bytecode](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** graf dolaşma işlemlerine yönelik programlama dilinden bağımsız bir belirtimdir. Cosmos DB Graph henüz desteklemiyor. @No__t-0 kullanın ve çapraz geçişi metin dizesi olarak geçirin.

* ***```property(set, 'xyz', 1)```*** kümesi kardinalitesi bugün desteklenmez. Bunun yerine ```property(list, 'xyz', 1)``` kullanın.

* ***```match()```*** , bildirime dayalı model eşleştirmeyi kullanarak grafiklerin sorgulanmasına olanak tanır. Bu özellik kullanılamıyor.

* Köşelerin veya kenarlardaki ***Özellikler olarak nesneler*** desteklenmez. Özellikler yalnızca temel türler veya diziler olabilir.

* @No__t-1 ***dizi özelliklerine göre sıralama*** desteklenmez. Yalnızca temel türlere göre sıralama desteklenir.

* ***Ilkel olmayan JSON türleri*** desteklenmez. @No__t-0, ```number``` veya ```true``` @ no__t-3 @ no__t-4 türlerini kullanın. ```null``` değerleri desteklenmez. 

* ***GraphSONv3*** seri hale getirici bugün kullanılamıyor.

* Sistemin dağıtılmış doğası nedeniyle ***işlemler*** desteklenmiyor.  Gremlin hesabında uygun tutarlılık modelini "kendi yazınızla okumak" üzere yapılandırın ve çakışan yazmaları çözümlemek için iyimser eşzamanlılık kullanın.

## <a name="next-steps"></a>Sonraki adımlar
* Geri bildirim paylaşmak ve takımınızın sizin için önemli olan özelliklere odaklanmasına yardımcı olmak için [Cosmos DB Kullanıcı sesi](https://feedback.azure.com/forums/263030-azure-cosmos-db) sayfasını ziyaret edin.
