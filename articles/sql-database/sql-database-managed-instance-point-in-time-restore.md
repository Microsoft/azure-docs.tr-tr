---
title: Yönetilen örnek-zaman içinde geri yükleme
description: Yönetilen bir örnekteki SQL veritabanını önceki bir noktaya geri yükleyin.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: ad96d0a04b03e070a7108832370749377d723826
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821754"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Yönetilen bir örnekteki SQL veritabanını önceki zaman noktasına geri yükleme

Bir veritabanını geçmişteki bir zamanda başka bir veritabanının kopyası olarak oluşturmak için belirli bir noktaya geri yükleme (ıNR) kullanın. Bu makalede, bir Azure SQL veritabanı yönetilen örneğinde bir veritabanının bir noktadan noktaya geri yüklemesi nasıl yapılacağı açıklanır.

Zaman içinde geri yükleme, hatalardan kaynaklanan olaylar, yanlış yüklenmiş veriler veya önemli verilerin silinmesi gibi kurtarma senaryolarında yararlıdır. Bunu yalnızca test veya denetim için de kullanabilirsiniz. Yedekleme dosyaları, veritabanı ayarlarınıza bağlı olarak 7-35 gün boyunca tutulur.

Zaman içindeki bir noktaya geri yükleme şunları yapabilir:

- Mevcut bir veritabanından bir veritabanını geri yükleyin.
- Silinen bir veritabanından bir veritabanını geri yükleyin.

Yönetilen bir örnek için, bir noktadan noktaya geri yükleme de şunları yapabilir:

- Bir veritabanını aynı yönetilen örneğe geri yükleyin.
- Veritabanını başka bir yönetilen örneğe geri yükleyin.

> [!NOTE]
> Tüm yönetilen bir örnek için bir noktadan noktaya geri yükleme mümkün değildir. Bu makalede, yalnızca mümkün olan özellikler açıklanmaktadır: yönetilen bir örnekte barındırılan bir veritabanının zaman içindeki nokta geri yüklemesi.

## <a name="limitations"></a>Sınırlamalar

Yönetilen bir örnekten diğerine geri yüklerken, her iki örnek de aynı abonelikte ve bölgede olmalıdır. Bölgeler arası ve çapraz abonelik geri yüklemesi şu anda desteklenmiyor.

> [!WARNING]
> Yönetilen örneğinizin depolama boyutunu unutmayın. Geri yüklenecek verilerin boyutuna bağlı olarak, örnek depolamanın dışında kalabilirsiniz. Geri yüklenen veriler için yeterli alan yoksa farklı bir yaklaşım kullanın.

Aşağıdaki tabloda, yönetilen örnekler için zaman içinde geri yükleme senaryoları gösterilmektedir:

|           |Mevcut VERITABANıNı aynı yönetilen örneğe geri yükle| Varolan VERITABANıNı başka bir yönetilen örneğe geri yükleme|Bırakılan VERITABANıNı aynı yönetilen örneğe geri yükle|Bırakılan VERITABANıNı başka bir yönetilen örneğe geri yükleme|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portal**| Evet|Hayır |Hayır|Hayır|
|**Azure CLI**|Evet |Evet |Hayır|Hayır|
|**PowerShell**| Evet|Evet |Evet|Evet|

## <a name="restore-an-existing-database"></a>Var olan bir veritabanını geri yükleme

Azure portal, PowerShell veya Azure CLı kullanarak mevcut bir veritabanını aynı örneğe geri yükleyin. Bir veritabanını başka bir örneğe geri yüklemek için, hedef yönetilen örnek ve kaynak grubunun özelliklerini belirleyebilmeniz için PowerShell veya Azure CLı kullanın. Bu parametreleri belirtmezseniz, veritabanı varsayılan olarak mevcut örneğe geri yüklenir. Azure portal Şu anda başka bir örneğe geri yüklemeyi desteklemiyor.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. [Azure portalında](https://portal.azure.com) oturum açın. 
2. Yönetilen örneğinize gidin ve geri yüklemek istediğiniz veritabanını seçin.
3. Veritabanı sayfasında **geri yükle** ' yi seçin:

    ![Azure portal kullanarak bir veritabanını geri yükleme](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. **Geri yükle** sayfasında, veritabanını geri yüklemek istediğiniz tarih ve saat için noktayı seçin.
5. Veritabanınızı geri yüklemek için **Onayla** ' yı seçin. Bu eylem, yeni bir veritabanı oluşturan ve belirli bir zaman noktasındaki özgün veritabanındaki verilerle dolduran geri yükleme işlemini başlatır. Kurtarma işlemi hakkında daha fazla bilgi için bkz. [Kurtarma süresi](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Zaten yüklü Azure PowerShell yoksa, bkz. [Azure PowerShell modülünü yükleme](https://docs.microsoft.com/powershell/azure/install-az-ps).

Veritabanını PowerShell kullanarak geri yüklemek için aşağıdaki komutta parametre değerlerini belirtin. Ardından şu komutu çalıştırın:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Veritabanını başka bir yönetilen örneğe geri yüklemek için, hedef kaynak grubu ve yönetilen örnek adlarını da belirtin:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Ayrıntılar için bkz. [restore-Azsqlınstancedatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı yüklü değilse, bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli?view=azure-cli-latest).

Azure CLı kullanarak veritabanını geri yüklemek için aşağıdaki komutta parametre değerlerini belirtin. Ardından şu komutu çalıştırın:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Veritabanını başka bir yönetilen örneğe geri yüklemek için, hedef kaynak grubu ve yönetilen örnek adlarını da belirtin:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Kullanılabilir parametrelerin ayrıntılı bir açıklaması için bkz. [yönetilen bir örneğe bir veritabanını geri yüklemek Için CLI belgeleri](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Silinen veritabanını geri yükleme

Silinen bir veritabanının geri yüklenmesi, yalnızca PowerShell kullanılarak yapılabilir. Veritabanı aynı örneğe veya başka bir örneğe geri yüklenebilir.

Silinen bir veritabanını PowerShell kullanarak geri yüklemek için aşağıdaki komutta parametre değerlerini belirtin. Ardından şu komutu çalıştırın:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

Silinen veritabanını başka bir örneğe geri yüklemek için kaynak grubunun ve yönetilen örneğin adlarını değiştirin. Ayrıca, konum parametresinin kaynak grubu ve yönetilen örnek konumuyla eşleştiğinden emin olun.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-an-existing-database"></a>Var olan veritabanının üzerine yaz

Var olan bir veritabanının üzerine yazmak için şunları yapmanız gerekir:

1. Üzerine yazmak istediğiniz var olan veritabanını bırakın.
2. Zaman içinde geri yüklenen veritabanını, bıraktığınız veritabanının adıyla yeniden adlandırın.

### <a name="drop-the-original-database"></a>Özgün veritabanını bırak

Azure portal, PowerShell veya Azure CLı kullanarak veritabanını bırakabilirsiniz.

Ayrıca, yönetilen örneğe doğrudan bağlanarak SQL Server Management Studio (SSMS) ve ardından aşağıdaki Transact-SQL (T-SQL) komutunu çalıştırarak veritabanını da bırakabilirsiniz:

```sql
DROP DATABASE WorldWideImporters;
```

Yönetilen örnekteki veritabanınıza bağlanmak için aşağıdaki yöntemlerden birini kullanın:

- [Bir Azure sanal makinesi aracılığıyla SSMS/Azure Data Studio](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Noktadan siteye](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Ortak uç nokta](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal, yönetilen örnekten veritabanını seçin ve **Sil**' i seçin.

   ![Azure portal kullanarak bir veritabanını silme](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Yönetilen bir örnekten var olan bir veritabanını bırakmak için aşağıdaki PowerShell komutunu kullanın:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Yönetilen bir örnekten var olan bir veritabanını bırakmak için aşağıdaki Azure CLı komutunu kullanın:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Yeni veritabanı adını özgün veritabanı adıyla eşleşecek şekilde değiştirin

Doğrudan yönetilen örneğe bağlanın ve SQL Server Management Studio başlatın. Ardından, aşağıdaki Transact-SQL (T-SQL) sorgusunu çalıştırın. Sorgu, geri yüklenen veritabanının adını üzerine yazmak istediğiniz bırakılmış veritabanının adını değiştirecek.

```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Yönetilen örnekteki veritabanınıza bağlanmak için aşağıdaki yöntemlerden birini kullanın:

- [Azure sanal makinesi](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Noktadan siteye](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Ortak uç nokta](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Sonraki adımlar

[Otomatikleştirilmiş yedeklemeler](sql-database-automated-backups.md)hakkında bilgi edinin.
