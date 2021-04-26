---
title: Kullanıcı tanımlı geri yükleme noktaları
description: Adanmış SQL Havuzu (eski adıyla SQL DW) için geri yükleme noktası oluşturma.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 097a3132208eee98b3f95291e414263e637bc265
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545596"
---
# <a name="user-defined-restore-points-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Adanmış bir SQL Havuzu (eski adıyla SQL DW) için Kullanıcı tanımlı geri yükleme noktaları

Bu makalede, PowerShell ve Azure portal kullanarak Azure SYNAPSE Analytics 'te adanmış bir SQL Havuzu (eski adıyla SQL DW) için yeni bir Kullanıcı tanımlı geri yükleme noktası oluşturmayı öğreneceksiniz.

## <a name="create-user-defined-restore-points-through-powershell"></a>PowerShell aracılığıyla Kullanıcı tanımlı geri yükleme noktaları oluşturma

Kullanıcı tanımlı geri yükleme noktası oluşturmak için [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet 'ini kullanın.

1. Başlamadan önce [Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)' yi yüklediğinizden emin olun.
2. PowerShell’i açın.
3. Azure hesabınıza bağlanın ve hesabınızla ilişkili tüm abonelikleri listeleyin.
4. Geri yüklenecek veritabanını içeren aboneliği seçin.
5. Veri ambarınızın anlık bir kopyası için geri yükleme noktası oluşturun.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Tüm mevcut geri yükleme noktalarının listesine bakın.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Azure portal aracılığıyla Kullanıcı tanımlı geri yükleme noktaları oluşturma

Kullanıcı tanımlı geri yükleme noktaları, Azure portal aracılığıyla da oluşturulabilir.

1. [Azure Portal](https://portal.azure.com/) hesabınızda oturum açın.

2. İçin geri yükleme noktası oluşturmak istediğiniz adanmış SQL havuzuna (eski adıyla SQL DW) gidin.

3. Sol bölmeden **genel bakış** ' ı seçin, **+ Yeni geri yükleme noktası**' nı seçin. Yeni geri yükleme noktası düğmesi etkinleştirilmemişse, adanmış SQL havuzunun (eski adıyla SQL DW) duraklatıldığından emin olun.

    ![Yeni geri yükleme noktası](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Kullanıcı tanımlı geri yükleme noktanız için bir ad belirtin ve **Uygula**' ya tıklayın. Kullanıcı tanımlı geri yükleme noktalarının varsayılan saklama süresi yedi gündür.

    ![Geri yükleme noktasının adı](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Var olan ayrılmış bir SQL havuzunu geri yükleme (eski adıyla SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Silinen ayrılmış bir SQL havuzunu geri yükleme (eski adıyla SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Coğrafi yedekleme adanmış SQL havuzundan geri yükleme (eski adıyla SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
