---
title: Silinmiş bir veri ambarını geri yükleme
description: Silinen bir Azure SQL veri ambarını geri yükleme kılavuzu.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: cb09b4808bd6d59d2f70e85d204ab8451d501cee
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692603"
---
# <a name="restore-a-deleted-azure-sql-data-warehouse"></a>Silinen bir Azure SQL veri ambarını geri yükleme

Bu makalede, Azure portal ve PowerShell kullanarak silinmiş bir SQL veri ambarını geri yüklemeyi öğreneceksiniz:

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**DTU kapasitenizi doğrulayın.** Her SQL veri ambarı, varsayılan DTU kotasına sahip bir SQL Server (örneğin, myserver.database.windows.net) tarafından barındırılır.  SQL Server 'ın geri yüklenmekte olan veritabanı için yeterli DTU kotasına sahip olduğunu doğrulayın. DTU 'yu nasıl hesaplayacağınızı veya daha fazla DTU isteğinde bulunmanız için bkz. [DTU kota değişikliği isteme][Request a DTU quota change].

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Silinen bir veri ambarını PowerShell aracılığıyla geri yükleme

Silinen bir SQL veri ambarını geri yüklemek için [restore-AzSqlDatabase][Restore-AzSqlDatabase] cmdlet 'ini kullanın. Karşılık gelen mantıksal sunucu da silinmişse, bu veri ambarını geri alamazsınız.

1. Başlamadan önce [Azure PowerShell][Install Azure PowerShell]' yi yüklediğinizden emin olun.
2. PowerShell’i açın.
3. Azure hesabınıza bağlanın ve hesabınızla ilişkili tüm abonelikleri listeleyin.
4. Geri yüklenecek silinen veri ambarını içeren aboneliği seçin.
5. Silinen belirli veri ambarını alın.
6. Silinen veri ambarını geri yükleme
    1. Silinen SQL veri ambarını farklı bir mantıksal sunucuya geri yüklemek için diğer mantıksal sunucu adını belirttiğinizden emin olun.  Bu mantıksal sunucu, farklı bir kaynak grubunda ve bölgede de olabilir.
    1. Farklı bir aboneliğe geri dönmek için [Taşı][Move] düğmesini kullanarak mantıksal sunucuyu başka bir aboneliğe taşıyın.
1. Geri yüklenen veri ambarının çevrimiçi olduğunu doğrulayın.
1. Geri yükleme tamamlandıktan sonra, [kurtarma sonrasında veritabanınızı yapılandırma][Configure your database after recovery]' yı izleyerek kurtarılan veri Ambarınızı yapılandırabilirsiniz.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
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

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Silinen veritabanını Azure portal kullanarak geri yükleme

1. [Azure portalında][Azure portal] oturum açın.
2. Silinen veri ambarınızın barındırıldığından SQL Server 'a gidin.
3. İçindekiler tablosunda **silinen veritabanları** simgesini seçin.

    ![Silinen veritabanları](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Geri yüklemek istediğiniz silinen SQL veri ambarını seçin.

    ![Silinen veritabanları seçin](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Yeni bir **veritabanı adı** belirtin ve **Tamam 'a** tıklayın

    ![Veritabanı adını belirtin](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Sonraki Adımlar
- [Mevcut bir veri ambarını geri yükleme][Restore an existing data warehouse]
- [Coğrafi yedekleme veri ambarından geri yükleme][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[support ticket]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket
[Move]:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
