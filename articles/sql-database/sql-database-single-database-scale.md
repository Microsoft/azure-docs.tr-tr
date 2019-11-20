---
title: Tek veritabanı kaynaklarını ölçeklendirme
description: Bu makalede, Azure SQL veritabanı 'nda tek bir veritabanı için kullanılabilen işlem ve depolama kaynaklarının nasıl ölçeklendiriyapılacağı açıklanır.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: 2a16735e65201314328d2315479ccc467b9d555e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820992"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Azure SQL veritabanı 'nda tek veritabanı kaynaklarını ölçeklendirme

Bu makalede, sağlanan işlem katmanında bir Azure SQL veritabanı için kullanılabilir işlem ve depolama kaynaklarının nasıl ölçeklendiriyapılacağı açıklanır. Alternatif olarak, [sunucusuz bilgi işlem katmanı](sql-database-serverless.md) , kullanılan işlem için saniye başına işlem otomatik ölçeklendirme ve fatura sağlar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

## <a name="change-compute-size-vcores-or-dtus"></a>İşlem boyutunu değiştirme (sanal çekirdekler veya DTU 'Lar)

Sanal çekirdekler veya DTU sayısını ilk kez seçtikten sonra, [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)veya REST API kullanarak gerçek deneyime göre tek bir veritabanını dinamik olarak yukarı veya aşağı ölçeklendirdirebilirsiniz [ ](https://docs.microsoft.com/rest/api/sql/databases/update).

Aşağıdaki videoda, tek bir veritabanı için kullanılabilir DTU 'ları artırmak üzere hizmet katmanını ve işlem boyutunu dinamik olarak değiştirme gösterilmektedir.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Hizmet katmanını değiştirmenin veya işlem boyutunun yeniden boyutlandırmasından etkileri

Genellikle hizmet katmanını veya işlem boyutunu değiştirmek hizmeti aşağıdaki adımları gerçekleştirmeyle ilgilidir:

1. Veritabanı için yeni işlem örneği oluştur  

    İstenen hizmet katmanı ve işlem boyutu ile yeni bir işlem örneği oluşturulur. Hizmet katmanı ve işlem boyutu değişikliklerinin bazı birleşimleri için, veritabanının bir kopyasının oluşturulması ve toplam gecikme süresinin kesin bir şekilde etkilenmesinin yanı sıra yeni işlem örneğinde oluşturulması gerekir. Ne olursa olsun, bu adım sırasında veritabanı çevrimiçi kalır ve bağlantılar özgün işlem örneğindeki veritabanına yönlendirilmeye devam eder.

2. Bağlantıların yeni işlem örneğine yönlendirilmesini Değiştir

    Özgün işlem örneğindeki veritabanına yönelik var olan bağlantılar bırakılmıştı. Yeni işlem örneğindeki veritabanına yeni bağlantılar oluşturulur. Hizmet katmanı ve işlem boyutu değişikliklerinin bazı birleşimleri için, veritabanı dosyaları, anahtar sırasında ayrılır ve yeniden eklenir.  Ne olursa olsun, veritabanı genellikle 30 saniyeden kısa bir süre boyunca ve genellikle birkaç saniye boyunca kullanılamadığında kısa bir hizmet kesintisine neden olabilir. Bağlantılar bırakıldığında çalışan uzun süre çalışan işlemler varsa, bu adımın süresi durdurulan işlemleri kurtarmak için daha uzun sürebilir. [Hızlandırılmış veritabanı kurtarma](sql-database-accelerated-database-recovery.md) , uzun süre çalışan işlemleri iptal etme etkisini azaltabilir.

> [!IMPORTANT]
> İş akışındaki herhangi bir adım sırasında hiçbir veri kaybolmaz. Hizmet katmanı değiştirildiğinde Azure SQL veritabanı 'nı kullanan uygulamalar ve bileşenlerde bazı [yeniden deneme mantığı](sql-database-connectivity-issues.md) uyguladığınızdan emin olun.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Hizmet katmanını değiştirme veya işlem boyutunu boyutlandırma gecikmesi

Hizmet katmanını değiştirme veya tek bir veritabanının veya elastik havuzun işlem boyutunu yeniden boyutlandırma için beklenen gecikme süresi şu şekilde parametrelenir:

|Hizmet katmanı|Temel tek veritabanı,</br>Standart (S0-S1)|Temel elastik havuz,</br>Standart (S2-S12), </br>Hiper ölçekli </br>Tek veritabanı veya elastik havuz Genel Amaçlı|Premium veya İş Açısından Kritik tek veritabanı veya elastik havuz|
|:---|:---|:---|:---|
|**Temel tek veritabanı,</br> standart (S0-S1)**|&bull; &nbsp;sabit zaman gecikmesi kullanılan alandan bağımsız</br>&bull; &nbsp;genellikle 5 dakikadan az|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|
|**Temel elastik havuz, </br>standart (S2-S12), </br>Hyperscale, </br>Genel Amaçlı tek veritabanı veya elastik havuz**|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|&bull; &nbsp;sabit zaman gecikmesi kullanılan alandan bağımsız</br>&bull; &nbsp;genellikle 5 dakikadan az|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|
|**Premium veya İş Açısından Kritik tek veritabanı veya elastik havuz**|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|veri kopyalama nedeniyle kullanılan veritabanı alanıyla orantılı &bull; &nbsp;gecikme süresi</br>&bull; &nbsp;genellikle 1 dakikadan az kullanılan alan kullanılır|

> [!TIP]
> Sürmekte olan işlemleri izlemek için bkz.: [SQL REST API kullanarak Işlemleri yönetme](https://docs.microsoft.com/rest/api/sql/operations/list), [CLI kullanarak Işlemleri yönetme](/cli/azure/sql/db/op), [T-SQL kullanarak Işlemleri izleme](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) ve bu Iki PowerShell komutu: [Get-azsqldatabaseactivity](/powershell/module/az.sql/get-azsqldatabaseactivity) ve [ Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Hizmet katmanı değişiklikleri veya işlem temizleme işlemleri iptal ediliyor

Bir hizmet katmanı değişikliği veya işlem yeniden oluşturma işlemi iptal edilebilir.

#### <a name="azure-portal"></a>Azure portal

Veritabanına genel bakış dikey penceresinde **Bildirimler** ' e gidin ve devam eden bir işlem olduğunu belirten kutucuğa tıklayın:

![Devam eden işlem](media/sql-database-single-database-scale/ongoing-operations.png)

Sonra, **Bu Işlemi Iptal et**etiketli düğmeye tıklayın.

![Devam eden işlemi iptal et](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

PowerShell komut isteminden `$ResourceGroupName`, `$ServerName`ve `$DatabaseName`ayarlayın ve ardından aşağıdaki komutu çalıştırın:

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Hizmet katmanını değiştirirken veya işlem boyutunu yeniden oluştururken dikkat edilecek ek noktalar

- Daha yüksek bir hizmet katmanına veya işlem boyutuna yükseltiyorsanız, açıkça daha büyük bir boyut (MaxSize) belirtmediğiniz müddetçe veritabanının en büyük boyutu artmaz.
- Bir veritabanının indirgenmesini sağlamak için, kullanılan veritabanı hedef hizmet katmanının izin verilen en büyük boyuttan ve işlem boyutundan daha küçük olmalıdır.
- **Premium** 'dan **Standart** katmana düşürme yaparken, her ikisi de (1) veritabanının en büyük boyutu hedef işlem boyutunda destekleniyorsa ve (2) en büyük boyut, hedef işlem boyutunun dahil edilen depolama miktarını aşarsa, ek bir depolama maliyeti geçerlidir. Örneğin, boyut üst sınırı 500 GB olan bir P1 veritabanı S3 olarak boyutlandırıldıysa, S3 en büyük 1 TB boyutunu desteklediğinden ve dahil edilen depolama miktarı yalnızca 250 GB olduğundan, ek bir depolama maliyeti uygulanır. Bu nedenle, ek depolama miktarı 500 GB – 250 GB = 250 GB olur. Ek Depolama fiyatlandırması için bkz. [SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/). Kullanılan gerçek alan miktarı, dahil edilen depolama miktarından daha azsa, veritabanının maksimum boyutu dahil edilen tutara azaltılarak bu ek maliyetin önlenebilir olmasını sağlayabilirsiniz.
- [Coğrafi çoğaltma](sql-database-geo-replication-portal.md) etkinken bir veritabanını yükseltirken, birincil veritabanını yükseltmeden önce ikincil veritabanlarını istenen hizmet katmanına ve işlem boyutuna yükseltin (en iyi performans için genel kılavuz). Farklı bir sürümüne yükseltirken, önce ikincil veritabanının yükseltilmesi gerekir.
- [Coğrafi çoğaltma](sql-database-geo-replication-portal.md) etkinken bir veritabanını indirgemeden, ikincil veritabanını eski sürüme indirgemeden önce, birincil veritabanlarının istenen hizmet katmanına ve işlem boyutuna indirgenmesini sağlar (en iyi performans için genel kılavuz). Farklı bir sürüme düşürme yaparken öncelikle birincil veritabanını eski sürüme düşürüyordu.
- Geri yükleme hizmeti teklifleri, çeşitli hizmet katmanları için farklılık gösterir. **Temel** katmana eski sürüme düşürürseniz, daha düşük bir yedekleme saklama süresi vardır. Bkz. [Azure SQL veritabanı yedeklemeleri](sql-database-automated-backups.md).
- Veritabanının yeni özellikleri, değişiklikler tamamlanana kadar uygulanmaz.

### <a name="billing-during-compute-rescaling"></a>İşlem yeniden oluşturma sırasında faturalandırma

Kullanımdan veya veritabanının bir saatten az etkin kalıp kalmadığından bağımsız olarak, bu saat boyunca uygulanan en yüksek hizmet katmanı + işlem boyutunu kullanarak bir veritabanının mevcut olduğu her saat için faturalandırılırsınız. Örneğin, tek bir veritabanı oluşturup beş dakika sonra silerseniz faturanız bir veritabanı saati için bir ücret yansıtır.

## <a name="change-storage-size"></a>Depolama boyutunu değiştir

### <a name="vcore-based-purchasing-model"></a>Sanal çekirdek tabanlı satın alma modeli

- Depolama birimi, 1 GB Artımlar kullanılarak en fazla boyut sınırına sağlanabilir. Yapılandırılabilir veri depolaması en az 5 GB 'dir
- Tek bir veritabanı için depolama, [Azure Portal](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)veya [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)kullanılarak en büyük boyutu arttırılarak veya azaltılarak sağlanabilir.
- SQL veritabanı, günlük dosyaları için ek depolama alanının %30 ' u ve TempDB için vCore başına 32 ' i ayırır, ancak 384GB ' ı aşamaz. TempDB, tüm hizmet katmanlarında eklenmiş bir SSD üzerinde bulunur.
- Tek bir veritabanı için depolama fiyatı, veri depolama ve günlük depolama tutarlarının toplamı hizmet katmanının depolama birimi fiyatıyla çarpılır. TempDB 'nin maliyeti, sanal çekirdek fiyatına dahildir. Ek depolama alanı fiyatına ilişkin ayrıntılar için bkz. [SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU tabanlı satın alma modeli

- Tek bir veritabanı için DTU fiyatı, ek ücret ödemeden belirli miktarda depolama alanı içerir. Dahil edilen miktarın ötesinde daha fazla depolama alanı, 250 GB ile 1 TB arasında artan maksimum boyut sınırına kadar ek bir maliyet ve sonra da 1 TB 'ın üzerinde 256 GB 'lık artışlarla sağlanabilir. Dahil edilen depolama miktarları ve maksimum boyut sınırları için bkz. [tek veritabanı: depolama boyutları ve işlem boyutları](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Tek bir veritabanı için ek depolama, Azure portal, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [POWERSHELL](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)veya [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)kullanılarak en büyük boyutu arttırılarak sağlanabilir.
- Tek bir veritabanı için ek depolama alanı fiyatı, hizmet katmanının ek depolama birimi fiyatı ile çarpılmış olan ek depolama miktarıdır. Ek depolama alanı fiyatına ilişkin ayrıntılar için bkz. [SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>En büyük boyut 1 TB 'den büyük olduğunda P11 ve P15 kısıtlamaları

Premium katmanda 1 TB 'den fazla depolama alanı şu anda tüm bölgelerde kullanılabilir: Çin Doğu, Çin Kuzey, Almanya Orta, Almanya Kuzeydoğu, Orta Batı ABD, US DoD bölgeleri ve ABD Devlet Merkezi. Bu bölgelerde Premium katmanda depolama için 1 TB üst sınırı uygulanır. En büyük boyut olan 1 TB 'den büyük olan P11 ve P15 veritabanları için aşağıdaki önemli noktalar ve sınırlamalar geçerlidir:

- Bir P11 veya P15 veritabanının en büyük boyutu 1 TB 'den büyük bir değere ayarlandıysa, bu, yalnızca bir P11 veya P15 veritabanına geri yüklenebilir veya kopyalanabilir.  Daha sonra, veritabanı farklı bir işlem boyutuna ölçeklendirildi olabilir, ancak bu işlem sırasında ayrılan alan miktarı yeni işlem boyutunun en büyük boyut sınırlarını aşmaz.
- Etkin coğrafi çoğaltma senaryoları için:
  - Coğrafi çoğaltma ilişkisi ayarlama: birincil veritabanı P11 veya P15 ise, ikincil (ies) da P11 veya P15 olmalıdır; daha düşük işlem boyutu 1 TB 'den fazla destekleme yeteneğine sahip olmadıkları için ikincil olarak reddedilir.
  - Coğrafi çoğaltma ilişkisinde birincil veritabanını yükseltme: birincil veritabanında en büyük boyutu 1 TB 'den fazlasına değiştirmek ikincil veritabanında aynı değişikliği tetikler. Birincil değişikliğin etkili olabilmesi için her iki yükseltme de başarılı olmalıdır. 1 TB 'den fazla seçeneği için bölge sınırlamaları geçerlidir. İkincil, 1 TB 'ın üzerinde bir değer desteklemeyen bir bölgedeyse, birincil yükseltilmemiştir.
- 1 TB 'den fazla P11/P15 veritabanlarını yüklemek için Içeri/dışarı aktarma hizmeti kullanımı desteklenmez. Verileri [içeri](sql-database-import.md) ve [dışarı](sql-database-export.md) aktarmak için SqlPackage. exe ' yi kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Genel kaynak sınırları için bkz. [SQL veritabanı sanal çekirdek tabanlı kaynak limitleri-tek veritabanları](sql-database-vcore-resource-limits-single-databases.md) ve [SQL veritabanı DTU tabanlı kaynak limitleri-elastik havuzlar](sql-database-dtu-resource-limits-single-databases.md).
