---
title: Sunucular
description: Azure SQL veritabanı sunucuları ve bunların yönetimi hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: a3ad3314b8a18ec6a63b5d51bc7d8b3f9d7a6260
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818295"
---
# <a name="azure-sql-database-servers-and-their-management"></a>Azure SQL veritabanı sunucuları ve bunların yönetimi

## <a name="what-is-an-azure-sql-database-server"></a>Azure SQL veritabanı sunucusu nedir?

SQL veritabanı sunucusu, birden çok tek veya [havuza alınmış](sql-database-elastic-pool.md) veritabanları, [oturum açmalar](sql-database-manage-logins.md), [güvenlik duvarı kuralları](sql-database-firewall-configure.md), [denetim kuralları](sql-database-auditing.md), [tehdit algılama ilkeleri](sql-database-threat-detection.md)ve [için merkezi bir yönetim noktası görevi gören mantıksal bir yapıdır. Yük devretme grupları](sql-database-auto-failover-group.md) BIR SQL veritabanı sunucusu, kaynak grubundan farklı bir bölgede olabilir. SQL veritabanı sunucusu, Azure SQL veritabanını oluşturabilmeniz için mevcut olmalıdır. SQL veritabanı sunucusu tarafından yönetilen tüm veritabanları, SQL veritabanı sunucusuyla aynı bölge içinde oluşturulur.

Bir SQL veritabanı sunucusu, şirket içi dünyada bildiğiniz bir SQL Server örneğinden farklıdır. Özellikle, SQL veritabanı hizmeti, veritabanlarını yöneten SQL veritabanı sunucusuyla ilişkili olarak veritabanlarının konumuyla ilgili hiçbir garanti vermez ve örnek düzeyinde erişim veya özellik gerektirmez. Buna karşılık, yönetilen bir örnekteki örnek veritabanlarının hepsi, şirket içi dünyada SQL Server hakkında bilgi sahibi olduğunuz şekilde birlikte bulunur.

Bir SQL veritabanı sunucusu oluşturduğunuzda, bu sunucu üzerindeki ana veritabanına ve bu sunucuda oluşturulan tüm veritabanlarına yönetici haklarına sahip bir sunucu oturum açma hesabı ve parolası sağlarsınız. Bu ilk hesap bir SQL oturum açma hesabıdır. Azure SQL veritabanı kimlik doğrulaması için SQL kimlik doğrulamasını ve Azure Active Directory kimlik doğrulamasını destekler. Oturum açma ve kimlik doğrulama hakkında bilgi için bkz. [Azure SQL veritabanı 'Nda veritabanlarını ve oturum açma Işlemlerini yönetme](sql-database-manage-logins.md). Windows Kimlik Doğrulaması desteklenmez.

Bir SQL veritabanı sunucusu:

- Bir Azure aboneliği içinde oluşturulur, ancak içerdiği kaynaklarla birlikte başka bir aboneliğe taşınabilir
- Veritabanları, elastik havuzlar ve veri ambarları için üst kaynaktır
- Veritabanları, elastik havuzlar ve veri ambarları için bir ad alanı sağlar
- Güçlü yaşam süresi semantiğinin bulunduğu mantıksal bir kapsayıcıdır; bir sunucuyu silin ve kapsanan veritabanlarını, elastik havuzları ve veri ambarları siler
- [Azure rol tabanlı erişim denetimi (RBAC)](/azure/role-based-access-control/overview) -veritabanlarına, elastik havuzlara ve sunucu içindeki veri ambarlarına katılan, sunucudan erişim haklarını devralır
- , Azure Kaynak Yönetimi amaçları için veritabanları, elastik havuzlar ve veri ambarlarının kimliğinin yüksek sıralı bir öğesidir (bkz. veritabanları ve havuzlar için URL şeması)
- Bir bölgedeki kaynakları birlikte bulundurur
- Veritabanı erişimi için bağlantı uç noktası sağlar (`<serverName>`.database.windows.net)
- Bir ana veritabanına bağlanarak DMV’ler aracılığıyla içerdiği kaynaklarla ilgili meta verilere erişim sağlar
- Veritabanları için, oturum açma bilgileri, güvenlik duvarı, denetim, tehdit algılama ve gibi yönetim ilkeleri için kapsam sağlar
- , Üst abonelik içindeki bir kota ile kısıtlanır (varsayılan olarak abonelik başına altı sunucu) ve [burada abonelik sınırlarına bakın](../azure-subscription-service-limits.md).
- , İçerdiği kaynaklar için veritabanı kotası ve DTU veya sanal çekirdek kotası kapsamını sağlar (örneğin, 45.000 DTU)
- Kapsanan kaynaklarda etkinleştirilen yetenekler için sürüm kapsamıdır
- Sunucu düzeyinde asıl kullanıcı bilgileri bir sunucudaki tüm veritabanlarını yönetebilir
- Şirketinizde sunucu üzerindeki bir veya daha fazla veritabanına erişim verilmiş SQL Server örneklerinde bulunanlara benzer kullanıcı bilgileri içerebilir ve sınırlı yönetici hakları alabilir. Daha fazla bilgi için bkz. [Kullanıcı Bilgileri](sql-database-manage-logins.md).
- Bir SQL veritabanı sunucusunda oluşturulan tüm veritabanları için varsayılan harmanlama `SQL_LATIN1_GENERAL_CP1_CI_AS`, `LATIN1_GENERAL` Ingilizce (Birleşik Devletler), `CP1` kod sayfası 1252, `CI` büyük/küçük harfe duyarsızdır ve `AS` aksan duyarsızdır.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Azure portal kullanarak Azure SQL sunucularını, veritabanlarını ve güvenlik duvarlarını yönetme

Azure SQL veritabanının kaynak grubunu zaman içinde veya sunucunun kendisini oluştururken oluşturabilirsiniz. Yeni bir SQL Server oluşturmak için veya yeni bir veritabanı oluşturmanın bir parçası olarak yeni bir SQL Server formu almak için birden çok yöntem vardır.

### <a name="create-a-blank-sql-database-server"></a>Boş bir SQL veritabanı sunucusu oluşturma

[Azure Portal](https://portal.azure.com)kullanarak BIR Azure SQL veritabanı sunucusu (veritabanı olmadan) oluşturmak için boş bir SQL Server (mantıksal sunucu) formuna gidin.  

### <a name="create-a-blank-or-sample-sql-database"></a>Boş veya örnek bir SQL veritabanı oluşturun

[Azure Portal](https://portal.azure.com)kullanarak BIR Azure SQL veritabanı oluşturmak için boş bir SQL veritabanı formuna gidin ve istenen bilgileri sağlayın. Azure SQL veritabanı 'nın kaynak grubunu ve SQL veritabanı sunucusunu zaman içinde veya veritabanını oluştururken oluşturabilirsiniz. Boş bir veritabanı oluşturabilir veya Adventure Works LT ' y a göre örnek bir veritabanı oluşturabilirsiniz.

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Veritabanınızın fiyatlandırma katmanını seçme hakkında daha fazla bilgi için bkz. [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md).

Yönetilen bir örnek oluşturmak için bkz. [yönetilen örnek oluşturma](sql-database-managed-instance-get-started.md)

### <a name="manage-an-existing-sql-server"></a>Mevcut bir SQL Server 'ı yönetme

Var olan bir sunucuyu yönetmek için, belirli SQL veritabanı sayfasından, **SQL Server** sayfasından veya **tüm kaynaklar** sayfasından farklı yöntemler kullanarak sunucuya gidin.

Var olan bir veritabanını yönetmek için **SQL veritabanları** sayfasına gidin ve yönetmek istediğiniz veritabanına tıklayın. Aşağıdaki ekran görüntüsünde, bir veritabanının **genel bakış** sayfasından bir veritabanı için sunucu düzeyinde güvenlik duvarının ayarlanmasına nasıl başlayabileceğiniz gösterilmektedir.

   ![sunucu güvenlik duvarı kuralı](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Bir veritabanının performans özelliklerini yapılandırmak için bkz. [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md).
> [!TIP]
> Azure portal hızlı başlangıç için bkz. [Azure Portal Azure SQL veritabanı oluşturma](sql-database-single-database-get-started.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>PowerShell kullanarak Azure SQL sunucularını, veritabanlarını ve güvenlik duvarlarını yönetme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Azure PowerShell ile Azure SQL Server, veritabanları ve güvenlik duvarları oluşturup yönetmek için aşağıdaki PowerShell cmdlet 'lerini kullanın. PowerShell 'i yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-az-ps). Elastik havuzlar oluşturma ve yönetme için bkz. [elastik havuzlar](sql-database-elastic-pool.md).

| Cmdlet | Açıklama |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Bir veritabanı oluşturur |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Bir veya daha fazla veritabanını alır|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Bir veritabanının özelliklerini ayarlar veya var olan bir veritabanını esnek bir havuza taşıanlar|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Bir veritabanını kaldırır|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Bir kaynak grubu oluşturur|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Sunucu oluşturur|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Sunucular hakkında bilgi döndürür|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Bir sunucunun özelliklerini değiştirir|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Bir sunucuyu kaldırır|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Sunucu düzeyinde bir güvenlik duvarı kuralı oluşturur |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Bir sunucu için güvenlik duvarı kurallarını alır|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Bir sunucudaki güvenlik duvarı kuralını değiştirir|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Bir güvenlik duvarı kuralını sunucudan siler.|
| New-AzSqlServerVirtualNetworkRule | Sanal ağ hizmeti uç noktası olan bir alt ağa bağlı olarak bir [*sanal ağ kuralı*](sql-database-vnet-service-endpoint-rule-overview.md)oluşturur. |

> [!TIP]
> PowerShell hızlı başlangıcı için bkz. [PowerShell kullanarak Azure SQL tek veritabanı oluşturma](sql-database-single-database-get-started.md). PowerShell örnek betikleri için bkz. PowerShell kullanarak [Azure SQL tek veritabanı oluşturma ve bir güvenlik duvarı kuralı yapılandırma](scripts/sql-database-create-and-configure-database-powershell.md) ve [POWERSHELL kullanarak Azure SQL tek veritabanını izleme ve ölçeklendirme](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Azure CLı kullanarak Azure SQL sunucularını, veritabanlarını ve güvenlik duvarlarını yönetme

Azure [CLI](/cli/azure)Ile Azure SQL Server, veritabanları ve güvenlik duvarları oluşturup yönetmek Için AŞAĞıDAKI [Azure CLI SQL veritabanı](/cli/azure/sql/db) komutlarını kullanın. CLI’yi tarayıcınızda çalıştırmak için [Cloud Shell](/azure/cloud-shell/overview) kullanın veya macOS, Linux ya da Windows’da [yükleyin](/cli/azure/install-azure-cli). Elastik havuzlar oluşturma ve yönetme için bkz. [elastik havuzlar](sql-database-elastic-pool.md).

| Cmdlet | Açıklama |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Bir veritabanı oluşturur|
|[az SQL DB List](/cli/azure/sql/db#az-sql-db-list)|Bir sunucudaki tüm veritabanlarını ve veri ambarlarını veya elastik havuzdaki tüm veritabanlarını listeler|
|[az SQL DB List-Editions](/cli/azure/sql/db#az-sql-db-list-editions)|Kullanılabilir hizmet hedeflerini ve depolama sınırlarını listeler|
|[az SQL DB List-kullanımlar](/cli/azure/sql/db#az-sql-db-list-usages)|Veritabanı kullanımlarını döndürür|
|[az SQL DB Show](/cli/azure/sql/db#az-sql-db-show)|Bir veritabanını veya veri ambarını alır|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Bir veritabanını güncelleştirir|
|[az SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete)|Bir veritabanını kaldırır|
|[az group create](/cli/azure/group#az-group-create)|Bir kaynak grubu oluşturur|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Sunucu oluşturur|
|[az SQL Server List](/cli/azure/sql/server#az-sql-server-list)|Sunucuları listeler|
|[az SQL Server List-kullanımlar](/cli/azure/sql/server#az-sql-server-list-usages)|Sunucu kullanımlarını döndürür|
|[az SQL Server Show](/cli/azure/sql/server#az-sql-server-show)|Bir sunucu alır|
|[az SQL Server Update](/cli/azure/sql/server#az-sql-server-update)|Bir sunucuyu güncelleştirir|
|[az SQL Server DELETE](/cli/azure/sql/server#az-sql-server-delete)|Bir sunucuyu siler|
|[az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Sunucu güvenlik duvarı kuralı oluşturur|
|[az SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Bir sunucudaki güvenlik duvarı kurallarını listeler|
|[az SQL Server Firewall-Rule Show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Bir güvenlik duvarı kuralının ayrıntılarını gösterir|
|[az SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Bir güvenlik duvarı kuralını güncelleştirir|
|[az SQL Server Firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Bir güvenlik duvarı kuralını siler|

> [!TIP]
> Azure CLı hızlı başlangıç için bkz. Azure [CLI kullanarak Azure SQL tek veritabanı oluşturma](sql-database-cli-samples.md). Azure CLı örnek betikleri için bkz. [CLI kullanarak Azure SQL tek veritabanı oluşturma ve bir güvenlik duvarı kuralı yapılandırma](scripts/sql-database-create-and-configure-database-cli.md) ve [CLI kullanarak bir Azure SQL tek veritabanını izleme ve ölçeklendirme](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Transact-SQL kullanarak Azure SQL sunucularını, veritabanlarını ve güvenlik duvarlarını yönetme

Transact-SQL ile Azure SQL Server, veritabanları ve güvenlik duvarları oluşturup yönetmek için aşağıdaki T-SQL komutlarını kullanın. Bu komutları, Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)veya bir Azure SQL veritabanı sunucusuna BAĞLANABILECEK ve Transact-SQL komutlarını geçirebilmeniz gereken herhangi bir programı kullanarak verebilirsiniz. Elastik havuzları yönetmek için bkz. [elastik havuzlar](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Transact-SQL kullanarak sunucu oluşturamaz veya silemezsiniz.
>

| Komut | Açıklama |
| --- | --- |
|[VERITABANı oluşturma (Azure SQL veritabanı)](/sql/t-sql/statements/create-database-azure-sql-database)|Yeni bir veritabanı oluşturur. Yeni bir veritabanı oluşturmak için ana veritabanına bağlı olmanız gerekir.|
| [ALTER DATABASE (Azure SQL veritabanı)](/sql/t-sql/statements/alter-database-azure-sql-database) |Bir Azure SQL veritabanını değiştirir. |
|[ALTER DATABASE (Azure SQL veri ambarı)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Bir Azure SQL veri ambarını değiştirir.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Bir veritabanını siler.|
|[sys. database_service_objectives (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL veritabanı veya Azure SQL veri ambarı için sürüm (hizmet katmanı), hizmet hedefi (Fiyatlandırma Katmanı) ve elastik havuz adı döndürür. Azure SQL veritabanı sunucusunda ana veritabanında oturum açarsa, tüm veritabanlarına ilişkin bilgileri döndürür. Azure SQL veri ambarı için ana veritabanına bağlı olmanız gerekir.|
|[sys. dm_db_resource_stats (Azure SQL veritabanı)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Bir Azure SQL veritabanı veritabanı için CPU, GÇ ve bellek tüketimini döndürür. Veritabanında etkinlik olmasa bile, her 15 saniyede bir satır vardır.|
|[sys. resource_stats (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Bir Azure SQL veritabanı için CPU kullanımı ve depolama verilerini döndürür. Veriler, beş dakikalık aralıklar içinde toplanır ve toplanır.|
|[sys. database_connection_stats (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Veritabanı bağlantısı başarıları ve hatalarıyla ilgili bir genel bakış sunan SQL veritabanı veritabanı bağlantı olayları için istatistikleri içerir. |
|[sys. event_log (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Başarılı Azure SQL veritabanı bağlantılarını, bağlantı başarısızlıklarını ve kilitlenmeleri döndürür. Bu bilgileri, SQL veritabanı ile veritabanı etkinliğinizi izlemek veya sorunlarını gidermek için kullanabilirsiniz.|
|[sp_set_firewall_rule (Azure SQL veritabanı)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|SQL veritabanı sunucunuz için sunucu düzeyinde güvenlik duvarı ayarlarını oluşturur veya güncelleştirir. Bu saklı yordam yalnızca ana veritabanında sunucu düzeyinde asıl oturum açma için kullanılabilir. Sunucu düzeyi güvenlik duvarı kuralı, yalnızca ilk sunucu düzeyi güvenlik duvarı kuralı, Azure düzeyinde izinlerle bir kullanıcı tarafından oluşturulduktan sonra Transact-SQL kullanılarak oluşturulabilir|
|[sys. firewall_rules (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Microsoft Azure SQL Veritabanı ilişkili sunucu düzeyi güvenlik duvarı ayarları hakkında bilgi döndürür.|
|[sp_delete_firewall_rule (Azure SQL veritabanı)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|SQL veritabanı sunucunuzdaki sunucu düzeyi güvenlik duvarı ayarlarını kaldırır. Bu saklı yordam yalnızca ana veritabanında sunucu düzeyinde asıl oturum açma için kullanılabilir.|
|[sp_set_database_firewall_rule (Azure SQL veritabanı)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Azure SQL veritabanınız veya SQL veri ambarınız için veritabanı düzeyinde güvenlik duvarı kuralları oluşturur veya güncelleştirir. Veritabanı güvenlik duvarı kuralları, ana veritabanı ve SQL veritabanı 'ndaki Kullanıcı veritabanları için yapılandırılabilir. Kapsanan veritabanı kullanıcıları kullanılırken veritabanı güvenlik duvarı kuralları yararlı olur. |
|[sys. database_firewall_rules (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Microsoft Azure SQL Veritabanı ilişkili veritabanı düzeyinde güvenlik duvarı ayarları hakkında bilgi döndürür. |
|[sp_delete_database_firewall_rule (Azure SQL veritabanı)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Azure SQL veritabanı veya SQL veri Ambarınızdan veritabanı düzeyinde güvenlik duvarı ayarını kaldırır. |

> [!TIP]
> Microsoft Windows üzerinde SQL Server Management Studio kullanan bir hızlı başlangıç için bkz. [Azure SQL veritabanı: bağlanmak ve veri sorgulamak için SQL Server Management Studio kullanma](sql-database-connect-query-ssms.md). MacOS, Linux veya Windows üzerinde Visual Studio Code kullanan bir hızlı başlangıç için bkz. [Azure SQL veritabanı: bağlanmak ve veri sorgulamak için Visual Studio Code kullanma](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>REST API kullanarak Azure SQL sunucularını, veritabanlarını ve güvenlik duvarlarını yönetme

Azure SQL Server, veritabanları ve güvenlik duvarları oluşturup yönetmek için bu REST API isteklerini kullanın.

| Komut | Açıklama |
| --- | --- |
|[Sunucular-oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Yeni bir sunucu oluşturur veya güncelleştirir.|
|[Sunucular-Sil](https://docs.microsoft.com/rest/api/sql/servers/delete)|Bir SQL sunucusunu siler.|
|[Sunucular-al](https://docs.microsoft.com/rest/api/sql/servers/get)|Bir sunucu alır.|
|[Sunucular-liste](https://docs.microsoft.com/rest/api/sql/servers/list)|Sunucu listesini döndürür.|
|[Sunucular-kaynak grubuna göre Listele](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Bir kaynak grubundaki sunucuların listesini döndürür.|
|[Sunucular-Güncelleştir](https://docs.microsoft.com/rest/api/sql/servers/update)|Var olan bir sunucuyu güncelleştirir.|
|[Veritabanları-oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Yeni bir veritabanı oluşturur veya var olan bir veritabanını güncelleştirir.|
|[Veritabanları-Sil](https://docs.microsoft.com/rest/api/sql/databases/delete)|Bir veritabanını siler.|
|[Veritabanları-al](https://docs.microsoft.com/rest/api/sql/databases/get)|Bir veritabanını alır.|
|[Veritabanları-elastik havuza göre Listele](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Elastik havuzdaki veritabanlarının listesini döndürür.|
|[Veritabanları-sunucuya göre listeleme](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Bir sunucudaki veritabanlarının listesini döndürür.|
|[Veritabanları-Güncelleştir](https://docs.microsoft.com/rest/api/sql/databases/update)|Var olan bir veritabanını güncelleştirir.|
|[Güvenlik duvarı kuralları-oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Bir güvenlik duvarı kuralı oluşturur veya güncelleştirir.|
|[Güvenlik duvarı kuralları-Sil](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Bir güvenlik duvarı kuralını siler.|
|[Güvenlik duvarı kuralları-al](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Bir güvenlik duvarı kuralı alır.|
|[Güvenlik duvarı kuralları-sunucuya göre Listele](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Güvenlik Duvarı kurallarının bir listesini döndürür.|

## <a name="next-steps"></a>Sonraki adımlar

- SQL Server bir veritabanını Azure 'a geçirme hakkında bilgi edinmek için bkz. [Azure SQL veritabanı 'Na geçiş](sql-database-single-database-migrate.md).
- Desteklenen özellikler hakkında bilgi edinmek için bkz. [Özellikler](sql-database-features.md).
