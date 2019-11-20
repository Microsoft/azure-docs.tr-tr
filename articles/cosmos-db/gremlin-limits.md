---
title: Azure Cosmos DB Gremlin sınırları
description: Graph Engine çalışma zamanı sınırlamaları için başvuru belgeleri
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029858"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin limitleri
Bu makale, Azure Cosmos DB Gremlin motorunun sınırlarını ve müşteri traversals nasıl etkileyebileceğini açıklar.

Cosmos DB Gremlin, Cosmos DB altyapısının üzerine kurulmuştur. Bu nedenle, [Azure Cosmos DB hizmet limitlerinde](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) açıklanan tüm sınırlar hala geçerlidir. 

## <a name="limits"></a>Sınırlar

Gremlin sınırına ulaşıldığında, bir azaltma hatasını gösteren bir **x-MS-Status-code** 429 ile çapraz geçiş iptal edilir. Daha fazla bilgi için bkz. [Gremlin sunucu yanıt üst bilgileri](gremlin-limits.md) .

**Kaynak**    | **Varsayılan sınır** | **Açıklama**
--- | --- | ---
*Betik uzunluğu* | **64 KB** | İstek başına Gremlin dolaşma betiği uzunluğu üst sınırı.
*İşleç derinliği* | **400** |  Dolaşmadaki benzersiz adımların toplam sayısı. Örneğin, ```g.V().out()``` ' ın operatör sayısı 2: V () ve out (), ```g.V('label').repeat(out()).times(100)``` ' in 3: V (), Repeat () ve out () işleç derinliği vardır çünkü ```.times(100)``` ```.repeat()``` işlecine bir parametredir.
*Paralellik derecesi* | **32** | Depolama katmanına yönelik tek bir istekte sorgulanan depolama bölümü sayısı üst sınırı. Yüzlerce bölümden oluşan grafikler bu sınıra göre etkilenecektir.
*Yineleme sınırı* | **32** | Bir ```.repeat()``` işleci yürütmesi için yineleme sayısı üst sınırı. Çoğu durumda ```.repeat()``` adımındaki her yineleme, ilk çapraz geçişi çalıştırır, bu da herhangi bir geçişinin köşeler arasında en çok 32 sıçrama ile sınırlı olduğu anlamına gelir.
*Dolaşma zaman aşımı* | **30 saniye** | Bu süre geçtiğinde çapraz geçiş iptal edilecek. Cosmos DB Graph, dolaşmaların büyük çoğunluğunun milisaniyeler içinde tamamlandığı bir OLTP veritabanıdır. Cosmos DB Graph üzerinde OLAP sorguları çalıştırmak için [grafik veri çerçeveleri](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) ve [Cosmos DB Spark Bağlayıcısı](https://github.com/Azure/azure-cosmosdb-spark)ile [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) kullanın.
*Boştaki bağlantı zaman aşımı* | **1 saat** | Gremlin hizmetinin boş WebSocket bağlantılarını açık tutamayacağı zaman miktarı. TCP etkin tut paketleri veya HTTP etkin tut istekleri bu sınırın ötesinde bağlantı ömrü genişletmez. Cosmos DB Graph Engine, üzerinde çalışan etkin bir Gremlin isteği yoksa, WebSocket bağlantılarını boşta olarak değerlendirir.
*Kaynak belirteci/saat* | **100** | Gremlin istemcileri tarafından bölgedeki Gremlin hesabına bağlanmak için kullanılan benzersiz kaynak belirteçlerinin sayısı. Uygulama saatlik benzersiz belirteç sınırını aştığında, sonraki kimlik doğrulama isteğinde `"Exceeded allowed resource token limit of 100 that can be used concurrently"` döndürülür.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Cosmos DB Gremlin yanıt üst bilgileri](gremlin-headers.md) 
* [Gremlin ile kaynak belirteçleri Azure Cosmos DB](how-to-use-resource-tokens-gremlin.md)
