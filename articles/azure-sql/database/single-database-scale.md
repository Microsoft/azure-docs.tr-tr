---
title: Tek veritabanı kaynaklarını ölçeklendirme
description: Bu makalede, Azure SQL veritabanı 'nda tek bir veritabanı için kullanılabilen işlem ve depolama kaynaklarının nasıl ölçeklendiriyapılacağı açıklanır.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: ae1b3cc41d709c28ba517d672eb98cb60a837a8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779084"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Azure SQL Veritabanında tek veritabanı kaynaklarını ölçeklendirme

Bu makalede, sağlanan işlem katmanında bir Azure SQL veritabanı için kullanılabilir işlem ve depolama kaynaklarının nasıl ölçeklendiriyapılacağı açıklanır. Alternatif olarak, [sunucusuz bilgi işlem katmanı](serverless-tier-overview.md) , kullanılan işlem için saniye başına işlem otomatik ölçeklendirme ve fatura sağlar.

Sanal çekirdekler veya DTU sayısını ilk kez seçtikten sonra, kullanarak gerçek deneyim temelinde tek bir veritabanını dinamik olarak yukarı veya aşağı ölçekleyebilirsiniz:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure portalı](single-database-manage.md#the-azure-portal)
* [PowerShell](/powershell/module/az.sql/set-azsqldatabase)
* [Azure CLI](/cli/azure/sql/db#az_sql_db_update)
* [REST API](/rest/api/sql/databases/update)


Aşağıdaki videoda, tek bir veritabanı için kullanılabilir DTU 'ları artırmak üzere hizmet katmanını ve işlem boyutunu dinamik olarak değiştirme gösterilmektedir.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](file-space-manage.md).

## <a name="impact"></a>Etki

Genellikle hizmet katmanını veya işlem boyutunu değiştirmek hizmeti aşağıdaki adımları gerçekleştirmeyle ilgilidir:

1. Veritabanı için yeni bir işlem örneği oluşturun. 

    İstenen hizmet katmanı ve işlem boyutu ile yeni bir işlem örneği oluşturulur. Hizmet katmanı ve işlem boyutu değişikliklerinin bazı birleşimleri için, veritabanının bir kopyasının yeni işlem örneğinde oluşturulması gerekir. Bu, verilerin kopyalanmasını ve genel gecikmeyi kesin bir şekilde etkileyebilir. Ne olursa olsun, bu adım sırasında veritabanı çevrimiçi kalır ve bağlantılar özgün işlem örneğindeki veritabanına yönlendirilmeye devam eder.

2. Bağlantıların yeni bir işlem örneğine yönlendirilmesini değiştirin.

    Özgün işlem örneğindeki veritabanına yönelik var olan bağlantılar bırakılmıştı. Yeni işlem örneğindeki veritabanına yeni bağlantılar oluşturulur. Hizmet katmanı ve işlem boyutu değişikliklerinin bazı birleşimleri için, veritabanı dosyaları, anahtar sırasında ayrılır ve yeniden eklenir.  Ne olursa olsun, veritabanı genellikle 30 saniyeden kısa bir süre boyunca ve genellikle birkaç saniye boyunca kullanılamadığında kısa bir hizmet kesintisine neden olabilir. Bağlantılar bırakıldığında çalışan uzun süre çalışan işlemler varsa, bu adımın süresi, durdurulan işlemleri kurtarmak için daha uzun sürebilir. [Hızlandırılmış veritabanı kurtarma](../accelerated-database-recovery.md) , uzun süre çalışan işlemleri iptal etme etkisini azaltabilir.

> [!IMPORTANT]
> İş akışındaki herhangi bir adım sırasında hiçbir veri kaybolmaz. Hizmet katmanı değiştirildiğinde Azure SQL veritabanı 'nı kullanan uygulamalar ve bileşenlerde bazı [yeniden deneme mantığı](troubleshoot-common-connectivity-issues.md) uyguladığınızdan emin olun.

## <a name="latency"></a>Gecikme süresi

Hizmet katmanını değiştirmek, tek bir veritabanının veya elastik havuzun işlem boyutunu ölçeklendirmek, bir veritabanını elastik havuzun içine/dışına taşımak veya bir veritabanını elastik havuzlar arasında taşımak için beklenen gecikme süresi şu şekildedir:

|Hizmet katmanı|Temel tek veritabanı,</br>Standart (S0-S1)|Temel elastik havuz,</br>Standart (S2-S12), </br>Tek veritabanı veya elastik havuz Genel Amaçlı|Premium veya İş Açısından Kritik tek veritabanı veya elastik havuz|Hiper Ölçek
|:---|:---|:---|:---|:---|
|**Temel tek veritabanı, </br> Standart (S0-S1)**|&bull;&nbsp;Kullanılan alandan bağımsız sabit zaman gecikmesi</br>&bull;&nbsp;Genellikle, 5 dakikadan az|&bull;&nbsp;Veri kopyalama nedeniyle kullanılan veritabanı alanıyla gecikme süresi</br>&bull;&nbsp;Genellikle, 1 dakikadan az boş alan kullanılır|&bull;&nbsp;Veri kopyalama nedeniyle kullanılan veritabanı alanıyla gecikme süresi</br>&bull;&nbsp;Genellikle, 1 dakikadan az boş alan kullanılır|&bull;&nbsp;Veri kopyalama nedeniyle kullanılan veritabanı alanıyla gecikme süresi</br>&bull;&nbsp;Genellikle, 1 dakikadan az boş alan kullanılır|
|**Temel elastik havuz, </br> Standart (S2-S12), </br> genel amaçlı tek veritabanı veya elastik havuz**|&bull;&nbsp;Veri kopyalama nedeniyle kullanılan veritabanı alanıyla gecikme süresi</br>&bull;&nbsp;Genellikle, 1 dakikadan az boş alan kullanılır|&bull;&nbsp;Tek veritabanları için, kullanılan alandan bağımsız sabit gecikme süresi</br>&bull;&nbsp;Genellikle, tek veritabanları için 5 dakikadan az</br>&bull;&nbsp;Elastik havuzlar için, veritabanı sayısıyla orantılı|&bull;&nbsp;Veri kopyalama nedeniyle kullanılan veritabanı alanıyla gecikme süresi</br>&bull;&nbsp;Genellikle, 1 dakikadan az boş alan kullanılır|&bull;&nbsp;Veri kopyalama nedeniyle kullanılan veritabanı alanıyla gecikme süresi</br>&bull;&nbsp;Genellikle, 1 dakikadan az boş alan kullanılır|
|**Premium veya İş Açısından Kritik tek veritabanı veya elastik havuz**|&bull;&nbsp;Veri kopyalama nedeniyle kullanılan veritabanı alanıyla gecikme süresi</br>&bull;&nbsp;Genellikle, 1 dakikadan az boş alan kullanılır|&bull;&nbsp;Veri kopyalama nedeniyle kullanılan veritabanı alanıyla gecikme süresi</br>&bull;&nbsp;Genellikle, 1 dakikadan az boş alan kullanılır|&bull;&nbsp;Veri kopyalama nedeniyle kullanılan veritabanı alanıyla gecikme süresi</br>&bull;&nbsp;Genellikle, 1 dakikadan az boş alan kullanılır|&bull;&nbsp;Veri kopyalama nedeniyle kullanılan veritabanı alanıyla gecikme süresi</br>&bull;&nbsp;Genellikle, 1 dakikadan az boş alan kullanılır|
|**Hiper Ölçek**|Yok|Yok|Yok|&bull;&nbsp;Kullanılan alandan bağımsız sabit zaman gecikmesi</br>&bull;&nbsp;Genellikle 2 dakikadan az|

> [!NOTE]
> Ayrıca, standart (S2-S12) ve Genel Amaçlı veritabanları için, veritabanı Premium dosya paylaşma ([PFS](../../storage/files/storage-files-introduction.md)) depolaması kullanıyorsa, bir veritabanını elastik havuzun içine/dışına veya elastik havuzlar arasında taşıma gecikmesi veritabanı boyutuyla orantılıdır.
>
> Bir veritabanının PFS depolama kullanıp kullanmadığını anlamak için, veritabanı bağlamında aşağıdaki sorguyu yürütün. AccountType sütunundaki değer `PremiumFileStorage` veya ise `PremiumFileStorage-ZRS` , veritabanı PFS depolama alanını kullanıyor demektir.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!NOTE]
> Bölge yedekli özelliği, İş Açısından Kritik Genel Amaçlı katmanına ölçeklendirilirken varsayılan olarak aynı kalacaktır. Bölge yedekliliği etkinleştirildiğinde bu düşürme için gecikme süresi ve Genel Amaçlı katmanının bölge yedekliliğe geçiş gecikmesi veritabanı boyutuyla orantılıdır.

> [!TIP]
> Sürmekte olan işlemleri izlemek için bkz.: [SQL REST API kullanarak Işlemleri yönetme](/rest/api/sql/operations/list), [CLI kullanarak Işlemleri yönetme](/cli/azure/sql/db/op), [T-SQL kullanarak Işlemleri izleme](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) ve bu Iki PowerShell komutu: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) ve [stop-azsqldatabaseactivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Değişiklikler iptal ediliyor

Bir hizmet katmanı değişikliği veya işlem yeniden oluşturma işlemi iptal edilebilir.

### <a name="the-azure-portal"></a>Azure portal

Veritabanına genel bakış dikey penceresinde **Bildirimler** ' e gidin ve devam eden bir işlem olduğunu gösteren kutucuğa tıklayın:

![Devam eden işlem](./media/single-database-scale/ongoing-operations.png)

Sonra, **Bu Işlemi Iptal et** etiketli düğmeye tıklayın.

![Devam eden işlemi iptal et](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

PowerShell komut isteminden `$resourceGroupName` ,, `$serverName` ve öğesini ayarlayın `$databaseName` ve ardından aşağıdaki komutu çalıştırın:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Diğer konular

- Daha yüksek bir hizmet katmanına veya işlem boyutuna yükseltiyorsanız, açıkça daha büyük bir boyut (MaxSize) belirtmediğiniz müddetçe veritabanının en büyük boyutu artmaz.
- Bir veritabanının indirgenmesini sağlamak için, kullanılan veritabanı hedef hizmet katmanının izin verilen en büyük boyuttan ve işlem boyutundan daha küçük olmalıdır.
- **Premium** 'dan **Standart** katmana düşürme yaparken, her ikisi de (1) veritabanının en büyük boyutu hedef işlem boyutunda destekleniyorsa ve (2) en büyük boyut, hedef işlem boyutunun dahil edilen depolama miktarını aşarsa, ek bir depolama maliyeti geçerlidir. Örneğin, boyut üst sınırı 500 GB olan bir P1 veritabanı S3 olarak boyutlandırıldıysa, S3 en büyük 1 TB boyutunu desteklediğinden ve dahil edilen depolama miktarı yalnızca 250 GB olduğundan, ek bir depolama maliyeti uygulanır. Bu nedenle, ek depolama miktarı 500 GB – 250 GB = 250 GB olur. Ek depolama alanı fiyatlandırması için bkz. [Azure SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/). Kullanılan gerçek alan miktarı, dahil edilen depolama miktarından daha azsa, veritabanının maksimum boyutu dahil edilen tutara azaltılarak bu ek maliyetin önlenebilir olmasını sağlayabilirsiniz.
- [Coğrafi çoğaltma](active-geo-replication-configure-portal.md) etkinken bir veritabanını yükseltirken, birincil veritabanını yükseltmeden önce ikincil veritabanlarını istenen hizmet katmanına ve işlem boyutuna yükseltin (en iyi performans için genel kılavuz). Farklı bir sürüme yükseltirken, önce ikincil veritabanının yükseltilme zorunluluğu vardır.
- [Coğrafi çoğaltma](active-geo-replication-configure-portal.md) etkinken bir veritabanını indirgemeden, ikincil veritabanını eski sürüme indirgemeden önce, birincil veritabanlarının istenen hizmet katmanına ve işlem boyutuna indirgenmesini sağlar (en iyi performans için genel kılavuz). Farklı bir sürüme düşürme yaparken, öncelikle birincil veritabanının indirgenmesi gereksinimidir.
- Geri yükleme hizmeti teklifleri, çeşitli hizmet katmanları için farklılık gösterir. **Temel** katmana eski sürüme düşürüyorsanız, daha düşük bir yedekleme saklama süresi vardır. Bkz. [Azure SQL veritabanı yedeklemeleri](automated-backups-overview.md).
- Veritabanı için yeni özellikler, değişiklikler tamamlanana kadar uygulanmaz.
- Bir veritabanını ölçeklendirmek için veri kopyalama gerektiğinde (bkz. [gecikme süresi](#latency)) hizmet katmanını değiştirirken, ölçek işlemine yönelik olarak yüksek kaynak kullanımı eşzamanlı ölçeklendirme sürelerine neden olabilir. [Hızlandırılmış veritabanı kurtarma (ADR)](/sql/relational-databases/accelerated-database-recovery-concepts)sayesinde, uzun süre çalışan işlemlerin geri alınması önemli bir gecikme kaynağı değildir, ancak yüksek eşzamanlı kaynak kullanımı, özellikle daha küçük bilgi işlem boyutları için ölçekleme için daha az işlem, depolama ve ağ bant genişliği kaynakları bırakabilir.

## <a name="billing"></a>Faturalandırma

Kullanımdan veya veritabanının bir saatten az etkin kalıp kalmadığından bağımsız olarak, bu saat boyunca uygulanan en yüksek hizmet katmanı + işlem boyutunu kullanarak bir veritabanının mevcut olduğu her saat için faturalandırılırsınız. Örneğin, tek bir veritabanı oluşturup beş dakika sonra silerseniz faturanız bir veritabanı saati için bir ücret yansıtır.

## <a name="change-storage-size"></a>Depolama boyutunu değiştir

### <a name="vcore-based-purchasing-model"></a>Sanal çekirdek tabanlı satın alma modeli

- Depolama, 1 GB 'lik artışlarla veri depolama maksimum boyut sınırına sağlanabilir. Yapılandırılabilir veri depolaması en az 1 GB 'dir. Her hizmet hedefi için veri depolama maksimum boyut sınırları için, bkz. tek veritabanlarına ait kaynak sınırları için kaynak sınırı belge sayfaları, [DTU satın alma modelini kullanarak tek veritabanlarının](resource-limits-dtu-single-databases.md) [sanal çekirdek satın alma modelini](resource-limits-vcore-single-databases.md) ve kaynak sınırlarını kullanarak.
- Tek bir veritabanı için veri depolama, [Azure Portal](https://portal.azure.com), [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update)veya [REST API](/rest/api/sql/databases/update)kullanılarak en büyük boyutu arttırılarak veya azaltılarak sağlanabilir. En büyük boyut değeri bayt cinsinden belirtilirse, 1 GB (1073741824 bayt) katlarından biri olmalıdır.
- Bir veritabanının veri dosyalarında depolanabilecek veri miktarı, yapılandırılan veri depolama maksimum boyutuyla sınırlıdır. Bu depolamaya ek olarak, Azure SQL veritabanı işlem günlüğü için kullanılmak üzere %30 daha fazla depolama alanı ayırır.
- Azure SQL veritabanı, veritabanı için otomatik olarak 32 GB ayırır `tempdb` . `tempdb` , tüm hizmet katmanlarında yerel SSD depolamada bulunur.
- Tek bir veritabanı veya elastik havuz için depolama fiyatı, veri depolama ve işlem günlüğü depolama tutarlarının toplamıdır ve hizmet katmanının depolama birimi fiyatıyla çarpılır. Maliyeti `tempdb` fiyata dahildir. Depolama fiyatı hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>DTU tabanlı satın alma modeli

- Tek bir veritabanı için DTU fiyatı, ek ücret ödemeden belirli miktarda depolama alanı içerir. Dahil edilen miktarın ötesinde daha fazla depolama alanı, 250 GB ile 1 TB arasında artan maksimum boyut sınırına kadar ek bir maliyet ve sonra da 1 TB 'ın üzerinde 256 GB 'lık artışlarla sağlanabilir. Dahil edilen depolama miktarları ve maksimum boyut sınırları için bkz. [tek veritabanı: depolama boyutları ve işlem boyutları](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Tek bir veritabanı için ek depolama, Azure portal, [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [POWERSHELL](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update)veya [REST API](/rest/api/sql/databases/update)kullanılarak en büyük boyutu arttırılarak sağlanabilir.
- Tek bir veritabanı için ek depolama alanı fiyatı, hizmet katmanının ek depolama birimi fiyatı ile çarpılmış olan ek depolama miktarıdır. Ek depolama alanı fiyatına ilişkin ayrıntılar için bkz. [Azure SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](file-space-manage.md).

### <a name="geo-replicated-database"></a>Coğrafi olarak çoğaltılan veritabanı

Çoğaltılan bir ikincil veritabanının veritabanı boyutunu değiştirmek için, birincil veritabanının boyutunu değiştirin. Bu değişiklik daha sonra çoğaltılır ve ikincil veritabanına uygulanır.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>En büyük boyut 1 TB 'den büyük olduğunda P11 ve P15 kısıtlamaları

Premium katmanda 1 TB 'den fazla depolama alanı şu anda tüm bölgelerde kullanılabilir: Çin Doğu, Çin Kuzey, Almanya Orta ve Almanya Kuzeydoğu. Bu bölgelerde, Premium katmanda en fazla depolama alanı 1 TB ile sınırlıdır. En büyük boyut olan 1 TB 'den büyük olan P11 ve P15 veritabanları için aşağıdaki önemli noktalar ve sınırlamalar geçerlidir:

- Bir P11 veya P15 veritabanının en büyük boyutu 1 TB 'den büyük bir değere ayarlandıysa, bu, yalnızca bir P11 veya P15 veritabanına geri yüklenebilir veya kopyalanabilir.  Daha sonra, veritabanı farklı bir işlem boyutuna ölçeklendirildi, ancak bu işlem sırasında ayrılan alan miktarı, yeni işlem boyutunun en büyük boyut sınırlarını aşmaz.
- Etkin coğrafi çoğaltma senaryoları için:
  - Coğrafi çoğaltma ilişkisi ayarlama: birincil veritabanı P11 veya P15 ise, ikincil (ies) da P11 veya P15 olmalıdır. Daha düşük işlem boyutu 1 TB 'den fazla destekleme yeteneğine sahip olmadığından ikincil olarak reddedilir.
  - Coğrafi çoğaltma ilişkisinde birincil veritabanını yükseltme: birincil veritabanında en büyük boyutu 1 TB 'den fazlasına değiştirmek ikincil veritabanında aynı değişikliği tetikler. Birincil değişikliğin etkili olabilmesi için her iki yükseltme de başarılı olmalıdır. 1 TB 'den fazla seçeneği için bölge sınırlamaları geçerlidir. İkincil, 1 TB 'ın üzerinde bir değer desteklemeyen bir bölgedeyse, birincil yükseltilmemiştir.
- 1 TB 'tan fazla P11/P15 veritabanlarını yüklemek için Içeri/dışarı aktarma hizmeti kullanımı desteklenmez. Verileri [içeri](database-import.md) ve [dışarı](database-export.md) aktarmak için SqlPackage.exe kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Genel kaynak sınırları için bkz. [Azure SQL veritabanı sanal çekirdek tabanlı kaynak limitleri-tek veritabanları](resource-limits-vcore-single-databases.md) ve [Azure SQL veritabanı DTU tabanlı kaynak limitleri-tek veritabanları](resource-limits-dtu-single-databases.md).
