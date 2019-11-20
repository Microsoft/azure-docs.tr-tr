---
title: Elastik havuz kaynaklarını ölçeklendirme
description: Bu sayfada, Azure SQL veritabanı 'nda elastik havuzlara yönelik ölçek kaynakları açıklanmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 3/14/2019
ms.openlocfilehash: ed67a21107f6a7d90341ae40feeb817671785778
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823807"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Azure SQL veritabanı 'nda elastik havuz kaynaklarını ölçeklendirme

Bu makalede, Azure SQL veritabanı 'nda esnek havuzlar ve havuza alınmış veritabanları için kullanılabilen işlem ve depolama kaynaklarının nasıl ölçeklenmesi açıklanmaktadır.

## <a name="change-compute-resources-vcores-or-dtus"></a>İşlem kaynaklarını değiştirme (sanal çekirdekler veya DTU 'Lar)

Sanal çekirdekler veya eDTU sayısını ilk kez seçtikten sonra, [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [POWERSHELL](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)veya [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)kullanarak gerçek deneyime bağlı olarak elastik bir havuzun ölçeğini dinamik olarak veya azaltabilirsiniz.

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Hizmet katmanını değiştirmenin veya işlem boyutunun yeniden boyutlandırmasından etkileri

Elastik havuzun hizmet katmanını veya işlem boyutunu değiştirmek, tek veritabanları için benzer bir model izler ve genellikle aşağıdaki adımları gerçekleştirerek hizmeti içerir:

1. Elastik havuz için yeni işlem örneği oluştur  

    Elastik havuz için yeni bir işlem örneği, istenen hizmet katmanı ve işlem boyutu ile oluşturulur. Hizmet katmanı ve işlem boyutu değişikliklerinin bazı birleşimleri için, her bir veritabanının bir kopyasının yeni işlem örneğinde oluşturulması gerekir ve bu da verilerin kopyalanmasını içerir ve genel gecikmeyi kesin bir şekilde etkileyebilir. Ne olursa olsun, bu adım sırasında veritabanları çevrimiçi kalır ve bağlantılar orijinal işlem örneğindeki veritabanlarına yönlendirilmeye devam eder.

2. Bağlantıların yeni işlem örneğine yönlendirilmesini Değiştir

    Özgün işlem örneğindeki veritabanlarına yönelik mevcut bağlantılar bırakılır. Yeni işlem örneğindeki veritabanlarına yeni bağlantı oluşturulur. Hizmet katmanı ve işlem boyutu değişikliklerinin bazı birleşimleri için, veritabanı dosyaları, anahtar sırasında ayrılır ve yeniden eklenir.  Ne olursa olsun, veritabanları genellikle 30 saniyeden kısa bir süre boyunca ve genellikle birkaç saniye boyunca kullanılamadığında kısa bir hizmet kesintiye neden olabilir. Bağlantılar bırakıldığında çalışan uzun süre çalışan işlemler varsa, bu adımın süresi durdurulan işlemleri kurtarmak için daha uzun sürebilir. [Hızlandırılmış veritabanı kurtarma](sql-database-accelerated-database-recovery.md) , uzun süre çalışan işlemleri iptal etme etkisini azaltabilir.

> [!IMPORTANT]
> İş akışındaki herhangi bir adım sırasında hiçbir veri kaybolmaz.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Hizmet katmanını değiştirme veya işlem boyutunu boyutlandırma gecikmesi

Hizmet katmanını değiştirme veya tek bir veritabanının veya elastik havuzun işlem boyutunu yeniden boyutlandırma için beklenen gecikme süresi şu şekilde parametrelenir:

|Hizmet katmanı|Temel tek veritabanı,</br>Standart (S0-S1)|Temel elastik havuz,</br>Standart (S2-S12), </br>Hiper ölçekli </br>Tek veritabanı veya elastik havuz Genel Amaçlı|Premium veya İş Açısından Kritik tek veritabanı veya elastik havuz|
|:---|:---|:---|:---|
|**Temel tek veritabanı,</br> standart (S0-S1)**|&bull; &nbsp;sabit zaman gecikmesi kullanılan alandan bağımsız</br>&bull; &nbsp;genellikle 5 dakikadan az|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|
|**Temel elastik havuz, </br>standart (S2-S12), </br>Hyperscale, </br>Genel Amaçlı tek veritabanı veya elastik havuz**|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|&bull; &nbsp;sabit zaman gecikmesi kullanılan alandan bağımsız</br>&bull; &nbsp;genellikle 5 dakikadan az|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|
|**Premium veya İş Açısından Kritik tek veritabanı veya elastik havuz**|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|

> [!NOTE]
>
> - Hizmet katmanını değiştirme veya elastik havuz için işlem yeniden oluşturma durumunda, havuzdaki tüm veritabanları genelinde kullanılan alanın toplamı, tahmini hesaplamak için kullanılmalıdır.
> - Bir veritabanını elastik bir havuza taşımak durumunda, elastik havuz tarafından kullanılan alana değil, yalnızca veritabanı tarafından kullanılan alan gecikmeyi etkiler.
>
> [!TIP]
> Sürmekte olan işlemleri izlemek için bkz.: [SQL REST API kullanarak Işlemleri yönetme](https://docs.microsoft.com/rest/api/sql/operations/list), [CLI kullanarak Işlemleri yönetme](/cli/azure/sql/db/op), [T-SQL kullanarak Işlemleri izleme](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) ve bu Iki PowerShell komutu: [Get-azsqldatabaseactivity](/powershell/module/az.sql/get-azsqldatabaseactivity) ve [ Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Hizmet katmanını değiştirirken veya işlem boyutunu yeniden oluştururken dikkat edilecek ek noktalar

- Elastik havuz için sanal çekirdekleri veya eDTU 'ları yeniden boyutlandırdığınızda, kullanılan havuzun izin verilen en büyük boyuttan daha küçük olması gerekir ve havuz eDTU 'ları.
- Elastik havuz için Vçekirdekleri veya eDTU 'ları yeniden oluştururken, (1) havuzun en büyük depolama boyutu hedef havuz tarafından destekleniyorsa ve (2) depolama en büyük boyutu hedef havuzun dahil edilen depolama miktarını aşarsa, ek bir depolama maliyeti uygulanır. Örneğin, en fazla 100 GB boyutundaki 100 eDTU standart havuzu bir 50 eDTU standart havuzuna yeniden boyutlandırıldıysa, hedef havuz en büyük boyut olan 100 GB 'ı desteklediğinden ve dahil edilen depolama miktarı yalnızca 50 GB olduğundan, ek bir depolama maliyeti uygulanır. Bu nedenle, ek depolama miktarı 100 GB – 50 GB = 50 GB olur. Ek Depolama fiyatlandırması için bkz. [SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/). Kullanılan gerçek alan miktarı, dahil edilen depolama miktarından daha azsa, veritabanının maksimum boyutu dahil edilen tutara azaltılarak bu ek maliyetin önlenebilir olmasını sağlayabilirsiniz.

### <a name="billing-during-rescaling"></a>Yeniden oluşturma sırasında faturalandırma

Kullanımdan veya veritabanının bir saatten az etkin kalıp kalmadığından bağımsız olarak, bu saat boyunca uygulanan en yüksek hizmet katmanı + işlem boyutunu kullanarak bir veritabanının mevcut olduğu her saat için faturalandırılırsınız. Örneğin, tek bir veritabanı oluşturup beş dakika sonra silerseniz faturanız bir veritabanı saati için bir ücret yansıtır.

## <a name="change-elastic-pool-storage-size"></a>Elastik havuz depolama boyutunu değiştir

> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Sanal çekirdek tabanlı satın alma modeli

- Depolama alanı en fazla boyut sınırına sağlanabilir:

  - Standart veya genel amaçlı hizmet katmanlarında depolama için 10 GB 'lik artışlarla boyutu artırın veya azaltın
  - Premium veya iş açısından kritik hizmet katmanlarında depolama için, boyutu 250 GB 'lik artışlarla artırın veya azaltın
- Elastik havuz depolama alanı, en büyük boyutu arttırılarak veya azaltılarak sağlanabilir.
- Elastik havuz için depolama fiyatı, depolama miktarıdır ve hizmet katmanının depolama birimi fiyatıyla çarpılır. Ek depolama alanı fiyatına ilişkin ayrıntılar için bkz. [SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU tabanlı satın alma modeli

- Elastik havuz için eDTU fiyatı, ek ücret ödemeden belirli miktarda depolama alanı içerir. Dahil edilen miktarın ötesinde daha fazla depolama alanı, 250 GB ile 1 TB arasında artan maksimum boyut sınırına kadar ek bir maliyet ve sonra da 1 TB 'ın üzerinde 256 GB 'lık artışlarla sağlanabilir. Dahil edilen depolama miktarları ve maksimum boyut sınırları için bkz. [elastik havuz: depolama boyutları ve işlem boyutları](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Esnek havuz için ek depolama, [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [POWERSHELL](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)veya [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)kullanılarak en büyük boyutu arttırılarak sağlanabilir.
- Elastik havuz için ek depolama alanı fiyatı, ek depolama miktarıdır ve hizmet katmanının ek depolama birimi fiyatıyla çarpılır. Ek depolama alanı fiyatına ilişkin ayrıntılar için bkz. [SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](sql-database-file-space-management.md).

## <a name="next-steps"></a>Sonraki adımlar

Genel kaynak sınırları için bkz. [SQL veritabanı sanal çekirdek tabanlı kaynak limitleri-elastik havuzlar](sql-database-vcore-resource-limits-elastic-pools.md) ve [SQL veritabanı DTU tabanlı kaynak limitleri-elastik havuzlar](sql-database-dtu-resource-limits-elastic-pools.md).
