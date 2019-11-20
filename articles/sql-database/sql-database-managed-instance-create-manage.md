---
title: Yönetilen örnek için yönetim API 'SI başvurusu
description: Azure SQL veritabanı yönetilen örnekleri oluşturma ve yönetme hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 713217a933c646cc4d04759f5697bbc0312827ce
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823413"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Azure SQL veritabanı yönetilen örnekleri için yönetilen API başvurusu

Azure portal, PowerShell, Azure CLı, REST API ve Transact-SQL kullanarak Azure SQL veritabanı yönetilen örnekleri oluşturabilir ve yönetebilirsiniz. Bu makalede, yönetilen örnek oluşturmak ve yapılandırmak için kullanabileceğiniz işlevlere ve API 'ye genel bir bakış bulabilirsiniz.

## <a name="azure-portal-create-a-managed-instance"></a>Azure portal: yönetilen örnek oluşturma

Azure SQL veritabanı yönetilen örneği oluşturmayı gösteren bir hızlı başlangıç için bkz. [hızlı başlangıç: Azure SQL veritabanı yönetilen örneği oluşturma](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: yönetilen örnekler oluşturma ve yönetme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Azure PowerShell ile yönetilen örnekler oluşturup yönetmek için aşağıdaki PowerShell cmdlet 'lerini kullanın. PowerShell 'i yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-az-ps).

> [!TIP]
> PowerShell örnek betikleri için bkz. [hızlı başlangıç betiği: PowerShell kitaplığı kullanarak Azure SQL yönetilen örneği oluşturma](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Açıklama |
| --- | --- |
|[New-Azsqlınstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Azure SQL veritabanı yönetilen örneği oluşturur |
|[Get-Azsqlınstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Azure SQL yönetilen örneği hakkında bilgi döndürür|
|[Set-Azsqlınstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Azure SQL veritabanı yönetilen örneği için özellikleri ayarlar|
|[Remove-Azsqlınstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Azure SQL yönetilen veritabanı örneğini kaldırır|
|[New-Azsqlınstancedatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Azure SQL veritabanı yönetilen örnek veritabanı oluşturur|
|[Get-Azsqlınstancedatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Azure SQL yönetilen örnek veritabanı hakkında bilgi döndürür|
|[Remove-Azsqlınstancedatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Azure SQL yönetilen veritabanı örneği veritabanını kaldırır|
|[Restore-Azsqlınstancedatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Azure SQL yönetilen veritabanı örneği veritabanını geri yükler|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLı: yönetilen örnekler oluşturma ve yönetme

[Azure CLI](/cli/azure)ile yönetilen örnekler oluşturup yönetmek Için AŞAĞıDAKI [Azure CLI SQL yönetilen örnek](/cli/azure/sql/mi) komutlarını kullanın. CLI’yi tarayıcınızda çalıştırmak için [Cloud Shell](/azure/cloud-shell/overview) kullanın veya macOS, Linux ya da Windows’da [yükleyin](/cli/azure/install-azure-cli).

> [!TIP]
> Azure CLı hızlı başlangıcı için bkz. [Azure CLI kullanarak SQL yönetilen örneği Ile çalışma](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Açıklama |
| --- | --- |
|[az SQL mı oluştur](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Yönetilen bir örnek oluşturur|
|[az SQL mı listesi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Kullanılabilir yönetilen örnekleri listeler|
|[az SQL mı Show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Yönetilen bir örnek için ayrıntıları alın|
|[az SQL mı güncelleştirmesi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Yönetilen bir örneği güncelleştirir|
|[az SQL mi Delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Yönetilen bir örneği kaldırır|
|[az SQL mıdb Create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Yönetilen bir veritabanı oluşturur|
|[az SQL mıdb List](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Kullanılabilir yönetilen veritabanlarını listeler|
|[az SQL mıdb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Yönetilen bir veritabanını geri yükleme|
|[az SQL mıdb Delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Yönetilen bir veritabanını kaldırır|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: örnek veritabanları oluşturma ve yönetme

Yönetilen örnek oluşturulduktan sonra örnek veritabanı oluşturmak ve yönetmek için aşağıdaki T-SQL komutlarını kullanın. Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio) [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is)kullanarak bu komutları verebilirsiniz. [Visual Studio Code](https://code.visualstudio.com/docs)veya BIR Azure SQL veritabanı sunucusuna bağlanabilecek ve Transact-SQL komutlarını geçirebilirler.

> [!TIP]
> Microsoft Windows üzerinde SQL Server Management Studio kullanarak yönetilen bir örneği yapılandırıp bu sunucuya bağlanmanız gerektiğini gösteren hızlı başlangıç için bkz. [hızlı başlangıç: Azure SQL veritabanı yönetilen örneğine bağlanmak Için Azure VM 'Yi yapılandırma](sql-database-managed-instance-configure-vm.md) ve [hızlı başlangıç: Şirket içinden Azure SQL veritabanı yönetilen örneği ile noktadan siteye bağlantı yapılandırın](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Transact-SQL ' y i kullanarak yönetilen bir örnek oluşturamaz veya silemezsiniz.

| Komut | Açıklama |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Yeni bir yönetilen örnek veritabanı oluşturur. Yeni bir veritabanı oluşturmak için ana veritabanına bağlı olmanız gerekir.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Azure SQL yönetilen örnek veritabanını değiştirir.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: yönetilen örnekler oluşturma ve yönetme

Yönetilen örnekler oluşturmak ve yönetmek için bu REST API isteklerini kullanın.

| Komut | Açıklama |
| --- | --- |
|[Yönetilen örnekler-oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Yönetilen bir örnek oluşturur veya güncelleştirir.|
|[Yönetilen örnekler-Sil](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Yönetilen bir örneği siler.|
|[Yönetilen örnekler-al](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Yönetilen bir örnek alır.|
|[Yönetilen örnekler-liste](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Bir abonelikteki yönetilen örneklerin listesini döndürür.|
|[Yönetilen örnekler-kaynak grubuna göre Listele](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Bir kaynak grubundaki yönetilen örneklerin listesini döndürür.|
|[Yönetilen örnekler-güncelleştirme](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Yönetilen bir örneği güncelleştirir.|

## <a name="next-steps"></a>Sonraki adımlar

- SQL Server bir veritabanını Azure 'a geçirme hakkında bilgi edinmek için bkz. [Azure SQL veritabanı 'Na geçiş](sql-database-single-database-migrate.md).
- Desteklenen özellikler hakkında bilgi edinmek için bkz. [Özellikler](sql-database-features.md).
