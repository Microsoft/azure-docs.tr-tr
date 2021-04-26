---
title: Azure PowerShell betik örnekleri
description: Azure SQL veritabanı ve Azure SQL yönetilen örnek kaynakları oluşturmanıza ve yönetmenize yardımcı olması için Azure PowerShell betik örnekleri kullanın.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 7d732f0e42bc5eef8f72a2b9ce2adee28deb9a67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690983"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL veritabanı ve Azure SQL yönetilen örneği için Azure PowerShell örnekleri
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve Azure SQL yönetilen örneği, Azure PowerShell kullanarak veritabanlarınızı, örneklerinizi ve havuzlarınızı yapılandırmanızı sağlar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici AZ PowerShell 1.4.0 veya üstünü gerektirir. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="azure-sql-database"></a>[Azure SQL Veritabanı](#tab/single-database)

Aşağıdaki tablo, Azure SQL Veritabanı’na yönelik örnek Azure PowerShell betiklerinin bağlantılarını içerir.

|Bağlantı|Description|
|---|---|
|**Tek veritabanları ve elastik havuzlar oluşturma ve yapılandırma**||
| [Tek bir veritabanı oluşturma ve sunucu düzeyinde güvenlik duvarı kuralı yapılandırma](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği tek bir veritabanı oluşturur ve sunucu düzeyinde bir IP güvenlik duvarı kuralı yapılandırır. |
| [Elastik havuzlar oluşturma ve havuza alınan veritabanlarını taşıma](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği elastik havuzlar oluşturur, havuza alınmış veritabanlarını taşımaktadır ve işlem boyutlarını değiştirir.|
|**Coğrafi çoğaltmayı ve yük devretmeyi yapılandırma**||
| [Etkin coğrafi çoğaltma kullanarak tek bir veritabanını yapılandırma ve yük devretme](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, tek bir veritabanı için etkin Coğrafi çoğaltmayı yapılandırır ve ikincil çoğaltmaya yük devreder. |
| [Etkin coğrafi çoğaltma kullanarak havuza alınmış bir veritabanını yapılandırma ve yük devretme](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, elastik havuzdaki bir veritabanı için etkin Coğrafi çoğaltmayı yapılandırır ve ikincil çoğaltmaya yük devreder. |
|**Yük devretme grubu yapılandırma**||
| [Tek bir veritabanı için yük devretme grubu yapılandırma](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği bir veritabanı ve yük devretme grubu oluşturur, veritabanını yük devretme grubuna ekler ve ikincil sunucuya yük devretmeyi sınar. |
| [Elastik havuz için bir yük devretme grubu yapılandırma](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği bir veritabanı oluşturur, bir elastik havuza ekler, elastik havuzu yük devretme grubuna ekler ve ikincil sunucuya yük devretmeyi sınar. |
|**Tek bir veritabanını ve elastik havuzu ölçekleme**||
| [Tek bir veritabanını ölçekleme](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, tek bir veritabanının performans ölçümlerini izler, daha yüksek bir işlem boyutuna ölçeklendirir ve performans ölçümlerinden birinde bir uyarı kuralı oluşturur. |
| [Elastik havuzu ölçekleme](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, elastik bir havuzun performans ölçümlerini izler, daha yüksek bir işlem boyutuna ölçeklendirir ve performans ölçümlerinden birinde bir uyarı kuralı oluşturur. |
| **Denetim ve tehdit algılama** |
| [Denetimi ve tehdit algılamayı yapılandırma](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, bir veritabanı için denetim ve tehdit algılama ilkelerini yapılandırır. |
| **Veritabanını geri yükleme, kopyalama ve içeri aktarma**||
| [Veritabanını geri yükleme](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, bir veritabanını coğrafi olarak yedekli bir yedekten geri yükler ve silinen bir veritabanını en son yedeklemeye geri yükler. |
| [Bir veritabanını yeni bir sunucuya kopyalama](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, var olan bir veritabanının yeni bir sunucuda kopyasını oluşturur. |
| [Bacpac dosyasından bir veritabanını içeri aktarma](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği bir veritabanını bacpac dosyasından Azure SQL veritabanı 'na aktarır. |
| **Veritabanları arasında verileri eşitleme**||
| [Veritabanları arasında verileri eşitleme](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, Azure SQL veritabanı 'nda birden çok veritabanı arasında eşitlenecek veri eşitlemesini yapılandırır. |
| [SQL Veritabanı ile şirket içi SQL Server arasında verileri eşitleme](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, Azure SQL veritabanı 'ndaki bir veritabanı ile SQL Server şirket içi veritabanı arasında eşitlenecek veri eşitlemesini yapılandırır. |
| [SQL Data Sync için eşitleme şemasını güncelleştirme](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, Veri Eşitleme için eşitleme şemasından öğeler ekler veya öğeleri kaldırır. |
|||

[Tek veritabanı Azure PowerShell API 'si](single-database-manage.md#powershell)hakkında daha fazla bilgi edinin. 

## <a name="azure-sql-managed-instance"></a>[Azure SQL Yönetilen Örnek](#tab/managed-instance)

Aşağıdaki tabloda, Azure SQL yönetilen örneği için örnek Azure PowerShell betiklerin bağlantıları yer almaktadır.

|Bağlantı|Description|
|---|---|
|**Yönetilen örnekleri oluşturma ve yapılandırma**||
| [Yönetilen örnek oluşturma ve yönetme](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Bu PowerShell betiği, Azure PowerShell kullanarak yönetilen bir örnek oluşturmayı ve yönetmeyi gösterir. |
| [Azure Resource Manager şablonunu kullanarak yönetilen bir örnek oluşturma ve yönetme](../managed-instance/create-template-quickstart.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, Azure PowerShell ve Azure Resource Manager şablonunu kullanarak yönetilen bir örnek oluşturmayı ve yönetmeyi gösterir.|
| [Veritabanını başka bir coğrafi bölgede yönetilen bir örneğe geri yükleme](../managed-instance/scripts/restore-geo-backup.md) | Bu PowerShell betiği bir veritabanının yedeğini alır ve başka bir bölgeye geri yükler. Bu, coğrafi geri yükleme olağanüstü durum kurtarma senaryosu olarak bilinir. |
| **Saydam veri şifrelemesini yapılandırma**||
| [Azure Key Vault, kendi anahtarınızı kullanarak yönetilen bir örnekteki saydam veri şifrelemesini yönetin](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Bu PowerShell betiği, Azure Key Vault bir anahtar kullanarak Azure SQL yönetilen örneği için Kendi Anahtarını Getir senaryosunda saydam veri şifrelemesini yapılandırır.|
|**Yük devretme grubu yapılandırma**||
| [Yönetilen örnek için bir yük devretme grubu yapılandırma](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Bu PowerShell betiği, iki yönetilen örnek oluşturur, bunları bir yük devretme grubuna ekler ve ardından birincil yönetilen örnekten yük devretmeyi ikinci yönetilen örneğe sınar. |
|||

[Azure SQL yönetilen örneği Için PowerShell cmdlet 'leri](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances)hakkında daha fazla bilgi edinin.

---

## <a name="additional-resources"></a>Ek kaynaklar

Bu sayfada listelenen örnekler, Azure SQL kaynaklarını oluşturmak ve yönetmek için [PowerShell cmdlet 'lerini](/powershell/module/az.sql/) kullanır. Sorgu çalıştırmak ve birçok veritabanı görevinin gerçekleştirilmesi için ek cmdlet 'ler, [SqlServer](/powershell/module/sqlserver/) modülünde bulunur. Daha fazla bilgi için bkz. [PowerShell SQL Server](/sql/powershell/sql-server-powershell/).