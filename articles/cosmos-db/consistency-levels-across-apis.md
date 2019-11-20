---
title: Tutarlılık düzeyleri ve Azure Cosmos DB API’leri
description: Azure Cosmos DB 'teki API 'lerde tutarlılık düzeylerini anlama.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 6779e434339935b64440d7cf617c8946338e36e3
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756991"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Tutarlılık düzeyleri ve Azure Cosmos DB API’leri

Azure Cosmos DB popüler veritabanları için kablolu protokol ile uyumlu API 'Ler için yerel destek sağlar. Bunlar MongoDB, Apache Cassandra, Gremlin ve Azure Tablo depolama 'yı içerir. Bu veritabanları, tutarlılık düzeyleri için tam olarak tanımlanmış tutarlılık modelleri veya SLA ile desteklenen garantiler sunmaz. Genellikle, Azure Cosmos DB tarafından sunulan beş tutarlılık modelinin yalnızca bir alt kümesini sağlar. 

SQL API, Gremlin API ve Tablo API'si kullanıldığında, Azure Cosmos hesabında yapılandırılan varsayılan tutarlılık düzeyi kullanılır. 

MongoDB için Cassandra API veya Azure Cosmos DB API 'sini kullanırken, uygulamalar, sırasıyla Apache Cassandra ve MongoDB tarafından sunulan bir tutarlılık düzeyi kümesini, hatta daha güçlü tutarlılık ve dayanıklılık garantisi elde eder. Bu belgede Apache Cassandra ve MongoDB tutarlılık düzeyleri için karşılık gelen Azure Cosmos DB tutarlılığı düzeyleri gösterilmektedir.


## <a id="cassandra-mapping"></a>Apache Cassandra ve Azure Cosmos DB tutarlılık düzeyleri arasında eşleme

Azu, SmoS DB 'nin aksine Apache Cassandra kesin olarak tanımlanmış tutarlılık garantisi sağlamaz.  Bunun yerine Apache Cassandra, yüksek kullanılabilirlik, tutarlılık ve gecikme süresi avantajları sağlamak için bir yazma tutarlılığı düzeyi ve bir okuma tutarlılığı düzeyi sağlar. Azure Cosmos DB Cassandra API kullanırken: 

* Apache Cassandra 'ın yazma tutarlılığı düzeyi, Azure Cosmos hesabınızda yapılandırılmış varsayılan tutarlılık düzeyiyle eşleştirilir. 

* Azure Cosmos DB, Cassandra istemci sürücüsüyle belirtilen okuma tutarlılığı düzeyini, bir okuma isteği üzerinde dinamik olarak yapılandırılmış Azure Cosmos DB tutarlılık düzeylerinden birine dinamik olarak eşler. 

Aşağıdaki tabloda, Cassandra API kullanılırken yerel Cassandra tutarlılık düzeylerinin Azure Cosmos DB tutarlılık düzeylerine nasıl eşlendiği gösterilmektedir:  

[Cassandra tutarlılık modeli eşlemesi![](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>MongoDB ve Azure Cosmos DB tutarlılık düzeyleri arasında eşleme

Azure Cosmos DB aksine, yerel MongoDB kesin olarak tanımlanmış tutarlılık garantisi sağlamaz. Bunun yerine, yerel MongoDB kullanıcıların, istenen tutarlılık düzeyini elde etmek için, okuma işlemlerini birincil veya ikincil çoğaltmalara yönlendirecek bir yazma sorunu, bir okuma sorunu ve isMaster yönergesi sağlar. 

MongoDB için Azure Cosmos DB API 'SI kullanılırken MongoDB sürücüsü, yazma bölgenizi birincil çoğaltma olarak değerlendirir ve diğer tüm bölgeler okuma çoğaltmasıdır. Azure Cosmos hesabınızla ilişkilendirilen bölgeyi birincil çoğaltma olarak seçebilirsiniz. 

Azure Cosmos DB, MongoDB için API 'sini kullanırken:

* Yazma sorunu, Azure Cosmos hesabınızda yapılandırılmış varsayılan tutarlılık düzeyiyle eşleştirilir.
 
* Azure Cosmos DB, MongoDB istemci sürücüsüyle belirtilen okuma kaygısını, bir okuma isteği üzerinde dinamik olarak yapılandırılmış Azure Cosmos DB tutarlılık düzeylerinden birine dinamik olarak eşler. 

* Bölgeyi ilk yazılabilir bölge olarak yaparak Azure Cosmos hesabınızla ilişkili belirli bir bölgeye "ana" olarak ek açıklama ekleyebilirsiniz. 

Aşağıdaki tabloda, MongoDB için Azure Cosmos DB API 'SI kullanılırken yerel MongoDB yazma/okuma sorunlarının Azure Cosmos tutarlılık düzeylerine nasıl eşlendiği gösterilmektedir:

[![MongoDB tutarlılık modeli eşlemesi](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

Açık kaynaklı API 'lerle Azure Cosmos DB API 'Ler arasındaki tutarlılık düzeyleri ve uyumluluk hakkında daha fazla bilgi edinin. Aşağıdaki makalelere bakın:

* [Çeşitli tutarlılık düzeyleri için kullanılabilirlik ve performans avantajları](consistency-levels-tradeoffs.md)
* [MongoDB için Azure Cosmos DB API 'sinin desteklediği MongoDB özellikleri](mongodb-feature-support.md)
* [Azure Cosmos DB tarafından desteklenen Apache Cassandra özellikleri Cassandra API](cassandra-support.md)