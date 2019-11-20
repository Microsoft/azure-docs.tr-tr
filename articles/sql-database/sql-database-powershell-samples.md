---
title: Azure PowerShell betik örnekleri
description: Azure SQL Veritabanı sunucuları, elastik havuzlar, veritabanları ve güvenlik duvarları oluşturmanıza ve yönetmenize yardımcı olacak Azure PowerShell betik örnekleri.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: dd16753a9b057e441884b0a6a019701766aaa321
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821392"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure SQL Veritabanı için Azure PowerShell örnekleri

Azure SQL veritabanı, Azure PowerShell kullanarak veritabanlarınızı, örneklerinizi ve havuzlarınızı yapılandırmanızı sağlar.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici AZ PowerShell 1.4.0 veya üstünü gerektirir. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="single-database-and-elastic-poolstabsingle-database"></a>[Tek Veritabanı ve elastik havuzlar](#tab/single-database)

Aşağıdaki tablo, Azure SQL Veritabanı’na yönelik örnek Azure PowerShell betiklerinin bağlantılarını içerir.

| |  |
|---|---|
|**Tek veritabanları ve elastik havuzlar oluşturma ve yapılandırma**||
| [Tek bir veritabanı oluşturma ve veritabanı sunucusu güvenlik duvarı kuralını yapılandırma](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, tek bir Azure SQL veritabanı oluşturur ve sunucu düzeyinde güvenlik duvarı kuralı yapılandırır. |
| [Elastik havuzlar oluşturma ve havuza alınan veritabanlarını taşıma](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, Azure SQL Veritabanı elastik havuzları oluşturur, havuza alınmış veritabanlarını taşır ve işlem boyutlarını değiştirir.|
|**Coğrafi çoğaltmayı ve yük devretmeyi yapılandırma**||
| [Etkin coğrafi çoğaltmayı kullanarak tek bir veritabanını yapılandırma ve tek bir veritabanının yükünü devretme](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, tek bir Azure SQL veritabanı için etkin coğrafi çoğaltmayı yapılandırır ve ikincil bir çoğaltmaya yük devreder. |
| [Etkin coğrafi çoğaltmayı kullanarak havuza alınan veritabanını yapılandırma ve havuza alınmış veritabanının yükünü devretme](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, SQL elastik havuzundaki bir Azure SQL veritabanı için etkin coğrafi çoğaltmayı yapılandırır ve ikincil bir çoğaltmaya yük devreder. |
|**Yük devretme grubu yapılandırma**||
| [Tek bir veritabanı için yük devretme grubu yapılandırma](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği bir veritabanı ve bir yük devretme grubu oluşturur, veritabanını yük devretme grubuna ekler ve ikincil sunucuya yük devretmeyi sınar. | 
| [Elastik havuz için bir yük devretme grubu yapılandırma](scripts/sql-database-add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği bir veritabanı oluşturur, bir elastik havuza ekler, elastik havuzu yük devretme grubuna ekler ve ikincil sunucuya yük devretmeyi sınar. | 
|**Tek bir veritabanını ve elastik havuzu ölçekleme**||
| [Tek bir veritabanını ölçekleme](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, Azure SQL veritabanının performans ölçümlerini izler, veritabanını daha yüksek bir işlem boyutuna ölçekler ve performans ölçümlerinden birinde bir uyarı kuralı oluşturur. |
| [Elastik havuzu ölçekleme](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, Azure SQL Veritabanı elastik havuzu performans ölçümlerini izler, veritabanını daha yüksek bir işlem boyutuna ölçekler ve performans ölçümlerinden birinde bir uyarı kuralı oluşturur. |
| **Denetim ve tehdit algılama** |
| [Denetim ve tehdit algılamayı yapılandırma](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, Azure SQL veritabanı için denetim ve tehdit algılama ilkelerini yapılandırır. |
| **Veritabanını geri yükleme, kopyalama ve içeri aktarma**||
| [Veritabanını geri yükleme](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, coğrafi olarak yedekli bir yedeklemeden Azure SQL veritabanını geri yükler ve silinmiş bir Azure SQL veritabanını en son yedeklemeye geri yükler. |
| [Bir veritabanını yeni bir sunucuya kopyalama](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, yeni Azure SQL sunucusunda mevcut bir Azure SQL veritabanının kopyasını oluşturur. |
| [Bacpac dosyasından veritabanını içeri aktarma](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği bir veritabanını bacpac dosyasından Azure SQL sunucusuna içeri aktarır. |
| **Veritabanları arasında verileri eşitleme**||
| [SQL veritabanları arasında verileri eşitleme](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, Veri Eşitleme’yi birden çok Azure SQL veritabanı arasında eşitlenecek şekilde yapılandırır. |
| [SQL Veritabanı ile şirket içi SQL Server arasında verileri eşitleme](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, bir Azure SQL veritabanı ile SQL Server şirket içi veritabanı arasında eşitleme yapmak için Veri Eşitleme’yi yapılandırır. |
| [SQL Data Sync için eşitleme şemasını güncelleştirme](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, Veri Eşitleme için eşitleme şemasından öğeler ekler veya öğeleri kaldırır. |
|||

[Tek Veritabanı Azure PowerShell API 'si](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)hakkında daha fazla bilgi edinin.

## <a name="managed-instancetabmanaged-instance"></a>[Yönetilen örnek](#tab/managed-instance)

Aşağıdaki tabloda, Azure SQL veritabanı yönetilen örneği için örnek Azure PowerShell betiklerin bağlantıları yer almaktadır.

| |  |
|---|---|
|**Yönetilen örnekleri oluşturma ve yapılandırma**||
| [Yönetilen Örnek oluşturma ve yönetme](scripts/sql-database-create-configure-managed-instance-powershell.md) | Bu PowerShell betiği, Azure PowerShell kullanarak Yönetilen Örnek oluşturmayı ve yönetmeyi göstermektedir |
| [Azure Resource Manager şablonu kullanarak yönetilen bir örnek oluşturma ve yönetme](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, Azure PowerShell ve Azure Resource Manager şablonunu kullanarak yönetilen bir örnek oluşturmayı ve yönetmeyi gösterir.|
| [Veritabanını başka bir coğrafi bölgede yönetilen bir örneğe geri yükleme](scripts/sql-managed-instance-restore-geo-backup.md) | Bu PowerShell betiği, bir veritabanının yedeğini almak ve başka bir bölgeye geri yüklemektir. Bu, coğrafi geri yükleme olağanüstü durum kurtarma senaryosu olarak bilinir. |
| **Saydam Veri Şifrelemesi yapılandırma (TDE)**||
| [Yönetilen bir örnekteki Saydam Veri Şifrelemesi, Azure Key Vault kendi anahtarınızı kullanarak yönetin](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, Azure Key Vault bir anahtar kullanarak Azure SQL yönetilen örneği için Kendi Anahtarını Getir senaryosunda Saydam Veri Şifrelemesi (TDE) yapılandırır|
|**Yük devretme grubu yapılandırma**||
| [Yönetilen örnek için bir yük devretme grubu yapılandırma](scripts/sql-database-add-managed-instance-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, iki yönetilen örnek oluşturur, bunları bir yük devretme grubuna ekler ve ardından birincil yönetilen örnekten yük devretmeyi ikinci yönetilen örneğe sınar. | 
|||

[Yönetilen örnek Azure PowerShell API 'si](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)hakkında daha fazla bilgi edinin.

---

## <a name="additional-resources"></a>Ek kaynaklar

Bu sayfada listelenen örneklerde Azure SQL kaynaklarını oluşturmak ve yönetmek için [Azure SQL veritabanı cmdlet 'leri](/powershell/module/az.sql/) kullanılır. Sorgu çalıştırmaya yönelik ek cmdlet 'ler ve birçok veritabanı görevinin gerçekleştirilmesi [SqlServer](/powershell/module/sqlserver/) modülünde bulunur. Daha fazla bilgi için bkz. [PowerShell SQL Server](/sql/powershell/sql-server-powershell/).
