---
title: Azure Cosmos kapsayıcıları için küresel olarak dağıtılmış işlem ve analitik depolama
description: Azure Cosmos kapsayıcıları için işlem ve analitik depolama ve yapılandırma seçenekleri hakkında bilgi edinin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 22bb36e3b22f65bbf9922bd31e4b2e041cdb8979
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601227"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Azure Cosmos kapsayıcıları için küresel olarak dağıtılmış işlem ve analitik depolama

Azure Cosmos kapsayıcısı, iki depolama altyapısı tarafından dahili olarak, işlemsel depolama altyapısı ve güncelleştirilebilir analitik depolama altyapısı tarafından desteklenir. Her iki depolama altyapısı da daha hızlı güncelleştirmeler için günlük yapılandırılmış ve yazma için iyileştirilmiştir. Ancak, bunların her biri farklı kodlanır:

* **İşlemsel depolama altyapısı** – hızlı işlem okuma ve sorguları için satır odaklı biçimde kodlanır.

* **Analitik depolama altyapısı** -hızlı analitik sorgular ve taramalar için sütunlu biçimde kodlanır.

![Depolama motorları ve Azure Cosmos DB API eşleme](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

İşlem depolama altyapısı yerel SSD 'Ler tarafından desteklenir, ancak analitik depolama, pahalı olmayan bir SSD depolama alanında depolanır. Aşağıdaki tablo, işlem ve analitik depolama arasındaki önemli farklılıkları yakalar.


|Özellik  |İşlem depolama alanı  |Analitik depolama |
|---------|---------|---------|
|Azure Cosmos kapsayıcısı başına en fazla depolama alanı |   Sınırsız      |    Sınırsız     |
|Mantıksal bölüm anahtarı başına en fazla depolama alanı   |   10 GB      |   Sınırsız      |
|Depolama kodlaması  |   Satır odaklı, iç biçim kullanılarak.   |   Apache Parquet biçimi kullanılarak sütun odaklı. |
|Depolama konumu |   Yerel/küme içi SSD 'Ler tarafından desteklenen çoğaltılmış depolama. |  Yüksek maliyetli uzak/kapalı küme SSD 'Ler tarafından desteklenen çoğaltılmış depolama.       |
|Dayanıklılık  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|Verilere erişen API 'Ler  |   SQL, MongoDB, Cassandra, Gremlin, tablolar ve etcd.       | Apache Spark         |
|Bekletme (yaşam süresi veya TTL)   |  İlke odaklı, `DefaultTimeToLive` özelliği kullanılarak Azure Cosmos kapsayıcısında yapılandırıldı.       |   İlke odaklı, `ColumnStoreTimeToLive` özelliği kullanılarak Azure Cosmos kapsayıcısında yapılandırıldı.      |
|GB başına fiyat    |   [Fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın     |   [Fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın        |
|Depolama işlemlerinin fiyatı    |  [Fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın         |   [Fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>İşlem ve analitik depolamanın avantajları

### <a name="no-etl-operations"></a>ETL işlemi yok

Geleneksel analitik işlem hatları, her biri, bilgi işlem ve Depolama katmanlarına ayıklama-dönüştürme-yükleme (ETL) işlemleri gerektiren birden çok aşamada karmaşıktır. Karmaşık veri işleme mimarilerine neden olur. Bu, birden çok depolama ve işlem aşaması için yüksek maliyetler ve çeşitli depolama ve işlem aşamaları arasında aktarılan büyük hacimli veri nedeniyle yüksek gecikme süresi anlamına gelir.  

Azure Cosmos DB ile ETL işlemleri gerçekleştirme ek yükü yoktur. Her Azure Cosmos kapsayıcısı hem işlem hem de analitik depolama motorları tarafından desteklenir ve işlem ve analitik depolama altyapısı arasındaki veri aktarımı, veritabanı altyapısı içinde ve herhangi bir ağ atlaması olmadan yapılır. Ortaya çıkan gecikme süresi ve maliyet geleneksel analitik çözümlere kıyasla önemli ölçüde düşüktür. Ve hem işlem hem de analitik iş yükleri için tek bir global olarak dağıtılmış depolama sistemi alırsınız.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Birden çok sürümü depolama, analitik depolamayı güncelleştirme ve sorgulama

Analitik depolama tamamen güncelleştirilebilir ve Azure Cosmos kapsayıcısında gerçekleşen tüm işlem güncelleştirmelerinin eksiksiz sürüm geçmişini içerir.

İşlem depolamada yapılan tüm güncelleştirmeler, 30 saniye içinde analitik depolamaya görünür hale getirilir. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Küresel olarak dağıtılmış, çok yöneticili analitik depolama

Azure Cosmos hesabınız tek bir bölge kapsamalıyorsa, kapsayıcılardaki (işlem ve analitik depolamada) depolanan veriler de tek bir bölgeye eklenir. Öte yandan, Azure Cosmos hesabı küresel olarak dağıtılmışsa, kapsayıcılarda depolanan veriler de küresel olarak dağıtılır.

Birden çok yazma bölgesiyle yapılandırılmış Azure Cosmos hesaplarında, kapsayıcıya yazma (hem işlem hem de analitik depolama için) her zaman yerel bölgede gerçekleştirilir ve bu nedenle bunlar hızlıdır.

Tek bir yazma bölgesi (tek yönetici) veya birden fazla yazma bölgesi (çoklu yönetici olarak da bilinir) olursa olsun, hem tek hem de çok bölgeli Azure Cosmos hesapları için, belirtilen bölgede yerel olarak hem işlem hem de analitik okuma/sorgu gerçekleştirilir.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>İşlem ve analitik iş yükleri arasında performans yalıtımı

Belirli bir bölgede, işlem iş yükleri kapsayıcının işlem/satır depolamaya göre çalışır. Diğer yandan, analitik iş yükleri kapsayıcının analitik/sütun depolamaya göre çalışır. İki depolama altyapısı bağımsız olarak çalışır ve iş yükleri arasında sıkı performans yalıtımı sağlar.

İşlem iş yükleri sağlanan üretilen işi (ru) kullanır. İşlem iş yüklerinden farklı olarak, analitik iş yükleri verimlilik gerçek tüketimi temel alır. Analitik iş yükleri, kaynakları isteğe bağlı olarak tüketir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB yaşam süresi](time-to-live.md)
