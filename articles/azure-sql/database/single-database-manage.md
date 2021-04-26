---
title: Sunucu oluşturma & ve tek veritabanları yönetme
description: Azure portal, PowerShell, Azure CLı, Transact-SQL (T-SQL) ve REST API kullanarak Azure SQL veritabanı 'nda sunucular ve tek veritabanları oluşturma ve yönetme hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 4aaabdb3d21c41b973b21e6e52442be132796196
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781604"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Azure SQL veritabanı 'nda sunucular ve tek veritabanları oluşturma ve yönetme

Azure portal, PowerShell, Azure CLı, REST API ve Transact-SQL kullanarak Azure SQL veritabanı 'nda sunucular ve tek veritabanları oluşturabilir ve yönetebilirsiniz.

## <a name="the-azure-portal"></a>Azure portal

Azure SQL veritabanı için kaynak grubunu zaman içinde veya sunucunun kendisini oluştururken oluşturabilirsiniz.

### <a name="create-a-server"></a>Sunucu oluşturma

[Azure Portal](https://portal.azure.com)kullanarak sunucu oluşturmak Için Azure Marketi 'nden yeni bir [sunucu](logical-servers.md) kaynağı oluşturun. Alternatif olarak, bir Azure SQL veritabanı dağıtırken sunucu oluşturabilirsiniz.

  ![Sunucu oluştur](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Boş veya örnek veritabanı oluşturma

[Azure Portal](https://portal.azure.com)kullanarak tek BIR Azure SQL veritabanı oluşturmak Için Azure Market 'TE Azure SQL veritabanı kaynağını seçin. Kaynak grubunu ve sunucuyu önceden veya tek veritabanının kendisini oluştururken oluşturabilirsiniz. Boş bir veritabanı oluşturabilir veya Adventure Works LT ' y a göre örnek bir veritabanı oluşturabilirsiniz.

  ![create database-1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Veritabanınızın fiyatlandırma katmanını seçme hakkında daha fazla bilgi için bkz. [DTU tabanlı satın alma modeli](service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md).

## <a name="manage-an-existing-server"></a>Var olan bir sunucuyu yönetme

Var olan bir sunucuyu yönetmek için, belirli bir veritabanı sayfasından, **SQL Server** sayfasından veya **tüm kaynaklar** sayfasından bir dizi yöntemi kullanarak sunucuya gidin.

Var olan bir veritabanını yönetmek için **SQL veritabanları** sayfasına gidin ve yönetmek istediğiniz veritabanını seçin. Aşağıdaki ekran görüntüsünde, bir veritabanının **genel bakış** sayfasından bir veritabanı için sunucu düzeyinde güvenlik duvarının ayarlanmasına nasıl başlayabileceğiniz gösterilmektedir.

   ![sunucu güvenlik duvarı kuralı](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Bir veritabanının performans özelliklerini yapılandırmak için bkz. [DTU tabanlı satın alma modeli](service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md).
> [!TIP]
> Azure portal hızlı başlangıç için, bkz. [Azure Portal SQL veritabanı 'nda veritabanı oluşturma](single-database-create-quickstart.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Sunucular, tek ve havuza alınmış veritabanları ve Azure PowerShell ile sunucu düzeyi güvenlik duvarları oluşturmak ve yönetmek için aşağıdaki PowerShell cmdlet 'lerini kullanın. PowerShell 'i yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-az-ps).

> [!TIP]
> PowerShell örnek betikleri için bkz. PowerShell kullanarak [SQL veritabanı 'nda veritabanı oluşturma ve sunucu düzeyinde bir güvenlik duvarı kuralı yapılandırma](scripts/create-and-configure-database-powershell.md) ve [PowerShell kullanarak SQL veritabanı 'Nda bir veritabanını izleme ve ölçeklendirme](scripts/monitor-and-scale-database-powershell.md).

| Cmdlet | Açıklama |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Bir veritabanı oluşturur |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Bir veya daha fazla veritabanını alır|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Bir veritabanının özelliklerini ayarlar veya var olan bir veritabanını esnek bir havuza taşıanlar|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Bir veritabanını kaldırır|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Bir kaynak grubu oluşturur|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Sunucu oluşturur|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Sunucular hakkında bilgi döndürür|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Bir sunucunun özelliklerini değiştirir|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Bir sunucuyu kaldırır|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Sunucu düzeyinde bir güvenlik duvarı kuralı oluşturur |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Bir sunucu için güvenlik duvarı kurallarını alır|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Bir sunucudaki güvenlik duvarı kuralını değiştirir|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Bir güvenlik duvarı kuralını sunucudan siler.|
| New-AzSqlServerVirtualNetworkRule | Sanal ağ hizmeti uç noktası olan bir alt ağa bağlı olarak bir [*sanal ağ kuralı*](vnet-service-endpoint-rule-overview.md)oluşturur. |

## <a name="the-azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure)ile sunucuları, veritabanlarını ve güvenlik duvarlarını oluşturup yönetmek Için AŞAĞıDAKI [Azure CLI](/cli/azure/sql/db) komutlarını kullanın. CLI’yi tarayıcınızda çalıştırmak için [Cloud Shell](../../cloud-shell/overview.md) kullanın veya macOS, Linux ya da Windows’da [yükleyin](/cli/azure/install-azure-cli). Elastik havuzlar oluşturma ve yönetme için bkz. [elastik havuzlar](elastic-pool-overview.md).

> [!TIP]
> Azure CLı hızlı başlangıç için bkz. [Azure CLI kullanarak tek bir Azure SQL veritabanı oluşturma](az-cli-script-samples-content-guide.md). Azure CLı örnek betikleri için bkz. [CLI kullanarak Azure SQL veritabanında veritabanı oluşturma ve BIR SQL veritabanı güvenlik duvarı kuralı yapılandırma](scripts/create-and-configure-database-cli.md) ve [Azure SQL veritabanı 'nda bir veritabanını izlemek ve ölçeklendirmek için CLI](scripts/monitor-and-scale-database-cli.md)kullanma.
>

| Cmdlet | Açıklama |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Bir veritabanı oluşturur|
|[az SQL DB List](/cli/azure/sql/db#az_sql_db_list)|Bir sunucudaki tüm veritabanlarını ve veri ambarlarını veya elastik havuzdaki tüm veritabanlarını listeler|
|[az SQL DB List-Editions](/cli/azure/sql/db#az_sql_db_list_editions)|Kullanılabilir hizmet hedeflerini ve depolama sınırlarını listeler|
|[az SQL DB List-kullanımlar](/cli/azure/sql/db#az_sql_db_list_usages)|Veritabanı kullanımlarını döndürür|
|[az SQL DB Show](/cli/azure/sql/db#az_sql_db_show)|Bir veritabanını veya veri ambarını alır|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Bir veritabanını güncelleştirir|
|[az SQL DB Delete](/cli/azure/sql/db#az_sql_db_delete)|Bir veritabanını kaldırır|
|[az group create](/cli/azure/group#az_group_create)|Bir kaynak grubu oluşturur|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Sunucu oluşturur|
|[az SQL Server List](/cli/azure/sql/server#az_sql_server_list)|Sunucuları listeler|
|[az SQL Server List-kullanımlar](/cli/azure/sql/server#az_sql_server_list-usages)|Sunucu kullanımlarını döndürür|
|[az SQL Server Show](/cli/azure/sql/server#az_sql_server_show)|Bir sunucu alır|
|[az SQL Server Update](/cli/azure/sql/server#az_sql_server_update)|Bir sunucuyu güncelleştirir|
|[az SQL Server DELETE](/cli/azure/sql/server#az_sql_server_delete)|Bir sunucuyu siler|
|[az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Sunucu güvenlik duvarı kuralı oluşturur|
|[az SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Bir sunucudaki güvenlik duvarı kurallarını listeler|
|[az SQL Server Firewall-Rule Show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Bir güvenlik duvarı kuralının ayrıntılarını gösterir|
|[az SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Bir güvenlik duvarı kuralını güncelleştirir|
|[az SQL Server Firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Bir güvenlik duvarı kuralını siler|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Transact-SQL ile sunucular, veritabanları ve güvenlik duvarları oluşturup yönetmek için aşağıdaki T-SQL komutlarını kullanın. Bu komutları Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [VISUAL STUDIO Code](https://code.visualstudio.com/docs)ya da SQL veritabanı 'Nda sunucuya BAĞLANABILECEK ve Transact-SQL komutlarını geçirebilmeniz gereken başka bir programı kullanarak verebilirsiniz. Elastik havuzları yönetmek için bkz. [elastik havuzlar](elastic-pool-overview.md).

> [!TIP]
> Microsoft Windows üzerinde SQL Server Management Studio kullanan bir hızlı başlangıç için bkz. [Azure SQL veritabanı: bağlanmak ve veri sorgulamak için SQL Server Management Studio kullanma](connect-query-ssms.md). MacOS, Linux veya Windows üzerinde Visual Studio Code kullanan bir hızlı başlangıç için bkz. [Azure SQL veritabanı: bağlanmak ve veri sorgulamak için Visual Studio Code kullanma](connect-query-vscode.md).
> [!IMPORTANT]
> Transact-SQL kullanarak sunucu oluşturamaz veya silemezsiniz.

| Komut | Açıklama |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Yeni bir tek veritabanı oluşturur. Yeni bir veritabanı oluşturmak için ana veritabanına bağlı olmanız gerekir.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Bir veritabanını veya elastik havuzu değiştirir. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Bir veritabanını siler.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL veritabanı veya Azure SYNAPSE Analytics 'te adanmış bir SQL havuzu için sürüm (hizmet katmanı), hizmet hedefi (Fiyatlandırma Katmanı) ve elastik havuz adı döndürür. SQL veritabanı 'ndaki bir sunucuda ana veritabanında oturum açtıysanız tüm veritabanlarına bilgi döndürür. Azure SYNAPSE Analytics için ana veritabanına bağlı olmanız gerekir.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Azure SQL veritabanı 'nda bir veritabanı için CPU, GÇ ve bellek tüketimi döndürür. Veritabanında etkinlik olmasa bile, her 15 saniyede bir satır vardır.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Azure SQL veritabanı 'nda bir veritabanının CPU kullanımını ve depolama verilerini döndürür. Veriler, beş dakikalık aralıklar içinde toplanır ve toplanır.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Veritabanı bağlantısı başarılarına ve hatalara genel bir bakış sunan SQL veritabanı bağlantı olayları için istatistikler içerir. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Başarılı Azure SQL veritabanı bağlantılarını, bağlantı başarısızlıklarını ve kilitlenmeleri döndürür. Bu bilgileri, SQL veritabanı ile veritabanı etkinliğinizi izlemek veya sorunlarını gidermek için kullanabilirsiniz.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Sunucunuz için sunucu düzeyinde güvenlik duvarı ayarlarını oluşturur veya güncelleştirir. Bu saklı yordam yalnızca ana veritabanında sunucu düzeyinde asıl oturum açma için kullanılabilir. Sunucu düzeyi güvenlik duvarı kuralı, yalnızca ilk sunucu düzeyi güvenlik duvarı kuralı, Azure düzeyinde izinlerle bir kullanıcı tarafından oluşturulduktan sonra Transact-SQL kullanılarak oluşturulabilir|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Azure SQL veritabanı 'nda veritabanınızdan ilişkili sunucu düzeyi güvenlik duvarı ayarları hakkında bilgi döndürür.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Sunucu düzeyi güvenlik duvarı ayarlarını sunucudan kaldırır. Bu saklı yordam yalnızca ana veritabanında sunucu düzeyinde asıl oturum açma için kullanılabilir.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Azure SQL veritabanı 'nda veritabanınız için veritabanı düzeyinde güvenlik duvarı kuralları oluşturur veya güncelleştirir. Veritabanı güvenlik duvarı kuralları, ana veritabanı ve SQL veritabanı 'ndaki Kullanıcı veritabanları için yapılandırılabilir. Kapsanan veritabanı kullanıcıları kullanılırken veritabanı güvenlik duvarı kuralları yararlı olur. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Azure SQL veritabanı 'nda veritabanınızdan ilişkili veritabanı düzeyinde güvenlik duvarı ayarları hakkında bilgi döndürür. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Veritabanı düzeyinde güvenlik duvarı ayarını veritabanından kaldırır. |

## <a name="rest-api"></a>REST API

Sunucuları, veritabanlarını ve güvenlik duvarlarını oluşturup yönetmek için, bu REST API isteklerini kullanın.

| Komut | Açıklama |
| --- | --- |
|[Sunucular-oluştur veya güncelleştir](/rest/api/sql/servers/createorupdate)|Yeni bir sunucu oluşturur veya güncelleştirir.|
|[Sunucular-Sil](/rest/api/sql/servers/delete)|Bir SQL sunucusunu siler.|
|[Sunucular-al](/rest/api/sql/servers/get)|Bir sunucu alır.|
|[Sunucular-liste](/rest/api/sql/servers/list)|Bir abonelikteki sunucuların listesini döndürür.|
|[Sunucular-kaynak grubuna göre Listele](/rest/api/sql/servers/listbyresourcegroup)|Bir kaynak grubundaki sunucuların listesini döndürür.|
|[Sunucular-Güncelleştir](/rest/api/sql/servers/update)|Var olan bir sunucuyu güncelleştirir.|
|[Veritabanları-oluştur veya güncelleştir](/rest/api/sql/databases/createorupdate)|Yeni bir veritabanı oluşturur veya var olan bir veritabanını güncelleştirir.|
|[Veritabanları-Sil](/rest/api/sql/databases/delete)|Bir veritabanını siler.|
|[Veritabanları-al](/rest/api/sql/databases/get)|Bir veritabanını alır.|
|[Veritabanları-elastik havuza göre Listele](/rest/api/sql/databases/listbyelasticpool)|Elastik havuzdaki veritabanlarının listesini döndürür.|
|[Veritabanları-sunucuya göre listeleme](/rest/api/sql/databases/listbyserver)|Bir sunucudaki veritabanlarının listesini döndürür.|
|[Veritabanları-Güncelleştir](/rest/api/sql/databases/update)|Var olan bir veritabanını güncelleştirir.|
|[Güvenlik duvarı kuralları-oluştur veya güncelleştir](/rest/api/sql/firewallrules/createorupdate)|Bir güvenlik duvarı kuralı oluşturur veya güncelleştirir.|
|[Güvenlik duvarı kuralları-Sil](/rest/api/sql/firewallrules/delete)|Bir güvenlik duvarı kuralını siler.|
|[Güvenlik duvarı kuralları-al](/rest/api/sql/firewallrules/get)|Bir güvenlik duvarı kuralı alır.|
|[Güvenlik duvarı kuralları-sunucuya göre Listele](/rest/api/sql/firewallrules/listbyserver)|Güvenlik Duvarı kurallarının bir listesini döndürür.|

## <a name="next-steps"></a>Sonraki adımlar

- SQL Server bir veritabanını Azure 'a geçirme hakkında bilgi edinmek için bkz. [Azure SQL veritabanı 'Na geçiş](migrate-to-database-from-sql-server.md).
- Desteklenen özellikler hakkında bilgi için bkz. [Özellikler](features-comparison.md).
