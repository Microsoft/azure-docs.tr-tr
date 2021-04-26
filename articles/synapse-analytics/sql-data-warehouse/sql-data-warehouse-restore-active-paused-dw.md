---
title: Var olan ayrılmış bir SQL havuzunu geri yükleme (eski adıyla SQL DW)
description: Azure SYNAPSE Analytics 'te var olan ayrılmış bir SQL havuzunu geri yüklemek için nasıl yapılır Kılavuzu.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2ce552a13592c9d26ef70575f98b0b76ecc454ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592001"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Var olan ayrılmış bir SQL havuzunu geri yükleme (eski adıyla SQL DW)

Bu makalede, var olan bir adanmış SQL havuzunu (eski adıyla SQL DW) Azure portal ve PowerShell kullanarak geri yüklemeyi öğreneceksiniz.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU kapasitenizi doğrulayın.** Her havuz, varsayılan bir DTU kotasına sahip bir [MANTıKSAL SQL Server](../../azure-sql/database/logical-servers.md) (örneğin, myserver.Database.Windows.net) tarafından barındırılır. Sunucuda geri yüklenmekte olan veritabanının kalan DTU kotasının yeterli olduğunu doğrulayın. DTU 'yu nasıl hesaplayacağınızı veya daha fazla DTU isteğinde bulunmanız için bkz. [DTU kota değişikliği isteme](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Başlamadan önce

1. [Azure PowerShell yüklediğinizden](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)emin olun.
2. Geri yüklemek istediğiniz mevcut bir geri yükleme noktasına sahip olabilirsiniz. Yeni bir geri yükleme oluşturmak isterseniz, [Kullanıcı tanımlı yeni bir geri yükleme noktası oluşturmak için öğreticiye](sql-data-warehouse-restore-points.md)bakın.

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>Var olan ayrılmış bir SQL havuzunu (eski adıyla SQL DW) PowerShell aracılığıyla geri yükleme

Mevcut bir adanmış SQL havuzunu (eski adıyla SQL DW) geri yükleme noktasından geri yüklemek için [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet 'ini kullanın.

1. PowerShell’i açın.

2. Azure hesabınıza bağlanın ve hesabınızla ilişkili tüm abonelikleri listeleyin.

3. Geri yüklenecek veritabanını içeren aboneliği seçin.

4. Adanmış SQL havuzu için geri yükleme noktalarını listeleyin (eski adıyla SQL DW).

5. RestorePointCreationDate kullanarak istenen geri yükleme noktasını seçin.

6. Özel SQL havuzunu (eski adıyla SQL DW) [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet 'ini kullanarak istenen geri yükleme noktasına geri yükleyin.

    1. Ayrılmış SQL havuzunu (eski adıyla SQL DW) farklı bir sunucuya geri yüklemek için diğer sunucu adını belirttiğinizden emin olun.  Bu sunucu, farklı bir kaynak grubunda ve bölgede de olabilir.
    2. Farklı bir aboneliğe geri yüklemek için ' Taşı ' düğmesini kullanarak sunucuyu başka bir aboneliğe taşıyın.

7. Geri yüklenen adanmış SQL havuzunun (eski adıyla SQL DW) çevrimiçi olduğunu doğrulayın.

8. Geri yükleme tamamlandıktan sonra, [kurtarma sonrasında veritabanınızı yapılandırma](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)' yı izleyerek KURTARıLAN adanmış SQL havuzdan (eskı ADıYLA SQL DW) yapılandırabilirsiniz.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>Mevcut bir adanmış SQL havuzunu (eski adıyla SQL DW) Azure portal aracılığıyla geri yükleyin

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Geri yüklemek istediğiniz adanmış öğesine gidin.
3. Genel Bakış dikey penceresinin en üstünde **geri yükle**' yi seçin.

    ![ Geri Yüklemeye Genel Bakış](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. **Otomatik geri yükleme noktaları** veya **Kullanıcı tanımlı geri yükleme noktaları** seçeneklerinden birini belirleyin. Adanmış SQL havuzunda (eski adıyla SQL DW) otomatik geri yükleme noktaları yoksa, birkaç saat bekleyin veya geri yüklemeden önce Kullanıcı tanımlı geri yükleme noktası oluşturun. User-Defined geri yükleme noktaları için mevcut bir tane seçin veya yeni bir tane oluşturun. **Sunucu** için, farklı bir kaynak grubunda ve bölgede bir sunucu seçebilir veya yeni bir tane oluşturabilirsiniz. Tüm parametreleri sağladıktan sonra, **gözden geçir + geri yükle**' ye tıklayın.

    ![Otomatik Geri Yükleme Noktaları](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Sonraki Adımlar

- [Silinen ayrılmış bir SQL havuzunu geri yükleme (eski adıyla SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Coğrafi yedekleme adanmış SQL havuzundan geri yükleme (eski adıyla SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
