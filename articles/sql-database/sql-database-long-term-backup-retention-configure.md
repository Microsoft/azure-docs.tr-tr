---
title: Uzun süreli yedek saklamayı yönetme
description: Otomatik yedeklemeleri SQL Azure depolama alanında depolamayı ve sonra geri yüklemeyi öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 08/21/2019
ms.openlocfilehash: ea9a1da775a64f8ee405ced52df01d0824836c42
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820028"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Azure SQL veritabanı uzun süreli yedekleme bekletmesini yönetme

Azure SQL veritabanı 'nda, veritabanı yedeklerini 10 yıla kadar ayrı Azure Blob depolama kapsayıcılarında otomatik olarak saklamak için [uzun süreli yedek saklama](sql-database-long-term-retention.md) IlkesI (LTR) ile tek veya havuza alınmış bir veritabanı yapılandırabilirsiniz. Daha sonra Azure portal veya PowerShell kullanarak bu yedeklemeleri kullanarak bir veritabanını kurtarabilirsiniz.

> [!IMPORTANT]
> [Azure SQL veritabanı yönetilen örneği](sql-database-managed-instance.md) Şu anda uzun süreli yedekleme bekletmesini desteklememektedir.

## <a name="use-the-azure-portal-to-manage-long-term-backups"></a>Uzun süreli yedeklemeleri yönetmek için Azure portal kullanma

Aşağıdaki bölümlerde, uzun süreli saklama 'yi yapılandırmak, uzun süreli bekletmede yedeklemeleri görüntülemek ve uzun süreli bekletmede yedeklemeyi geri yüklemek için Azure portal nasıl kullanılacağı gösterilmektedir.

### <a name="configure-long-term-retention-policies"></a>Uzun süreli saklama ilkelerini yapılandırma

SQL veritabanını, [Otomatik yedeklemeleri](sql-database-long-term-retention.md) hizmet katmanınızın saklama süresinden daha uzun bir süre koruyacak şekilde yapılandırabilirsiniz. 

1. Azure portal SQL Server 'nizi seçin ve ardından **Yedeklemeleri Yönet**' e tıklayın. **Ilkeleri Yapılandır** sekmesinde, *uzun süreli yedek saklama ilkelerini ayarlamak veya değiştirmek istediğiniz veritabanının onay kutusunu seçin*. Veritabanının yanındaki onay kutusu seçili değilse, ilkeye yönelik değişiklikler bu veritabanına uygulanmaz.  

   ![Yedeklemeleri Yönet bağlantısı](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. **Ilkeleri Yapılandır** bölmesinde haftalık, aylık veya yıllık yedeklemeleri sürdürmek ve her biri için bekletme dönemini belirtmek istediğinizi seçin. 

   ![ilkeleri yapılandırma](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Tamamlandığında, **Uygula**' ya tıklayın.

> [!IMPORTANT]
> Uzun süreli yedek saklama ilkesini etkinleştirdiğinizde, ilk yedeklemenin görünür olması ve geri yükleme için kullanılabilir olması 7 güne kadar sürebilir. LTR yedekleme cadans ayrıntıları için bkz. [uzun süreli yedek saklama](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Azure portal kullanarak yedeklemeleri ve yedekten geri yüklemeyi görüntüleme

Bir LTR ilkesiyle belirli bir veritabanı için tutulan yedeklemeleri görüntüleyin ve bu yedeklerden geri yükleyin. 

1. Azure portal SQL Server 'nizi seçin ve ardından **Yedeklemeleri Yönet**' e tıklayın. **Kullanılabilir yedeklemeler** sekmesinde, kullanılabilir yedeklemeleri görmek istediğiniz veritabanını seçin.

   ![Veritabanı Seç](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. **Kullanılabilir yedeklemeler** bölmesinde, kullanılabilir yedeklemeleri gözden geçirin. 

   ![yedeklemeleri görüntüle](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Geri yüklemek istediğiniz yedeği seçin ve ardından yeni veritabanı adını belirtin.

   ![geri yükleme](./media/sql-database-long-term-retention/ltr-restore.png)

5. Veritabanınızı Azure SQL depolama 'daki yedekten yeni veritabanına geri yüklemek için **Tamam** ' a tıklayın.

6. Geri yükleme işinin durumunu görüntülemek için araç çubuğundaki bildirim simgesine tıklayın.

   ![geri yükleme işi ilerleme durumu](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Geri yükleme işi tamamlandığında, yeni geri yüklenen veritabanını görüntülemek için **SQL veritabanları** sayfasını açın.

> [!NOTE]
> Buradan [var olan veritabanına kopyalamak için geri yüklenen veritabanından veri ayıklama veya var olan veritabanını silerek geri yüklenen veritabanının adını var olan veritabanının adıyla değiştirme](sql-database-recovery-using-backups.md#point-in-time-restore) gibi görevleri gerçekleştirmek için SQL Server Management Studio kullanarak geri yüklenen veritabanına bağlanabilirsiniz.
>

## <a name="use-powershell-to-manage-long-term-backups"></a>Uzun süreli yedeklemeleri yönetmek için PowerShell 'i kullanma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Aşağıdaki bölümlerde, PowerShell kullanarak uzun süreli yedek saklama, Azure SQL depolamada yedeklemeleri görüntüleme ve Azure SQL depolama 'daki bir yedekten geri yükleme işlemleri gösterilmektedir.


### <a name="rbac-roles-to-manage-long-term-retention"></a>Uzun süreli saklama yönetimi için RBAC rolleri

**Get-AzSqlDatabaseLongTermRetentionBackup** ve **restore-azsqldatabase**için aşağıdaki rollerden birine sahip olmanız gerekir:

- Abonelik sahibi rolü veya
- SQL Server katkıda bulunan rolü veya
- Aşağıdaki izinlerle özel rol:

   Microsoft. SQL/Locations/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/ longTermRetentionBackups/okuma
 
**Remove-AzSqlDatabaseLongTermRetentionBackup**için aşağıdaki rollerden birine sahip olmanız gerekir:

- Abonelik sahibi rolü veya
- Aşağıdaki izne sahip özel rol:

   Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete


> [!NOTE]
> SQL Server katkıda bulunan rolünün LTR yedeklemeleri silme izni yok.

*Abonelik* ya da *kaynak grubu* kapsamında RBAC izinleri verilebilir. Ancak, bırakılan bir sunucuya ait olan LTR yedeklemelerine erişmek için, bu sunucunun *abonelik* kapsamında izin verilmesi gerekir.


### <a name="create-an-ltr-policy"></a>Bir LTR ilkesi oluşturma

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

# get the server
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR ilkelerini görüntüle
Bu örnek, bir sunucu içinde LTR ilkelerinin nasıl ekleneceğini gösterir

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Bir LTR ilkesini Temizleme
Bu örnekte, bir veritabanından bir LTR ilkesinin nasıl temizlediğiniz gösterilmektedir

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR yedeklemeleri görüntüle

Bu örnek, bir sunucu içinde LTR yedeklemelerin nasıl ekleneceğini gösterir. 

```powershell
# List all LTR backups under the current subscription in a specific Azure region 
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# List the LTR backups under a specific resource group in a specific Azure region 
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup

# List the LTR backups under an existing server
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup -ServerName $serverName

# List the LTR backups for a specific database 
# The backups are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.  
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database under a server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR yedeklemeleri Sil

Bu örnek, yedeklemeler listesinden bir LTR yedeklemenin nasıl silineceğini gösterir.

```powershell
# Remove the earliest backup from the list of backups
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> LTR yedeklemenin silinmesi geri alınamaz. Sunucu silindikten sonra bir LTR yedeklemesini silmek için abonelik kapsamı izninizin olması gerekir. Azure Izleyici 'de her silme hakkında bildirimleri, ' uzun süreli bekletme yedeklemesini siler ' işlemi için filtreleyerek ayarlayabilirsiniz. Etkinlik günlüğü, ne zaman ve ne zaman istek yaptığı hakkında bilgiler içerir. Ayrıntılı yönergeler için bkz. [etkinlik günlüğü uyarıları oluşturma](../azure-monitor/platform/alerts-activity-log.md) .
>

### <a name="restore-from-ltr-backups"></a>LTR yedeklemelerden geri yükleme
Bu örnek, bir LTR yedeğinden nasıl geri yükleneceğini gösterir. Bu arabirim değişmediği halde kaynak kimliği parametresi artık LTR yedekleme kaynağı kimliğini gerektiriyor. 

```powershell
# Restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup 
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Sunucu silindikten sonra bir LTR yedeklemesinden geri yüklemek için, sunucunun aboneliğine yönelik izinlere sahip olmanız ve bu aboneliğin etkin olması gerekir. İsteğe bağlı-ResourceGroupName parametresini de atmalısınız.  
>

> [!NOTE]
> Buradan, var olan veritabanına kopyalamak için geri yüklenen veritabanından veri ayıklama veya var olan veritabanını silme ve geri yükleme işlemini yeniden adlandırma gibi gerekli görevleri gerçekleştirmek için SQL Server Management Studio kullanarak geri yüklenen veritabanına bağlanabilirsiniz. veritabanı adının mevcut olduğunu. Bkz. [zaman noktası geri yükleme](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Sonraki adımlar

- Hizmet tarafından oluşturulan otomatik yedekler hakkında bilgi edinmek için bkz. [otomatik yedekler](sql-database-automated-backups.md)
- Uzun süreli yedek saklama hakkında bilgi edinmek için bkz. [uzun süreli yedek saklama](sql-database-long-term-retention.md)
