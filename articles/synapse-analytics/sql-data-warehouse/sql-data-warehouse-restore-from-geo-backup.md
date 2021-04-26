---
title: Ayrılmış bir SQL havuzunu coğrafi yedeklemeden geri yükleme
description: Azure SYNAPSE Analytics 'te adanmış bir SQL havuzunu coğrafi olarak geri yüklemek için nasıl yapılır Kılavuzu
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
ms.openlocfilehash: 4683bd84873506483209f4a0eb3751a1b163ed48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96449835"
---
# <a name="geo-restore-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış bir SQL havuzunu coğrafi geri yükleme

Bu makalede, adanmış SQL havuzunuzu (eski adıyla SQL DW) Azure portal ve PowerShell aracılığıyla bir coğrafi yedeklemeden geri yüklemeyi öğreneceksiniz.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU kapasitenizi doğrulayın.** Her adanmış SQL Havuzu (eski adıyla SQL DW), varsayılan DTU kotasına sahip bir [MANTıKSAL SQL Server](../../azure-sql/database/logical-servers.md) (örneğin, myserver.Database.Windows.net) tarafından barındırılır. SQL Server 'ın geri yüklenmekte olan veritabanı için yeterli DTU kotasına sahip olduğunu doğrulayın. DTU 'yu nasıl hesaplayacağınızı veya daha fazla DTU isteğinde bulunmanız için bkz. [DTU kota değişikliği isteme](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>PowerShell aracılığıyla Azure coğrafi bölgesinden geri yükleme

Bir coğrafi yedeklemeden geri yüklemek için [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ve [restore-azsqldatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet 'ini kullanın.

> [!NOTE]
> Gen2 'e coğrafi geri yükleme yapabilirsiniz! Bunu yapmak için, isteğe bağlı bir parametre olarak bir Gen2 ServiceObjectiveName (ör. DW1000 **c**) belirtin.
>

1. Başlamadan önce [Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)' yi yüklediğinizden emin olun.
2. PowerShell’i açın.
3. Azure hesabınıza bağlanın ve hesabınızla ilişkili tüm abonelikleri listeleyin.
4. Geri yüklenecek veri ambarını içeren aboneliği seçin.
5. Kurtarmak istediğiniz veri ambarını alın.
6. Veri ambarı için kurtarma isteği oluşturun.
7. Coğrafi olarak geri yüklenen veri ambarının durumunu doğrulayın.
8. Geri yükleme tamamlandıktan sonra veri Ambarınızı yapılandırmak için, [kurtarma sonrasında veritabanınızı yapılandırma]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)konusuna bakın.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
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

Ayrılmış bir SQL havuzunu (eski adıyla SQL DW) coğrafi yedeklemeden geri yüklemek için aşağıda açıklanan adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) hesabınızda oturum açın.
1. **ADANMıŞ SQL havuzları (eskıden SQL DW)** için arama yapın.

   ![Yeni DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. Ekle ' ye tıklayın ve **temel** bilgiler sekmesinde istenen bilgileri doldurun ve **İleri**' ye tıklayın.

   ![Temel Bilgiler](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

1. **Mevcut veri parametresini kullan** için **Yedekle** ' yi seçin ve aşağı kaydırma seçeneklerinden uygun yedeği seçin. **Gözden Geçir ve Oluştur**’a tıklayın.

   ![yedekleme](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

1. Veri ambarı geri yüklendikten sonra, **durumun** çevrimiçi olup olmadığını denetleyin.

## <a name="next-steps"></a>Sonraki Adımlar

- [Var olan ayrılmış bir SQL havuzunu geri yükleme (eski adıyla SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Silinen ayrılmış bir SQL havuzunu geri yükleme (eski adıyla SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
