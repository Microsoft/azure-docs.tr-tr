---
title: Silinen ayrılmış bir SQL havuzunu geri yükleme (eski adıyla SQL DW)
description: Azure SYNAPSE Analytics 'te silinmiş adanmış bir SQL havuzunu geri yükleme kılavuzu.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7264791654bf1b646338f0d429930b63f0cc3a06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96449929"
---
# <a name="restore-a-deleted-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Silinen adanmış bir SQL havuzunu (eski adıyla SQL DW) Azure SYNAPSE Analytics 'e geri yükleme

Bu makalede, Azure portal veya PowerShell kullanarak adanmış bir SQL havuzunu (eski adıyla SQL DW) geri yüklemeyi öğreneceksiniz.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU kapasitenizi doğrulayın.** Her adanmış SQL Havuzu (eski adıyla SQL DW), varsayılan DTU kotasına sahip bir [MANTıKSAL SQL Server](../../azure-sql/database/logical-servers.md) (örneğin, myserver.Database.Windows.net) tarafından barındırılır.  Sunucuda geri yüklenmekte olan veritabanı için kalan DTU kotasının yeterli olduğunu doğrulayın. DTU 'yu nasıl hesaplayacağınızı veya daha fazla DTU isteğinde bulunmanız için bkz. [DTU kota değişikliği isteme](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Silinen bir veri ambarını PowerShell aracılığıyla geri yükleme

Silinen ayrılmış bir SQL havuzunu (eski adıyla SQL DW) geri yüklemek için [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet 'ini kullanın. Karşılık gelen sunucu da silinmişse, bu veri ambarını geri alamazsınız.

1. Başlamadan önce [Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)' yi yüklediğinizden emin olun.
2. PowerShell’i açın.
3. Azure hesabınıza bağlanın ve hesabınızla ilişkili tüm abonelikleri listeleyin.
4. Geri yüklenecek silinmiş adanmış SQL havuzunu (eski adıyla SQL DW) içeren aboneliği seçin.
5. Silinen belirli veri ambarını alın.
6. Silinen adanmış SQL havuzunu geri yükle (eski adıyla SQL DW)
    1. Silinen adanmış SQL havuzunu (eski adıyla SQL DW) farklı bir sunucuya geri yüklemek için diğer sunucu adını belirttiğinizden emin olun.  Bu sunucu, farklı bir kaynak grubunda ve bölgede de olabilir.
    1. Farklı bir aboneliğe geri dönmek için [Taşı](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) düğmesini kullanarak sunucuyu başka bir aboneliğe taşıyın.
7. Geri yüklenen veri ambarının çevrimiçi olduğunu doğrulayın.
8. Geri yükleme tamamlandıktan sonra, [kurtarma sonrasında veritabanınızı yapılandırma](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)' yı izleyerek kurtarılan veri Ambarınızı yapılandırabilirsiniz.

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Silinen veritabanını Azure portal kullanarak geri yükleme

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Silinen veri ambarınızın barındırıldığı sunucuya gidin.
3. İçindekiler tablosunda **silinen veritabanları** simgesini seçin.

    ![Silinen veritabanları](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Geri yüklemek istediğiniz silinen Azure SYNAPSE Analytics 'i seçin.

    ![Silinen Veritabanları'nı seçin](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Yeni bir **veritabanı adı** belirtin ve **Tamam 'a** tıklayın

    ![Veritabanı adını belirtin](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Sonraki Adımlar

- [Var olan ayrılmış bir SQL havuzunu geri yükleme (eski adıyla SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Coğrafi yedekleme adanmış SQL havuzundan geri yükleme (eski adıyla SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
