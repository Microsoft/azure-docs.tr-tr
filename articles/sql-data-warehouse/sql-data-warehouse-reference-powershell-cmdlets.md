---
title: PowerShell cmdlet'leri
description: Azure SQL veri ambarı için en iyi PowerShell cmdlet 'lerini, bir veritabanını duraklatma ve devam etme dahil olmak üzere bulun.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b36a64bb82449ace7acc1de0b3c2bc7c5efebe70
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685549"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>SQL veri ambarı için PowerShell cmdlet 'leri ve REST API 'Leri
Birçok SQL veri ambarı yönetim görevi Azure PowerShell cmdlet 'leri ya da REST API 'Leri kullanılarak yönetilebilir.  Aşağıda, SQL veri ambarınızdaki ortak görevleri otomatikleştirmek için PowerShell komutlarının nasıl kullanılacağına ilişkin bazı örnekler verilmiştir.  Bazı iyi REST örnekleri için, [rest ile ölçeklenebilirliği yönetme][Manage scalability with REST]makalesine bakın.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell cmdlet 'lerini kullanmaya başlayın
1. Windows PowerShell'i açın.
2. PowerShell komut isteminde bu komutları çalıştırarak Azure Resource Manager oturum açın ve aboneliğinizi seçin.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>SQL veri ambarı örneğini Duraklat
"Server01" adlı sunucuda barındırılan "Database02" adlı bir veritabanını duraklatın.  Sunucu, "ResourceGroup1" adlı bir Azure Kaynak grubunda bulunur.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Bir çeşitleme, bu örnek, alınan nesneyi [askıya al-AzSqlDatabase][Suspend-AzSqlDatabase]' A yöneltin.  Sonuç olarak, veritabanı duraklatılır. Son komut sonuçları gösterir.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>SQL veri ambarı örneğini Başlat
"Server01" adlı sunucuda barındırılan "Database02" adlı bir veritabanının işlemini sürdürür. Sunucu, "ResourceGroup1" adlı bir kaynak grubunda bulunur.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Bu örnek, "ResourceGroup1" adlı bir kaynak grubunda yer alan "Server01" adlı bir sunucudan "Database02" adlı bir veritabanını alır. It [-AzSqlDatabase][Resume-AzSqlDatabase]için alınan nesneyi kanallar.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Sunucunuz foo.database.windows.net ise PowerShell cmdlet 'lerinde-ServerName olarak "foo" kullanın.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Desteklenen diğer PowerShell cmdlet 'leri
Bu PowerShell cmdlet 'leri Azure SQL veri ambarı ile desteklenir.

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoint][Get-AzSqlDatabaseRestorePoint]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Özgeçmişi-AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla PowerShell örneği için bkz.:

* [PowerShell kullanarak SQL veri ambarı oluşturma][Create a SQL Data Warehouse using PowerShell]
* [Veritabanı geri yükleme][Database restore]

PowerShell ile otomatikleştirilebilen diğer görevler için bkz. [Azure SQL veritabanı cmdlet 'leri][Azure SQL Database Cmdlets]. Azure SQL veri ambarı için tüm Azure SQL veritabanı cmdlet 'lerinin desteklenmediğini unutmayın.  REST ile otomatikleştirilen görevlerin bir listesi için bkz. [Azure SQL veritabanı Için işlemler][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/az.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup
[Get-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint
[New-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase
[Remove-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Resume-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase
<!-- It appears that Select-AzSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase
[Suspend-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
