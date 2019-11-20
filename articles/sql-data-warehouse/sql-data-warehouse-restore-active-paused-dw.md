---
title: Mevcut bir veri ambarını geri yükleme
description: Mevcut bir Azure SQL veri ambarını geri yüklemek için nasıl yapılır Kılavuzu.
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
ms.openlocfilehash: a73658510111df44c522d88ed5eceb7dcfa80d0d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685543"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Mevcut bir Azure SQL veri ambarını geri yükleme

Bu makalede, Azure portal ve PowerShell aracılığıyla mevcut bir SQL veri ambarını geri yüklemeyi öğreneceksiniz:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**DTU kapasitenizi doğrulayın.** Her SQL veri ambarı, varsayılan DTU kotasına sahip bir SQL Server (örneğin, myserver.database.windows.net) tarafından barındırılır. SQL Server 'ın geri yüklenmekte olan veritabanının kalan DTU kotasına sahip olduğunu doğrulayın. DTU 'yu nasıl hesaplayacağınızı veya daha fazla DTU isteğinde bulunmanız için bkz. [DTU kota değişikliği isteme][Request a DTU quota change].

## <a name="before-you-begin"></a>Başlamadan önce

1. [Azure PowerShell yüklediğinizden][Install Azure PowerShell]emin olun.
2. Geri yüklemek istediğiniz mevcut bir geri yükleme noktasına sahip olabilirsiniz. Yeni bir geri yükleme oluşturmak isterseniz, [Kullanıcı tanımlı yeni bir geri yükleme noktası oluşturmak için öğreticiye][the tutorial to create a new user-defined restore point]bakın.

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Mevcut bir veri ambarını PowerShell aracılığıyla geri yükleme

Mevcut bir veri ambarını geri yükleme noktasından geri yüklemek için [restore-AzSqlDatabase][Restore-AzSqlDatabase] PowerShell cmdlet 'ini kullanın.

1. PowerShell’i açın.

2. Azure hesabınıza bağlanın ve hesabınızla ilişkili tüm abonelikleri listeleyin.

3. Geri yüklenecek veritabanını içeren aboneliği seçin.

4. Veri ambarı için geri yükleme noktalarını listeleyin.

5. RestorePointCreationDate kullanarak istenen geri yükleme noktasını seçin.

6. [Restore-AzSqlDatabase][Restore-AzSqlDatabase] PowerShell cmdlet 'ini kullanarak veri ambarını istenen geri yükleme noktasına geri yükleyin.
        1. SQL veri ambarını farklı bir mantıksal sunucuya geri yüklemek için diğer mantıksal sunucu adını belirttiğinizden emin olun.  Bu mantıksal sunucu, farklı bir kaynak grubunda ve bölgede de olabilir.
        2. Farklı bir aboneliğe geri yüklemek için ' Taşı ' düğmesini kullanarak mantıksal sunucuyu başka bir aboneliğe taşıyın.

7. Geri yüklenen veri ambarının çevrimiçi olduğunu doğrulayın.

8. Geri yükleme tamamlandıktan sonra, [kurtarma sonrasında veritabanınızı yapılandırma][Configure your database after recovery]' yı izleyerek kurtarılan veri Ambarınızı yapılandırabilirsiniz.

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

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Mevcut bir veri ambarını Azure portal aracılığıyla geri yükleme

1. [Azure portalında][Azure portal] oturum açın.
2. Geri yüklemek istediğiniz SQL veri ambarı 'na gidin.
3. Genel Bakış dikey penceresinin en üstünde **geri yükle**' yi seçin.

    ![ Geri Yüklemeye Genel Bakış](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. **Otomatik geri yükleme noktaları** veya **Kullanıcı tanımlı geri yükleme noktaları**seçeneklerinden birini belirleyin. Veri ambarının hiçbir otomatik geri yükleme noktası yoksa, birkaç saat bekleyin veya geri yüklemeden önce Kullanıcı tanımlı geri yükleme noktası oluşturun. Kullanıcı tanımlı geri yükleme noktaları için, mevcut bir tane seçin veya yeni bir tane oluşturun. **Sunucu**için, farklı bir kaynak grubunda ve bölgede bir mantıksal sunucu seçebilir veya yeni bir tane oluşturabilirsiniz. Tüm parametreleri sağladıktan sonra, **gözden geçir + geri yükle**' ye tıklayın.

    ![Otomatik geri yükleme noktaları](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Sonraki Adımlar
- [Silinen bir veri ambarını geri yükleme][Restore a deleted data warehouse]
- [Coğrafi yedekleme veri ambarından geri yükleme][Restore from a geo-backup data warehouse]
 
<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[the tutorial to create a new user-defined restore point]:../sql-data-warehouse/sql-data-warehouse-restore-points.md
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
