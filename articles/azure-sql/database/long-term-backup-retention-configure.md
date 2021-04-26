---
title: 'Azure SQL veritabanı: uzun süreli yedekleme bekletmesini yönetme'
description: Azure depolama 'da Azure SQL veritabanı için otomatik yedeklemeleri depolamayı ve geri yüklemeyi öğrenin (10 yıla kadar) Azure portal ve PowerShell 'i kullanarak
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: fad19d360f7c476ba71a9bbe00b58387b92f8ac4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690574"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Azure SQL veritabanı uzun süreli yedekleme bekletmesini yönetme
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ile, yedeklemeleri 10 yıla kadar ayrı Azure Blob depolama kapsayıcılarında otomatik olarak saklamak için [uzun süreli bir yedekleme bekletme](long-term-retention-overview.md) IlkesI (LTR) ayarlayabilirsiniz. Daha sonra Azure portal veya PowerShell kullanarak bu yedeklemeleri kullanarak bir veritabanını kurtarabilirsiniz. [Azure SQL yönetilen örneği](../managed-instance/long-term-backup-retention-configure.md)için uzun süreli saklama ilkeleri de desteklenir.

## <a name="using-the-azure-portal"></a>Azure portalını kullanma

Aşağıdaki bölümlerde, uzun süreli saklama ilkeleri ayarlamak, kullanılabilir uzun süreli bekletme yedeklemelerini yönetmek ve kullanılabilir bir yedekten geri yüklemek için Azure portal nasıl kullanılacağı gösterilmektedir.

### <a name="configure-long-term-retention-policies"></a>Uzun süreli saklama ilkelerini yapılandırma

SQL veritabanını, [Otomatik yedeklemeleri](long-term-retention-overview.md) hizmet katmanınızın saklama süresinden daha uzun bir süre koruyacak şekilde yapılandırabilirsiniz.

1. Azure portal sunucunuza gidin ve **yedeklemeler**' i seçin. Yedekleme bekletme ayarlarınızı değiştirmek için **bekletme ilkeleri** sekmesini seçin.

   ![bekletme ilkeleri deneyimi](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. Bekletme ilkeleri sekmesinde, uzun süreli yedek saklama ilkelerini ayarlamak veya değiştirmek istediğiniz veritabanlarını seçin. Seçilmemiş veritabanları etkilenmeyecektir.

   ![Yedekleme bekletme ilkelerini yapılandırmak için veritabanını seçin](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. **Ilkeleri Yapılandır** bölmesinde, haftalık, aylık veya yıllık yedeklemeler için istediğiniz saklama süresini belirtin. Uzun süreli yedek saklama alanının ayarlanması gerektiğini belirtmek için ' 0 ' bekletme dönemini seçin.

   ![ilkeleri Yapılandır bölmesi](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. Seçilen bekletme ayarlarını seçili tüm veritabanlarına uygulamak için **Uygula** ' yı seçin.

> [!IMPORTANT]
> Uzun süreli yedek saklama ilkesini etkinleştirdiğinizde, ilk yedeklemenin görünür olması ve geri yükleme için kullanılabilir olması 7 güne kadar sürebilir. LTR yedekleme cadans ayrıntıları için bkz. [uzun süreli yedek saklama](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Yedeklemeleri görüntüleme ve yedekten geri yükleme

Bir LTR ilkesiyle belirli bir veritabanı için tutulan yedeklemeleri görüntüleyin ve bu yedeklerden geri yükleyin.

1. Azure portal sunucunuza gidin ve **yedeklemeler**' i seçin. Belirli bir veritabanı için kullanılabilir LTR yedeklemeleri görüntülemek için, kullanılabilir LTR yedeklemeler sütununun altında **Yönet** ' i seçin. Seçili veritabanı için kullanılabilir LTR yedeklemelerin listesini içeren bir bölme görüntülenir.

   ![kullanılabilir yedeklemeler deneyimi](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. Görüntülenen **KULLANILABILIR LTR yedeklemeler** bölmesinde, kullanılabilir yedeklemeler ' i gözden geçirin. Geri yüklenecek veya silinecek bir yedekleme seçebilirsiniz.

   ![kullanılabilir LTR yedeklemeleri görüntüle](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. Kullanılabilir bir LTR yedeklemesinden geri yüklemek için, geri yüklemek istediğiniz yedeği seçin ve ardından **geri yükle**' yi seçin.

   ![kullanılabilir LTR yedeğinden geri yükle](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. Yeni veritabanınız için bir ad seçin ve geri yükleme 'nizin ayrıntılarını gözden geçirmek için **gözden geçir + oluştur** ' u seçin. Veritabanınızı seçili yedekten geri yüklemek için **Oluştur** ' u seçin.

   ![geri yükleme ayrıntılarını yapılandırma](./media/long-term-backup-retention-configure/restore-ltr.png)

1. Araç çubuğunda, geri yükleme işinin durumunu görüntülemek için bildirim simgesini seçin.

   ![geri yükleme işi ilerleme durumu](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Geri yükleme işi tamamlandığında, yeni geri yüklenen veritabanını görüntülemek için **SQL veritabanları** sayfasını açın.

> [!NOTE]
> Buradan [var olan veritabanına kopyalamak için geri yüklenen veritabanından veri ayıklama veya var olan veritabanını silerek geri yüklenen veritabanının adını var olan veritabanının adıyla değiştirme](recovery-using-backups.md#point-in-time-restore) gibi görevleri gerçekleştirmek için SQL Server Management Studio kullanarak geri yüklenen veritabanına bağlanabilirsiniz.

## <a name="using-powershell"></a>PowerShell’i kullanma

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Aşağıdaki bölümlerde, PowerShell kullanarak uzun süreli yedek saklama, Azure depolama 'daki yedeklemeleri görüntüleme ve Azure Storage 'daki bir yedekten geri yükleme işlemleri gösterilmektedir.

### <a name="azure-roles-to-manage-long-term-retention"></a>Uzun süreli saklama yönetimi için Azure rolleri

**Get-AzSqlDatabaseLongTermRetentionBackup** ve **restore-azsqldatabase** için aşağıdaki rollerden birine sahip olmanız gerekir:

- Abonelik sahibi rolü veya
- SQL Server katkıda bulunan rolü veya
- Aşağıdaki izinlerle özel rol:

   Microsoft. SQL/Locations/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Read

**Remove-AzSqlDatabaseLongTermRetentionBackup** için aşağıdaki rollerden birine sahip olmanız gerekir:

- Abonelik sahibi rolü veya
- Aşağıdaki izne sahip özel rol:

   Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete

> [!NOTE]
> SQL Server katkıda bulunan rolünün LTR yedeklemeleri silme izni yok.

Azure RBAC izinleri, *abonelik* ya da *kaynak grubu* kapsamında verilebilir. Ancak, bırakılan bir sunucuya ait olan LTR yedeklemelerine erişmek için, bu sunucunun *abonelik* kapsamında izin verilmesi gerekir.

- Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete

### <a name="create-an-ltr-policy"></a>Bir LTR ilkesi oluşturma

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR ilkelerini görüntüle

Bu örnek, bir sunucu içinde LTR ilkelerinin nasıl ekleneceğini gösterir

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>Bir LTR ilkesini Temizleme

Bu örnekte, bir veritabanından bir LTR ilkesinin nasıl temizlediğiniz gösterilmektedir

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR yedeklemeleri görüntüle

Bu örnek, bir sunucu içinde LTR yedeklemelerin nasıl ekleneceğini gösterir.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR yedeklemeleri Sil

Bu örnek, yedeklemeler listesinden bir LTR yedeklemenin nasıl silineceğini gösterir.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR yedeklemenin silinmesi geri alınamaz. Sunucu silindikten sonra bir LTR yedeklemesini silmek için abonelik kapsamı izninizin olması gerekir. Azure Izleyici 'de her silme hakkında bildirimleri, ' uzun süreli bekletme yedeklemesini siler ' işlemi için filtreleyerek ayarlayabilirsiniz. Etkinlik günlüğü, ne zaman ve ne zaman istek yaptığı hakkında bilgiler içerir. Ayrıntılı yönergeler için bkz. [etkinlik günlüğü uyarıları oluşturma](../../azure-monitor/alerts/alerts-activity-log.md) .

### <a name="restore-from-ltr-backups"></a>LTR yedeklemelerden geri yükleme

Bu örnek, bir LTR yedeğinden nasıl geri yükleneceğini gösterir. Bu arabirim değişmediği halde kaynak KIMLIĞI parametresi artık LTR yedekleme kaynağı KIMLIĞINI gerektiriyor.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Sunucu veya kaynak grubu silindikten sonra bir LTR yedeklemesinden geri yüklemek için, sunucunun aboneliğine sahip izninizin olması gerekir ve bu aboneliğin etkin olması gerekir. İsteğe bağlı-ResourceGroupName parametresini de atmalısınız.

> [!NOTE]
> Buradan var olan veritabanına kopyalamak için geri yüklenen veritabanından veri ayıklama veya var olan veritabanını silerek geri yüklenen veritabanının adını var olan veritabanının adıyla değiştirme gibi görevleri gerçekleştirmek için SQL Server Management Studio kullanarak geri yüklenen veritabanına bağlanabilirsiniz. Bkz. [zaman noktası geri yükleme](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Sınırlamalar
- Bir LTR yedeklemesinden geri yüklerken, okuma ölçeği özelliği devre dışıdır. Etkinleştirmek için geri yüklenen veritabanında ölçeği okuyun, oluşturulduktan sonra veritabanını güncelleştirin.
- Veritabanı elastik bir havuzda olduğunda oluşturulan bir LTR yedeklemesinden geri yüklerken hedef hizmet düzeyi hedefini belirtmeniz gerekir. 

## <a name="next-steps"></a>Sonraki adımlar

- Hizmet tarafından oluşturulan otomatik yedekler hakkında bilgi edinmek için bkz. [otomatik yedekler](automated-backups-overview.md)
- Uzun süreli yedek saklama hakkında bilgi edinmek için bkz. [uzun süreli yedek saklama](long-term-retention-overview.md)
