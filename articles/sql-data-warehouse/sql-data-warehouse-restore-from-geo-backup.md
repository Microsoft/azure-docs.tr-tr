---
title: Bir coğrafi yedeklemeden veri ambarını geri yükleme
description: Azure SQL veri ambarı coğrafi geri yükleme için nasıl yapılır Kılavuzu.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 69eb1221686da61868df8b06ed80664ae76d1627
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685508"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Azure SQL veri ambarını coğrafi olarak geri yükleme

Bu makalede, Azure portal ve PowerShell aracılığıyla veri Ambarınızı coğrafi yedeklemeden geri yüklemeyi öğreneceksiniz.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**DTU kapasitenizi doğrulayın.** Her SQL veri ambarı, varsayılan DTU kotasına sahip bir SQL Server (örneğin, myserver.database.windows.net) tarafından barındırılır. SQL Server 'ın geri yüklenmekte olan veritabanı için yeterli DTU kotasına sahip olduğunu doğrulayın. DTU 'yu nasıl hesaplayacağınızı veya daha fazla DTU isteğinde bulunmanız için bkz. [DTU kota değişikliği isteme][Request a DTU quota change].

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>PowerShell aracılığıyla Azure coğrafi bölgesinden geri yükleme

Bir coğrafi yedeklemeden geri yüklemek için [Get-AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup] ve [restore-azsqldatabase][Restore-AzSqlDatabase] cmdlet 'ini kullanın.

> [!NOTE]
> Gen2 'e coğrafi geri yükleme yapabilirsiniz! Bunu yapmak için, isteğe bağlı bir parametre olarak bir Gen2 ServiceObjectiveName (ör. DW1000**c**) belirtin.
>

1. Başlamadan önce [Azure PowerShell][Install Azure PowerShell]' yi yüklediğinizden emin olun.
2. PowerShell’i açın.
2. Azure hesabınıza bağlanın ve hesabınızla ilişkili tüm abonelikleri listeleyin.
3. Geri yüklenecek veri ambarını içeren aboneliği seçin.
4. Kurtarmak istediğiniz veri ambarını alın.
5. Veri ambarı için kurtarma isteği oluşturun.
6. Coğrafi olarak geri yüklenen veri ambarının durumunu doğrulayın.
7. Geri yükleme tamamlandıktan sonra veri Ambarınızı yapılandırmak için, [kurtarma sonrasında veritabanınızı yapılandırma][Configure your database after recovery]konusuna bakın.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

Kaynak veritabanı TDE etkinse Kurtarılan veritabanı TDE etkinleştirilir.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Azure portal aracılığıyla bir Azure coğrafi bölgesinden geri yükleme

Bir coğrafi yedeklemeden Azure SQL veri ambarı 'nı geri yüklemek için aşağıda açıklanan adımları izleyin:

1. [Azure Portal][Azure portal] hesabınızda oturum açın.
1. **+ Kaynak oluştur ' a** tıklayın ve SQL veri ambarı için arama yapın ve **Oluştur**' a tıklayın.

    ![Yeni DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. **Temel** bilgiler sekmesinde istenen bilgileri doldurun ve **İleri**' ye tıklayın.

    ![Temel Bilgiler](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. **Mevcut veri parametresini kullan** için **Yedekle** ' yi seçin ve aşağı kaydırma seçeneklerinden uygun yedeği seçin. **Gözden geçir + oluştur**' a tıklayın.
 
   ![yedekleme](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. Veri ambarı geri yüklendikten sonra, **durumun** çevrimiçi olup olmadığını denetleyin.

## <a name="next-steps"></a>Sonraki Adımlar
- [Mevcut bir veri ambarını geri yükleme][Restore an existing data warehouse]
- [Silinen bir veri ambarını geri yükleme][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
