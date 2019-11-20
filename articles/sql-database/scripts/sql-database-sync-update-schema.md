---
title: PowerShell örneği-SQL Data Sync eşitleme şemasını Güncelleştir
description: SQL Data Sync için eşitleme şemasını güncelleştirmek için Azure PowerShell örnek betiği
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: d8c4f21630afa4a57d3c3886819ec2842e3cb681
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691473"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Mevcut bir eşitleme grubunda eşitleme şemasını güncelleştirmek için PowerShell kullanma

Bu PowerShell örneği, mevcut bir SQL Data Sync eşitleme grubundaki eşitleme şemasını güncelleştirir. Birden çok tabloyu eşitlerken bu betik, eşitleme şemasını verimli bir şekilde güncelleştirmenize yardımcı olur. Bu örnek, GitHub’da **UpdateSyncSchema.ps1** olarak kullanılabilir olan [UpdateSyncSchema](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1) betiğinin kullanımını göstermektedir.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici AZ PowerShell 1.4.0 veya üstünü gerektirir. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

SQL Data Sync hizmetine genel bakış için bkz. [Azure SQL Data Sync ile birden fazla bulut ve şirket içi veritabanı arasında veri eşitleme](../sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync Şu anda Azure SQL veritabanı yönetilen **örneğini desteklemez.**

## <a name="sample-script"></a>Örnek betik

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Örnek 1 - Eşitleme şemasına tüm tabloları ekleme

Aşağıdaki örnek, veritabanı şemasını yeniler ve eşitleme şemasının hub veritabanındaki tüm geçerli tabloları ekler.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Örnek 2 - Tabloları ve sütunları ekleme ve kaldırma

Aşağıdaki örnek, eşitleme şemasına `[dbo].[Table1]` ve `[dbo].[Table2].[Column1]` ekler ve `[dbo].[Table3]` öğesini kaldırır.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Betik parametreleri

**UpdateSyncSchema** betiği aşağıdaki parametrelere sahiptir:

| Parametre | Notlar |
|---|---|
| $SubscriptionId | Eşitleme grubunun oluşturulduğu abonelik. |
| $ResourceGroupName | Eşitleme grubunun oluşturulduğu kaynak grubu.|
| $ServerName | Hub veritabanının sunucu adı.|
| $DatabaseName | Hub veritabanı adı. |
| $SyncGroupName | Eşitleme grubu adı. |
| $MemberName | Hub veritabanı yerine, eşitleme üyesinden veritabanı şemasını yüklemek istiyorsanız üye adını belirtin. Hub’dan veritabanı şemasını yüklemek istiyorsanız bu parametreyi boş bırakın. |
| $TimeoutInSeconds | Betik, veritabanı şemasını yenilediğinde gerçekleşen zaman aşımı. Varsayılan değer 900 saniyedir. |
| $RefreshDatabaseSchema | Betiğin, veritabanı şemasını yenilemesi gerekip gerekmediğini belirtin. Önceki yapılandırmadan veritabanı şemanız değiştiyse (örneğin, yeni bir tablo veya yeni bir sütun eklediyseniz), yeniden yapılandırmadan önce şemayı yenilemeniz gerekir. Varsayılan değer false’tur. |
| $AddAllTables | Bu değer true olursa, tüm geçerli tablolar ve sütunlar eşitleme şemasına eklenir. $TablesAndColumnsToAdd ve $TablesAndColumnsToRemove değerleri yoksayılır. |
| $TablesAndColumnsToAdd | Eşitleme şemasına eklenecek tabloları veya sütunları belirtin. Her bir tablo veya sütun adının şema adıyla tam olarak ayrılmış olması gerekir. Örneğin: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Birden çok tablo veya sütun adı belirtilebilir ve virgül (,) ile ayrılabilir. |
| $TablesAndColumnsToRemove | Eşitleme şemasından kaldırılacak tabloları veya sütunları belirtin. Her bir tablo veya sütun adının şema adıyla tam olarak ayrılmış olması gerekir. Örneğin: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Birden çok tablo veya sütun adı belirtilebilir ve virgül (,) ile ayrılabilir. |
|||

## <a name="script-explanation"></a>Betik açıklaması

**UpdateSyncSchema** betiği aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Bir eşitleme grubu hakkındaki bilgileri döndürür. |
| [Update-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Bir eşitleme grubunu güncelleştirir. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Bir eşitleme üyesi hakkındaki bilgileri döndürür. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Bir eşitleme şeması hakkındaki bilgileri döndürür. |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Bir eşitleme şemasını güncelleştirir. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Ek SQL Veritabanı PowerShell betiği örnekleri [Azure SQL Veritabanı PowerShell betikleri](../sql-database-powershell-samples.md) içinde bulunabilir.

SQL Data Sync hakkında daha fazla bilgi için bkz.:

-   Genel Bakış- [Azure SQL Data Sync ile birden çok bulut ve şirket içi veritabanı arasında veri eşitleme](../sql-database-sync-data.md)
-   Veri eşitlemesini ayarlama
    - Portalda- [öğreticide, Azure SQL veritabanı ve şirket içi SQL Server arasında veri eşitlemek için SQL Data Sync ayarlama](../sql-database-get-started-sql-data-sync.md)
    - PowerShell ile
        -  [PowerShell kullanarak birden çok Azure SQL veritabanı arasında eşitleme](sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell kullanarak bir Azure SQL Veritabanı ile SQL Server şirket içi veritabanı arasında eşitleme](sql-database-sync-data-between-azure-onprem.md)
-   Veri eşitleme Aracısı- [Azure SQL Data Sync Için veri eşitleme Aracısı](../sql-database-data-sync-agent.md)
-   En iyi uygulamalar- [Azure SQL Data Sync Için en iyi yöntemler](../sql-database-best-practices-data-sync.md)
-   İzleyici- [Azure izleyici günlükleri ile izleyici SQL Data Sync](../sql-database-sync-monitor-oms.md)
-   Sorun giderme- [Azure SQL Data Sync sorunlarını giderme](../sql-database-troubleshoot-data-sync.md)
-   Eşitleme şemasını güncelleştirme
    -   Transact-SQL- [Azure SQL Data Sync şema değişikliklerinin çoğaltılmasını otomatikleştirin](../sql-database-update-sync-schema.md)

SQL Veritabanı hakkında daha fazla bilgi için bkz.:

-   [SQL Veritabanı'na Genel Bakış](../sql-database-technical-overview.md)
-   [Veritabanı Yaşam Döngüsü Yönetimi](https://msdn.microsoft.com/library/jj907294.aspx)
