---
title: Toplu yürütücü kitaplığı kullanarak Azure Cosmos DB verileri toplu içe aktarma ve güncelleştirme
description: Toplu yürütücü kitaplığı tarafından sunulan toplu içeri aktarma ve toplu güncelleştirme API 'Leri aracılığıyla Azure Cosmos DB toplu işlemler gerçekleştirin.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 8d2cb30e0f18485d500344e571b3ac5f2cde008f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615700"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure Cosmos DB toplu yürütücü kitaplığına genel bakış
 
Azure Cosmos DB hızlı, esnek ve küresel ölçekte dağıtılan bir veritabanı hizmeti olarak, esnek bir şekilde ölçeği genişleterek şunları desteklemek için tasarlanmıştır: 

* Büyük okuma ve yazma işlem hızı (saniyede milyonlarca işlem).  
* Öngörülebilir milisaniye düzeyinde bir gecikme süresiyle çok büyük (yüzlerce terabayt, hatta daha fazla) işlem verilerini depolama.  

Toplu yürütücü kitaplığı bu muazzam işlem hızı ve depolamadan yararlanmanıza yardımcı olur. Toplu yürütücü kitaplığı toplu içeri aktarma ve toplu güncelleştirme API'leri üzerinden Azure Cosmos DB'de toplu işlemler yapmanızı sağlar. Aşağıdaki bölümlerde toplu yürütücü kitaplığının özellikleri hakkında verilen bilgileri okuyabilirsiniz. 

> [!NOTE] 
> Şu anda, toplu yürütücü kitaplığı içeri aktarma ve güncelleştirme işlemlerini destekler ve bu kitaplık yalnızca Azure Cosmos DB SQL API ve Gremlin API hesapları tarafından desteklenir.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Toplu yürütücü kitaplığı 'nın temel özellikleri  
 
* Bir kapsayıcıya ayrılan üretilen işi Cumartesi için gereken istemci tarafı işlem kaynaklarını önemli ölçüde azaltır. Toplu içeri aktarma API 'sini kullanarak veri yazan tek bir iş parçacıklı uygulama, verileri paralel olarak yazan çok iş parçacıklı bir uygulamayla karşılaştırıldığında, istemci makinenin CPU 'SU Cumartesi sırasında 10 kat daha fazla yazma işi elde eder.  

* İsteğin hız sınırlandırma, istek zaman aşımları ve diğer geçici özel durumlar için kitaplık içinde verimli bir şekilde işlenerek uygulama mantığı yazma sıkıcı görevlerinin üstesinden gelir.  

* Ölçeği genişletmek için toplu işlemler gerçekleştiren uygulamalar için basitleştirilmiş bir mekanizma sağlar. Azure VM 'de çalışan tek bir toplu yürütücü örneği, 500 k RU/sn 'den büyük bir ücret tüketebilir ve tek tek istemci VM 'lerine ek örnekler ekleyerek daha yüksek bir aktarım hızı elde edebilirsiniz.  
 
* Ölçek Genişletme mimarisi kullanarak bir saat içinde birkaç terabaytlık verileri toplu olarak içeri aktarabilir.  

* Azure Cosmos kapsayıcılarındaki mevcut verileri düzeltme eki olarak toplu güncelleştirebilir. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Toplu yürütücü nasıl çalışır? 

Belgeleri içeri veya dışarı aktarmaya yönelik toplu bir işlem, bir dizi varlıkla tetiklendiğinde, başlangıçta Azure Cosmos DB bölüm anahtarı aralığına karşılık gelen demetlere bölünmüştür. Bölüm anahtar aralığına karşılık gelen her bir demet içinde, bunlar mini toplu işlemlere bölünür ve her mini yığın sunucu tarafında yürütülen bir yük olarak davranır. Toplu yürütücü kitaplığı, bu mini işlerin içinde ve bölüm anahtarı aralıklarında aynı anda yürütülmesi için iyileştirmelere sahiptir. Aşağıdaki görüntüde toplu yürütücü verilerinin farklı bölüm anahtarlarına nasıl toplu olarak alınacağını gösterir:  

![Toplu yürütücü mimarisi](./media/bulk-executor-overview/bulk-executor-architecture.png)

Toplu yürütücü kitaplığı, bir koleksiyona ayrılan üretilen işi büyük ölçüde kullanmaya özen açar. Hız sınırlandırma ve zaman aşımlarını verimli bir şekilde işlemek için her bir Azure Cosmos DB bölüm anahtarı aralığı için bir [Aimd stili tıkanıklık denetim mekanizması](https://tools.ietf.org/html/rfc5681) kullanır. 

## <a name="next-steps"></a>Sonraki Adımlar 
  
* [.Net](bulk-executor-dot-net.md) ve [Java](bulk-executor-java.md)'daki toplu yürütücü kitaplığını kullanan örnek uygulamaları deneyerek daha fazla bilgi edinin.  
* [.Net](sql-api-sdk-bulk-executor-dot-net.md) ve [Java](sql-api-sdk-bulk-executor-java.md)'daki toplu yürütücü SDK bilgilerine ve sürüm notlarına göz atın.
* Toplu yürütücü kitaplığı, Cosmos DB Spark Bağlayıcısı ile tümleşiktir, daha fazla bilgi edinmek için bkz. [Azure Cosmos DB Spark Bağlayıcısı](spark-connector.md) makalesi.  
* Toplu yürütücü kitaplığı, verileri kopyalamak için Azure Data Factory [Azure Cosmos DB bağlayıcısının](https://aka.ms/bulkexecutor-adf-v2) yeni bir sürümüyle tümleşiktir.
