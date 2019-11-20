---
title: Azure Cosmos DB okuma ve yazma maliyetini en iyi duruma getirme
description: Bu makalede, veriler üzerinde okuma ve yazma işlemleri gerçekleştirirken Azure Cosmos DB maliyetlerinin nasıl azaltılacağı açıklanmaktadır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 934853b80c6e6377923df4c2b5cce7b7d7d57d7c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754924"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Azure Cosmos DB 'de okuma ve yazma maliyetlerini iyileştirin

Bu makalede Azure Cosmos DB verileri okumak ve yazmak için gereken maliyetin nasıl hesaplandığı açıklanır. Öğelerin ve yazma işlemlerinde Get işlemlerine yönelik okuma işlemleri, öğelerin INSERT, Replace, DELETE ve upsert öğelerini içerir.  

## <a name="cost-of-reads-and-writes"></a>Okuma ve yazma maliyeti

Azure Cosmos DB, sağlanan aktarım hızı modeli kullanılarak üretilen iş ve gecikme süresi bakımından öngörülebilir performansı güvence altına alır. Sağlanan aktarım hızı, saniye başına [Istek birimi](request-units.md) veya ru/sn cinsinden temsil edilir. Istek birimi (RU), bir isteği gerçekleştirmek için gerekli olan CPU, bellek, GÇ vb. işlem kaynakları üzerinde mantıksal bir soyutlamadır. Sağlanan aktarım hızı (ru), öngörülebilir aktarım hızı ve gecikme süresi sağlamak için kapsayıcı veya veritabanınıza ayrılır. Sağlanan aktarım hızı, Azure Cosmos DB her ölçekte öngörülebilir ve tutarlı performans, garantili düşük gecikme süresi ve yüksek kullanılabilirlik sağlamasına olanak sağlar. İstek birimleri, bir uygulamanın ihtiyacı olan kaynak sayısını kolaylaştıran normalleştirilmiş para birimini temsil eder. 

Okuma ve yazma işlemleri arasında istek birimlerini farklılaştırmayı düşünmek zorunda değilsiniz. İstek birimlerinin Birleşik para birimi modeli, birbirinin yerine, hem okuma hem de yazma işlemleri için aynı verimlilik kapasitesini kullanır. Aşağıdaki tabloda, 1 KB ve 100 KB boyutundaki öğeler için RU/s bakımından okuma ve yazma maliyeti gösterilmektedir.

|**Öğe boyutu**  |**Bir okuma maliyeti** |**Bir yazma maliyeti**|
|---------|---------|---------|
|1 KB |1 RU |5 ru |
|100 KB |10 RU |50 ru |

Boyut maliyetlerinde 1 KB olan bir öğeyi okumak bir RU. 1 KB 'lik maliyette beş ru olan bir öğe yazılıyor. Okuma ve yazma maliyetleri, varsayılan oturum [tutarlılığı düzeyi](consistency-levels.md)kullanılırken geçerlidir.  Ru 'daki hususlar şunlardır: öğe boyutu, özellik sayısı, veri tutarlılığı, dizinli özellikler, dizin oluşturma ve sorgu desenleri.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>1\.000.000 okuma ve yazma işlemleri için normalleştirilmiş maliyet

1\.000 RU/s sağlama, 3.600.000 RU/Hour 'a çevrilir ve saat (ABD ve Avrupa) için maliyet $0,08. 1 KB 'lik bir öğe için, bu sağlanan verimlilik ile 3.600.000 okuma veya 720.000 yazma işlemleri yapabilirsiniz (Bu değer şu şekilde hesaplanır: `3.6 million RU / 5`). Milyon okuma ve yazma işlemleri için, maliyet 1.000.000 okuma için $0,022 (Bu değer: $0.08/3.6 milyon) ve 1.000.000 yazmaları için $0,111 (Bu değer şöyle hesaplanır: $0.08/0,72 milyon).

## <a name="number-of-regions-and-the-request-units-cost"></a>Bölge sayısı ve istek birimleri maliyeti

Yazma maliyeti, Azure Cosmos hesabıyla ilişkilendirilen bölge sayısından bağımsız olarak sabittir. Diğer bir deyişle, bir 1 KB yazma, hesap ile ilişkili bölge sayısından bağımsız olarak beş ru 'a kadar ücretlendirilir. Her bölgedeki çoğaltma trafiğini çoğaltmak, kabul etmek ve işlemek için harcanan önemsiz olmayan miktarda kaynak vardır. Çok bölgeli maliyet iyileştirmesi hakkında daha fazla bilgi için bkz. [çok bölgeli Cosmos hesaplarının maliyetini En Iyi duruma getirme](optimize-cost-regions.md) makalesi.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Yazma ve okuma maliyetlerini iyileştirin

Yazma işlemleri gerçekleştirdiğinizde, saniye başına gereken yazma sayısını desteklemek için yeterli kapasite sağlamanız gerekir. Yazma işlemini gerçekleştirmeden önce SDK, Portal, CLı kullanarak sağlanan aktarım hızını artırabilir ve ardından yazma işlemleri tamamlandıktan sonra aktarım hızını azaltabilirsiniz. Yazma dönemi için üretilen iş hacmi, belirtilen veriler için gereken en düşük aktarım hızı ve başka iş yükünün çalışmadığı varsayılarak ekleme iş yükü için gereken aktarım hızı olur. 

Diğer iş yüklerini eşzamanlı olarak çalıştırıyorsanız (örneğin, sorgu/okuma/güncelleştirme/silme), bu işlemler için gereken ek istek birimlerini de eklemeniz gerekir. Yazma işlemleri hız sınırlıysa, Azure Cosmos DB SDK 'Ları kullanarak yeniden deneme/geri alma ilkesini özelleştirebilirsiniz. Örneğin, küçük bir isteklerin hız sınırlı olana kadar yükü artırabilirsiniz. Oran sınırı oluşursa, istemci uygulama, belirtilen yeniden deneme aralığı için hız sınırlama isteklerinde yeniden kapatılmalıdır. Yazmaları yeniden denemeden önce, denemeler arasındaki en az bir zaman aralığı miktarına sahip olmalısınız. Yeniden deneme ilkesi desteği, SQL .NET, Java, Node. js ve Python SDK 'larına ve .NET Core SDK 'larının tüm desteklenen sürümlerine dahildir. 

Ayrıca, [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)kullanarak desteklenen kaynak veri mağazalarından Azure Cosmos DB Azure Cosmos DB veya verileri toplu olarak ekleyebilirsiniz. Azure Data Factory, veri yazarken en iyi performansı sağlamak için Azure Cosmos DB toplu API ile yerel olarak tümleştirilir.

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, aşağıdaki makalelerle Azure Cosmos DB maliyet iyileştirmesi hakkında daha fazla bilgi edinebilirsiniz:

* [Geliştirme ve test Için iyileştirme](optimize-dev-test.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB Faturanızı Anlama](understand-your-bill.md) hakkında daha fazla bilgi edinin
* [Verimlilik maliyetini iyileştirme](optimize-cost-throughput.md) hakkında daha fazla bilgi edinin
* [Depolama maliyetini iyileştirme](optimize-cost-storage.md) hakkında daha fazla bilgi edinin
* [Sorguların maliyetini En Iyi duruma getirme](optimize-cost-queries.md) hakkında daha fazla bilgi edinin
* [Çok bölgeli Azure Cosmos hesaplarının maliyetini En Iyi duruma getirme](optimize-cost-regions.md) hakkında daha fazla bilgi edinin
