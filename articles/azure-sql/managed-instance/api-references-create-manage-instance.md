---
title: Azure SQL yönetilen örneği için yönetim API 'SI başvurusu
description: Azure SQL yönetilen örneği 'nin yönetilen örneklerini oluşturma ve yapılandırma hakkında bilgi edinin.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 148b24aea42072f1901c76c7a09a126340ef9951
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784380"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Azure SQL Yönetilen Örneği için Yönetilen API başvurusu
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure portal, PowerShell, Azure CLı, REST API ve Transact-SQL ' i kullanarak Azure SQL yönetilen örneği 'nin yönetilen örneklerini oluşturabilir ve yapılandırabilirsiniz. Bu makalede, yönetilen örnekleri oluşturmak ve yapılandırmak için kullanabileceğiniz işlevlere ve API 'ye genel bir bakış bulabilirsiniz.

## <a name="azure-portal-create-a-managed-instance"></a>Azure portal: yönetilen örnek oluşturma

Yönetilen bir örnek oluşturmayı gösteren bir hızlı başlangıç için bkz. [hızlı başlangıç: yönetilen örnek oluşturma](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: yönetilen örnekler oluşturma ve yapılandırma

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Azure PowerShell ile yönetilen örnekler oluşturup yönetmek için aşağıdaki PowerShell cmdlet 'lerini kullanın. PowerShell 'i yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure PowerShell modülünü yüklemek](/powershell/azure/install-az-ps).

> [!TIP]
> PowerShell örnek betikleri için bkz. [hızlı başlangıç betiği: PowerShell kitaplığı kullanarak yönetilen örnek oluşturma](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell).

| Cmdlet | Açıklama |
| --- | --- |
|[New-Azsqlınstance](/powershell/module/az.sql/new-azsqlinstance)|Yönetilen bir örnek oluşturur. |
|[Get-Azsqlınstance](/powershell/module/az.sql/get-azsqlinstance)|Yönetilen bir örnek hakkında bilgi döndürür.|
|[Set-Azsqlınstance](/powershell/module/az.sql/set-azsqlinstance)|Yönetilen bir örnek için özellikleri ayarlar.|
|[Remove-Azsqlınstance](/powershell/module/az.sql/remove-azsqlinstance)|Yönetilen bir örneği kaldırır.|
|[Get-Azsqlınstanceoperation](/powershell/module/az.sql/get-azsqlinstanceoperation)|Yönetilen örnek veya belirli bir işlem üzerinde gerçekleştirilen yönetim işlemlerinin bir listesini alır.|
|[Stop-Azsqlınstanceoperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)|Yönetilen örnekte gerçekleştirilen belirli yönetim işlemini iptal eder.|
|[New-Azsqlınstancedatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|SQL yönetilen örnek veritabanı oluşturur.|
|[Get-Azsqlınstancedatabase](/powershell/module/az.sql/get-azsqlinstancedatabase)|SQL yönetilen örnek veritabanı hakkında bilgi döndürür.|
|[Remove-Azsqlınstancedatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|SQL yönetilen örnek veritabanını kaldırır.|
|[Restore-Azsqlınstancedatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|SQL yönetilen örnek veritabanını geri yükler.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLı: yönetilen örnekler oluşturma ve yapılandırma

[Azure CLI](/cli/azure)ile yönetilen örnekler oluşturmak ve yapılandırmak IÇIN, [SQL yönetilen örneği IÇIN aşağıdaki Azure CLI komutlarını](/cli/azure/sql/mi)kullanın. CLı 'yi tarayıcınızda çalıştırmak veya macOS, Linux veya Windows 'a [yüklemek](/cli/azure/install-azure-cli) için [Azure Cloud Shell](../../cloud-shell/overview.md) kullanın.

> [!TIP]
> Azure CLı hızlı başlangıcı için bkz. [Azure CLI kullanarak SQL yönetilen örneği Ile çalışma](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Açıklama |
| --- | --- |
|[az SQL mı oluştur](/cli/azure/sql/mi#az_sql_mi_create) |Yönetilen bir örnek oluşturur.|
|[az SQL mı listesi](/cli/azure/sql/mi#az_sql_mi_list)|Kullanılabilir yönetilen örnekleri listeler.|
|[az SQL mı Show](/cli/azure/sql/mi#az_sql_mi_show)|Yönetilen bir örnek için ayrıntıları alır.|
|[az SQL mı güncelleştirmesi](/cli/azure/sql/mi#az_sql_mi_update)|Yönetilen bir örneği güncelleştirir.|
|[az SQL mi Delete](/cli/azure/sql/mi#az_sql_mi_delete)|Yönetilen bir örneği kaldırır.|
|[az SQL mı op List](/cli/azure/sql/mi/op#az_sql_mi_op_list)|Yönetilen örnek üzerinde gerçekleştirilen yönetim işlemlerinin bir listesini alır.|
|[az SQL mı op Show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|Yönetilen örnek üzerinde gerçekleştirilen belirli yönetim işlemini alır.|
|[az SQL mı op Cancel](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|Yönetilen örnekte gerçekleştirilen belirli yönetim işlemini iptal eder.|
|[az SQL mıdb Create](/cli/azure/sql/midb#az_sql_midb_create) |Yönetilen bir veritabanı oluşturur.|
|[az SQL mıdb List](/cli/azure/sql/midb#az_sql_midb_list)|Kullanılabilir yönetilen veritabanlarını listeler.|
|[az SQL mıdb restore](/cli/azure/sql/midb#az_sql_midb_restore)|Yönetilen bir veritabanını geri yükler.|
|[az SQL mıdb Delete](/cli/azure/sql/midb#az_sql_midb_delete)|Yönetilen bir veritabanını kaldırır.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: örnek veritabanları oluşturma ve yapılandırma

Yönetilen örnek oluşturulduktan sonra örnek veritabanları oluşturup yapılandırmak için aşağıdaki T-SQL komutlarını kullanın. Bu komutları, Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs)veya BIR sunucuya bağlanabilecek ve Transact-SQL komutlarını geçirebilmeniz gereken diğer programları kullanarak verebilirsiniz.

> [!TIP]
> Microsoft Windows üzerinde SQL Server Management Studio kullanarak yönetilen bir örneğe nasıl yapılandırılacağını ve bağlanalınacağını gösteren hızlı başlangıç için bkz. [hızlı başlangıç: Azure SQL yönetilen örneğine bağlanmak Için Azure VM 'Yi yapılandırma](connect-vm-instance-configure.md) ve [hızlı başlangıç: ŞIRKET Içinden Azure SQL yönetilen örneği ile noktadan siteye bağlantı yapılandırma](point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Transact-SQL ' y i kullanarak yönetilen bir örnek oluşturamaz veya silemezsiniz.

| Komut | Açıklama |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|SQL yönetilen örneği 'nde yeni bir örnek veritabanı oluşturur. Yeni bir veritabanı oluşturmak için ana veritabanına bağlı olmanız gerekir.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |SQL yönetilen örneğindeki bir örnek veritabanını değiştirir.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: yönetilen örnekler oluşturma ve yapılandırma

Yönetilen örnekler oluşturmak ve yapılandırmak için bu REST API isteklerini kullanın.

| Komut | Açıklama |
| --- | --- |
|[Yönetilen örnekler-oluştur veya güncelleştir](/rest/api/sql/managedinstances/createorupdate)|Yönetilen bir örnek oluşturur veya güncelleştirir.|
|[Yönetilen örnekler-Sil](/rest/api/sql/managedinstances/delete)|Yönetilen bir örneği siler.|
|[Yönetilen örnekler-al](/rest/api/sql/managedinstances/get)|Yönetilen bir örnek alır.|
|[Yönetilen örnekler-liste](/rest/api/sql/managedinstances/list)|Bir abonelikteki yönetilen örneklerin listesini döndürür.|
|[Yönetilen örnekler-kaynak grubuna göre Listele](/rest/api/sql/managedinstances/listbyresourcegroup)|Bir kaynak grubundaki yönetilen örneklerin listesini döndürür.|
|[Yönetilen örnekler-güncelleştirme](/rest/api/sql/managedinstances/update)|Yönetilen bir örneği güncelleştirir.|
|[Yönetilen örnek Işlemleri-yönetilen örneğe göre listeleme](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Yönetilen örnek üzerinde gerçekleştirilen yönetim işlemlerinin bir listesini alır.|
|[Yönetilen örnek Işlemleri-Get](/rest/api/sql/managedinstanceoperations/get)|Yönetilen örnek üzerinde gerçekleştirilen belirli yönetim işlemini alır.|
|[Yönetilen örnek Işlemleri-Iptal](/rest/api/sql/managedinstanceoperations/cancel)|Yönetilen örnekte gerçekleştirilen belirli yönetim işlemini iptal eder.|

## <a name="next-steps"></a>Sonraki adımlar

- SQL Server bir veritabanını Azure 'a geçirme hakkında bilgi edinmek için bkz. [Azure SQL veritabanı 'Na geçiş](../database/migrate-to-database-from-sql-server.md).
- Desteklenen özellikler hakkında bilgi için bkz. [Özellikler](../database/features-comparison.md).